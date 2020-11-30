# MMAP-commands
# Convert Plink Files to MMAP-compatible files 
# Converting the phenotype file
# located in the “.fam” file in last three columns; use R to extract them 
Phenotypes1<- read.table(“qtl_mas_2012.fam”)

# Rename columns: 
colnames(phenotypes1)[colnames(phenotypes1) %in% c(“V2”, “V6”, “V7”, “V8”)]<- c(“IID”, “Trait 1”, “Trait 2”, “Trait 3”) 
# Remove unwanted columns: 
phenotypes1[c 1;3:5]<- NULL ;  double check with: names(phenotypes1) 
# Remove last 1,000 animals as there is no phenotype information for them in this file: 
info_phenotypes<- phenotypes1[1:3000, ]
# write phenotype file as a .csv file: 
write.csv(info_phenotypes, “info_phenotypes.csv”)
# Convert the pedigree and genotype files 
./mmap.2018_04_07_13_28.intel --plink_bfile2mmap --plink_bfile qtl_mas_2012 --binary_output_prefix pedigree.ped.csv ; 
# Rename files as appropriate in WINSCP 

# Transform genotype file from Marker-by-Subject format to Subject-by-Marker format with the following command:
<./mmap>--transpose_binary_genotype_file --binary_input_filename  genotypes.bin-- binary_output_filename SxM.bin 
# Filename: SxM.bin

# Create additive genomic relationship matrix with the following command: 
./mmap2018_04_07_13_28.intel --write_binary_gmatrix_file --binary_genotype_filename SxM.bin --binary_output_filename grm.bin --group_size 10000 --single_pedigree 
# Filename: grm.bin

# Obtain variance decomposition estimate with the following command: 
./mmap2018_04_07_13_28.intel --ped pedigree.ped.csv --phenotype_filename phenotypes.csv --trait Trait 1 --estimate_variance_components --variance_component_filename grm.bin --num_em_reml_burnin 2 --use_ai_reml --use_dpotrs --variance_component_label G --num_mkl_threads 2 --single_pedigree 
# Filename: Trait.1.variance.components.T.csv

# Calculate heritability estimate with the following command:
./mmap2018_04_07_13_28.intel --ped pedigree.ped.csv --read_binary_covariance_file grm.bin --trait Trait 1 --phenotype_id IID --phenotype_filename phenotypes.csv --single_pedigree 
# Filename: Trait.poly.cov.T.csv


# Perform GWAS with the following command: 
./mmap2018_04_07_13_28.intel --ped pedigree.ped.csv --read_binary_covariance_file grm.bin --trait Trait 1 --phenotype_id IID --phenotype_filename phenotypes.csv --binary_genotype_filename genotypes.bin --single_pedigree 
# Filename: 1.add.spor.cov.T.csv

# Use R to generate a Manhattan plot to explain GWAS results with the qqman  function to plot the p-values of each individual SNP against the -log10 (P value) on the y axis. The dotted line signifies the significance level that we are using. In this case, it will be 0.05/3000.



