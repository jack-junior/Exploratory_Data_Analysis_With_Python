# Build Plots with Python
Visualization is a core component of a robust analytical workflow and should be used throughout the data process to:
- verify structure and detect inconsistencies
- understand distributions and temporal dynamics
- assess assumptions and guide model selection

**Skipping visualization can lead to:**
- undetected data quality issues
- incorrect variable interpretation
- inappropriate modeling strategies

**The objective of this document is to demonstrate how Python can be used to:**
- build plots from structured datasets (pandas)
- handle longitudinal and categorical data
- produce clear scientific visualizations
- scale plotting through automation

**The 3 Fundamental Before Plotting**
Before writing any plotting code, clarify:
- Variable type (numeric vs categorical)
- Time structure (longitudinal vs cross-sectional)
- Objective (distribution, evolution, composition, relationship)


---

## Map of Plot Types
Different analytical objectives require different types of visualizations and choosing the right type is essential before coding.

- Evolution over Time (Longitudinal Data)
line plot, multiple trajectories, mean trend
Used when analyzing repeated measurements over time.

![](images/1.png)

- Distribution of a Variable (histogram, boxplot, violin plot)
Used to understand variability and distribution.

![](images/2.png)

- Categorical Composition (stacked bar chart, proportion plots)
Used to show proportions of categories.

![](images/3.png)

- Relationship Between Variables (scatter plot, heatmap, density plots)
Used to explore associations between variables.

![](images/4.png)

--- 

## Python Libraries for Plotting

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

# Line plot
Multiple Trajectories + Mean Trend

```r
import pandas as pd
import matplotlib.pyplot as plt

# Load data
df = pd.read_excel("steps_data.xlsx")

# Create figure
plt.figure(figsize=(12,7))

# Plot individual trajectories
for pid, g in df.groupby("PID"):
    
    plt.plot(
        g["day_index"],
        g["steps"],
        alpha=0.2
    )

# Compute mean trend
mean_trend = df.groupby("day_index")["steps"].mean()

# Plot mean trend
plt.plot(
    mean_trend.index,
    mean_trend.values,
    linewidth=3,
    label="Mean"
)

# Labels and formatting
plt.xlabel("Day")
plt.ylabel("Steps")
plt.title("Daily Steps — Individual Trajectories + Mean")
plt.legend()

plt.show()

```

## Building a Plot for One Participant
Before automating plot generation, it is important to understand how to build a simple graph.

We start with **a single participant**.

## Import Required Libraries

```r
import pandas as pd
import matplotlib.pyplot as plt
```

- **pandas** manages tabular data and allows manipulation of datasets  
- **matplotlib.pyplot** provides functions used to create plots  

---

# Load the Dataset

```r
df = pd.read_excel("steps_data.xlsx")
```

This command loads the dataset into a **DataFrame**, which is the main table structure used in pandas.

Example structure:

| PID | day_index | steps |
|-----|------|------|
| P01 | 1 | 5200 |
| P01 | 2 | 6100 |

---

# Select One Participant

```r
participant_data = df[df["PID"] == "P01"]
```

This filters the dataset to keep only observations belonging to **participant P01**.
---

# Create the Plot 
1. creates an empty plotting canvas
```r
plt.figure(figsize=(10,6))
# The parameter figsize defines the size of the figure.
```
2. Draw the Trajectory
```r
plt.plot(
    participant_data["day_index"], # x-axis
    participant_data["steps"]  # y-axis
)
```
3. Customize the Plot
```r
plt.xlabel("Day")
plt.ylabel("Steps")
plt.title("Daily Steps — P01")
plt.show()
plt.grid(True)
```
4. Save the Plot
```r
plt.savefig("single_participant_plot.png")
```

# Extending the Plot to Multiple Participants

Once we understand how a single trajectory is plotted, we can extend the graph to multiple participants.

Example: Let's suppose the dataset looks like this:

| PID | day_index | steps |
|-----|----------|------|
| P01 | 1 | 5200 |
| P01 | 2 | 6100 |
| P02 | 1 | 4300 |
| P02 | 2 | 5000 |

1. splits the dataset into smaller subsets based on the participant identifier (`PID`). Each subset corresponds to the data of one participant.

```r
df = pd.read_excel("steps_data.xlsx")
df.groupby("PID")
```

```r
pid = "P01"
g =
PID  day_index  steps
P01      1      5200
P01      2      6100
```

