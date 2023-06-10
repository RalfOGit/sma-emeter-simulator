# sma-emeter-simulator
An SMA(TM) emeter simulator written in C++. It mimics an SMA(TM) emeter device on your local network by generating the same kind of udp packets that an actual SMA(TM) emeter would generate.

SMA-Emeters(TM) send out udp packets including electrical power and energy measurements at intervals of 1000ms. The general udp packet format is specified in a publicly available specification document provided by the manufacturer (https://developer.sma.de/fileadmin/content/global/Partner/Documents/SMA_Labs/EMETER-Protokoll-TI-en-10.pdf).

The simulator supports different emeter types. Depending on how you configure the simulator, it can generate these different udp packet formats.:

Emeter-10

    Susy-ID: 270 (0x010E)
    Protocol-ID: 0x6069
    UDP packet size: 600 bytes
    SMA header size: 18 bytes
    Multicast: 239.12.255.254 port 9522

Emeter-20

    Susy-ID: 349 (0x015D)
    Protocol-ID: 0x6069
    UDP packet size: 608 bytes since ObisData::Frequency was added with firmware version 2.03.4.R; 600 bytes was used before
    SMA header size: 18 bytes
    Multicast: 239.12.255.254 port 9522

Sunny Home Manager 20

    Susy-ID: 372 (0x0174)
    Protocol-ID: 0x6069
    UDP packet size: 608 bytes since ObisData::Frequency was added with firmware version 2.03.4.R; 600 bytes was used before
    SMA header size: 18 bytes
    Multicast: 239.12.255.254 port 9522
    Unicast: if configured
    
    Firmware version 2.07.x used a different format for unicast transmission:
    Susy-ID: 372 (0x0174)
    Protocol-ID: 0x6081
    UDP packet size: 610 bytes
    SMA header size: 20 bytes
    Unicast: if configured

The executable starts by assembling a udp datagram according to one of the above mentioned configurations.

Afterwards it starts an infinite main loop. Within the main loop it repetitively transmits the same udp datagram every 1000 milliseconds, while updating the timestamp inside the packet.

The software comes as is. No warrantees whatsoever are given and no responsibility is assumed in case of failure. There is neither a GUI nor a configuration file. Configurations must be tweaked by modifying main.cpp.

The code is based on a Speedwire(TM) access library implementation https://github.com/RalfOGit/libspeedwire. The libspeedwire library implements a full parser for the sma header and the emeter datagram structure, including obis filtering. In addition, it implements some parsing functionality for inverter query and response datagrams. For convenience you may want to place the libspeedwire/ folder right next to the src/ and include/ folders of this repository.

The accompanied CMakeLists.txt assumes the following folder structure:

    sma-emeter-simulator
        src
        include
        libspeedwire
            src
            include
            CMakeLists.txt
        ... build path ...
        CMakeLists.txt

The code has been tested against the following environment:

    OS: CentOS 8(TM), IDE: VSCode (TM)
    OS: Windows 10(TM), IDE: Visual Studio Community Edition 2019 (TM)

Measurements are encoded as OBIS data fields. OBIS is defined in IEC 62056-6-1. There is a German wikipedia page https://de.wikipedia.org/wiki/OBIS-Kennzahlen about it. The order of measurements in SMA(TM) emeter packets is as described below. The values provided are just examples:

    // totals
    ObisData::PositiveActivePowerTotal   (0,  1, 4,  121.60) 
    ObisData::PositiveActiveEnergyTotal  (0,  1, 8, 1320.34) 
    ObisData::NegativeActivePowerTotal   (0,  2, 4,    0.00)
    ObisData::NegativeActiveEnergyTotal  (0,  2, 8,  305.03) 
    ObisData::PositiveReactivePowerTotal (0,  3, 4,    0.00) 
    ObisData::PositiveReactiveEnergyTotal(0,  3, 8,    5.90) 
    ObisData::NegativeReactivePowerTotal (0,  4, 4,  188.90) 
    ObisData::NegativeReactiveEnergyTotal(0,  4, 8,  949.68)
    ObisData::PositiveApparentPowerTotal (0,  9, 4,  224.60)
    ObisData::PositiveApparentEnergyTotal(0,  9, 8, 1757.41) 
    ObisData::NegativeApparentPowerTotal (0, 10, 4,    0.00) 
    ObisData::NegativeApparentEnergyTotal(0, 10, 8,  327.62) 
    ObisData::PowerFactorTotal           (0, 13, 4,    0.54) 
    ObisData::Frequency                  (0, 14, 4,   50.16) 
    
    // L1
    ObisData::PositiveActivePowerL1      (0, 21, 4,    0.00) 
    ObisData::PositiveActiveEnergyL1     (0, 21, 8,  337.53) 
    ObisData::NegativeActivePowerL1      (0, 22, 4,   21.70) 
    ObisData::NegativeActiveEnergyL1     (0, 22, 8,  141.54) 
    ObisData::PositiveReactivePowerL1    (0, 23, 4,    0.00) 
    ObisData::PositiveReactiveEnergyL1   (0, 23, 8,    2.48)
    ObisData::NegativeReactivePowerL1    (0, 24, 4,   22.30) 
    ObisData::NegativeReactiveEnergyL1   (0, 24, 8,  176.48) 
    ObisData::PositiveApparentPowerL1    (0, 29, 4,    0.00) 
    ObisData::PositiveApparentEnergyL1   (0, 29, 8,  473.68) 
    ObisData::NegativeApparentPowerL1    (0, 30, 4,   31.10) 
    ObisData::NegativeApparentEnergyL1   (0, 30, 8,  144.26) 
    ObisData::CurrentL1                  (0, 31, 4,    0.18) 
    ObisData::VoltageL1                  (0, 32, 4,  231.97) 
    ObisData::PowerFactorL1              (0, 33, 4,    0.70) 
    												 
    // L2                                            
    ObisData::PositiveActivePowerL2      (0, 41, 4,  160.80) 
    ObisData::PositiveActiveEnergyL2     (0, 41, 8,  775.23) 
    ObisData::NegativeActivePowerL2      (0, 42, 4,    0.00) 
    ObisData::NegativeActiveEnergyL2     (0, 42, 8,   77.80) 
    ObisData::PositiveReactivePowerL2    (0, 43, 4,    0.00) 
    ObisData::PositiveReactiveEnergyL2   (0, 43, 8,    7.38) 
    ObisData::NegativeReactivePowerL2    (0, 44, 4,  126.00) 
    ObisData::NegativeReactiveEnergyL2   (0, 44, 8,  535.19) 
    ObisData::PositiveApparentPowerL2    (0, 49, 4,  204.30) 
    ObisData::PositiveApparentEnergyL2   (0, 49, 8,  974.19) 
    ObisData::NegativeApparentPowerL2    (0, 50, 4,    0.00) 
    ObisData::NegativeApparentEnergyL2   (0, 50, 8,   89.10) 
    ObisData::CurrentL2                  (0, 51, 4,    1.12) 
    ObisData::VoltageL2                  (0, 52, 4,  230.66) 
    ObisData::PowerFactorL2              (0, 53, 4,    0.79) 
    												 
    // L3                                            
    ObisData::PositiveActivePowerL3      (0, 61, 4,    0.00) 
    ObisData::PositiveActiveEnergyL3     (0, 61, 8,  271.21) 
    ObisData::NegativeActivePowerL3      (0, 62, 4,   17.60) 
    ObisData::NegativeActiveEnergyL3     (0, 62, 8,  149.31) 
    ObisData::PositiveReactivePowerL3    (0, 63, 4,    0.00) 
    ObisData::PositiveReactiveEnergyL3   (0, 63, 8,    1.70) 
    ObisData::NegativeReactivePowerL3    (0, 64, 4,   40.66) 
    ObisData::NegativeReactiveEnergyL3   (0, 64, 8,  243.67)  
    ObisData::PositiveApparentPowerL3    (0, 69, 4,    0.00) 
    ObisData::PositiveApparentEnergyL3   (0, 69, 8,  434.62) 
    ObisData::NegativeApparentPowerL3    (0, 70, 4,   44.30) 
    ObisData::NegativeApparentEnergyL3   (0, 70, 8,  156.83) 
    ObisData::CurrentL3                  (0, 71, 4,    0.23) 
    ObisData::VoltageL3                  (0, 72, 4,  230.09) 
    ObisData::PowerFactorL3              (0, 73, 4,    0.40) 
    
    // software version, end of data
    ObisData::SoftwareVersion            (144,0, 0, "2.03.4.R") 
    ObisData::EndOfData                  (0,  0, 0, 0) 
