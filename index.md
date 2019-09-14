---
layout: page
title: R/BiomeHorizon User Guide
tagline: Plot Microbiome Time Series
description: 
---

[section to jump to different parts]

BiomeHorizon is an R package for visualizing longitudinal microbiome data in the form of a horizon plot. A horizon plot provides a compact way to visualize multiple time series in parallel by overlying the values at different ranges of magnitude. Though this package is designed for microbiome data, it can be used to visualize other types of longitudinal data as well.

In this tutorial, we walk through refining the data and creating a horizon plot from sample data. We use a sample OTU table with over 1000 OTUs and samples from six individuals, metadata with collection dates, and taxonomy information. Samples in this data set are collected at irregular time intervals over a period of several years, and each subject has a different number of samples.

The sample data sets and dependencies are loaded automatically on download, so we just need to install and load in the package.

```
## Install devtools if you don't already have the package
install.packages("devtools")

devtools::install_github("isaacfink21/biomehorizon")
library(biomehorizon)
```

## Preview of Sample Data

```
## OTU Table format. The first column contains OTU IDs, and all other columns are samples. 
## Values represent sample reads per OTU within a given sample. 
## Though in this case values are integer sample reads, they can also ## be represented as proportions or percentages of the total sample.
colnames(otusample)
head(otusample, 20)

## Metadata format. Contains sample IDs matching the column names of otusample, 
## subject IDs, and collection dates.
head(metadatasample, 10)

## Taxonomydata format. Describes taxonomy of each OTU from Kingdom through Genus.
## Levels without classification have NA values.
head(taxonomydata)
```

