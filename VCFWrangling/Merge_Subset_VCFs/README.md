# Merge and Subset individual VCF files  

In this workflow we will take 7 individual VCF files, extract 3 regions of interest from each of these and then merge them into one master VCF file. 


## Step 1: compress and index VCF files  

This repository includes 7 example VCF files (chromosome 19) from the 1000 Genomes project to work with:  

![Image of example VCFs](https://github.com/MerrimanLab/CommonWorkflows/blob/master/VCFWrangling/Merge_Subset_VCFs/ExampleVCFs.png)  

We need to compress these files using ```bgzip```, and the index them using ```tabix```:

```
parallel bgzip ::: *vcf
parallel tabix -p vcf ::: *vcf.gz
```

This results in 7 ```vcf.gz``` files and 7 corresponding ```.tbi``` files:

![Image of compressed VCFs](https://github.com/MerrimanLab/CommonWorkflows/blob/master/VCFWrangling/Merge_Subset_VCFs/ExampleCompressedVCF.png)  

## Step 2: Create BED file for our gene regions    

You can subset VCF files either directly in the command line using ```bcftools merge -r```, or you can create a BED file (CHR, START, END) to subset multiple regions with ```bcftools merge -R <bed_file.bed>```. We are going to use a BED file to extract 3 obseity-related genes from each individual VCF file. The genes have the following coordinates:

| Gene | CHR | Start (bp) | End (bp) |  
| :--- | --- | ---------- | -------- |  
| KCTD15 | 19 | 33,795,933 | 33,815,763 |  
| GIPR | 19 | 45,668,193 | 45,683,724 |  
| TMEM160 | 19 | 47,045,907 | 47,048,784 |  

We will create a file, ```genes.bed```, which contains these 3 regions (plus a little padding around them):

![Image of BED file](https://github.com/MerrimanLab/CommonWorkflows/blob/master/VCFWrangling/Merge_Subset_VCFs/ExampleBEDFile.png)  

Notes:  

  1. The BED file should *not* contain a header  
  2. The columns are tab separated  


## Step 3: Merge and subset VCFs  

We are going to extract the gene regions and combine the individual VCF files in one command. If you do not want to filter the VCF files, you can simply remove the ```-R genes.bed``` option.  

```
bcftools merge -R genes.bed *vcf.gz > combined_genotypes.vcf
```

This will create a new ```combined_genotypes.vcf``` file as shown below:

![Image Combined VCF](https://github.com/MerrimanLab/CommonWorkflows/blob/master/VCFWrangling/Merge_Subset_VCFs/ExampleCombinedVCF.png)  

As you can see, the final VCF file contains a column for each individual.

