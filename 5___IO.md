# 5.1 I/O Devices

## Table of contents

## Intro

Each IO device has a limit on data rate (e.g. 56K modem: 7kb/sec).

I/O usually requires a mechanical and an electronic component.

- _Mechanical component_: the **device** itself
- _Electronic component_: the **device controller** (from the OS's perspective this is the device)
  - Controllers can handle a couple identical devices

How the driver talks with the device controller?

- Each controller has a few registers for communicating with the CPU
- Mapped IO
  - maps all the cotnrol registers into the memory space
    - each control register is assigned unique memory address to which no memory is assignec
  - the OS uses `load` and `store` instructions to read and write the registers

How does the controller talk with the disk?

- **Without DMA** (with programmed I/O (PIO)) the controller reads the block from the drive serially, bit by bit, until the entire block is in the internal buffer
  - the controller then causes an interrupt, notifying the OS....

- **With DMA**:
  - benefit: only interrupt the OS once

- **Direct Memory Access (DMA) _Special hardware_**
  - while DMA saves CPU work, the number of references to central memory remains the same
  - Main memory can still be a bottleneck
  - However, communications on the bus is dramatically reduced (which is good)

### Software side

Goals:

_**Device indepencence**_

- write programs that can access any I/O device without specifying the device in advance
- the OS _does_ take care of the differences among devices (which may require different command sequences)

_**File names should be device-independent**_

_**Buffering**_

- data must be put into an output buffer in advance to decouple th erate at which the buffer is filled from the rate at which it is emptied
- expensive (extensive copying; major impact on I/O performance

_**Shareable vs. dedicated device**_

- Shareable: e.g. disk
- Dedicated: e.g. CD-ROM

## Layers

![io-layer](static/io-layers.png)

### Device-independent OS software 

- drivers have the same interface between it and the OS
- OS defines the set of functions the driver must supply

#### Naming

- Major device number —> locate the appropriate driver
- Minor device number —> for which device the request is intended (a driver can support multiple devices)

#### Protection 

- Prevent users from accessing devices they're not allowed to access
- For both unix and windows:
  - devices appear in the file as *named objects*
  - Unix uses the `rwx` bits on files; the sam eprotection rules for files
- Up to the system admin to set the proper permissions

#### Buffering

- harmonizes the speed at which user accesses and the speed at which the driver supplies data

#### Library routines

- system calls are normally made by library procedures `e.g. count = read(fd, buffer, nbytes)`

#### Utilities and Daemons

- Deamon and spooling are used to deal w/ dedicated IO devices (e.g. printer)
- Deamon has the exclusive permission to use the device
- Data copied to a spooling directory; once finished, daemon writes back to the device

