# LNG1100 — static web app

French course version (LNG-1100, Université Laval) of the Stats 101 app:
interactive visualizations of error bars, standard error vs. sample size,
*t*-test error rates, ANOVA + Tukey, and linear/logistic regression.

Pure client-side port of the Quarto/Shiny dashboard in `~/Repos/lng1100_dash`
(formerly <https://guilherme.shinyapps.io/LNG1100/>). No server: all
simulation and model fitting run in the browser.

Live at <https://fr.gdgarcia.ca/stats101> (a redirect to this repo's GitHub Pages site,
<https://guilhermegarcia.github.io/lng1100_app/>). Pushing to `main` deploys.

**`~/Repos/stats101_app` is the source of truth.** This repo shares every
file with it except `docs/js/config.js` (French, no Bayes tab), the `<head>`
of `docs/index.html`, and this README. Make changes there and sync them here (see that README).

## Layout

```
docs/                 the site (GitHub Pages serves main:/docs; no build step)
├── index.html        static <head>: lang, title, description (per repo)
├── css/app.css
├── js/
│   ├── config.js     language, tab list, citation (per repo)
│   ├── i18n.js       EN + FR strings
│   ├── stats.js      pure statistics (lm, glm, t.test, aov, TukeyHSD,
│   │                 density, LearnBayes::beta.select), no DOM
│   └── app.js        one builder per tab
└── vendor/           d3 7.9.0, @observablehq/plot 0.6.17, jStat 1.9.6
tests/check.mjs       compares stats.js against R
```

## Development

```sh
python3 -m http.server 8000 -d docs   # http://localhost:8000
node tests/check.mjs                  # needs Rscript + jsonlite + LearnBayes
```

`tests/check.mjs` generates data in JS, has R fit the same models, and
compares: `lm`/`confint`, `glm` (IRLS, SEs, profile-likelihood CIs, the
`geom_smooth` ribbon), Welch `t.test`, `aov` + `TukeyHSD`, boxplot stats,
`density` (bw.nrd0), and `beta.select` across every Bayes slider setting.
Random draws come from a seeded JS generator, so the simulated samples differ
from R's `set.seed(123)` streams, but the statistics computed on them match.

## Differences from the Shiny dashboard

- *t* test tab: the "sample size" slider now sets the size of each sample
  (1,000 simulations). In the dashboard it set the number of simulations
  while every sample stayed at n = 100, so moving it could not change the
  Type II error rate.
- LMb / GLM: (x, y) pairs are drawn together, so increasing n adds points
  to the same sample instead of redrawing every y.
- English strings that were left in French in the dashboard (GLM slider and
  axis, *t*-test subtitle) are translated; the BibTeX entry's missing brace
  is fixed.
