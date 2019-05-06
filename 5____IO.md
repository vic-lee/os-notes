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

## Disk hardware

### Magnetic disks

- RPM
- Tracks per surface
- Sectors per track (bit density)
- Tracks per cylinder
- Seek time: the time required to seek the desired cylinder
  - overlapping seek
- Rotational latency
- Transfer rate: the rate at which data flow between drive and computer 

### Redundant Array of Inexpensive/Independent Disks (RAID)

- a box full of disks as a single drive
- RAID looks like a **Single Large Expensive Disk (SLED)** to the OS
- RAID has different configurations (0-6 level)

#### Level 0: Striping

- no redundancy

#### Level 1: Mirroring

- True RAID: duplicate all the disks
- on write: every strip is written twice (worse performance)
- on read: either copy can be used (helps w/ distributing the load over more drives, up to twice as good)
- great fault tolerance

#### Parity and XOR

- Parity helps check whether something goes wrong (e.g. if the data is corrupt)
- A XOR B = C ==> A = C XOR B and ...

#### Level 2: synchronized disks, bit interleaving, multiple hamming checksums disks

- not used anymore 

#### Level 3: Synchronized disks, bit interleaved, single parity disk (simplified L2)

#### Level 4: Stripping + Parity Disk

![L4](static/raid-l4.png)

- if one of the disks go down, it can be recovered by the parity disk (PD) ad the rest of the disks
- performs poorly for small updates (small update; but requires to compute XOR value)

