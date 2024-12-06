# Notes for trying to fix my keyboard 

**Link for the manual is below:**
[Link for the manual](https://www.manualslib.com/manual/3387033/Cidoo-Abm066.html#resources)

Keyboard model : CIDOO ABM066

So I got this new ergonomic keyboard and I really like it, however the Win / Fn key is not working. I can see LED's shining through it. However I NEED the windows / Fn key to work in order to change the LED's and access basic linux and windows functions. 


# What i've tried so far 

What i've tried so far is using a voltmeter to check the current to make sure the PCB is unaffected. However I feature of this board is that it has foam inside it, therefore blocking the terminal connections on the PCB to read current. So in order to measure this a little bit more accurately , I will need to take off the whole keyboard and the foam in order to 
expose the PCB. Now I could simply just return it for a replacement, which would be the easy thing to do. But you see , I see this as a chance to work on a simple hardware project and see if I can actually fix some hardware. 

This will be a first for me , i've recently purchased a soldering iron so  I  won't back down from this challenge. Plus in life, it's better to learn how to repair certain things in the event one day a replacment is not available. 


I've also checked the switch itself in the board and was not able to see any issues with the switch itself , the pins look normal and i've even swapped out the switch with a cherry mx red switch and still no luck. I'm not sure if there's some setting I need to set or firmware on the keyboard I need to download in order for the windows key to working. 



Ok so there's some VIA software that allows you to configure your keyboard in real time, I might be able to check the windows key mapping there,  in the manual I found online , it directs you to install the latest version of VIA on the link below  

**Link:** [link](https://github.com/WestBerryVIA/via-releases/releases)


However the newest version of the software doesn't auto detect my keyboard , and there's no way to upload the .json file it directs you to install for the "profile" of the keyboard. What's cool is this is written in python3 so if I need to fix anything it shoulden't be too big of an ask. 


So i'm opting to actually download a prior version of the software instead , version 2.0.5 is demonstrated to work with the keyboard I have. 


Ok so it turns out the switch did in fact have a bent terminal lol. So now I can change the LED's

So the key now works to change the LED's , however there is no real "windows" key on the keyboard yet, I bet it's a setting somewhere I just need to turn on. 

I'm just gonna say fuck it and use AutoHotKey on windows to rebind it for the time being cause I want to get to work.
