# build2024-demo
 
Workstation Azure VM:
Standard_D8s_v3 VM
Standard security type
Windows 11 Pro 23H2 Preview
Allow Port 8080 Inbound

Workstation Azure VM Configuration:
WSL2 enabled: `wsl.exe --install --no-distribution`
AlmaLinux 9 installed from [wsl.almalinux.org](https://wsl.almalinux.org/9/)
[GitHub Desktop](https://desktop.github.com/)
[Code](https://code.visualstudio.com/) with WSL extension

AlmaLinux 9 WSL Configuration:
Enable [systemd](https://learn.microsoft.com/en-us/windows/wsl/wsl-config#systemd-support)
Installed [Docker CE](https://docs.determined.ai/latest/setup-cluster/on-prem/requirements.html#install-docker)
Install iproute and pip: `sudo dnf install python3-pip iproute -y`
Upgrade pip: `pip install pip --upgrade`
Installed [Determined](https://www.determined.ai/): `pip install determined`
