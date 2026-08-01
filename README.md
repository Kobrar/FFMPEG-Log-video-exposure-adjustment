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

The scene was shot on a Lumix S1 in V-Log. The reference is a frame
at **ISO 2500** — no exposure correction applied. Keep in mind, for ISO 5000 and 10000 camera kicks into higher base ISO.


| ISO | Shutter | Correction |
|-----|---------|------------|
| 640 | 1/15 s  | **+5 EV**  |
| 640 | 1/8 s   | **+4 EV**  |
| 640 | 1/4 s   | **+3 EV**  |
| 640 | 1/2 s   | **+2 EV**  |
| 1250 | 1/2 s  | **+1 EV**  |
| 2500 | 1/2 s  | **0 EV**   |
| 5000 | 1/2 s  | **−1 EV**  |
| 10000 | 1/2 s | **−2 EV**  |

---

## 1. With LUT vs without LUT (ISO 2500 reference)

Each row: the same scene, different ISO / shutter. The reference (ISO 2500,
16-bit) shows the intended look. The *Without LUT* column is the log footage
without the cosmetic LUT; the *With LUT* column is the same footage after
applying the cosmetic LUT (default 30-point curve).

### ISO 640 → +2 EV

| Reference ISO 2500 | Without LUT | Reference ISO 2500 with LUT | With LUT |
|--------------------|-------------|-----------------------------|----------|
| ![ref](images/ISO_2500_S_2_REFERENCE.png) | ![640 without LUT](images/ISO_640_S_2_2ev.png) | ![ref with LUT](images/ISO_2500_S_2_REFERENCE_lut.png) | ![640 with LUT](images/ISO_640_S_2_2ev_lut.png) |

### ISO 1250 → +1 EV

| Reference ISO 2500 | Without LUT | Reference ISO 2500 with LUT | With LUT |
|--------------------|-------------|-----------------------------|----------|
| ![ref](images/ISO_2500_S_2_REFERENCE.png) | ![1250 without LUT](images/ISO_1250_S_2_1ev.png) | ![ref with LUT](images/ISO_2500_S_2_REFERENCE_lut.png) | ![1250 with LUT](images/ISO_1250_S_2_1ev_lut.png) |

### ISO 2500 → 0 EV

| Reference ISO 2500 | With LUT |
|--------------------|----------|
| ![ref](images/ISO_2500_S_2_REFERENCE.png) | ![ref with LUT](images/ISO_2500_S_2_REFERENCE_lut.png) |

### ISO 5000 → −1 EV

| Reference ISO 2500 | Without LUT | Reference ISO 2500 with LUT | With LUT |
|--------------------|-------------|-----------------------------|----------|
| ![ref](images/ISO_2500_S_2_REFERENCE.png) | ![5000 without LUT](images/ISO_5000_S_2_-1ev.png) | ![ref with LUT](images/ISO_2500_S_2_REFERENCE_lut.png) | ![5000 with LUT](images/ISO_5000_S_2_-1ev_lut.png) |

### ISO 10000 → −2 EV

| Reference ISO 2500 | Without LUT | Reference ISO 2500 with LUT | With LUT |
|--------------------|-------------|-----------------------------|----------|
| ![ref](images/ISO_2500_S_2_REFERENCE.png) | ![10000 without LUT](images/ISO_10000_S_2_-2ev.png) | ![ref with LUT](images/ISO_2500_S_2_REFERENCE_lut.png) | ![10000 with LUT](images/ISO_10000_S_2_-2ev_lut.png) |

### Bonus: correction range at ISO 640

The same sensor at ISO 640 with different shutter speeds — correction from
**+2 EV** to **+5 EV**.

| Reference ISO 2500 with LUT | +2 EV (1/2 s) | +3 EV (1/4 s) | +4 EV (1/8 s) | +5 EV (1/15 s) |
|-----------------------------|---------------|---------------|---------------|----------------|
| ![ref with LUT](images/ISO_2500_S_2_REFERENCE_lut.png) | ![2](images/ISO_640_S_2_2ev_lut.png) | ![3](images/ISO_640_S_4_3ev_lut.png) | ![4](images/ISO_640_S_8_4ev_lut.png) | ![5](images/ISO_640_S_15_5ev_lut.png) |

---

## 2. Influence of the LUT curve point count

The default number of points is **30**.
Below are the same corrections with the exposure curve built from **5, 10, 30, 50,
100 and 150** points.

NOTE: at very high point counts curves filter throws error. Not very significant as diminishing returns kick in very early.

### ISO 640 → +2 EV

| 5 pts | 10 pts | 30 pts (default) |
|-------|--------|------------------|
| ![5p](images/ISO_640_S_2_2ev_lut_5p.png) | ![10p](images/ISO_640_S_2_2ev_lut_10p.png) | ![30p](images/ISO_640_S_2_2ev_lut.png) |

| 50 pts | 100 pts | 150 pts |
|--------|---------|---------|
| ![50p](images/ISO_640_S_2_2ev_lut_50p.png) | ![100p](images/ISO_640_S_2_2ev_lut_100p.png) | ![150p](images/ISO_640_S_2_2ev_lut_150p.png) |

### ISO 10000 → −2 EV

| 5 pts | 10 pts | 30 pts (default) |
|-------|--------|------------------|
| ![5p](images/ISO_10000_S_2_-2ev_lut_5p.png) | ![10p](images/ISO_10000_S_2_-2ev_lut_10p.png) | ![30p](images/ISO_10000_S_2_-2ev_lut.png) |

| 50 pts | 100 pts | 150 pts |
|--------|---------|---------|
| ![50p](images/ISO_10000_S_2_-2ev_lut_50p.png) | ![100p](images/ISO_10000_S_2_-2ev_lut_100p.png) | ![150p](images/ISO_10000_S_2_-2ev_lut_150p.png) |

### Difference vs the 150-point reference (PSNR/SSIM)

Reference for the comparison is the **150-point** variant (highest point count
available). The 5-point variant clearly deviates; from 10 points on,
differences are at the level of conversion noise (47–50 dB, SSIM ≈ 0.99+).

**ISO 640 / +2 EV**

| Variant | PSNR vs 150 pts | SSIM vs 150 pts |
|---------|-----------------|-----------------|
| 5 pts | 33.0 dB | 0.992 |
| 10 pts | 49.3 dB | 0.996 |
| 30 pts | 48.3 dB | 0.996 |
| 50 pts | 48.8 dB | 0.995 |
| 100 pts | 47.3 dB | 0.993 |
| 150 pts | — (reference) | — (reference) |

**ISO 10000 / −2 EV**

| Variant | PSNR vs 150 pts | SSIM vs 150 pts |
|---------|-----------------|-----------------|
| 5 pts | 32.7 dB | 0.990 |
| 10 pts | 49.9 dB | 0.997 |
| 30 pts | 48.0 dB | 0.995 |
| 50 pts | 49.7 dB | 0.997 |
| 100 pts | 49.9 dB | 0.996 |
| 150 pts | — (reference) | — (reference) |
