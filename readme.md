# Retro ISP Project

### Building on the retro dial up setup for my classic computers, I've now embarked on a mission to employ mid-90's ISP grade equipment for enterprise level POTS, PPP and Dial-Up to the modern internet. Will we be able tog et on the internet? Probably. Will it be usable? Who knows?

#### Differences between the old Dial-Up setup: 
  - The SPA2102 was a VOIP ATA that has very finicky operation (the modems are not happy with the digital compression
  - Only 2 ports total (without adding more ATAs and cross-configuring them together)
  - Dial through was achieved by querying another modem and connecting a modem-modem link. In order to connect to the internet, I would have had to setup a linux server as a NAT + PPP authenticator
  - Even with that setup, I'd only be able to do one dial-up client at a time and switch cables everytime

#### Benefits of the Cisco 3725 System 
  - Enterprise grade gear - we have a proper Cisco console port
  - 2X Fast Ethernet 10/100 that can NAT incoming PPP connections
  - NM Ports - the 3725 has two NM ports (more on this below) and 3 WIC ports
  - Multiple NM Module configurations (few relevant ones below)
  -   -NM-HDV: digital VOIP network module (this does not work with POTS fxs cards unfortunately and requires discreet DSP modules) 
  -   -NM-HD-2V: 2 FXS/FXO WIC Ports with built-in DSPs
  -   -NM-4A/S: four serial ports
  -   -NM-2FE-2W: 2 additional WIC ports and 2 F/E ports
  -   -WIC-T1-DSU/CSU: CSU/DSU Card that allows for T1 link aggregation (to a telco CO)
  -   -WIC-1AM-V2: WIC analog modem that can be dialed into through the FXS port
  -   -VIC4-FXS: 4-port analog field exchange station (provides dialtone/line voltage)

The idea is that I'll setup the 3725 with some dial-through ports and a modem bank to mimic a small local ISP with POTS support. Multiple computers would then be able to dial into the 3725 through configured numbers and then handed off to an analog modem (either external, or internal WIC). The 3725 would then assign an IP and NAT it to one of the Fast Ethernet ports so that dialed-in computers would be assigned an internal IP (192) but access the internet and the LAN (10). 
