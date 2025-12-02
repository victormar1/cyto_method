# cyto_method
# Methods

## Single-Cell RNA Sequencing Data

### Mouse Brain Dataset
Single-cell RNA sequencing (scRNA-seq) data from mouse brain were obtained from https://www.cell.com/neuron/fulltext/S0896-6273(25)00695-6. The dataset comprised 597,668 cells across 26,431 genes, spanning six developmental timepoints: embryonic day 18.5 (E18.5), postnatal days 4, 14, and 32 (P4, P14, P32), and 90 weeks (aged adult). Cell type annotations were derived from orinal work (https://www.cell.com/neuron/fulltext/S0896-6273(25)00695-6), yielding 31 distinct superclusters including perivascular fibroblasts (PVF/Fibroblast), astrocytes, neurons, oligodendrocytes, microglia, endothelial cells, pericytes, and vascular leptomeningeal cells (VLMC).

### Marmoset Brain Dataset  
Comparative scRNA-seq data from common marmoset (*Callithrix jacchus*) brain were obtained from (https://www.cell.com/neuron/fulltext/S0896-6273(25)00695-6). This dataset included 881,832 cells across 22,582 genes from multiple age stages. Cell type annotation followed the original paper, resulting in 10 superclusters with nomenclature adapted to enable cross-species comparison.

## Data Preprocessing

All analyses were performed using Python (v3.11) with Scanpy (v1.11.x) for single-cell analysis. Raw count matrices were quality-controlled and normalized using library-size normalization followed by log-transformation (log1p). For memory efficiency during large-scale analyses, backed mode (HDF5) was employed for initial data loading, with targeted subsampling (maximum 2,000 cells per cell type) for computationally intensive analyses.

Cell type annotations were simplified by aggregating:
- Neuronal subtypes (Neuron_*) → "Neurons"
- Astrocyte subtypes (Astro*, Astrocyte_*) → "Astrocyte"  
- Oligodendrocyte subtypes (Oligo*, excluding OPC) → "Oligodendrocyte"

Gene name harmonization between species was performed using case-insensitive matching to account for nomenclature differences (mouse: lowercase; marmoset: uppercase).

## WNT Pathway Expression Analysis

WNT pathway activity was quantified by computing mean expression scores across 19 canonical WNT ligand genes (*WNT1, WNT2, WNT2B, WNT3, WNT3A, WNT4, WNT5A, WNT5B, WNT6, WNT7A, WNT7B, WNT8A, WNT8B, WNT9A, WNT9B, WNT10A, WNT10B, WNT11, WNT16*) plus the non-canonical WNT receptor *ROR2*. Expression scores were calculated per cell type as the mean of normalized gene expression values.

For non-canonical WNT (ncWNT) pathway analysis, a focused gene set comprising *WNT5A* and *ROR2* was used to assess the WNT5A-ROR2 signaling axis specifically associated with perivascular fibroblasts.

## Cell-Cell Communication Analysis

Ligand-receptor interaction analysis was performed using LIANA (last stable build), a framework that integrates multiple cell-cell communication inference methods. The `rank_aggregate` function was applied with the following parameters:
- **Resource database**: 'consensus' (integrating CellChatDB, CellPhoneDB, and other curated databases)
- **Expression threshold**: expr_prop = 0.1 (minimum 10% of cells expressing ligand/receptor)
- **Permutations**: n_perms = 100
- **Grouping variable**: Simplified cell type annotations

Communication hub scores were computed for each cell type by combining:
- **Outgoing score**: Number of unique interactions × (1 - mean magnitude_rank) as source
- **Incoming score**: Number of unique interactions × (1 - mean magnitude_rank) as target
- **Total hub score**: Sum of outgoing and incoming scores
- **Hub ratio**: Outgoing score / (Incoming score + 1), where ratio >1 indicates sender-dominant phenotype

WNT-specific interactions were extracted by filtering for interactions involving WNT family ligands from fibroblast source cells.

## Statistical Analysis

Cross-species conservation was assessed by comparing WNT pathway expression rankings and LIANA hub scores between mouse and marmoset datasets.

## Software and Code Availability

All analysis code can be made available on demand. Key software versions:
- Python 3.11.x
- Scanpy 1.11.x  
- LIANA [**MISSING: version**]
- matplotlib 3.x
- seaborn 0.x
- pandas 2.x
- numpy 1.x
