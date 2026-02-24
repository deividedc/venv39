# venv39

A virtual environment configured to run a set of 5 AutoMLs: LFAML, TPOT, AutoGluon, H2O, and AutoKeras.

## Requirements
- Linux or WSL
- Python 3.9
- flaml==2.3.4
- tpot==0.12.2
- autogluon==1.2
- h2o==3.46.0.7
- autokeras==1.0.16
- keras-tuner==1.0.4
- numpy==1.23

____

## Instructions for use

1. Clone the repository and install the requirements:
```bash
git clone git@github.com:deividedc/venv39.git
```
2. Install Python 3.9

For Linux or WSL
```bash
sudo apt install python3.9 python3.9-venv python3.9-dev -y
```

3. Create a virtual environment with Python 3.9
```bash
python3.9 -m venv ~/env39
```

4. Update pip
```bash
pip install --update pip
```

5. Install PDM
```bash
pip install pdm
```

7. Navigate to the venv39 folder with the virtual environment active

8. Install the library package with PDM
```bash
pdm install
```


