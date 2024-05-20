# build2024-demo
 
Azure DevBox:
Windows 11 Pro 23H2 Preview
Allowed Ports 300, 8080, 29400 Inbound

Azure DevBox Configuration:
WSL2 enabled: `wsl.exe --install --no-distribution`
AlmaLinux 9 installed from [wsl.almalinux.org](https://wsl.almalinux.org/9/)
[GitHub Desktop](https://desktop.github.com/)
[Code](https://code.visualstudio.com/) with WSL extension
Forwarded ports from Windows host to WSL in PowerShell:
``powershell
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=8080 connectaddress=172.31.72.108
netsh interface portproxy add v4tov4 listenport=29400 listenaddress=0.0.0.0 connectport=29400 connectaddress=172.31.72.108
``

AlmaLinux 9 WSL Configuration:
Enable [systemd](https://learn.microsoft.com/en-us/windows/wsl/wsl-config#systemd-support)
Installed [Docker CE](https://docs.determined.ai/latest/setup-cluster/on-prem/requirements.html#install-docker)
Install iproute and pip: `sudo dnf install python3-pip iproute -y`
Upgrade pip: `pip install pip --upgrade`
Installed [Determined AI](https://www.determined.ai/): `pip install determined`

Azure GPU Node:
Standard NV12ads A10 v5
NVIDIA GPU-Optimized VMI with vGPU driver
VMI comes pre-configured with NVIDIA drivers and Docker.
Remove outdated Python libraries managed by apt:
`sudo apt remove python3-requests python3-urllib3 python3-cryptography`
Upgrade pip:
`pip install pip --upgrade`
Installed [Determined AI](https://www.determined.ai/): `pip install determined`

Steps:

On Azure DevBox, clone repo, open in Code, use the WSL extension to connect to AlmaLinux, and in the built-in Code Terminal run:
`det deploy local cluster-up --no-gpu`

Open Determined web interface at `http://localhost:8080`.

Change to our model training directory:
`cd mnist_pytorch`

Train the model locally on CPU using Adaptive ASHA hyperparameter algorithm:
`det experiment create adaptive.yaml .`

SSH to GPU Node 1:
`ssh Hayden@40.124.169.220`

Run:
`export DET_MASTER=40.124.116.172`
`det user login determined` on first login
`det deploy local agent-up $DET_MASTER`

Look for CUDA slot at `http://localhost:8080/det/clusters`.

Repeat for GPU Node 2:
`ssh Hayden@40.124.169.220`

Disconnect from GPU Nodes:
`exit`

Run experiment on GPU Nodes:
`det experiment create distributed_adaptive.yaml . --include .`