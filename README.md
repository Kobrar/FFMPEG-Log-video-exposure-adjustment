# Log exposure correction via ffmpeg `curves`

If you've ever recorded some log footage that turned out badly exposed, and
your go to fast editing suite is `ffmpeg`, then this tool should help you out
big.
This tool generates a command that uses `curves` filter to freely adjust video
exposure in mathematically correct fashion. Should support most common log
formats.

The tool (and this readme) has been vibe coded and I've only been able to test
V-Log. YMMV.

---

## Usage

Open [`src/index.html`](https://html-preview.github.io/?url=https://github.com/Kobrar/FFMPEG-Log-video-exposure-adjustment/blob/main/src/index.html) in a browser, select the log profile, set the EV
correction and the number of LUT curve points, enter input/output
file names and any extra filters/options. The tool generates a copy-pasteable
`ffmpeg` command, e.g.:

```
ffmpeg -i input.mp4 -vf "curves=all='0/0 0.014/0.009 0.02/0.013 0.027/0.018 ...'" output.mp4
```

The tool uses yuv444 conversion before curves to minimize conversion inaccuracies.

---

## Sample footage

The scene was shot on a Lumix S1 in V-Log. The reference is a frame at
**ISO 2500** — no exposure correction applied. Keep in mind, above ISO 4000
camera kicks into higher base ISO.

---

## 1. Comparisons

### ISO 640 → +2 EV

| Reference | Without LUT | Reference with LUT | With LUT |
|--------------------|-------------|-----------------------------|----------|
| ![ref](images/ISO_2500_S_2_REFERENCE.png) | ![640 without LUT](images/ISO_640_S_2_2ev.png) | ![ref with LUT](images/ISO_2500_S_2_REFERENCE_lut.png) | ![640 with LUT](images/ISO_640_S_2_2ev_lut.png) |

### ISO 2500 → 0 EV

| Reference | Reference with LUT |
|--------------------|----------|
| ![ref](images/ISO_2500_S_2_REFERENCE.png) | ![ref with LUT](images/ISO_2500_S_2_REFERENCE_lut.png) |

### ISO 10000 → −2 EV

| Reference | Without LUT | Reference with LUT | With LUT |
|--------------------|-------------|-----------------------------|----------|
| ![ref](images/ISO_2500_S_2_REFERENCE.png) | ![10000 without LUT](images/ISO_10000_S_2_-2ev.png) | ![ref with LUT](images/ISO_2500_S_2_REFERENCE_lut.png) | ![10000 with LUT](images/ISO_10000_S_2_-2ev_lut.png) |

---

## 2. Influence of the curve point count

The default number of points is **20**.
Below are the same corrections with the exposure curve built from **6, 10, 20, 30,
50, 100 and 150**.

NOTE: at very high point counts curves filter throws error. Not very significant as diminishing returns kick in very early.

### ISO 640 → +2 EV

| Reference | 6 pts | 10 pts | 20 pts |
|-----------|-------|--------|--------|
| ![ref](images/ISO_2500_S_2_REFERENCE.png) | ![6p](images/ISO_640_S_2_2ev_6p.png) | ![10p](images/ISO_640_S_2_2ev_10p.png) | ![20p](images/ISO_640_S_2_2ev_20p.png) |

| 30 pts | 50 pts | 100 pts | 150 pts |
|--------|--------|---------|---------|
| ![30p](images/ISO_640_S_2_2ev.png) | ![50p](images/ISO_640_S_2_2ev_50p.png) | ![100p](images/ISO_640_S_2_2ev_100p.png) | ![150p](images/ISO_640_S_2_2ev_150p.png) |

### ISO 10000 → −2 EV

| Reference | 6 pts | 10 pts | 20 pts |
|-----------|-------|--------|--------|
| ![ref](images/ISO_2500_S_2_REFERENCE.png) | ![6p](images/ISO_10000_S_2_-2ev_6p.png) | ![10p](images/ISO_10000_S_2_-2ev_10p.png) | ![20p](images/ISO_10000_S_2_-2ev_20p.png) |

| 30 pts | 50 pts | 100 pts | 150 pts |
|--------|--------|---------|---------|
| ![30p](images/ISO_10000_S_2_-2ev.png) | ![50p](images/ISO_10000_S_2_-2ev_50p.png) | ![100p](images/ISO_10000_S_2_-2ev_100p.png) | ![150p](images/ISO_10000_S_2_-2ev_150p.png) |

### Difference vs the 150-point reference (PSNR/SSIM)

Reference for the comparison is the **150-point** variant (highest point count
available). The 6-point variant clearly deviates; from 10 points on,
differences are at the level of conversion noise.

**ISO 640 / +2 EV**

| Variant | PSNR vs 150 pts | SSIM vs 150 pts |
|---------|-----------------|-----------------|
| 6 pts | 36.7 dB | 0.996 |
| 10 pts | 55.1 dB | 0.999 |
| 20 pts | 54.2 dB | 0.998 |
| 30 pts | 54.2 dB | 0.999 |
| 50 pts | 54.8 dB | 0.998 |
| 100 pts | 53.3 dB | 0.997 |
| 150 pts | — (reference) | — (reference) |

**ISO 10000 / −2 EV**

| Variant | PSNR vs 150 pts | SSIM vs 150 pts |
|---------|-----------------|-----------------|
| 6 pts | 37.5 dB | 0.997 |
| 10 pts | 55.5 dB | 0.999 |
| 20 pts | 53.7 dB | 0.998 |
| 30 pts | 53.5 dB | 0.998 |
| 50 pts | 55.1 dB | 0.999 |
| 100 pts | 55.1 dB | 0.998 |
| 150 pts | — (reference) | — (reference) |

### Round trip

A round-trip test on the ISO 2500 reference frame: the exposure curve was
applied **5 times** (4× **+0.5 EV**, then **−2 EV**).

| Reference | 6 pts | 10 pts | 20 pts |
|-----------|-------|--------|--------|
| ![ref](images/ISO_2500_S_2_REFERENCE.png) | ![5p](images/ISO_2500_S_2_REFERENCE_05ev_05ev_05ev_05ev_-2ev_6p.png) | ![10p](images/ISO_2500_S_2_REFERENCE_05ev_05ev_05ev_05ev_-2ev_10p.png) | ![20p](images/ISO_2500_S_2_REFERENCE_05ev_05ev_05ev_05ev_-2ev_20p.png) |

| 30 pts | 50 pts | 100 pts | 150 pts |
|--------|--------|---------|---------|
| ![30p](images/ISO_2500_S_2_REFERENCE_05ev_05ev_05ev_05ev_-2ev_30p.png) | ![50p](images/ISO_2500_S_2_REFERENCE_05ev_05ev_05ev_05ev_-2ev_50p.png) | ![100p](images/ISO_2500_S_2_REFERENCE_05ev_05ev_05ev_05ev_-2ev_100p.png) | ![150p](images/ISO_2500_S_2_REFERENCE_05ev_05ev_05ev_05ev_-2ev_150p.png) |

| Variant | PSNR vs reference | SSIM vs reference |
|---------|-------------------|-------------------|
| 6 pts | 32.1 dB | 0.982 |
| 10 pts | 41.2 dB | 0.988 |
| 20 pts | 44.1 dB | 0.989 |
| 30 pts | 40.5 dB | 0.989 |
| 50 pts | 44.1 dB | 0.989 |
| 100 pts | 44.0 dB | 0.987 |
| 150 pts | 43.9 dB | 0.987 |
