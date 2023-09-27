# Installing Conda via Homebrew

Conda is simple way to install cross-platform Python distribution for scientific computing. It comes bundled with many scientific libraries, including NumPy, Pandas, Matplotlib, Scikit-Learn and much more.

##### Install via [Homebrew](https://brew.sh/)

```
brew install --cask anaconda
```

##### Activate Conda

````shell
~/../../opt/homebrew/anaconda3/bin/conda init zsh
````
[^1]

##### Update to use the latest tools

```shell
conda update -n base -c defaults conda
```

##### Create Conda environment

```shell
conda env create -f environment.yml -n <env-name>
```

This will create a new `conda` environment containing every library you will needed to run all the notebook.

#### Activate Conda environment

```shell
conda activate <env-name>
```

#### Juypter Kernel

```
python3 -m ipykernel install --user --name=<juypter-name>
```

#### Using Kernel in [VSCode](https://code.visualstudio.com/docs/datascience/jupyter-kernel-management#_jupyter-kernels)

1. Open the notebook in Visual Studio Code
2. Click the 'Select Kernel' button
3. Select another Kernel -> Juypter Kernel -> Find the Kernel installed created in (should be stored in `/opt/homebrew/anaconda3/envs/<env-name>`).

#### Listing all Jupyter kernels installed

```shell
jupyter kernelspec list
```

#### Removing Jupyter kernel

```shell
jupyter kernelspec remove <kernel_name>
```

#### Delete Conda environment

```shell
conda env remove -n <env-name>
```

#### Using Docker

Alternatively, build a docker image that uses [continuumio/miniconda3](https://hub.docker.com/r/continuumio/miniconda3/) to include everything that is needed, then run a docker container and use the Juypter server in your browser or connect to the server via VSCode.

[^1] Stackoverflow [answer](https://stackoverflow.com/a/66296716) 

---

#macos #homebrew #vscode #juypter
