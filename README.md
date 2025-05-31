# Wai Worker Node
We earn W points by running a worker node and contributing GPUs to the WAI Protocol's distributed AI inference system. W points track your contributions and are likely to convert to rewards in the future.

# Requirement
Hardware:
* Any M-Series (Apple Silicon) chip:
  * Macbook/Mac Mini M1, M2, M3, M4
* NVIDIA GPUs: Compute capability of 5.0+
  * GTX 1050, RTX 2060, RTX 3070, RTX 4080, etc.
  
Software:
* Recommended Nvidia [driver](https://www.nvidia.com/en-us/drivers/details/245674/) (Local PC)
* CUDA Toolkit `12.4` (only supports this version)

---

# Signup Wai Node Dashboard
* 1- Signup your email in [Wai Dashboard](https://w.ai/r/MOEI)
* 2- Create a new Key in `API Keys` section.

---

# Installation Method
* **Desktop Users: (Linux, Windows, MacOS)**
  * Install GUI Application on your local PC from the `Download` section of the dashboard.
  * If you don't want to install applications on your system, I recommend go through `Windows CLI` version.
 
* **CLI (Windows):**
  * Windows: Install `Ubuntu WSL` on your Windows using this [guide](https://github.com/0xmoei/Install-Linux-on-Windows)
  * A good internet connection is needed.

* **CLI (Rent QuickPod GPU):**
  * Signup on [QuickPod](https://console.quickpod.io?affiliate=f621de18-b6ac-4416-b87f-01f29f8339b5) using email and verify it.
  * Fund your QuickPod with crypto by clicking on `+Add` on top-right.
  * In `Template` section, Choose `CUDA 12.4`.
  * Now in GPU selection, you can buy a cheap one for this node (You find some for $0.05/hr)
  * But more more miners need more vRAM.
  * Click on `Create POD` to rent GPU.
  * Choose at least `100GB` Disk.
  * Press `Connect` on your Pod.
  * You can `Connect to Web Terminal` or just copy the `SSH Command` to connect through your own client like Windows Powershell, VScode, Termius, etc.
  * In `QuickPod` method , you don't need to create `SSH-key` and all the steps are as above.
 
* **CLI (Rent Vast GPU):**
  * Alternatively, you can follow this [Rent and Setup GPU Guide](https://github.com/0xmoei/Rent-and-Config-GPU) if you want to rent `Vast` GPUs.
  * I personally use both `QuickPod` and `Vast`, choosing based on my needs and the server's price.

---

# Method: CLI
## Step 1. Install Dependecies
```console
# Update System Packages
apt update && apt upgrade -y

# Install General Utilities and Tools
apt install screen curl iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev  -y

# Install Python
apt install python3 python3-pip python3-venv python3-dev -y

# Install NodeJS
apt update
curl -fsSL https://deb.nodesource.com/setup_22.x | bash -
apt install -y nodejs
node -v
npm install -g yarn
yarn -v
```

---

## Step 2. Install Nvidia & 12.4 CUDA Toolkit
* Verify Cuda version is `12.4` by running `nvidia-smi` and `nvcc --version`.
* If you've rented a GPU, then there's no need to install this step since you initially could choose `Cuda 12.4` Templates.
* Only if you are a local pc user or with custom sepecification, then follow this to make sure your system is compatible with `Cuda 12.4`.
* This step is only for `Windows (WSL Users)` and NOT for native-Linux users.

### Install the NVIDIA Driver
* Download and Install the NVIDIA driver version `550.90.07` from [this link](https://www.nvidia.com/en-us/drivers/details/245674/).
* Note: No need to install a new driver if you think your current NVIDIA driver is 5+ and compatible with `Cuda 12.4`.

### Install CUDA 12.4:
Follow if you don't have any version of Cuda Toolkits installed:
* For Windows users 
```
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-wsl-ubuntu.pin
sudo mv cuda-wsl-ubuntu.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.4.0/local_installers/cuda-repo-wsl-ubuntu-12-4-local_12.4.0-1_amd64.deb
sudo dpkg -i cuda-repo-wsl-ubuntu-12-4-local_12.4.0-1_amd64.deb
sudo cp /var/cuda-repo-wsl-ubuntu-12-4-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-4
```
```
echo 'export PATH=/usr/local/cuda-12.4/bin:$PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=/usr/local/cuda-12.4/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc
```
* Check NVIDIA driver version: `nvidia-smi`
   * Look for Driver Version: `550+` and CUDA Version: `12.4`.
* Check the CUDA version with: `nvcc --version`
  * Should show Cuda release `12.4`.

### Upgrading or Downgrading to CUDA 12.4:
Follow if you have a different CUDA version installed
```console
# Uninstall the existing CUDA version:
sudo apt remove --purge cuda
sudo apt autoremove
```
* Now, Follow Install CUDA `12.4` step above.

---

## Step 3. Install Wai CLI
```console
curl -fsSL https://app.w.ai/install.sh | bash
```

---

## Step 4. Run wai Worker
```console
# Add API key
export W_AI_API_KEY=your key here

# Run worker
wai run
```

![image](https://github.com/user-attachments/assets/467d9a24-961d-442c-a9db-105dd7eb4d6b)

---

## Step 5. Run multiple workers
After you ensure your worker's running good, you can try running `multiple` workers in the background to mine more coins.

### Install PM2
```console
npm install -g pm2
```

### Create a PM2 Configuration File
Create `wai.config.js` file:
```console
nano wai.config.js
```
Paste the following configuration in it:
```js
module.exports = {
  apps : [{
    name: 'wai-node', // A name for your process
    script: 'wai',   // The command to run
    args: 'run',     // Arguments for the command
    instances: 4,    // Updated number of instances
    autorestart: true, // Automatically restart if it crashes
    watch: false,    // Don't watch for file changes to restart
    max_memory_restart: '1G', // Restart if memory usage exceeds 1GB (adjust as needed)
    // Combine all environment variables into a single env block
    env: {
      NODE_ENV: 'production',
      W_AI_API_KEY: 'your-key-here'
    }
  }]
};
```
* Replace `your-key-here` with your API key.
* You can adjust the `instances` number based on how many concurrent wai worker nodes your GPU server can handle effectively.

### Run workers
```bash
pm2 start wai.config.js
```
* PM2 will read the configuration file and start the specified number of `wai run` instances. They will run in the `background`.

### Check logs
```console
pm2 logs wai-node
```
* Takes a few minutes until you get rid of `errors` and `checking for update..`

![Screenshot_772](https://github.com/user-attachments/assets/e81e16bc-40ee-4379-9217-49d4131031d4)
* Wait...

![Screenshot_773](https://github.com/user-attachments/assets/ab2faeda-9511-4ba0-be3b-b94a1ff572dd)
* Still wait...

![Screenshot_774](https://github.com/user-attachments/assets/d750aeb1-3ede-4ce4-af2b-4b51297e1809)
* BOOM! You are mining coins now!

### Useful Commands
```console
# Status of all workers
pm2 list

# Logs of all workers
pm2 logs wai-node

# Logs of a specific worker
pm2 logs 0   # To see logs for the worker with ID 0

# Stop workers
pm2 stop wai.config.js

# Restart workers
pm2 delete wai-node
pm2 restart wai.config.js

# Kill & Wipe all the workers instances
pm2 kill
```
* If you made any modification to `wai.config.js` config, you need to run *Stop and Restart workers* commands above.

### Monitor Hardwares
```console
# Monitor GPU vRAM usage
nvidia-smi

# Monitor Disk usage
du -sh /root/.wombo

# Monitor CPU and RAM (it's shared in rented GPUs)
htop
```

I'm curretly running 9 worker instances on this GPU ($0.06/hr):

![image](https://github.com/user-attachments/assets/72f8be34-3a8c-4246-9d19-6db5d36e428c)

