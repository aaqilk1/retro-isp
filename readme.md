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
      - NM-HDV: digital VOIP network module (this does not work with POTS fxs cards unfortunately and requires discreet DSP PVDM modules) 
      - NM-HD-2V: 2 FXS/FXO WIC Ports with built-in DSPs
      - NM-4A/S: four serial ports
      - NM-2FE-2W: 2 additional WIC ports and 2 F/E ports
      - WIC-T1-DSU/CSU: CSU/DSU Card that allows for T1 link aggregation (to a telco CO)
      - WIC-1AM-V2: WIC analog modem that can be dialed into through the FXS port
      - VIC2-FXS: 2-port analog field exchange station (provides dialtone/line voltage to 2 ports)
      - VIC4-FXS: 4-port analog field exchange station (provides dialtone/line voltage to 4 ports)

The idea is that I'll setup the 3725 with some dial-through ports and a modem bank to mimic a small local ISP with POTS support. Multiple computers would then be able to dial into the 3725 through configured numbers and then handed off to an analog modem (either external, or internal WIC). The 3725 would then assign an IP and NAT it to one of the Fast Ethernet ports so that dialed-in computers would be assigned an internal IP (192) but access the internet and the LAN (10). If this works reliably, I may consider adding in another Cisco router (1700, 2600-potentially) to act as the "telco central office". In this config, the upstream router will be connected to modern-day Gigabit LAN via its 10/100 FE port and then transmit LAN packets over T1 interface between two CSU/DSU cards to the downstream router. The 3725 will then provide POTS dialtone and a modem bank and handle PPP and auth/NAT routing to bring the vintage computers online to the shared T1 LAN network. 

### Hardware
Cisco 3725 (purchased off eBay). This is the brains of the entire operation – an enterprise grade Cisco router with lots of flexibility and modularity,

