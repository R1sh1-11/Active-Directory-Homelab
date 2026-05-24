# 01 - VirtualBox & Windows Server 2022 Setup

## What I Did
- Created a Windows Server 2022 VM in VirtualBox (4GB RAM, 2 CPUs, 60GB disk)
- Troubleshot ISO mounting issues caused by Windows Smart App Control blocking the file
- Installed Windows Server 2022 Standard Evaluation (Desktop Experience)
- Set Administrator password and took a clean snapshot

## Problems & Fixes
| Problem | Fix |
|---|---|
| VirtualBox couldn't access the ISO | Windows Smart App Control was blocking it — disabled SAC and manually mounted the ISO |
| VM kept rebooting into installer loop | Removed the ISO from VM storage after install completed |

## What I Learned
- How VM storage and boot order works in VirtualBox
- ISOs must be detached after OS installation or the VM loops back into setup
- Windows security features can interfere with lab environments

## Screenshots

![VM Running](../screenshots/WindowsServer2022_running.png)
![Server Desktop](../screenshots/WindowsServer2022_desktop.png) 

## Next Step
Configure Active Directory on this VM via Server Manager
