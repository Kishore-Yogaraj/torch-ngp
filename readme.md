- Install Anaconda for linux
	- https://docs.anaconda.com/anaconda/install/
	- May need to install curl first if linux is fresh
	
```bash
sudo apt update
sudo apt install curl
```

- Install git
```bash
sudo apt install git
```

- Clone repo
```bash
git clone https://github.com/ashawkey/torch-ngp.git
```

- Create Anaconda env with appropriate version of torch and cuda
```bash
conda creat -n torch-ngp python=3.9 pytorch=1.10 torchvision torchaudio cudatoolkit=11.3 -c pytroch -c nvidia
```

- Inside the anaconda env install all the python dependencies
```bash
cd torch-ngp
pip install -r requirements.txt
```

- Your pytorch version and your cuda version will change when you pip install to match dependencies
	- To fix this you need to reinstall the correct torch and cuda version and then manually install the correct dependencies with pip

```bash
pip uninstall torch torchvision torchaudio -y
conda install pytorch=1.10 torchvision=0.11 torchaudio=0.10 cudatoolkit=11.3 -c pytorch -c nvidia
```

- When you run the bash script for the setup.py you run into an error where the correct cuda version isn't installed
	- To solve this you need to install the correct cuda version by installing the .run file
	- https://developer.nvidia.com/cuda-11.3.0-download-archive?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=20.04&target_type=runfile_local
	- Manually install the latest driver because drivers are backwards compatible with cuda version and then select the option to not install the drivers suggests
	- Steps are shown below:

```bash 
# We first want to work with a blank slate so remove all cuda installations first
sudo apt-get --purge remove "cuda*"
sudo apt-get --purge remove "nvidia-*"
sudo apt autoremove
sudo apt autoclean

#Remove any existing drivers
sudo apt-get purg '^nvidia-.*'
sudo apt-get remove

#Ensure nouveau drivers are disabled
sudo nano /etc/modprobe.d/blacklist-nouveau.conf

#Add following lines
blacklist nouveau
options nouveau modset=0

#Rebuild the initramfs
sudo update-initramfs -u

#Reboot system
sudo reboot

#Install the latest drivers for your gpu independently
sudo apt-get install -y nvidia-driver-<version number>

#Reboot system
sudo reboot

#Test driver installation (should see details about gpu)
nvidia-smi

#Go to the link mentioned above and find the .run package and then execute the commands shown on the web page. It should look like the following:
wget https://developer.download.nvidia.com/compute/cuda/11.3.0/local_installers/cuda_11.3.0_465.19.01_linux.run

#After this command, follow the prompts shown in the terminal. You will get to page in the terminal that shown various options for installations such as drivers, toolkit, documentation, etc. Make sure you untick the drivers because the correct drivers for your gpu are already installed
sudo sh cuda_11.3.0_465.19.01_linux.run

#Check cuda installation
ls /usr/local/cuda-11.3

#Add cuda to the env variables
nano ~/.bashrc

#Add to end of bashrc file and save
export PATH=/usr/local/cuda-11.3/bin:$PATH 
export LD_LIBRARY_PATH=/usr/local/cuda-11.3/lib64:$LD_LIBRARY_PATH

#Source changes
source ~/.bashrc

#Check cuda version. This should result in the appropriate cuda version
nvcc --version
```

### Potential Compiler Errors

```bash
#Verify path and existence of compilers
which gcc-10
which g++-10

#Explicitly set compiler
export CC=/usr/bin/gcc-10
export CXX=/usr/bin/g++-10

#Verify compiler versions (commands should return 10.5.0)
$CC --version
$CXX --version
```

#### Force compiler version for build

```bash
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-10 10
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-10 10
sudo update-alternatives --config gcc
sudo update-alternatives --config g++

#Check version
gcc --version
g++ --version

#Build project
bash scripts/install_ext.sh
```


#### Command to find torch and cuda version

```bash
python -c "import torch; print(torch.__version__)"
python -c "import torch; print(torch.version.cuda)"
```


### Video Output Errors

```bash
#Instal in conda env
pip install imageio[ffmpeg]

#or
pip install imageio[pyav]
```
