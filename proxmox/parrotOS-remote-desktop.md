# Proxmox Lab: Building a ParrotOS Remote Desktop Environment

## 1. Summary
I built a ParrotOS 7 virtual machine on top of the Proxmox type-1 hypervisor for Hack The Box practice in order to offload resource usage from my main workstation. The goal was to find a sufficiently smooth remote desktop solution for a Wayland-based environment without relying on unnecessary third-party applications.

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
- Proxmox VE:
- Network:
- Storage:

### Guest
- Distribution: ParrotOS 7
- Desktop environment: KDE Plasma
- Display protocol: Wayland
- VM resources:
  - vCPU: 4
  - RAM: 8 GB
  - Disk: 100 GB
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

**Cons**
- Wayland + KDE + Linux guest is not necessarily the best combination
- Did not appear to be the best fit for this use case

**Conclusion**
- Not the first choice for this purpose

### NoMachine
**Pros**
- Appears to provide a better desktop experience than basic console-based solutions
- Supports Linux client + Linux host usage

**Cons**
- Wayland/KDE support did not appear completely trouble-free
- Introduces another separate stack to manage

**Conclusion**
- A possible fallback option

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
- Available directly through Proxmox
- A simple way to improve the experience compared to the web console
- Likely good enough for desktop use

**Cons**
- Clipboard functionality may not work reliably in a Wayland/KDE/Hyprland combination

**Conclusion**
- The best initial solution for this use case

## 7. Final Decision
I chose SPICE as the first implementation because it is simple, directly supported by Proxmox, and likely sufficient for HTB-related use without turning the environment into a more complex project of its own.

## 8. Implementation

### Proxmox Side
- The VM was created in Proxmox
- SPICE was enabled
- virt-viewer / remote-viewer was planned as the client

### Guest Side
- ParrotOS 7 installed
- KDE Plasma in use
- Wayland in use
- Possible additional packages:
  - `spice-vdagent`
  - `qemu-guest-agent`

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
| Test                         | Result | Notes |
| ---------------------------- | ------ | ----- |
| Proxmox web console          |        |       |
| SPICE desktop responsiveness |        |       |
| Clipboard host -> guest      |        |       |
| Clipboard guest -> host      |        |       |
| Overall usability            |        |       |

## 10. Observations
- Proxmox's built-in console is suitable for administration, but not comfortable for regular desktop use
- SPICE appeared to be the most reasonable compromise between usability and setup effort
- Clipboard integration is not always reliable in Wayland-based environments
- A perfect solution may not be necessary when the use case is limited to HTB practice

## 11. Risks / Limitations
- Clipboard functionality may be unreliable
- Wayland-based combinations can behave differently depending on the compositor
- The solution may not be suitable for heavy multimedia use or long GUI-based working sessions

## 12. Future Improvements
- Test NoMachine if SPICE is not sufficient
- Compare SPICE and NoMachine based on actual user experience
- Document the final HTB workflow
- Expand the lab to additional VMs if needed

## 13. What I Learned
- A hypervisor's built-in management console is not the same as a proper remote desktop solution
- Wayland has practical implications for remote desktop implementation
- When choosing a solution, the intended use case matters more than finding the technically "best" option
- Documentation supports both learning and presenting technical skills

## 14. Portfolio Value
This work demonstrates practical skills in:
- virtualization environment management
- Linux system usage
- evaluating remote access solutions
- technical problem solving
- documentation

## 15. Summary for Employers
I built a ParrotOS-based practice environment on Proxmox for ethical hacking and cybersecurity training, while also evaluating different remote access options in a Wayland-based Linux environment. I selected SPICE as the first implementation because it provided the best compromise between simplicity, usability, and the intended use case.