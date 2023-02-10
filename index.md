## Extracting WiFi credentials from a Tuya smart bulb

About a year ago I purchased 10 LED smart bulbs that were a generically branded for 10$ a piece. I wanted to check out the hype revolving smart home solutions, but I was not interesting in paying almost 4 times the price for the popular Phillips Hue bulbs. However, me being a security enthusiast knew that a high focus on price and schedue tends to impact the quality attributes of the system, including security. I tried out the bulbs for several months and my impression was that they did the job OK, but they did not provide a high quality feel in terms of app responsiveneness. 


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
5. From now on the bulb is connected to Tuya servers on the internet that it receives commands from
6. The Smart Home app communicates with the Tuya servers on the internet, requesting it to send commands to the device

### Security Concerns
Based on the above operation, there are multiple security concerns:
1. The fact that system architecture requires internet access in itself an security concern. When adding the bulb to your home network you are essentially increasing the attack surface. You expose the device to any threat actor on the internet, which are a lot. There will be a very high likelyhood that the bulb contains vulnerabilitites waiting to be exploted by adversaries. In short, system development often results in undesired emerging behavior, due the interactions of system components, that the developers did not anticipate.
2.  Since the bulb know how to connect to the home Wifi it must have stored the SSID and password inside the device. For embedded devices it is common to store such information in flash memory, either inside a SoC or in external flash memory. Emebedded low cost IoT devices may not have the proper security modules that is able to encrypt the flash memory, which may leave sensitive information explosed. There may be possible for an adversary to

In this blog post, we will explore the latter security concern by attempting to dump the flash memory of the smart bulb and see if we can retreive the password for the home WiFi. This will require physical access to the device, but if we consider the smart bulb's life cycle we can easily imagine that one throws away a defect bulb for replacement which could end up in the hands of an adversary. Access to your home Wifi could be the foothold that an adversary needs to launch further attacks that may lead to important assets being compromised.

### Step 1 - Exposing the hardware
I used a hacksaw to cut my way through the metal casing coated with plastic in a couple of minutes.
*image of exposed circuit*

There are two PCBs. One is driving the LED lights and the other contains the power step down circuit, connectivity and logic. The main PCB could easily be separated from the LED PCB as it was only connected through header pins.

*Image of standalone connectivity PCB*


### Step 2 - Identifying the hardware components
Since we know that SoC's don't run of 230V directly, we know that the board contains circuitry to step down the voltage to around 2,7V - 6V. The through hole capacitors and transformers are probably releated to this part. Obviously, the large module board draws our attention. After doing some research it turns out that this hardware board is probably the WB3S module developed by Tuya based on the formfactor and pinout. Ref:(https://developer.tuya.com/en/docs/iot/wb3s-module-datasheet?id=K9dx20n6hz5n4). The datasheet of the WB3S reads that it contains a 2MB flash onboard, and I did not find any other flash ICs on the PCB. 

*Insert image here*

I then desoldered the module from the PCB because I wanted to be able to read if there was any silkprint on the board. After desoldering it, it turns out that it shows some basic infromation about the pins.
*Image of backside of module*

### Step 3 - Test Configuration
In order to verify that the circuit still works after it has been desoldered from the main PCB I hooked it up according to the data sheet and powered it on. It still works! This means that from now on the module is powered from my lab bench power supply.

### Step 4 - Checking serial ports  
Most embedded devices contains serial interfaces that is used to debug and program the device before it leaves the factory. I observed 2 pairs of "TX/RX" pins which I suspected was UART ports. After inspecting both pair's TX port on an oscilloscope we observe that one of the ports contains data when the device boots. By looking at the signal we can determine the baudrate which seems to be 115 200 Hz. After rebooting the device and connecting the TX port to my logic analyzer at 15200 Hz we obtain the following information

> V:BK7231S_1.0.5





