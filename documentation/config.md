---
layout: page
title: Example analysis
---

This tutorial is the example analysis with PoweREST on human intraductal papillary mucinous neoplasms (IPMN) data from [GSE233254]("https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi").

## Required input data

`PoweREST` requires 10X spatial transcriptomics data in `Seurat` format.
The example data for runing the tutorial can be downloaded in this [page]()

## Load the package and data
```r
library(PoweREST)
three_areas <- readRDS("~/your_path_to_GSE233293_scMC.all.3areas.final")
Idents(three_areas)
# Levels: Peri Juxta Epi
```
The dataset has three histologically directed spot selection of epilesional, juxtalesional, and perilesional areas. We would like to perform the power estimation upon each of the three areas.

## Preprocess data
```r
# Split the ST data by areas
SeuratObject_splitlist<-SplitObject(three_areas, split.by = "ident")

str(SeuratObject_splitlist[[1]][['Type']])
# Factor w/ 3 levels "LG","HG","IPMNPDAC"

# The original dataset has three cancer subtypes. According to the paper, 'HG' and 'IPMNPDAC' are combined into one 'HR' (high-risk) group
for (i in 1:length(SeuratObject_splitlist)) {
  SeuratObject_splitlist[[i]][['Condition']]<-ifelse(SeuratObject_splitlist[[i]][['Type']]=='LG','LG','HR')
}

# Set identity classes to the 'Condition' column in meta data
for (i in 1:length(SeuratObject_splitlist)) {
  Idents(SeuratObject_splitlist[[i]])<-"Condition"
}
Idents(SeuratObject_splitlist[[1]])
# Levels: HR LG
```

## Power estimation though bootstrapping
```r
# First perform PoweREST upon 'Peri' area
Peri<-SeuratObject_splitlist[[1]]
```
`Test Version`: set 'iteration' to 5, 'replicates' is the sample size per group, 'spots_num' is the average number of spots across the tissue samples which in our case is 80.
```r
test<-PoweREST(Peri,cond='Condition',replicates=5,spots_num=80,iteration=5)
```
`Default Version`: set 'iteration' to 100, it may take hours to run the code if iteration is 100 (default setting). We recommand you install `presto` by
```r
install.packages('devtools')
devtools::install_github('immunogenomics/presto')
# then run this 
result<-PoweREST(Peri,cond='Condition',replicates=5,spots_num=80,iteration=100)
```
### Instead of using Wilcoxon test
The default test in Seurat's `FindMarkers` is Wilcoxon test. Users can specify any test avalible in [FindMarkers](https://satijalab.org/seurat/reference/findmarkers) function.
```r
# For example, use the Student's t-test
result2<-PoweREST(Peri,cond='Condition',replicates=5,spots_num=80,iteration=100,test.use="t")
```
### Power estimation upon a subset of genes
Users can also use `PoweREST_gene` and `PoweREST_subset` to perform the power estimation upon one gene or a subset of genes. But be carefull 


## Fit power surface

```r
links:
  header:
    - title: GitHub
      url: https://github.com/allejo/jekyll-docs-theme
  footer:
    - title: GitHub
      url: https://github.com/allejo/jekyll-docs-theme
    - title: Issues
      url: https://github.com/allejo/jekyll-docs-theme/issues?state=open
```

| Field   | Description                           |
|:--------|:--------------------------------------|
| `title` | The textual representation of the URL |
| `url`   | The URL of the link                   |

## UI

The ui object will contain all the settings in regards to the aesthetics of the website

```yaml
ui:
  header:
    color1: "#080331"
    color2: "#673051"
    trianglify: true
```

| Field               | Description                                                               |
|:--------------------|:--------------------------------------------------------------------------|
| `color1` & `color2` | The two colors that will create the gradient of the page header           |
| `trianglify`        | When set to true, the page header will be a generated triangular pattern  |

## Analytics

```yaml
analytics:
    google: UA-123456-1
```

| Field    | Description                                                                   |
|:---------|:------------------------------------------------------------------------------|
| `google` | The unique identifier for Google Analytics; typically looks like `U-123456-1` |

