## Mendelian Randomization 

	- **Author(s):** Sara Bandres-Ciga
	- **Date Last Updated:** Sept 2019

### Load the libraries..
```
library(data.table)
library(TwoSampleMR)
library(MRInstruments)
library(devtools)
```

## Read instrumental variables from Uric Acid GWAS (Exposure)
```
COLESTEROL <- read.table("Cholesterol_instrument.txt", header =TRUE)
COLESTEROL$effect_allele <- as.character(COLESTEROL$effect_allele)
COLESTEROL$other_allele <- as.character(COLESTEROL$other_allele)
COLESTEROL$beta <- as.numeric(COLESTEROL$beta)
COLESTEROL$SNP <- as.character(COLESTEROL$SNP)
```

## Format instrumental variables from Cholesterol GWAS (Exposure)
```
Exp_data <- format_data(COLESTEROL, type="exposure")
Exp_data <- clump_data(Exp_data)
```
## Clump the data using default parameters (r2=0.001, kb=10,000) in order to select independent SNPs
In this case, we take the SNPs from the Cholesterol GWAS and extract them from the ALS summary stats
```	
ALS <- read.table("ALS_Cholesterol.txt", header =TRUE)
ALS$effect_allele <- as.character(ALS$effect_allele)
ALS$other_allele <- as.character(ALS$other_allele)
ALS$SNP <- as.character(ALS$SNP)
```

## Format instrumental variables from the ALS GWAS (Outcome)
```
Out_data <- format_data(ALS, type="outcome")
```
## Harmonization of both Exposure and Outcome
```
dat <- harmonise_data(exposure_dat=Exp_data, outcome_dat=Out_data, action=2)
```
## Mendelian Randomization analyses
```
res <-mr(dat)
res
```
## Sensitivity analyses
```
mr_heterogeneity(dat)
mr_pleiotropy_test(dat)
```
## Forest Plot
```
res_single <- mr_singlesnp(dat)
p2 <- mr_forest_plot(res_single)
p2[[1]]
ggsave(p2[[1]], file="Acido_Urico_PD.jpeg", width=7, height=7)
```
## Funnel Plot
```
res_single <- mr_singlesnp(dat)
p4 <- mr_funnel_plot(res_single)
p4[[1]]
ggsave(p4[[1]], file="Funnel_plot_AcidoUrico_PD.jpeg", width=7, height=7)
```
## Leave-one-out analysis
```
res_loo <- mr_leaveoneout(dat)
res_loo
fwrite(res_loo, file = "resultados_AcidoUrico_PD_LOO.tab", na = "NA", quote = F, row.names = F, sep = "\t")
```

## Leave-one-out analysis Forest plot
```
p3 <- mr_leaveoneout_plot(res_loo)
ggsave(p3[[1]], file="LOO_AcidoUrico_PD.jpeg", width=7, height=7)
```
