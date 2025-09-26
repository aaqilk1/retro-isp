## Tangent - Resurrecting the Cisco 2611 

After successfully getting the Cisco 3725 to act as a POTS FXS and ISP with PPP/CHAP, I had this bright idea of employing a Cisco 2600 with one of the DSU/CSU cards as an 
upstream Telco station providing my "small town ISP" with a T1 line for voice/data. A 2611 popped up on offer up for very cheap (with the disclaimer "AS-IS") and I picked 
it up for a few dollars. 

After opening the case and applying power, it dawned on me what I had gotten myself into. This 2600 was a 2611, with a 32MB flash storage card (no internal/external CF card
like the 3725) and proprietary small DIMM RAM sticks (unlike the DDR sticks on the 3725). It was also stuck at the dreaded ROMMON prompt. After setting up a TFTP server
on debian on my ESXi box, I proceeded to search the interwebs for a compatible image with voice package. 

I found a number of images and transferred them over to the 2611 over TFTP and FastEthernet, saved them to the flash (worked successfully every time), but when I would go to 
boot them, it would always get stuck right before "self-decompressing image" and fall back to the ROMMON prompt. 

After trying about 10 compatible images that had successfully been copied to the flash with correct checksums, I was ready to recycle this. Further changing the boot behavior
to force boot the images, it still failed. 

I decided to crack open the case and see if there was any hardware issues that could be causing it to continously fail to boot. One clue was the abnormal amount of corrosion on
the rear of the router. I checked the flash stick's pins and the RAM stick's pins and found one stick with corrosion on half of a single pin. The rest of the hardware looked good. 
After removing the corroded stick (and bringing my effective memory to 64MB) and plugging everything in again, we had success! The image began self-decompressing and the router 
booted into iOS. Hooray!

After getting it to boot into iOS, I realized my second issue. This particular 2600 router was a 2611, one of the earlier models. This meant that it had the lowest flash capacity 
and supported only the older iOS builds. It also meant that I couldn't use any of the newer NM-HD-2V or VIC2-FXS/VIC-4FXS cards, as they were "technically" second generation. 
The 2611 would only support the VIC-2FXS cards and the NM-VOICE-2V modules (1st gen modules). 

Since I had also picked up an NM-VOICE-2V module with the 2611 and it did show up on the 2611's iOS, I tried it with the 3725, hoping that it would support my VIC2-2FXS cards, but
it looks like we're dealing with a hardware limitation. I may have to pick up a VIC-2FXS to run a couple of voice ports over my T1 line to the upstream Telco. 


## Anyways, here are some of the ROMMON commands for upgrading the software and configuring the TFTP server

At the ROMMON prompt, type this 

#conf T 
