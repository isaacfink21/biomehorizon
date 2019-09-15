---
layout: page
title: R/BiomeHorizon User Guide
tagline: Plot Microbiome Time Series
description: 
---

BiomeHorizon is an R package for visualizing longitudinal microbiome data in the form of a horizon plot. A horizon plot provides a compact way to visualize multiple time series in parallel by overlying the values at different ranges of magnitude. Though this package is designed for microbiome data, it can be used to visualize other types of longitudinal data as well.

In this tutorial, we walk through creating a horizon plot from sample data, and then add several modifications to the visualization to demonstrate the versatility of the package. We use a sample OTU table with 8814 OTUs and 1781 samples from six individuals, a metadata table with collection dates, and a table with taxonomy information. Samples in this data set are collected at irregular time intervals over a period of several years, with a different number of samples for each subject.

### Loading in the package

The sample data sets and dependencies are loaded automatically on download, so we just need to install and load in the package.

```
## Install devtools if you don't already have the package
install.packages("devtools")

devtools::install_github("isaacfink21/biomehorizon")
library(biomehorizon)
```

### Preview of Sample Data

```
## OTU Table format. The first column contains OTU IDs, and all other columns are samples. 
## Values represent sample reads per OTU within a given sample. 
## Though in this case values are integer sample reads, they can also be represented as proportions or 
## percentages of the total sample.
otusample %>% 
	arrange(desc(sample_11406_0024_S24_L001)) %>% 
	select(1:6) %>% 
	head()
```

```
     otuid sample_11406_0024_S24_L001 sample_11406_0028_S28_L001 sample_11406_0029_S29_L001
1 otu_2457                       2338                        362                       2011
2 otu_2354                       1369                        276                          0
3 otu_2526                       1264                        630                        570
4 otu_2478                       1134                          0                          0
5 otu_7662                       1108                         46                         36
6 otu_5723                       1070                         98                          0
  sample_11406_0030_S30_L001 sample_11406_0033_S33_L001
1                        223                         89
2                         59                        108
3                        234                        107
4                          2                          0
5                          0                         33
6                          0                          0
```

```
## Metadata format. Contains sample IDs matching the column names of otusample, 
## subject IDs, and collection dates.
head(metadatasample, 10)
```

```
                      sample   subject collection_date
1 sample_11406_0024_S24_L001 subject_1      2001-12-27
2 sample_11406_0028_S28_L001 subject_2      2006-11-01
3 sample_11406_0030_S30_L001 subject_3      2006-11-01
4 sample_11406_0038_S38_L001 subject_4      2005-09-21
5 sample_11406_0045_S45_L001 subject_5      2005-09-22
6 sample_11406_0048_S48_L001 subject_6      2005-09-23
```

```
## Taxonomydata format. Describes taxonomy of each OTU from Kingdom through Genus.
## Levels without classification have NA values.
head(taxonomydata)
```

```
     otuid  Kingdom           Phylum               Class            Order            Family
1   otu_10 Bacteria       Firmicutes          Clostridia    Clostridiales    Peptococcaceae
2  otu_100 Bacteria       Firmicutes          Clostridia    Clostridiales   Ruminococcaceae
3 otu_1000 Bacteria Gemmatimonadetes       Longimicrobia Longimicrobiales Longimicrobiaceae
4 otu_1001 Bacteria Gemmatimonadetes       Longimicrobia Longimicrobiales Longimicrobiaceae
5 otu_1002 Bacteria   Proteobacteria Deltaproteobacteria          RCP2-54              <NA>
6 otu_1003 Bacteria   Proteobacteria Deltaproteobacteria          RCP2-54              <NA>
            Genus
1     Peptococcus
2 Subdoligranulum
3            <NA>
4            <NA>
5            <NA>
6            <NA>
```

### Data Refining and OTU Selection

Before we visualize the data using the main function, we must first prepare the data sets and other variables for analysis using the prepanel function. This will do several things, most importantly:

1. Filter OTUs
2. Filter samples to just one subject
3. Convert values to percentages, if they are not already in that 4. format
5. Check data formats
6. Check for and catch common user errors

Then, it will output the refined arguments in a list that can be supplied to the main function.

You can use this function with just an OTU table, but this will assume that all samples come from the same subject. Since *otusample* has 6 subjects, we will need to provide additional metadata matching samples to their subjects. Our metadata also contains collection dates for each sample, which will allow us to ensure samples are ordered chronologically.

Let’s select “subject_1”.

```
paramList <- prepanel(otudata = otusample, metadata = metadatasample, subj = "subject_1") 
```

