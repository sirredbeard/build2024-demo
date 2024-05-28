# build2024-demo

We will be training a model on a dataset of handwriting samples, to build a model for handwriting detection, OCR, using PyTorch.

This is a relatively small dataset so we can fit training into the length of this demo.

We will train on the Azure Dev Box on just a CPU and review the results.

We will then connect our Azure GPU VMs to our Azure Dev Box and re-run the training with distributed training enabled, then compare the results.

Using distributed training, we could scale this training to 5, 100, or 1,000 GPUs as needed in Azure, maximizing our performance across the cloud.

Below is the configuration I used. You will need to modify usernames and IP addresses for your specific environment.

See the [mnist_pytorch demo README.md](https://github.com/sirredbeard/build2024-demo/blob/main/mnist_pytorch/README.md) for information on the training script and dataset.
 
## Azure DevBox:

Windows 11 Pro 23H2 Preview

Allowed Ports 300, 8080, 29400 Inbound

## Azure DevBox Configuration:

WSL2 enabled: 

`wsl.exe --install --no-distribution`

AlmaLinux 9 installed from [wsl.almalinux.org](https://wsl.almalinux.org/9/)

[GitHub Desktop](https://desktop.github.com/)

[Code](https://code.visualstudio.com/) with WSL extension

Forwarded ports from Windows host to WSL in PowerShell:
```powershell
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=8080 connectaddress=172.31.72.108
netsh interface portproxy add v4tov4 listenport=29400 listenaddress=0.0.0.0 connectport=29400 connectaddress=172.31.72.108
```

## AlmaLinux 9 WSL Configuration:
Enable [systemd](https://learn.microsoft.com/en-us/windows/wsl/wsl-config#systemd-support)

Installed [Docker CE](https://docs.determined.ai/latest/setup-cluster/on-prem/requirements.html#install-docker)

Install iproute and pip: 

`sudo dnf install python3-pip iproute -y`

Upgrade pip: 

`pip install pip --upgrade`

Installed [Determined AI](https://www.determined.ai/): 

`pip install determined`

## Azure GPU Node:

Standard NV12ads A10 v5

NVIDIA GPU-Optimized VMI with vGPU driver

VMI comes pre-configured with NVIDIA drivers, Docker, and NVIDIA Container Runtime.

Remove outdated Python libraries managed by apt:

`sudo apt remove python3-requests python3-urllib3 python3-cryptography`

Upgrade pip:

`pip install pip --upgrade`

Installed [Determined AI](https://www.determined.ai/): 

`pip install determined`

# Demo Steps:

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

```bash
export DET_MASTER=40.124.116.172
det user login determined # on first login
det deploy local agent-up $DET_MASTER
```

Look for CUDA slot at `http://localhost:8080/det/clusters`.

Repeat for GPU Node 2:
`ssh Hayden@40.124.118.74`

Disconnect from GPU Nodes:

`exit`

Run experiment on GPU Nodes:

`det experiment create distributed_adaptive.yaml . --include .`