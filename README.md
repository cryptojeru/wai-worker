# wai-worker

# Windows users:
Recommended Nvidia [driver](https://www.nvidia.com/en-us/drivers/details/245674/)

# Method: CLI
### Install Dependecies
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


### Install wai CLI
```console
curl -fsSL https://app.w.ai/install.sh | bash
```

### Run wai Worker
```console
# Add API key
export W_AI_API_KEY=your key here

# Run worker
wai run
```

![image](https://github.com/user-attachments/assets/467d9a24-961d-442c-a9db-105dd7eb4d6b)


### Run multiple workers
After you ensure your worker's running good, you can try running `multiple` workers in the background to mine more coins.

Install PM2
```console
npm install -g pm2
```

Create a PM2 Configuration File
```console
nano wai.config.js
```
Paste the following configuration in it
```
module.exports = {
  apps : [{
    name: 'wai-node', // A name for your process
    script: 'wai',   // The command to run
    args: 'run',     // Arguments for the command
    instances: 6,    // Updated number of instances
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