![IMG_2153](https://github.com/user-attachments/assets/2aeb13c7-9029-4531-9e04-83fb3192aba5)
The front panel is probably my favorite front panel next to the 486dx2 minitower front panel.

![IMG_2148](https://github.com/user-attachments/assets/08e56032-d946-453b-85d4-1121e2713bc3)
Rear of the device with the expansion ports, console cable and NM visible. The NM-HDV is atop the router and I've got the NM-HD-2V on the top left (slot 2) port with a VIC-4FXS and VIC-2FXS for through-dial testing. The black cable was hooked up to a USB modem and the white to a telephone. 

![IMG_2150](https://github.com/user-attachments/assets/e132de8b-9b08-415c-baad-84d12ead1c12)
Under the hood, Note the space on the top left for a redundant power supply, the RAM slots, the Cisco internal CF card and expansion headers on the motherboard. This one has 2 NM slots with the top one accomodating a double-wide card and the bottom one accomodating a regular card. 

![IMG_2152](https://github.com/user-attachments/assets/a14d87af-b40e-4862-9d0a-affe27407741)
Detail showing the two FXS cards (same footprint but electrically incompatible with WIC slots/ports) in the NM-HD-2V slots

![DSCF5455](https://github.com/user-attachments/assets/f87e068e-f573-46b3-b757-574ccfe3ef92)
Setup at my desk with the Latitude acting as client and Macbook Pro for console + logging (this photo was an excuse to use the XT1)


### Okay, time for business - Console Configuration
The 3725 uses a standard Cisco console cable. For Windows, I'm using PuTTy and Mac I'm using SerialTools. Settings are 9600/8/1 and it works reliably through a USB-Serial adapter. It is a little limiting to have to sit in front of the loud router during config, so I may throw an RPi Zero with a USB OTG port and raspbian on the network to act as a remote console that I can conveniently ssh into. (Totally going on a tangent here, but I could also use GPIO to remotely turn the unit on/off). 

Instead of pasting the Config directly here (and making this readme a full 100,000 words), I'll make another md file that has each block config spelled out and some codeblocks that can be used to paste the commands into the serial session. 

### The plan 
Host POTS lines on the router, use them to dial into the modems, get on the 'net. 
Possible configurations: 
  1. Employ a single NM-HD-2V with 2x VIC-4FXS cards and a WIC-1AM-V2 (currently what I have right now). Under this config, I will have 8 POTS ports total, 4 complete lines and will be able to host up to 2 simultaneous dial-in clients. I could theoretically get 2x WIC-2AM-V1/V2 cards for 4 total modems, but those are few and far in between on eBay. 1 WIC-1AM allows me to run 1 dial up client at a time.
  2. Employ 2x NM-HD-2V cards with either 4x VIC-4FXS (16 ports) cards or 2x VIC-4FXS and 2x VIC-2FXS (10 ports) cards. This would give me a LOT of dial in options but then leads to the issue of modems and who am I trying to dial through to? It would be a cool concept if we were running landline phones to every room AND providing dialup access, but it's a bit far fetched and more of a proof-of-concept than anything (albeit, tempting).
  3. Employ 1 NM-HD-2V with 2x VIC-4FXS cards and an NM-4AS card. This would give me the capability to run 4 through-dial connections simultaneously to 4 external AT modems (similar to how a modem bank may have been in the mid-90's, but preliminary ebay research shows the 60-pin serial to RS-232 cables being cost prohibitive (I will add update it here with the Cisco cable number), even though the actual network module itself isn't too expensive. This would be just plain cool, but would require 4 external modems (I really like the PM14400FXMT design) and external power. We also may run into issues with modem configuration, so I'll probably go with the next option.
  4. Employ 1 NM-HD-2V with 2x VIC-4FXS cards and a NM-2FE-2W card. The NM-2FE-2W adds 2 more 10/100 FastEthernet ports and 2 more WIC slots. I'd then be able to run a total of 4x WIC-1AM-V2 cards, 1 WIC-T1-DSU/CSU card and have two extra 10/100 ports for internal DNS redirects (foreshadowing!!). This seems like the most viable option as the WIC-1AM cards allow for full control through the Cisco OS (knowing how finicky the config can be, I'm also foreshadowing AT config issues with external modems).
  5. Employ 1 NM-HD-2V with 2x VIC-4FXS cards and use 1 or 2 of the WIC-2A/S cards. These are similar to the NM-4A/S but only occupy one of the WIC slots and provide 2 serial ports with the micro connector (I will update it here with the Cisco cable number). This is probably the most flexible option, if used in conjunction with an NM-2FE-2W card, as I can have both WIC and external analog modems for lab testing. I'll also have 2 additional FE ports. This is what I'll probably go with.

### So what happened?
Well, I initally went down a very misinformed ChatGPT rabbit hole to find a suitable NM card that would support FXS (darn you, GPT5!) After purchasing 2x NM-HDV cards, 2x VIC-2FXS cards, and finding 2x rare and elusive PVDM slot modules, I realized that this simply was not going to work and I'd need the only supported NM in the Cisco documentation, the NM-HD-2V. Thankfully the NM-HDVs were had for next-to-nothing and I plan on throwing them back up on eBay sometime soon. 

With the NM-HD-2V, the voice ports came to life and I was able to get cracking at assigning phone numbers and dialplans to the analog ports. This was pretty seamless, however I was having trouble saving my configuration and would have to reconfigure everything after rebooting. 

Once I had confirmed that the POTS lines were functional and the modems were happy dialing and negotiating through them, I hunted down a WIC-1AM-V2 analog modem card. This fit perfectly and was recognized with no issues (a special thank you to clabretro for all of the trial and error getting his 2600/3725 configured). Once setup, I was able to setup a username, password, internal IP range, internal FE port, external FE port and dialplan and then have the modem accept incoming calls and authenticate them over PPP before putting them on the internal IP range and connecting them to the internet via the NAT. This was setup using the Dell Latitude (running Windows Vista) as the client computer and it was quite seamless and smooth in getting on the internet. I was able to ping my local network (10.0.0.0/24) and the internet (8.8.8.8) and the Google homepage successfully loaded on Supermium after a ton of time. 

### And then...?
Having reached my initial goal of dialing up to the internet with the help of a Cisco 3725, I then set out to connect the Windows 98SE PIII tower I built 2 weeks ago on the net. This proved a little difficult, as I had installed the ISA modem card from the 6x86 computer (currently dead with a unresponseive original PSU) and plugged in the PM14400FXMT via RS-232 cable to one of the serial ports. Although the line was live and the cableing was correct, I kept receiving error 630 and error 666 (ooop) when trying to use the default Windows 98 dialup client. After some digging, it turns out that:
  - Windows 98 does not have plug-and-play modem support (although technically possible, it's not very seamless)
  - For whatever reason (hardware, BIOS, OS, I haven't figured it out yet) the ISA 56k modem was also being assigned COM2 as the serial port, same as the PM14400FXMT. This meant that whenever I'd attempt to dial up, whether through the AOL 5.0 or default Windows app, it would cause the serial bus to crash. Removing the PM14400FXMT, a restart and re-adding the ISA modem solved this. I could finally dial in to the 3725 with my PPP credentials!

### The World Wide Web
Once dialed in, I was assigned an IP by the 3725 and officially connected to the network. But it was slow. Not retro nostalgic slow, but borderline unusable slow. I was able to successfully ping my DNS server (10.0.0.10) with most packets being returned, but it timed out before I could receive a response from Google (8.8.8.8). When trying to visit google.com or any other old, low bandwith sites, Explorer acknowledged that the site was connected, but failed to display the page. 

I'll need to dig deeper into what is causing this and maybe try to drop iperf3 to get some real life numbers on the throughput of this setup. I'll also need to do more reading into what might be causing such a bottleneck, and whether adding yet another layer (whether it be external AT modems through the AS card or a "telco co" with another router) will introduce even more latency. If so, I may try using a RPi zero/Atomic Pi as a DNS on the internal network to host some old websites compatible with Windows 98SE's version of Internet Explorer for shits and giggles. 
