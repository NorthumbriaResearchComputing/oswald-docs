# Oswald Documentation Website

Oswald is Northumbria University's HPC cluster. See [the documentation website](https://nu-cem.github.io/HPC_internship_2021/) for more information.

## Building the Docs Offline

To build and preview the docs offline (eg. for more significant changes than content changes), you can use [conda](https://docs.conda.io/en/latest/):

```
git clone https://github.com/NU_SEM/HPC_internship_2021/
cd HPC_internship_2021
conda env create -p ./env -f environment.yml
conda activate ./env
mkdocs serve # does `mkdocs build` for you
```

### Changing Dependencies

If you add or change any dependencies, you will need to update the conda environment's dependencies file in the root of the repo. You can use the following command as a guide, but be aware that it doesn't include all needed packages (eg. pip-installed packages):

```
conda env export --from-history | grep -v prefix:
```