```r
pid = "P02"
g =
PID  day_index  steps
P02      1      4300
P02      2      5000
```
2. Iteration and plotting 
```r
plt.figure(figsize=(12,7))

for pid, g in df.groupby("PID"):

    plt.plot(
        g["day_index"],
        g["steps"],
        alpha=0.2
    )
```
The parameter `alpha=0.2` controls the transparency of the line. Using a low transparency allows many participant trajectories to be displayed simultaneously without making the plot unreadable. 
This allows many trajectories to be visualized simultaneously.

3. Adding a Mean Trend
To better understand the overall population pattern we can compute a mean trajectory.

```r
mean_trend = df.groupby("day_index")["steps"].mean()

plt.plot(
    mean_trend.index,
    mean_trend.values,
    linewidth=3,
    label="Mean Trend"
)
```
**Step 1 — Compute the mean value per day**

```r
mean_trend = df.groupby("day_index")["steps"].mean()
```
For example, if the dataset looks like this:

| PID | day_index | steps |
|-----|----------|------|
| P01 | 1 | 5200 |
| P02 | 1 | 4300 |
| P03 | 1 | 6100 |
| P01 | 2 | 5800 |
| P02 | 2 | 4900 |
| P03 | 2 | 6200 |

Result:

| day_index | mean_steps |
|----------|-----------|
| 1 | 5200 |
| 2 | 5633 |

The object `mean_trend` is therefore a **Series containing the average value for each day**.

**Step 2 — Plot the mean trajectory**

```r
plt.plot(
    mean_trend.index,
    mean_trend.values,
    linewidth=3,
    label="Mean Trend"
)
```
- `mean_trend.index` → represents the **day index** (x-axis)
- `mean_trend.values` → represents the **average number of steps for each day** (y-axis)

Additional parameters:

- `linewidth=3` makes the line thicker so it stands out from the individual trajectories
- `label="Mean Trend"` adds a label that can later appear in the plot legend

### Interpretation

The resulting line represents the **average behavior of the population across time**.

This is useful because:

- individual trajectories can be noisy
- the mean trend highlights the **general pattern shared by participants**
- it helps identify overall increases, decreases, or stable behaviors over time

---


# Distribution of a Variable (histogram, boxplot, violin plot)
Distribution plots are used to understand how a numeric variable is spread.

They help identify:
- variability
- skewness
- outliers

## Histogram
```r
plt.figure()

plt.hist(df["steps"], bins=30)

plt.xlabel("Steps")
plt.ylabel("Frequency")
plt.title("Distribution of Daily Steps")

plt.show()
```

**Add normality curve**

```r
import numpy as np
import matplotlib.pyplot as plt
from scipy.stats import norm

# Extract variable
data = df["steps"].dropna()

# Create figure
plt.figure()

# Histogram (density=True for normalization)
plt.hist(data, bins=5, density=True, alpha=0.6)

# Generate normal curve
x = np.linspace(data.min(), data.max(), 100)
y = norm.pdf(x, mean, std)

# Plot normal curve
plt.plot(x, y, linewidth=2)

# Labels
plt.xlabel("Steps")
plt.ylabel("Density")
plt.title("Steps Distribution with Normal Curve")

plt.show()
```

**Automation Across Variables**
```r
variables = ["steps", "heart_rate", "sleep_duration"]

for var in variables:
    
    plt.figure()
    
    plt.hist(df[var].dropna(), bins=30)
    
    plt.title(f"Distribution of {var}")
    plt.xlabel(var)
    
    plt.show()
```
## Boxplot
```r
plt.figure()

plt.boxplot(df["steps"])

plt.ylabel("Steps")
plt.title("Steps Distribution (Boxplot)")

plt.show()
```


## Violin Plot
```r
import seaborn as sns

plt.figure()

sns.violinplot(y=df["steps"])

plt.ylabel("Steps")
plt.title("Steps Distribution (Violin Plot)")

plt.show()
```

## Stratified Distribution (by Group)

Example: compare distributions across a grouping variable (e.g., sex, site)

**Boxplot by Group**
```r
plt.figure()

df.boxplot(column="steps", by="sex")

plt.title("Steps Distribution by Sex")
plt.suptitle("")  # remove default title
plt.xlabel("Sex")
plt.ylabel("Steps")

plt.show()
```
**Violin Plot by Group**
```r
import seaborn as sns

plt.figure()

sns.violinplot(x="sex", y="steps", data=df)

plt.xlabel("Sex")
plt.ylabel("Steps")
plt.title("Steps Distribution by Sex")

plt.show()
```

---

# Categorical Plots (Bar & Stacked)

Categorical plots are used to summarize counts or proportions of categories.

