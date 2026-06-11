# img2gradient

Turn any image into **pure-CSS radial gradients** that keep the original's spatial layout — colors stay where they belong. A soft, paintable approximation of your photo in a few kilobytes of CSS. 100% client-side: nothing is uploaded, everything runs in your browser.

**Try it: [marcindudekdev.github.io/img2gradient](https://marcindudekdev.github.io/img2gradient/)**

![what it does](https://img.shields.io/badge/output-pure%20CSS-blue) ![no backend](https://img.shields.io/badge/backend-none-green) ![single file](https://img.shields.io/badge/files-1-orange)

## What makes it different

Existing image→gradient tools extract a dominant-color palette and build an abstract mesh — the output doesn't resemble the photo. img2gradient samples the image on a grid and places one `radial-gradient()` blob per cell **at the colors' real positions**, so the result is a recognizable, out-of-focus version of the source.

Under the hood it does the math properly:

- **Compensation solver** — overlapping semi-transparent blobs alpha-composite "over" each other, so naively using each cell's average color blurs the image twice. With geometry fixed, the composite is linear in the blob colors; a SART iteration solves for colors so the *composite* matches the target. Model MSE drops ~3–10× vs. naive colors at identical CSS size.
- **Linear-light averaging** — canvas downscaling averages in gamma sRGB, which darkens and desaturates (the classic Brasseur gamma error). Sampling buckets pixels in linear light instead.
- **Minified output** — shortest-form hex colors, premultiplied-safe `#rrggbb00` end stops, no decorative whitespace. ~27% smaller than the naive emission, and the repetitive structure gzips to a fraction of that.

## Modes

| Mode | What it does | Best for |
|---|---|---|
| **Grid** | uniform N×N blobs (slider 2–16) | photos, landscapes |
| **Adaptive** | greedy blobs placed at max-residual locations, sized from local error | logos, UI shots, subjects on plain backgrounds |
| **Linear** | dominant-color `linear-gradient` (in oklab) | abstract backgrounds |

## Use as image placeholders (LQIP)

At 8–16 blobs (~0.4–1.5 KB) the output works as a low-quality image placeholder with structural advantages over the usual suspects: it paints before any image byte or JavaScript arrives (BlurHash/ThumbHash render only after their JS decoder runs), it can't hurt LCP (CSS gradients are excluded as LCP candidates), and it needs zero decode and zero dependencies — it's just a `background` declaration on the image's wrapper.

```css
.hero-img {
  /* paste the generated declarations */
  background-color: #9b8d72;
  background-image: radial-gradient(13.1% 13.1% at 3.8% 3.8%, #8888d4, #8888d400), /* … */;
}
```

## Run it

It's one self-contained HTML file. Open `index.html` in a browser, or serve it from anywhere. No build, no dependencies, no network calls (the webfonts degrade gracefully offline).

## Author

[Marcin Dudek](https://marcindudek.dev/) — WordPress performance & indie tools. More at [marcindudek.dev](https://marcindudek.dev/).

## License

MIT
