# ![GitHub Downloads (all assets, all releases)](https://img.shields.io/github/downloads/minios-linux/minios-live/total?style=for-the-badge&logoSize=30&label=%20TOTAL%20DOWNLOADS&labelColor=white&color=orange)

<img width="1280" height="800" alt="MiniOS Standard" src="images/minios.png" />

MiniOS is a reliable and user-friendly portable system with a graphical interface. These scripts build a bootable MiniOS ISO image.

## 🌐 Resources

Learn more about using and building MiniOS:

### 🖥️ Official Website

The [official website](https://minios.dev) is your central hub for information about MiniOS.  Find details on the different editions available, their respective features, community forums for support, and direct download links for the ISO images.

### 📚 Official Wiki

The [official Wiki](https://github.com/minios-linux/minios-live/wiki) provides in-depth knowledge and practical guidance for working with MiniOS. Explore comprehensive guides covering installation procedures, system configuration, customization options, and how to extend functionality with modules.

### 🚀 Quick Start Guide

New to MiniOS? Start with our comprehensive [Quick Start Guide](https://github.com/minios-linux/minios-live/wiki/Quick-Start) that covers everything from choosing the right edition to setting up security and customizing your system. Perfect for beginners and experienced users alike.

**Note:**

* For information on building MiniOS and modifying modules, read the [Building MiniOS Guide](https://github.com/minios-linux/minios-live/wiki/Building-MiniOS).

## ✍️ Authors

MiniOS was created by:
- [crims0n](https://github.com/crim50n) - the original author and maintainer of MiniOS
- [.nemesis](https://github.com/zukhovich) - designer and developer of the MiniOS graphical interface
- [FershoUno](https://github.com/fershouno) - tester and contributor to the MiniOS project
- [sfs-pra](https://github.com/sfs-pra) - developer of the PuppyRus Linux and contributor to the MiniOS project
- [betcher](https://github.com/betcher) - developer of the ROSA Barium and contributor to the MiniOS project
- [gumanzoy](https://github.com/gumanzoy) - developer of the PocketHandyBox and contributor to the MiniOS project
- [xDoofy92](https://github.com/xDoofy92) - media support for the MiniOS project

## 🔀 About This Fork

This fork builds MiniOS on Debian 14 (forky) with KDE Plasma. Target: Standard edition, amd64, run from USB.

As of the 2026-09-29 build:

| Component | Version |
|---|---|
| Base | Debian 14 (forky) |
| Kernel | 7.2 |
| Desktop | KDE Plasma, X11 session |
| Graphics | Mesa 26.1 |
| Browser | Falkon 26.08 |
| ISO size | 1.3 GB |

Kernel and package versions follow forky, so later builds may be newer.

Changes from upstream MiniOS:

- **Browser.** Falkon replaces Firefox. Its built-in PDF viewer is on by default. The other desktops still use Firefox.
- **NVIDIA.** On NVIDIA Turing (RTX 20 series) and newer, OpenGL runs on Zink over NVK, the open-source Vulkan driver. This works with the open-source nouveau driver only, not the proprietary one. The image includes NVIDIA's GSP firmware (version 570.144).
- **Apx.** Vanilla OS's container package manager. It installs packages from other distributions (Fedora, Arch, Alpine and others) into containers, keeping them off the base system.
- **MiniOS tools.** Everything except `minios-session-manager`, which depends on `dynfilefs`, and that needs `libfuse2`, which forky no longer ships.

Modules in the ISO:

| Module | Contents |
|---|---|
| `00-core` | Debian base system |
| `01-kernel` | Kernel and modules |
| `02-firmware` | Device firmware |
| `03-gui-base` | Xorg and shared desktop components |
| `04-plasma-desktop` | KDE Plasma and MiniOS tools |
| `05-mesa` | Mesa Vulkan drivers and NVIDIA GSP firmware |
| `06-apx` | Apx |
| `07-falkon` | Falkon |

To skip a module at boot, add `noload=` with its name to the boot options, for example `noload=falkon`.

To check Zink on an NVIDIA card:

```
sudo apt install mesa-utils
glxinfo -B | grep -i renderer    # should show: zink ... (NVK ...)
```

Upstream MiniOS remains the reference project; this fork tracks it.

## 🪟 Building on Windows with Debian WSL

You do not need a Linux machine to build this. Windows can run Debian inside itself (WSL), and the repository ships a helper script that does the rest.

**1. Install Debian from the Microsoft Store**

Open the Microsoft Store, search for **Debian**, and install it. Launch it once from the Start menu; it will ask you to pick a username and password. You can close it afterwards.

If launching it complains that WSL is not enabled, open PowerShell as Administrator, run `wsl --install`, reboot, and try again.

**2. Get this repository onto your PC**

Either use GitHub Desktop / `git clone`, or click **Code → Download ZIP** on GitHub and unzip it. Any folder is fine, for example `C:\Users\you\Documents\GitHub\minios-live`.

**3. Prepare Debian (once)**

Open PowerShell or Windows Terminal and run the command below, replacing the path with wherever you put the repository. Note that Windows paths are written as `/mnt/c/...` here:

```powershell
wsl -d Debian -u root -- bash /mnt/c/Users/you/Documents/GitHub/minios-live/tools/wsl-build.sh setup
```

This installs the build tools inside Debian. You only need to do it the first time.

**4. Build the ISO**

```powershell
wsl -d Debian -u root -- bash /mnt/c/Users/you/Documents/GitHub/minios-live/tools/wsl-build.sh build -
```

This takes a while (30 minutes to a couple of hours depending on your PC and internet connection) and needs around 20 GB of free space on your Windows drive. Leave the window open until it says the image has been created.

**5. Copy the ISO to Windows**

```powershell
wsl -d Debian -u root -- bash /mnt/c/Users/you/Documents/GitHub/minios-live/tools/wsl-build.sh fetch-iso
```

The finished `.iso` appears in a `build-output` folder inside the repository. Write it to a USB stick with [Rufus](https://rufus.ie) or [Ventoy](https://www.ventoy.net), or boot it in a virtual machine.

**Good to know**

- Building again after a change is faster: parts that have not changed are reused.
- The build can stop on an error without reporting a failure. Make sure the last lines say `The image ... has been created`.
- `wsl-build.sh clean` removes the whole build area inside Debian if you want to start fresh.
- Do not run the build from inside a Windows folder in Debian directly; the helper script takes care of copying the files to where Debian can build them.
