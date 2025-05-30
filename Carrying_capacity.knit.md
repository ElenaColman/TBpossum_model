---
title: "Carrying capacity"
author: "Elena Colman & Fiona Harris"
format:
  html:
    theme: 
      - flatly
    page-layout: full
    toc: true
    toc-location: left
    math: mathjax
    code-fold: true
    code-summary: "Show code"
    page-navigation: true
    embed-resources: true
editor: visual
---



# Carrying capacity

## What is the effect of the carrying capacity?

The carrying capacity, denoted by $\kappa$, represents the maximum number of possums the den can support. It acts as a **population cap**, influencing birth and death rates through density-dependent feedback. As the population approaches or exceeds this threshold, birth rates decrease and mortality rates increase to reflect environmental limitations like food, space, or ("normal") disease pressure.

In the model, this feedback is governed by the logistic modifier:

| Symbol | Modifier Name | Explanation | Formula |
|---------|------------------|------------------|-------------------------|
| $Y$ | Mortality modifier | Increases mortality with crowding (logistic function) | \\( Y = \\frac{1}{1 + \\exp(-\\rho(N_J + N\_{Ad} - \\kappa))} \\) |
| $X$ | Birth rate modifier | Reduces birth rate based on total population | \\( X = 1 - Y \\) |

: :Model’s modifiers {#tablemodifiers}

Here, $N$ is the total population, and $\rho$ determines how sharply the birth rate is reduced as $N$ approaches $\kappa$.

**! Please note that neither the parameters nor initial population settings were changed which can cause an initial peak or fall in numbers.**



::: {.cell context='server'}

:::

```{ojs}
//| echo: false

viewof kappa = Inputs.range([10, 100], {step: 5, label: "Carrying Capacity (κ)"})

filteredKappa = kappa_data.filter(d => d.kappa === kappa)

kappa_long = filteredKappa.flatMap(d => [
  { time: d.time, Count: d.SJ, Compartment: "Susceptible Juveniles" },
  { time: d.time, Count: d.EJ, Compartment: "Exposed Juveniles" },
  { time: d.time, Count: d.IJ, Compartment: "Infectious Juveniles" },
  { time: d.time, Count: d.SAd, Compartment: "Susceptible Adults" },
  { time: d.time, Count: d.EAd, Compartment: "Exposed Adults" },
  { time: d.time, Count: d.IAd, Compartment: "Infectious Adults" }
])

kappa_mortality = filteredKappa.flatMap(d => [
  { time: d.time, Deaths: d.natural_deaths, Type: "Natural Mortality" },
  { time: d.time, Deaths: d.tb_deaths, Type: "TB Mortality" }
])


kappa_total = filteredKappa.map(d => ({
  time: d.time,
  Total: d.SJ + d.EJ + d.IJ + d.SAd + d.EAd + d.IAd
}))

```

```{ojs}
//| echo: false

html`<div style="display: flex; gap: 2rem;">
  <div style="width: 250px;">
    <p><strong>Carrying capacity κ </strong></p>
    ${viewof kappa}
    <p style="font-size: 0.9em;">
    <strong>Key patterns:</strong><br>

    <strong>Low carrying capacity</strong> leads to rapid stabilization at smaller population sizes. High mortality or infection may cause populations to dip below the threshold needed for replacement. <br>

    <strong>High carrying capacity</strong> allows for prolonged growth, with population peaks near or above 50. This can intensify transmission and TB mortality as more individuals are present to sustain spread.<br>

    The carrying capacity directly limits total population size, but indirectly influences infection dynamics, as larger populations lead to more contacts and potentially higher disease burden.<br>

    
    

    </p>
  </div>

  <div>
    <h3>TB infection model</h3>
    ${Plot.plot({
      marks: [Plot.lineY(kappa_long, {x: "time", y: "Count", stroke: "Compartment"})],
      color: {
        type: "ordinal",
        domain: [
          "Susceptible Juveniles", "Exposed Juveniles", "Infectious Juveniles",
          "Susceptible Adults", "Exposed Adults", "Infectious Adults"
        ],
        range: ["blue", "orange", "red", "green", "purple", "brown"],
        legend: true
      },
      x: {label: "Time (Years)"},
      y: {label: "Possum Count"},
      width: 700,
      height: 350
    })}

    <h3>Mortality Over Time</h3>
    ${Plot.plot({
      marks: [Plot.lineY(kappa_mortality, {x: "time", y: "Deaths", stroke: "Type"})],
      color: {
        type: "ordinal",
        domain: ["Natural Mortality", "TB Mortality"],
        range: ["green", "black"],
        legend: true
      },
      x: {label: "Time (Years)"},
      y: {label: "Deaths per Unit Time"},
      width: 700,
      height: 350
    })}
    <h3>Total Population Over Time</h3>
${Plot.plot({
  marks: [Plot.lineY(kappa_total, {x: "time", y: "Total"})],
  color: {value: "steelblue"},
  x: {label: "Time (Years)"},
  y: {label: "Total Possums"},
  width: 700,
  height: 300
})}

  </div>
</div>`

```

