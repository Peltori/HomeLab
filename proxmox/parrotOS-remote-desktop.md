# Proxmox Lab: Building a ParrotOS Remote Desktop Environment

## 1. Summary
I built a ParrotOS 7 virtual machine on top of the Proxmox type-1 hypervisor for Hack The Box practice in order to offload resource usage from my main workstation. The goal was to find a sufficiently smooth remote desktop solution for a Wayland-based environment without relying on unnecessary complexity. After testing multiple options, I found that SPICE was usable but limited, NoMachine was not a good fit for this setup, and the most practical solution ended up being Remmina as the client and xrdp server on the quest VM.

## 2. Goal
- Free up resources on my main machine
- Use a ParrotOS environment remotely through Proxmox
- Find a practical remote desktop solution for HTB-related work
- Document decisions, tests, and observations

## 3. Environment

### Host
- Operating system: Fedora 43
- Window manager / desktop: Hyprland
- Display protocol / display server: Wayland

### Hypervisor
- Proxmox VE: 9.1.7 (Latest)
- Network: LAN
- Storage: 500GB NVME Drive

### Guest
- Distribution: ParrotOS 7
- Desktop environment: KDE Plasma
- Display protocol: Wayland
- VM resources:
  - vCPU: 4
  - RAM: 8 GB
  - Disk: 50 GB
  - Display: VirtIO-GPU + SPICE

## 4. Starting Point
The original goal was to use a ParrotOS virtual machine running on Proxmox for daily HTB-related work. Proxmox's built-in web console proved too laggy for continuous desktop use, so a better solution was needed.

## 5. Problem Statement
Which remote desktop solution works well enough in a Linux host -> Proxmox -> Linux guest setup when both the host and guest are using Wayland?

## 6. Evaluated Options

### Remmina / RDP
**Pros**
- Common and well known
- Easy to use as a client
- Worked great for the intended use case
- Provided better overall remote desktop experience than the noVNC console that proxmox provides

**Cons**
- Clipboard only works from host to quest but not the other way around
- Requires additional setup on the quest VM
- The Remmina client also requires additional setup

**Conclusion**
- Best overall solution for this purpose and use case

### NoMachine
**Pros**
- Appeared to provide a better desktop experience than basic console-based solutions
- Supports Linux client + Linux host usage

**Cons**
- Requires more setup than the Remmina and xrdp
- Additional setup required on the quest VM
- Additional setup required on the client machine
- Introduces another separate stack to manage
- Text and windows in-motion get pixelated due to the packing of the video when transferred between the quest and the client

**Conclusion**
- Tested but no suitable for this environment

### KDE Remote Desktop
**Pros**
- A more native KDE/Wayland-oriented approach

**Cons**
- Still appeared somewhat uncertain for practical daily use

**Conclusion**
- Not the first solution to test

### Sunshine / Moonlight
**Pros**
- Potentially smooth user experience

**Cons**
- Too much setup overhead for this need
- Overkill for an HTB lab at this stage
- Adds more moving parts, which reduces maintainability

**Conclusion**
- Not the first option to implement

### SPICE
**Pros**
- Available directly through Proxmox with the provided noVNC console
- A simple way to improve the experience compared to the web console
- Good enough for basic desktop access and administration

**Cons**
- Clipboard functionality works but is clunky due to the noVNC clipboard manager layer
- Still felt limited compared to a proper remote desktop solution

**Conclusion**
- Useful as an initial option, but not the final solution

## 7. Final Decision
Although SPICE was the first solution tested, it did not provide a complete enough desktop experience for regular use. NoMachine was also tested, but it was not a good fit for this Wayland-based setup. The final solution was Remmina with xrdp, as it provided the most practical balance between usability, stability, and overall desktop experience for HTB-related work.

## 8. Implementation

### Proxmox Side
- The VM was created in Proxmox
- SPICE was enabled for initial testing
- The built-in noVNC console were evaluated first

### Guest Side
- ParrotOS 7 installed (HTB-SPIN)
- MATE in use
- X11 in use
- Additional packages tested and used:
  - `spice-vdagent`
  - `qemu-guest-agent`
  - `xrdp`

### Client Side
- Remmina was used as the remote desktop client
- Wayland in use with Hyprland
- No additional packages needed other than the Remmina client

## 9. Testing

### Items to Test
- Overall desktop responsiveness
- Window movement and interaction
- Input latency
- Clipboard host -> guest
- Clipboard guest -> host
- Resolution handling and scaling
- Usability with HTB tools

### Test Results
| Test                         | Result                                                                                                                 | Notes                                                                                                                                                             |
| ---------------------------- | ---------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Proxmox web console          | Works but the experience is limited                                                                                    | Usable for administration, but too laggy for everday desktop use                                                                                                  |
| SPICE desktop responsiveness | Acceptable                                                                                                             | Better than the default noVNC console configuration that proxmox uses                                                                                             |
| Clipboard host -> guest      | Works after additional configuration (Check Notes for more information)                                                | Few options need to be enabled and spice-vdagent package is required on the quest VM                                                                              |
| Clipboard guest -> host      | Works but needs additional configuration for enabling the VNC mode for the cliboard (Check Notes for more information) | It works when enabling the VNC clipboard "mode" in the proxmox quest settings but is not perfect                                                                  |
| NoMachine                    | Not suitable                                                                                                           | Tested, but proved not to be a good solution due to the pixelating of the windows and text when in-motion                                                         |
| Remmina + xrdp               | Good                                                                                                                   | Best overall experience out of the tested options. Requires some setup, mainly on the client side. Quest side just required the xrdp server to be up and running. |
| Overall usability            | Good                                                                                                                   | Remmina with the xrdp was the most practical solution for daily HTB-related use                                                                                   |

## 10. Observations
- Proxmox's built-in console is suitable for administration, but not comfortable for regular desktop use
- SPICE was useful but not the best solution for long-term desktop use
- NoMachine didn't work well enough due to the pixelating text and windows when in-motion
- Remmina + xrdp proved to be the best overall experience in daily desktop and HTB-related use cases
- Clipboard integration is not always reliable in Wayland-based environments, mainly affecting the quest to host direction in this case
- A perfect solution may not be necessary when the use case is limited to HTB practice

## 11. Risks / Limitations
- Clipboard functionality may be unreliable depending on the desktop session and the client that are used
- Wayland-based combinations can behave differently depending on the compositor and remote desktop stack
- The solution may not be suitable for heavy multimedia use or long GUI-based working sessions
- Additional quest-side services such as xrdp can increase setup complexity compared to the provided noVNC console of proxmox

## 12. Future Improvements
- Continue testing Remmina + xrpd in daily desktop and HTB-related use
- Expand the lab to additional VMs if needed

## 13. What I Learned
- A hypervisor's built-in management console is not the same as a proper remote desktop solution
- Wayland has practical implications for remote desktop implementation
- When choosing a solution, the intended use case matters more than finding the technically "best" option
- Wayland has practical implications for remote desktop implementation
- Documentation supports both learning and presenting technical skills

## 14. Portfolio Value
This work demonstrates practical skills in:
- virtualization environment management
- Linux system usage
- evaluating remote access solutions
- technical problem solving
- documentation

## 15. Summary for Employers
I built a ParrotOS-based practice environment on Proxmox for ethical hacking and cybersecurity training, while also evaluating different remote access options in a Wayland-based Linux environment. During this process, I tested the proxmox provided noVNC console, SPICE, NoMachine, and finally Remmina with xrdp. The solution was selected based on practical usability rather than theoretical feature comparisons, resulting in a setup that was better suited for daily HTB-related work.