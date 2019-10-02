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
URI <- read.table("Uric_Acid_instrument.txt", header =TRUE)
URI$effect_allele <- as.character(URI$effect_allele)
URI$other_allele <- as.character(URI$other_allele)
URI$beta <- as.numeric(URI$beta)
URI$SNP <- as.character(URI$SNP)
```

## Format instrumental variables from Uric Acid GWAS (Exposure)
```
Exp_data <- format_data(URI, type="exposure")
```

## Clump the data using default parameters (r2=0.001, kb=10,000) in order to select independent SNPs
```
Exp_data <- clump_data(Exp_data)
```

## Read instrumental variables from Parkinson disease GWAS (Outcome)
In this case, we take the SNPs from the Uric Acid GWAS and extract them from the PD summary stats
```	
PD <- read.table("PD_Uric_Acid.txt", header =TRUE)
PD$effect_allele <- as.character(PD$effect_allele)
PD$other_allele <- as.character(PD$other_allele)
PD$SNP <- as.character(PD$SNP)
```

## Format instrumental variables from the Parkinson disease GWAS (Outcome)
```	
Out_data <- format_data(PD, type="outcome")
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
