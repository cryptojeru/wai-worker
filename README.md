# wai-worker

# Hardware Requirements
* Any M-Series (Apple Silicon) chip:
  * Macbook/Mac Mini M1, M2, M3, M4
* NVIDIA GPUs: Compute capability of 5.0+
  * GTX 1050, RTX 2060, RTX 3070, RTX 4080, etc.

# Environment
* Recommended Nvidia [driver](https://www.nvidia.com/en-us/drivers/details/245674/) (Local PC)
* CUDA Toolkit `12.4` (only supports this version)

# Signup wai
Signup your email in [Wai Dashboard](https://w.ai/r/MOEI)

# Installation Method
* Desktop Users: (Linux, Windows, MacOS)
  * Install GUI Application .e.g `.exe`,`.dmg`,`.deb`
 
* CLI (Windows):
  * Windows: Install Ubuntu WSL on your Windows using this [guide](https://github.com/0xmoei/Install-Linux-on-Windows)

* CLI (Rent QuickPod GPU):
  * Signup on [QuickPod](https://console.quickpod.io?affiliate=f621de18-b6ac-4416-b87f-01f29f8339b5).
  * Choose `CUDA 12.4` template.
  * Buy a cheap GPU (You find some for $0.05/hr)
  * 

# Method: CLI
## Install Dependecies
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


## Install wai CLI
```console
curl -fsSL https://app.w.ai/install.sh | bash
```

## Run wai Worker
```console
# Add API key
export W_AI_API_KEY=your key here

# Run worker
wai run
```

![image](https://github.com/user-attachments/assets/467d9a24-961d-442c-a9db-105dd7eb4d6b)


## Run multiple workers
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
