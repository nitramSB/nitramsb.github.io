## Home Page

### Fault Injection Attack

Fault Injection in an attack method that exploits the emerging behavior of components when driven outside their operating range. Ways to drive the target device outside the operating range are (not an exhaustive list):

1. Voltage
2. Electromagnetic interference
3. Frequency modulation

##Example attack on a microcontroller

If a microcontroller input voltage is outside the operating specifications this causes instructions and data to be corrupted. If the overvoltage is balanced for just the amount of time the microcontroller will skip a few instructions but continue to function. If an attacker correlates the time-execution of the code running inside the microcontroller with faulting the device he/she may be able to compromise the confidentiality, integrity or availability of the device. Such an successfull attack could lead to accessing the otherwise protected memory of the microcontroller, thus breaching the integrity and confedentiality of the device depending on the content of memory.
