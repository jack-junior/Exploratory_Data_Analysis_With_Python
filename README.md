---
title: "Build Plots with Python"
author: "Data Visualization Tutorial"
output: html_document
---

# Introduction

This document explains how to build scientific plots in **Python**. 

The objective is to demonstrate how Python can be used to:

- explore longitudinal health data
- build clear scientific visualizations
- automate the generation of multiple plots

The examples presented here come from a wearable data analysis pipeline including variables such as:

- daily steps
- physiological signals
- sleep patterns

Throughout this document we will progressively move from **simple plotting** to **automated plot generation**.

---

# Types of Graphs Generated in this Pipeline

The analysis pipeline generates three main types of figures.

## 1. Individual Trajectories Plot

This plot shows how a variable evolves over time for different participants.

Each thin line represents a participant and the thicker line represents the mean trend.

![](images/trajectories_example.png)

These plots are commonly used in:

- epidemiology
- clinical monitoring
- behavioral research
- wearable data studies

They allow researchers to observe both **individual variability** and **population trends**.

---

## 2. Stacked Categorical Proportion Plot

This plot shows the proportion of categorical states across time.

![](images/categorical_stacked_example.png)

Each bar represents a day and each color represents a state.

Typical examples include:

- activity states
- sleep stages
- behavioral categories

All bars sum to **100% of the observations for that day**.

---

## 3. Sleep Density Heatmap

This visualization shows the relationship between:

- sleep onset time
- sleep duration

![](images/sleep_heatmap_example.png)

The color intensity indicates where observations are more concentrated.

Such visualizations are common in:

- sleep science
- chronobiology
- digital health research

---

# Python Libraries for Plotting

Several libraries can be used to create visualizations in Python.

| Library | Description |
|------|------|
| Matplotlib | Core plotting library |
| Seaborn | Statistical visualization built on Matplotlib |
| Plotly | Interactive visualizations |
| Bokeh | Interactive browser-based plotting |
| Altair | Declarative visualization grammar |

In this project we use **Matplotlib**, which is the most fundamental plotting library in Python.

Advantages of Matplotlib:

- very flexible
- widely used in scientific computing
- integrates well with pandas
- suitable for automated pipelines

---

# Building a Plot for One Participant

Before automating plot generation, it is important to understand how to build a simple graph.

We start with **a single participant**.

## Import Required Libraries

```python
import pandas as pd
import matplotlib.pyplot as plt
