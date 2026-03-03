# Drug_target_genes_and_tissue_type_enrichment

Here I am trying to check how the maximum testing phase drugs make into changes with tissue specificity of their target genes. This is a changed version of the same for cell type enrichment in 

```url
https://github.com/TharinduTS/target_genes_for_drug_targets/blob/main/README.md#target-genes-for-drug-targets
```

Therefore I am starting with the Adding enrichment data step, but for tissue types this type. This is a continuation from chapter 3 of my previous drug targets and cell type enrichment pipeline

# 1)Adding enrichment data - Tissue type Enrichment

## 1-I introduction
I start here with my drug database with genes and side effects mapped from chapter 3

```
Final_data_with_cell_type_expression_data.tsv
```

and 

```
drug_info_with_genes_and_warnings_fully_cleaned.tsv
```

from previous pipelines 

```url
https://github.com/TharinduTS/Tissue_type_enrichment
```

and

```url
https://github.com/TharinduTS/target_genes_for_drug_targets
```
and 

## 1-II Script

gene_enrichment_join.py script from 

```url
https://github.com/TharinduTS/target_genes_for_drug_targets?tab=readme-ov-file#3-ii-script
```
## 1-III Run command

```bash
python gene_enrichment_join.py \
  --table1 drug_info_with_genes_and_warnings_fully_cleaned.tsv \
  --table2 Final_data_with_cell_type_expression_data.tsv \
  --output chembl_with_enrichment.tsv \
  --t1-col "ChEMBL_HGNC" \
  --t1-sep "," \
  --t2-gene-col "Gene name" \
  --t2-key-col "Tissue" \
  --t2-value-col "log2_enrichment_penalized" \
  --gene-key-sep "-" \
  --pair-sep ":" \
  --item-sep " & " \
  --newcol-name "ChEMBL_HGNC_enrichment"
```

## 1-IV selecting maximum enrichment

Just like in 
```url
https://github.com/TharinduTS/target_genes_for_drug_targets?tab=readme-ov-file#3-v-selecting-maximum-enrichment
```

```bash
python extract_extreme_enrichment.py \
  --input chembl_with_enrichment.tsv \
  --output chembl_with_enrichment_scored.tsv \
  --source-col "ChEMBL_HGNC_enrichment" \
  --item-sep " & " \
  --gene-key-sep "-" \
  --pair-sep ":" \
  --agg max \
  --label-col "Tissue_type_with_max_enrichment" \
  --value-col "Penalized_enrichment_value" \
  --html-unescape \
  --value-round 6
```

Here onwards, I am getting scripts and following everything from the previous pipeline chapter 4 onwards

# 2) Fill any missing values

## Run command
```bash
python fill_empty_values.py \
    -i chembl_with_enrichment_scored.tsv \
    -o chembl_with_enrichment_scored_empties_filled.tsv \
    --fill "Parent Molecule ChEMBL ID=none" \
    --fill "Parent Molecule Name=No_name" \
    --fill "Parent Molecule Type=none" \
    --fill "Target ChEMBL ID=none" \
    --fill "Target Name=none" \
    --fill "Action Type=none" \
    --fill "ChEMBL_HGNC=none" \
    --fill "Warning Type=No Warnings found" \
    --fill "Warning Class=none" \
    --fill "Description=none" \
    --fill "Country=none" \
    --fill "First Withdrawn Year=none" \
    --fill "EFO ID=none" \
    --fill "EFO Term=none" \
    --fill "ChEMBL_HGNC_enrichment=none" \
    --fill "Tissue_type_with_max_enrichment=none" \
    --fill "Max Phase=none" \
    --fill "First Approval=none" \
    --fill "Penalized_enrichment_value=none"
```

# 3) Making interactive plots

## Run command

```bash
python universal_plot_maker_plus.py \
  --file chembl_with_enrichment_scored_empties_filled.tsv \
  --out chembl_with_enrichment_scored_tissues.html \
  --plot-type bar \
  --x-choices "Parent Molecule ChEMBL ID | Parent Molecule Name | Target ChEMBL ID | Target Name" \
  --y-choices "Penalized_enrichment_value" \
  --default-x "Target ChEMBL ID" \
  --default-y "Penalized_enrichment_value" \
  --color-col "Max Phase" \
  --color-choices "Max Phase|Parent Molecule Name|Parent Molecule Type|First Approval|Target Name|Action Type|Warning Type|Warning Class" \
  --filter-cols "Max Phase|Parent Molecule Name|Parent Molecule Type|First Approval|Target Name|Action Type|Warning Type|Warning Class" \
  --search-cols "Max Phase|Parent Molecule Name|Parent Molecule Type|First Approval|Target Name|Action Type|Warning Type|Warning Class" \
  --details "Parent Molecule ChEMBL ID|Parent Molecule Name|Parent Molecule Type|Max Phase|First Approval|Target ChEMBL ID|Target Name|Action Type|ChEMBL_HGNC|Warning Type|Warning Class|Description|Country|First Withdrawn Year|EFO ID|EFO Term|Tissue_type_with_max_enrichment|Penalized_enrichment_value" \
  --title "Drug targets V1" \
  --dup-policy overlay \
  --sort-primary "Penalized_enrichment_value" \
  --sort-primary-order desc \
  --sort-secondary "Target Name" \
  --sort-secondary-order asc \
  --initial-zoom 100 \
  --self-contained \
  --lang en \
  --pt-enable \
  --pt-col "ChEMBL_HGNC_enrichment" \
  --pt-title "Enrichment for Tissue types" \
  --pt-x-label "Tissue type" \
  --pt-y-label "log2 Enrichment Penalized" \
  --pt-color "#2a9d8f" \
  --pt-height 360 \
  --pt-width auto \
  --pt-rotate -35 \
  --pt-container-id "present-tissues-plot" \
  --pt-enable --pt-mode flow \
  --pt-anchor "#rowDetails" --pt-position after \
  --pt-offset-x -300 --pt-offset-y -10
```

