# venv39

A virtual environment configured to run a set of 4 AutoMLs: LFAML, TPOT, AutoGluon and H2O.

## Requirements
- Linux or WSL (2.1.5.0)
- openjdk version "17.0.x"

____

## Instructions for use in Linux or WSL

1. Clone the repository and install the requirements:
```bash
git clone git@github.com:deividedc/venv39.git
```
2. Install openjdk
```bash
sudo apt update
sudo apt install openjdk-17-jre -y
```

3. Install Python 3.9
```bash
sudo apt install python3.9 python3.9-venv python3.9-dev -y
```

4. Create a virtual environment with Python 3.9
```bash
python3.9 -m venv ~/env39
```

5. Open the virtual environment
```bash
source env39/bin/activate
```

6. Update pip
```bash
pip install --update pip
```

7. Install PDM in the venv
```bash
pip install pdm
```
   7.1. If you are unable to install in the virtual environment, install the global PDM and repeat step 7.
   ```bash
   sudo apt update
   sudo apt install pdm -y
   ```

8. Navigate to the venv39 folder with the virtual environment active

9. Install the library package with PDM
```bash
pdm install
```


___
## How to install WSL (2.1.5.0)

1. Download WSL (2.1.5.0) from the Microsoft website or click [here](https://github.com/microsoft/WSL/releases/download/2.1.5/wsl.2.1.5.0.x64.msi?utm_source=chatgpt.com)

2. Open PowerShell as administrator.

3. Navigate to the file download location
```bash
cd "C:\Users\YOUR_USERNAME\Downloads"
```

4. Run
```bash
msiexec /i wsl.2.1.5.0.x64.msi
```

5. Open WSL and run the [instructions for use](##Instructions-for-use-in-Linux-or-WSL).
   

