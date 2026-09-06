# Raw DPPH Source Dataset

The raw modeling dataset used in this project is:

`DPPH_30min_Dataset.xlsx`

It originates from the public repository accompanying:

Ghironi, S., Viganò, E. L., Selvestrel, G., & Benfenati, E. (2025). *QSAR Models for Predicting the Antioxidant Potential of Chemical Substances*. Journal of Xenobiotics, 15(3), 80. DOI: 10.3390/jox15030080.

Original source repository:
https://github.com/EdoardoVigano/AntioxidantActivity

Original dataset location:
https://github.com/EdoardoVigano/AntioxidantActivity/blob/main/Data/DPPH_30min_Dataset.xlsx

The upstream GitHub repository makes the dataset publicly accessible but does not currently declare a repository-level license. For provenance and to avoid mirroring a third-party file without an explicit repository license, the XLSX file is therefore not redistributed here.

## Reproducing the analysis

Download `DPPH_30min_Dataset.xlsx` from the original source and place it locally at:

```text
data/raw/DPPH_30min_Dataset.xlsx
```

The main notebook will then use this local file as the starting dataset. Generated intermediate datasets are excluded from version control and are recreated by the analysis workflow.
