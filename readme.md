# Retro ISP Project

### Building on the retro dial up setup for my classic computers, I've now embarked on a mission to employ mid-90's ISP grade equipment for enterprise level POTS, PPP, and Dial-Up to the modern internet. Will we be able to get on the internet? Probably. Will it be usable? Who knows?

#### Differences between the old Dial-Up setup: 
  - The SPA2102 was a VOIP ATA that has very finicky operation (the modems are not happy with the digital compression
  - Only 2 ports total (without adding more ATAs and cross-configuring them together)
  - Dial through was achieved by querying another modem and connecting a modem-modem link. In order to connect to the internet I would have had to setup a linux server as a NAT + PPP authenticator
  - Even with that setup, I'd only be able to do one dial-up client at a time and switch cables everytime

#### Benefits of the Cisco 3725 System 
  - Enterprise grade gear - we have a proper Cisco console port
  - 2X Fast Ethernet 10/100 that can NAT incoming PPP connections
  - NM Ports - the 3725 has two NM ports (more on this below) and 3 WIC ports
  - Multiple NM Module configurations (few relevant ones below)
      - NM-HDV: digital VOIP network module (this does not work with POTS fxs cards unfortunately and requires discreet DSP modules) 
      - NM-HD-2V: 2 FXS/FXO WIC Ports with built-in DSPs
      - NM-4A/S: four serial ports
      - NM-2FE-2W: 2 additional WIC ports and 2 F/E ports
      - WIC-T1-DSU/CSU: CSU/DSU Card that allows for T1 link aggregation (to a telco CO)
      - WIC-1AM-V2: WIC analog modem that can be dialed into through the FXS port
      - VIC2-FXS: 2-port analog field exchange station (provides dialtone/line voltage to 2 ports)
      - VIC4-FXS: 4-port analog field exchange station (provides dialtone/line voltage to 4 ports)

The idea is that I'll setup the 3725 with some dial-through ports and a modem bank to mimic a small local ISP with POTS support. Multiple computers would then be able to dial into the 3725 through configured numbers and then handed off to an analog modem (either external, or internal WIC). The 3725 would then assign an IP and NAT it to one of the Fast Ethernet ports so that dialed-in computers would be assigned an internal IP (192) but access the internet and the LAN (10). 

### Hardware
Cisco 3725 (purchased off eBay). This is the brains of the entire operation – an enterprise grade Cisco router with lots of flexibility and modularity,

![IMG_2153](https://github.com/user-attachments/assets/2aeb13c7-9029-4531-9e04-83fb3192aba5)
The front panel is probably my favorite front panel next to the 486dx2 minitower front panel.

![IMG_2148](https://github.com/user-attachments/assets/08e56032-d946-453b-85d4-1121e2713bc3)
Rear of the device with the expansion ports, console cable and NM visible. The NM-HDV is atop the router and I've got the NM-HD-2V on the top left (slot 2) port with a VIC-4FXS and VIC-2FXS for through-dial testing. The black cable wsa hooked up to a USB modem and the white to a telephone. 

![IMG_2150](https://github.com/user-attachments/assets/e132de8b-9b08-415c-baad-84d12ead1c12)
Under the hood, Note the space on the top left for a redundant power supply, the RAM slots, the Cisco internal CF card and expansion headers on the motherboard. This one has 2 NM slots with the top one accomodating a double-wide card and the bottom one accomodating a regular card. 

![IMG_2152](https://github.com/user-attachments/assets/a14d87af-b40e-4862-9d0a-affe27407741)
Detail showing the two FXS cards (same footprint but electrically incompatible with WIC slots/ports) in the NM-HD-2V slots