## Bar Plot (Counts)
Used to quickly assess dominant vs rare categories.
```r
counts = df["activity"].value_counts()

plt.figure()
plt.bar(counts.index, counts.values)

plt.xlabel("Activity")
plt.ylabel("Count")
plt.title("Activity Distribution")

plt.show()
```
**value_counts()** → counts occurrences of each category
each bar → number of observations in that category

## Bar Plot (Proportions)
Allows comparisons across datasets/groups of different sizes
```r
prop = df["activity"].value_counts(normalize=True)

plt.figure()
plt.bar(prop.index, prop.values)

plt.xlabel("Activity")
plt.ylabel("Proportion")
plt.title("Activity Distribution (%)")

plt.show()
```

**normalize=True** → converts counts into proportions
sum of all bars = 1 (or 100%)

## Stacked Plot (Proportions Over Time)
```r
prop = (
    df
    .groupby(["day_index", "activity"])
    .size()
    .groupby(level=0)
    .apply(lambda x: x / x.sum())
    .unstack()
)

prop.plot(kind="bar", stacked=True, figsize=(12,6))

plt.xlabel("Day")
plt.ylabel("Proportion")
plt.title("Activity Composition Over Time")

plt.show()
```

Concept

groupby(["day_index", "activity"]) → count per day and category

.size() → number of observations

second groupby(level=0) → normalize within each day

.unstack() → reshape to wide format (required for plotting)

👉 Each bar = one day
👉 Each segment = proportion of a category


## Stratified Bar Plot
```r
ct = pd.crosstab(df["sex"], df["activity"], normalize="index")

ct.plot(kind="bar", stacked=True)

plt.xlabel("Sex")
plt.ylabel("Proportion")
plt.title("Activity Distribution by Sex")

plt.show()
```
Concept

pd.crosstab() → cross-tabulation between two variables

normalize="index" → proportions computed within each group

👉 Each bar = one group
👉 Used to compare category distribution across subgroups


## Automation Across Variables
```r
cat_vars = ["activity", "sleep_stage"]

for var in cat_vars:
    
    counts = df[var].value_counts()
    
    plt.figure()
    plt.bar(counts.index, counts.values)
    
    plt.title(f"{var} Distribution")
    
    plt.show()
```



















# Creating a Stacked Categorical Plot

Categorical variables require a different visualization approach compared to numerical variables.

In previous sections, we plotted **continuous measurements** such as steps or heart rate. These variables can be averaged or summed and represented as trajectories.

However, some variables represent **states or categories**, for example:

- activity type (walking, running, cycling)
- sleep stage (REM, deep sleep, light sleep)
- posture or behavioral states

Because these variables are **not numeric measurements**, we cannot compute a mean value or a trajectory in the same way. Instead, we are interested in understanding **how the proportion of each category evolves over time**.

A common way to visualize this is with a **stacked bar chart**.

Each bar represents a time unit (for example one day), and the bar is divided into colored segments representing the **proportion of each category**.

For example:

| Day | Walking | Running | Cycling |
|----|----|----|----|
| 1 | 60% | 25% | 15% |
| 2 | 55% | 30% | 15% |
| 3 | 50% | 35% | 15% |

Each bar will therefore sum to **100% of the observations for that day**.

---

## Example Code

```r
prop_wide.plot(
    kind="bar",
    stacked=True,
    figsize=(18,9)
)
```

This command uses the built-in plotting capability of **pandas DataFrames**.

The object `prop_wide` is typically a table where:

- rows represent **time units** (for example `day_index`)
- columns represent **categories**
- values represent the **proportion of observations**

Example structure of `prop_wide`:

| day_index | walking | running | cycling |
|-----------|--------|--------|--------|
| 1 | 0.60 | 0.25 | 0.15 |
| 2 | 0.55 | 0.30 | 0.15 |
| 3 | 0.50 | 0.35 | 0.15 |

Each row sums to **1 (or 100%)**.

---

## Explanation of the Parameters

### `kind="bar"`

This parameter tells pandas to create a **bar chart**.

Instead of plotting lines or points, the data will be represented as vertical bars.

Each bar corresponds to a row in the dataset (in this case, a day).

---

### `stacked=True`

This parameter tells pandas to **stack the categories on top of each other**.

Without stacking, the bars would appear side-by-side.  
With stacking, the segments are combined vertically to show the **composition of the total**.

This allows us to see how the **relative contribution of each category changes over time**.

---

### `figsize=(18,9)`

This controls the size of the figure in inches.

