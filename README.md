# Combining generative artificial intelligence and on-chip synthesis for de novo drug design

In this repository, you will find data and code associated with the study
**Combining Generative Artificial Intelligence and On-Chip Synthesis for De Novo Drug Design**, in which de novo design based on Long Short-Term Memory network was combined with a microfluidics platform, see [Grisoni, Huisman *et al.* 2020](https://chemrxiv.org/articles/preprint/Combining_Generative_Artificial_Intelligence_and_On-Chip_Synthesis_for_De_Novo_Drug_Design/13498587).
In this repository, you will find useful data and code to reproduce the results of our study.

1. [Data](#data)
3. [Virtual reaction filter code](#code)
    1. [Installation](#install)
    2. [Usage](#usage)
    3. [Example](#example)
4. [Generative deep learning code](#ai)
5. [How to cite](#cite)


## Data <a name="data"></a>

The input data is located in [data](/data). The folder contains the following files:

* LSTM_pretraining_data.zip: Contains the SMILES of the compounds used for pretraining the LSTM model.
* data_tr.txt: LSTM pretraining data used for training.
* decomposition_reactions.txt: Reaction SMARTS used to convert the molecules into their corresponding reactants.
* LSTM_FLOW-MOL_DB_DATA.npy: Molecular database of commercially available molecules. Each entry contains the number of molecule, the molecular SMILES, and the molecular weight is stored. The numpy array object is too large upload to git.
* mol_db_data.csv: Molecular database stored as a csv file. This file needs to be converted to the corresponding numpy array object in order to work with decompose.py and retrieve_bb.py.
* create_npy_db.py: Script for converting the csv file into the numpy array object. 

## Virtual reaction filter <a name="code"></a>

The retro-synthesis is performed in two steps. In the first step, a series of reactions are applied to each product molecule in order to decompose it in to its corresponding reactants (decompose.py). The reaction used and the reactant molecules are stored as a text file. Once all of the products have been decomposed, the reactant molecules are then compared against a database of known, commercially available molecules. If all of the reactant molecules for a given reaction can be found within the database, the product molecule along with the reaction and the retrieved reactants molecules are stored in the output file.

### Installation <a name="install"></a>

To access the code, first clone the git repository

```bash
git clone git@github.com:abuttonCH/retro-retrieval.git
```

This code requires rdkit version 2018.09.1 to be installed. The best way to do this is to create a conda environment. 
```bash
conda create -n my_retrieve_env -c rdkit rdkit=2018.09.1
```
Once you have created the conda environment, you need to activate it.

```bash
conda activate my_retrieve_env
```
### Create the Molecular DB
Before running the method, one first needs to create the mol db numpy array object:
```bash
python create_npy_db.py --input ../data/mol_db_data.csv --output ../data/LSTM_FLOW-MOL_DB_DATA.npy
```
```--input```: Molecules saved in a csv file (Number of Molecules,SMILES,Molecular Weight). **csv file**

```--output```: Molecules converted to a numpy array. **numpy array**

### Usage <a name="usage"></a>
The code for the decomposition and building block retrieval is located in the [code](/code) folder

The folder contains the following files:
* *create_npy_db.py* (generates the mol db array object)
* *decompose.py* (converts molecules into reactants)
* *retrieve_bb.py* (returns reactants found in mol db)
* *reaction_library.py* (defines the reaction object)
*  *reaction_class_auto.py* (performs the reaction)

### Conversion to Reactants
*decompose.py* applies each of the reactions specified in *decomposition_reactions.txt* to the input molecules. Each set of resulting reactants generated by a given molecule and reaction are written to the output file. 

```python
python decompose.py --mol molfile.txt --reaction decomposition_reactions.txt --out decomposition_output.txt --limit molecule_limit

```

```--mol```: The molecules to be decomposed. Each line of the input file should contain a single SMILES string. **text file**

```--reaction```: The decomposition reactions written in the SMARTS format (decomposition reaction | SMARTS | number of conserved  rings). **text file**

```--out```: The file path to output the results to. **text file**

```--limit```: The number of molecules to process. If not specified, then decompose.py will process the entire file. **int**


### Building Block Retrieval 
*retrieve_bb.py* searches the molecular database for matches between the decomposed reactant moleuces and the molecules in the database. If all of the reactants for a given input molecule are found, then the result is written to the output file. 

```python
python retrieve_bb.py --decomp decomposition_output.txt --mol_db molecular_database.npy --out retrieve_output.txt

```

```--decomp```: The decomposed products generated by *decompose.py*. **text file**

```--mol_db```: Datbase of commercially available molecules stored as numpy array object. **numpy array** 

```--out```: The file path specifying where to write the outputs to. **string**

## Example <a name="example"></a>
Examples of running decompose.py and retrieve_bb.py. All files have been provided except for the molecular_database file (LSTM_FLOW-MOL_DB_DATA.npy) as it was too large. LSTM_FLOW-MOL_DB_DATA.npy has been provided in the supplementary information of "Combining generative artificial intelligence and on-chip synthesis for de novo drug design".

```python
python code/create_npy_db.py --input data/mol_db_data.csv --output data/LSTM_FLOW-MOL_DB_DATA.npy
```

```python
python code/decompose.py --mol data/data_val.txt --reaction data/decomposition_reactions.txt --out output/test_decomp.txt --limit 100
```

```python
python code/retrieve_bb.py --decomp output/test_decomp.txt --mol_db data/LSTM_FLOW-MOL_DB_DATA.npy --out output/test_retrieve.txt
```
## Generative deep learning code <a name="ai"></a>

The code used for molecule generation can be found in the dedicated repository: [ETHmodlab/virtual_libraries](https://github.com/ETHmodlab/virtual_libraries).

## How to cite <a name="cite"></a>

If you use any data or scripts associated to this repo, please cite:

```
@article{grisoni2020,
  title         = {Combining generative artificial intelligence and on-chip synthesis for de novo drug design},
  author        = {Grisoni, Francesca and Huisman, Berend and Button, Alex and Moret, Michael and Atz, Kenneth and Merk, Daniel and Schneider, Gisbert},
  journal       = {ChemRXiv},
  volume        = {},
  number        = {},
  pages         = {}, 
  year          = {2020},
  doi           = {10.26434/chemrxiv.13498587.v1},
  url           = {https://doi.org/10.26434/chemrxiv.13498587.v1},
 publisher      = {}
```
