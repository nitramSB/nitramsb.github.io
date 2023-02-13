## Extracting WiFi credentials from a Tuya smart bulb

About a year ago I purchased 10 LED smart bulbs that were a generically branded for 10$ a piece. I wanted to check out the hype revolving smart home applications, but I was not interesting in paying almost 4 times the amount for the popular Phillips Hue bulbs. However, me being a security enthusiast knew that a high focus on price and schedue tends to impact the quality attributes of the system, including security. I tried out the bulbs for several months and my impression was that they worked, but they did not provide a high quality feel in terms of app responsiveneness. Two of the bulbs also stopped working way before the claimed 30 000 hours life time, which gave some hope of finding low hanging security fruites.  


### Installation process
From a user perspective, you set up the smart bulbs following these steps:
1. Plug in the bulb into a 230V socket and turn it on
2. Install Smart Home App (In my case for IOS)
3. Follow the pairing wizard

### How does the bulb work?

1. The first time the pairing process is launched the the bulb boots and creates it's own wifi network
2. The mobile phone running Smart Home connects to the bulb's network
3. The user inputs the home WiFi credentials and they are sent to the bulb
4. The next time the bulb boots it will join the home network using the saved credentials
5. From now on the bulb is connected to Tuya servers that it receives commands from
6. The Smart Home app communicates with the Tuya servers on the internet, requesting it to send commands to the device


### Security Concerns
Based on the above operation, there are multiple security concerns:
1. The fact that system architecture requires internet access in itself an security concern. When adding the bulb to your home network you are essentially increasing the attack surface. You expose the device to any threat actor on the internet, which are a lot. There will be a very high likelyhood that the bulb contains vulnerabilitites waiting to be exploted by adversaries. In short, system development often results in undesired emerging behavior, due the interactions of system components, that the developers did not anticipate.
2.  Since the bulb know how to connect to the home Wifi it must have stored the SSID and password inside the device. For embedded devices it is common to store such information in flash memory, either inside a SoC or in external flash memory. Emebedded low cost IoT devices may not have the proper security modules that is able to encrypt the flash memory, which may leave sensitive information exposed.  

In this blog post, we will explore the latter security concern by attempting to dump the flash memory of the smart bulb and see if we can retreive the password for the home WiFi. This will require physical access to the device, however, if we consider the smart bulb's life cycle we can easily imagine that one throws away a defect bulb in the trash which could end up in the hands of an adversary. Access to your home Wifi could be the foothold that an adversary needs to launch further attacks that may lead to important assets being compromised.

### Step 1 - Exposing the hardware
I used a hacksaw to cut my way through the metal casing coated with plastic in a couple of minutes.
![image](https://user-images.githubusercontent.com/13424965/218526753-da06b0b5-29b0-4a52-b44c-d5326e2e7737.png)

The electrical system comprises two PCBs. One is driving the LED lights and the other contains the power step down circuit, connectivity and logic. The main PCB was easily be separated from the LED PCB as it was only connected through header pins. I wanted to check if I was still able to see the device in the app to check if it was still functioning, so I attached 3.3V the the input pins, and confirmed that it still worked. 

![image](https://user-images.githubusercontent.com/13424965/218526914-ec42fa0b-c13f-4db2-85e9-b50885b6b8cc.png)

### Step 2 - Identifying the hardware components

Since we know that SoC's don't run of 230V directly, the board must contain circuitry to step down the voltage to the usual range of 2,7V - 6V. The through-hole components and transformers are related to this functionality. Obviously, the silver module board draws our attention and is the prime suspect of containing the flash memory that we are after. To be able to identify the module I desoldered it from the PCB. When inspecting the backside of the module it showed the silkprint there was any silkprint on the board. After desoldering it, it turns out that it shows some basic infromation about the pins.

After doing some research I suspect that this hardware board is probably the WB3S module developed by Tuya based on the formfactor and pinout. Ref:(https://developer.tuya.com/en/docs/iot/wb3s-module-datasheet?id=K9dx20n6hz5n4). The datasheet of the WB3S reads that it contains a 2MB flash onboard, and I did not find any other flash ICs on the PCB. 



![image](https://user-images.githubusercontent.com/13424965/218527002-c550c8f8-f4bd-4247-bacf-087ee8f981c2.png)

### Step 3 - Test Configuration
In order to verify that the circuit still works after it has been desoldered from the main PCB I hooked it up according to the data sheet and powered it on. It still works! This means that from now on the module is powered from my lab bench power supply.

### Step 4 - Checking serial ports  
Most embedded devices contains serial interfaces that is used to debug and program the device before it leaves the factory. I observed 2 pairs of "TX/RX" pins which I suspected was UART ports. After inspecting both pair's TX port on an oscilloscope we observe that one of the ports contains data when the device boots. By looking at the signal we can determine the baudrate which seems to be 115 200 Hz. After rebooting the device and connecting the TX port to my logic analyzer at 15200 Hz we obtain the following information

> V:BK7231S_1.0.5