A larger figure is useful when:

- there are many time points
- there are many categories
- we want the labels to remain readable

---

## Interpretation

Stacked categorical plots allow us to observe:

- how the **distribution of states changes over time**
- whether some states become more or less frequent
- whether patterns emerge in behavioral or physiological states

For example, in a sleep study we might observe:

- an increase in **REM sleep proportion**
- a decrease in **deep sleep**
- stable patterns across nights

This type of visualization is therefore very useful in:

- behavioral analysis
- wearable data research
- longitudinal categorical data studies
---

# Creating a Sleep Density Heatmap

Sleep patterns can be visualized using **density estimation**, which helps us understand where observations are most concentrated in a two-dimensional space.

In this case, we want to study the relationship between:

- **sleep onset time** (when sleep begins)
- **sleep duration** (how long the sleep lasts)

Instead of plotting each observation individually, density estimation allows us to visualize **regions where many observations occur together**.

This is particularly useful when analyzing sleep data collected from wearable devices across many nights and many participants.

---

## Example Code

```r
plt.imshow(
    Z_masked,
    origin="lower",
    aspect="auto",
    cmap="turbo"
)
```

This command displays a **2-dimensional density map** using Matplotlib.

The matrix `Z_masked` contains density values that were previously estimated from the data using a method such as **kernel density estimation (KDE)**.

Each cell of this matrix represents the estimated concentration of observations for a given combination of:

- sleep onset time
- sleep duration

---

## Explanation of the Parameters

### `Z_masked`

This is a **2-dimensional matrix of density values**.

Each value corresponds to the estimated probability density at a specific coordinate in the sleep onset × sleep duration space.

Higher values indicate that many sleep episodes occur in that region.

Low values indicate rare observations.

---

### `origin="lower"`

This parameter defines how the image is displayed on the vertical axis.

Setting `origin="lower"` ensures that the **lowest values appear at the bottom of the plot**, which matches the standard orientation used in most scientific plots.

Without this option, the image would appear vertically flipped.

---

### `aspect="auto"`

This controls the aspect ratio of the plot.

Setting `aspect="auto"` allows Matplotlib to **automatically adjust the scaling of the axes** so that the visualization fits properly within the plotting window.

This is useful when the ranges of the x-axis and y-axis are very different.

---

### `cmap="turbo"`

The parameter `cmap` defines the **color map** used to represent density values.

The `"turbo"` colormap is a smooth gradient that transitions from:

- blue (low density)
- green/yellow (moderate density)
- red (high density)

This makes it easy to visually identify where sleep patterns are most concentrated.

---

## Interpretation of the Heatmap

In this visualization:

- the **x-axis represents sleep onset time**
- the **y-axis represents sleep duration**
- the **color intensity represents the concentration of observations**

For example:

- a bright region around **23:00 and 7 hours** suggests that many participants tend to fall asleep around 11 PM and sleep for about 7 hours.
- darker regions indicate combinations that occur less frequently.

---

## Why This Visualization is Useful

A sleep density heatmap allows researchers to quickly identify patterns such as:

- typical sleep onset times
- common sleep durations
- variability across nights
- unusual or rare sleep behaviors

This type of visualization is widely used in:

- sleep science
- chronobiology
- digital health studies
- wearable data analytics

It provides a **compact summary of complex sleep behavior across large datasets**.

---

# Automating Plot Generation

Once the plotting process is understood, it can be automated.

Instead of manually plotting each variable, we can loop through multiple files.

```r
import os

files = os.listdir(data_folder)

for file in files:

    df = pd.read_excel(file)

    process_data(df)

    create_plot(df)
```

This loop automatically:

1. reads each dataset  
2. processes the data  
3. generates the corresponding plot  
4. saves the output  

Automation allows scaling the workflow to **many variables and many participants**.

---

# Automated Pipeline Output

The pipeline automatically generates:

For numeric variables:

```
Variable_DailyLevel.xlsx
Variable_Trajectories.png
```

For categorical variables:

```
Variable_CategoricalProportions.xlsx
Variable_DailyStateProportions.png
```

For sleep analysis:

```
SleepDuration_FullPeriod_MeanTrend.png
Sleep_Onset_vs_Duration_Heatmap.png
```

---

# Conclusion

Python provides powerful tools to visualize complex datasets.

By combining:

- **pandas** for data manipulation  
- **matplotlib** for plotting  
- **loops** for automation  

we can efficiently generate visual summaries across multiple participants and variables.

This approach allows to quickly explore large datasets and identify meaningful patterns.

