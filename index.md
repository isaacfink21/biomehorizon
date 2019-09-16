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

Before we visualize the data using the main function, we must first prepare the data sets and other variables for analysis using the `prepanel` function. This will do several things, most importantly:

1. Filter OTUs
2. Filter samples to just one subject
3. Convert values to percentages, if they are not already in that 4. format
5. Check data formats
6. Check for and catch common user errors

Then, it will output the refined arguments in a list that can be supplied to the main function.

You can use this function with just an OTU table, but this will assume that all samples come from the same subject. Since *otusample* has 6 subjects, we will need to provide additional metadata matching samples to their subjects. Our metadata also contains collection dates for each sample, which will allow us to ensure samples are ordered chronologically.

Let's select "subject_1".

```
paramList <- prepanel(otudata = otusample, metadata = metadatasample, subj = "subject_1") 
```

```
Constructed an OTU table and other variables with the following settings:
thresh_prevalence: 80
thresh_abundance: 0.5
thresh_NA: 5
subj: subject_1

23 OTUs met the filtering requirements, with the following stats:
     OTU_ID Average_abundance Prevalence Num_missing_samples
1   otu_121         0.5519409   97.53086                   0
2  otu_1243        14.2142798  100.00000                   0
3  otu_1530         4.1532489   97.53086                   0
4   otu_203         1.2380556   88.88889                   0
5  otu_2378         1.5814103   96.29630                   0
6  otu_2404         0.5103419   96.29630                   0
7  otu_2457         2.1052263  100.00000                   0
8  otu_2487         0.9898039   83.95062                   0
9  otu_2514         1.0179452   90.12346                   0
10 otu_2526         2.6775325   95.06173                   0
11 otu_2552         0.9270476   86.41975                   0
12 otu_3131         0.9506667   86.41975                   0
13 otu_3136         0.7341905   86.41975                   0
14 otu_3773         2.7263786  100.00000                   0
15 otu_4252         0.5308205   80.24691                   0
16 otu_6205         1.0759167   98.76543                   0
17 otu_6570         0.8166255  100.00000                   0
18 otu_6789         0.7530348   82.71605                   0
19 otu_6821        17.4279835  100.00000                   0
20 otu_6837         0.9456219   82.71605                   0
21 otu_7644         1.5073134   82.71605                   0
22 otu_7737         2.2286250   98.76543                   0
23 otu_8547         0.9883556   92.59259                   0
`biomehorizonpkg_otu_stats` was outputted to the environment.
```

Great. Now you should see in the console, the function selected several OTUs considered the most "significant". By default, this is done using four filtering standards.

- **thresh_prevalence**: How many samples in this OTU contain at least one sample read?
- **thresh_abundance**: Out of the samples that contain reads for this OTU, what was the average abundance of this OTU? i.e., what percentage of total sample reads are from this OTU?
- **thresh_abundance_override**: The same measurement as *thresh_abundance*, but if this higher threshold is reached, it overrides all other standards and the OTU is included.
- **thresh_NA**: This sets the maximum allowed percentage of samples with missing data (NA) for this OTU.

Looking at "otu_4252", 66/80 samples have >=1 sample read, giving it a "prevalence score" of ~80.3%. Out of the 66 samples with at least one read, the average proportion of total reads is ~.0053, giving it an "abundance score" of ~0.53%. This meets the default standards of 80% prevalence and 0.5% abundance, so the OTU is included.

```
library(dplyr)
## Retrieve samples from subject_1
otusample_subj1 <- otusample %>% 
	select(otuid, (metadatasample %>% filter(subject=="subject_1"))$sample)

## Samples reads for otu_4252
otusample_subj1 %>% 
	filter(otuid == "otu_4252") %>% 
	select(-otuid) %>% 
	as.numeric()
```

` [1] 130 559  21 164 368 156  50 128  22   `**`0`**` 144 570 491  47   9   1 248  56  38  42  15  13  66  29 510
[26] 238   `**`0`**`  11 415   `**`0`**`   `**`0`**`  38 464 236   `**`0`**`  26  64  80   2  45 470   `**`0`**` 457  45   `**`0`**` 103  74  97  48   `**`0`**`
[51]   `**`0`**` 464   `**`0`**`  91 470   6  26  71  60  14  96   `**`0`**`  26 536 117   `**`0`**` 470 101 213   `**`0`**` 144 258  22  21 127
[76]  22   `**`0`**`  11   5  74`

These 23 OTUs were selected using the default filtering thresholds, but maybe we want stricter standards.

```
paramList <- prepanel(otudata = otusample, metadata = metadatasample, subj = "subject_1", 
thresh_prevalence = 90, thresh_abundance = 1.5)
```

```
Constructed an OTU table and other variables with the following settings:
thresh_prevalence: 90
thresh_abundance: 1.5
thresh_NA: 5
subj: subject_1

8 OTUs met the filtering requirements, with the following stats:
    OTU_ID Average_abundance Prevalence Num_missing_samples
1 otu_1243         14.214280  100.00000                   0
2 otu_1530          4.153249   97.53086                   0
3 otu_2378          1.581410   96.29630                   0
4 otu_2457          2.105226  100.00000                   0
5 otu_2526          2.677532   95.06173                   0
6 otu_3773          2.726379  100.00000                   0
7 otu_6821         17.427984  100.00000                   0
8 otu_7737          2.228625   98.76543                   0
`biomehorizonpkg_otu_stats` was outputted to the environment.
```

Alternatively, we can manually select OTUs.

```
paramList <- prepanel(otudata = otusample, metadata = metadatasample, subj = "subject_1", 
otulist = c("otu_1243", "otu_6821", "otu_2378", "otu_7737", "otu_1530", "otu_8547", "otu_6570", "otu_2552"))
```

