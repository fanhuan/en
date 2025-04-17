---
layout: post
title: LD Prunning
categories: [notes]
tags: [stats]
---


In the era of whole genome sequencing of thousands of individuals, we are facing the problem of not too few genetic variants, but too many. A major task is to filter those variants. Recently there is a very good review paper on this topic by [Hemstrom et al](https://www.nature.com/articles/s41576-024-00738-6). One of the way of filtering is based on **linkage disequilibrium (LD)**.

### **1. What is Linkage Disequilibrium (LD)?**
- **LD** measures the statistical association between alleles at different loci.
- If two loci are in LD, the allele combinations at these loci occur more or less frequently than expected based on their individual allele frequencies.
- LD can be quantified using metrics like \( D' \), \( r^2 \), or \( D \):
  - \( r^2 \): Measures the correlation between alleles at two loci, ranging from 0 (no LD) to 1 (complete LD).
  - \( D \): Measures the deviation of observed haplotype frequencies from expected under linkage equilibrium.

### **2. Real-World Implications**
1. **Mapping Genes**:
   - LD is used in genome-wide association studies (GWAS) to link genetic markers to traits.
   - High LD regions may indicate physical proximity between a marker and a causal variant.

2. **Population Genomics**:
   - LD patterns provide insights into recombination landscapes, population structure, and demographic history.

3. **Selective Sweeps**:
   - Strong positive selection can maintain high LD around a beneficial allele, even in regions with moderate recombination rates.

Why do we want/need to ca

The relationship between **recombination rate** and **linkage disequilibrium (LD)** is a key concept in population genetics. LD describes the non-random association of alleles at two or more loci, while recombination rate determines how frequently genetic material is exchanged between loci during meiosis. Here's how they are related:

---




---

### **4. Mathematical Description**
LD decay due to recombination can be described by the equation:
\[
D_{t+1} = (1 - r) D_t
\]
Where:
- \( D_{t+1} \): LD at the next generation.
- \( r \): Recombination rate between two loci.
- \( D_t \): LD in the current generation.

This shows that:
- Higher recombination rates (\( r \)) reduce LD faster across generations.
- Lower recombination rates (\( r \)) allow LD to persist for longer periods.

---



---

### **6. Summary**
- **Recombination rate inversely affects LD**: High recombination reduces LD, while low recombination maintains it.
- LD patterns reflect the interplay of recombination, selection, mutation, and demographic factors.
- Understanding the relationship between recombination and LD is crucial for genetic mapping, evolutionary studies, and understanding population structure.

**Recombination rate** is typically calculated or estimated using genetic data, and it represents the frequency at which recombination occurs between two loci. This rate can be determined in different ways depending on the type of data and methods used. Below are the key approaches:

---

### **1. Using Genetic Maps**
A **genetic map** provides recombination rates in centiMorgans (cM) per physical distance (e.g., per megabase, Mb). 

- **Definition of 1 cM**:
  - 1 centiMorgan corresponds to a 1% chance of recombination occurring between two loci during meiosis.

- **Recombination Rate**:
  \[
  \text{Recombination Rate (cM/Mb)} = \frac{\text{Genetic Distance (cM)}}{\text{Physical Distance (Mb)}}
  \]

#### How Genetic Maps Are Built:
1. **Linkage Analysis**:
   - Use observed genetic markers (e.g., SNPs) from pedigree data or experimental crosses.
   - Recombination frequencies (\( r \)) between markers are measured.
   - The genetic distance is inferred using the **Haldane** or **Kosambi** mapping functions:
     - **Haldane** (no interference): \( d = -\frac{1}{2} \ln(1 - 2r) \)
     - **Kosambi** (with interference): \( d = \frac{1}{4} \ln\left(\frac{1 + 2r}{1 - 2r}\right) \)
   - Genetic distances are summed to build the map.

2. **High-Density SNP Data**:
   - Use population-based genetic data and haplotypes to infer recombination hotspots and recombination rates.

---

### **2. Using Population Genetic Data**
Recombination rates can also be inferred directly from population genetic data using linkage disequilibrium (LD).

#### Concept:
Recombination breaks down LD over time, so patterns of LD between markers can be used to estimate recombination rates.

1. **Statistical Models**:
   - LD-based methods estimate \( r \) by fitting population genetic models.
   - Software tools such as **LDhat** and **LDhelmet** are widely used for this purpose.

2. **Coalescent Framework**:
   - Recombination rates are estimated by modeling how haplotypes coalesce back in time under specific demographic and genetic scenarios.

3. **Formula Linking LD and Recombination**:
   LD decay due to recombination is modeled as:
   \[
   r^2 = \frac{1}{1 + 4N_e r}
   \]
   Where:
   - \( r^2 \): Linkage disequilibrium between loci.
   - \( N_e \): Effective population size.
   - \( r \): Recombination rate between loci.

This relationship allows estimation of \( r \) using LD patterns in population data.

---

### **3. Experimental Crosses**
In experimental populations (e.g., plants or animals), recombination rates can be measured directly by analyzing offspring genotypes from controlled crosses.

#### Steps:
1. Cross two genetically distinct parents to produce offspring.
2. Genotype markers (e.g., SNPs, microsatellites) in the offspring.
3. Count recombination events between adjacent markers.
4. Calculate recombination frequency:
   \[
   r = \frac{\text{Number of recombinant offspring}}{\text{Total number of offspring}}
   \]
5. Use mapping functions (e.g., Haldane or Kosambi) to convert recombination frequencies into genetic distances.

---

### **4. Using Molecular Data**
With advancements in sequencing, recombination rates can also be estimated using:
1. **Recombination Hotspots**:
   - High-resolution sequencing data reveals recombination hotspots (regions with very high recombination rates).
   - Tools like **PRDM9 motif analysis** can identify hotspots based on sequence patterns.
2. **Double-Strand Break (DSB) Mapping**:
   - Experimental methods (e.g., ChIP-seq for DSB proteins like Spo11) directly measure recombination activity at specific genomic regions.

---

### **5. Using Existing Recombination Maps**
For well-studied organisms like humans, mice, and certain crops, recombination maps are already available:
- **Human recombination maps** (e.g., HapMap or 1000 Genomes Project) provide rates in \( \text{cM/Mb} \) across the genome.
- These maps are often derived from large-scale genotyping and haplotype-based LD analyses.

---

### **Example in Humans**
In humans, the average recombination rate is ~1.2 cM/Mb, but it varies across the genome:
- **Recombination hotspots**: Regions with recombination rates >10 cM/Mb.
- **Recombination coldspots**: Regions with recombination rates <0.1 cM/Mb.

---

### **R Implementation Example**
If you have genetic distances (cM) and physical distances (Mb), you can calculate recombination rates like this:

```R
# Example data
genetic_distance <- c(1.5, 2.0, 0.5)  # in cM
physical_distance <- c(0.1, 0.2, 0.05)  # in Mb

# Calculate recombination rate in cM/Mb
recombination_rate <- genetic_distance / physical_distance

# Print results
print(recombination_rate)
```

#### Output:
```
[1] 15 10 10  # cM/Mb
```

---

### Summary:
- **Recombination rates** can be calculated from genetic maps, LD patterns, or experimental crosses.
- They are influenced by physical distance, recombination hotspots, and population genetics.
- Tools like **LDhat**, **LDhelmet**, and existing recombination maps are useful for estimation.