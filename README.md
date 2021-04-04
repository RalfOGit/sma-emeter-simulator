# sma-emeter-simulator
An SMA(TM) emeter simulator written in C++.

SMA-Emeters(TM) send out udp packets including electrical power and energy measurements at intervals of 1000ms. Each udp packet is 600 bytes long and its format is specified in an publicly available specification document provided by the manufacturer.

The executable starts by assembling a udp datagram as described in the SMA-Emeter(TM) specification document.

Afterwards it starts an infinite main loop. Within the main loop it repetitively transmits the same udp datagram every 1000 milliseconds, while updating the timestamp inside the packet.

The software comes as is. No warrantees whatsoever are given and no responsibility is assumed in case of failure. There is neither a GUI nor a configuration file. Configurations must be tweaked by modifying main.cpp. A number of obis definitions are given, some of them are commented out, since I do not need them.

The code is based on a Speedwire(TM) access library implementation https://github.com/RalfOGit/libspeedwire. The libspeedwire library implements a full parser for the sma header and the emeter datagram structure, including obis filtering. In addition, it implements some parsing functionality for inverter query and response datagrams. For convenience you may want to place the libspeedwire/ folder right next to the src/ and include/ folders of this repository.

The accompanied CMakeLists.txt assumes the following folder structure:

    sma-emeter-grabber
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
