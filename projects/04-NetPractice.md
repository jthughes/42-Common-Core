# 04 - NetPractice
## The Project
The NetPractice project provides a small simulation of different simple networks, where some portion of IP addresses, subnet masks, and/or routes are provided, and the student must fill in the rest of the detail to configure valid networks that allow designated devices to communicate with each other and/or the internet.

## Research 
IPv4 networks utilise 32-bit addresses, generally presented in dot-decimal notation, where the address is broken into 4 octets (or bytes) which are represented as integers in [0,255] separated by dots: ``192.168.0.1``

The address is generally broken into two parts:
- the network identifier
- the host identifier
The network identifier would be the portion at the start of the address and distinguishes between different networks. The host identifier is then the remainder of the address which is used to distinguish the different devices on the given network.

Various methods of dividing up the address space have been used over time, but the current method is to use Classless Inter-Domain Routing (CIDR), which allows variable network identifiers through subnetting (creating sub networks).

Subnets are created by giving a subnet mask - a bitmask that encodes a prefix length that determines the number of bits used in the network identifier. The mask takes the form of a number of 1-bits equal to the prefix length, the rest being 0-bits. The Binary AND operation performed on a network address and the network subnet mask will provide the same result for any addresses in the same network.

| Prefix Size | Mask | Addresses | Usable |
| :---------: | :--: | :-------: | ------ |
| /30 | 255.255.255.**252** | 4 | 2 |
| /29 | 255.255.255.**248** | 8 | 6 |
| /28 | 255.255.255.**240**| 16 | 14 |
| /27 | 255.255.255.**224** | 32 | 30 |
| /26 | 255.255.255.**192** | 64 | 62 |
| /25 | 255.255.255.**128** | 128 | 126 |
| /24 | 225.255.255.**0**   | 256 | 254 |
| /23 | 255.255.**254**.0   | 512 | 510 |
| /22 | 255.255.**252**.0   | 1024 | 1022 |
| /21 | 255.255.**248**.0   | 2048 | 2046 |
| /20 | 255.255.**240**.0   | 4096 | 4094 |
| /19 | 255.255.**224**.0   | 8192 | 8190 |
| /18 | 255.255.**192**.0   | 2^13 | 2^13 - 2 |
| /17 | 255.255.**128**.0   | 2^14 | 2^14 - 2 |
| /16 | 255.255.**0**.0     | 2^15 | 2^15 - 2 |
| ... | ... | ... | ... |
| /1  | **128**.0.0.0 | 2^31 | 2^31 - 2 |
| /0  | **0**.0.0.0   | 2^32 | 2^32 - 2 |

Take for example the standard network address for local networks ``192.168.0.1/24``. Its subnet mask is ``255.255.255.0`` as indicated by the `/24` - 24 bits fills up 3 of the 4 octets. 

```c++
(A): 192.168.0.1   = 11000000 10101000 00000000 00000001
(B): 255.255.255.0 = 11111111 11111111 11111111 00000000

       (A) AND (B) = 11000000 10101000 00000000 00000000
                   = 192.168.0.0
```
Any value of the last octet is part of the network, so a binary AND with ``192.168.0.117`` with the subnet mask should provide the same result as above.

```c++
(C): 192.168.0.117 = 11000000 10101000 00000000 01110101
(B): 255.255.255.0 = 11111111 11111111 11111111 00000000

       (C) AND (B) = 11000000 10101000 00000000 00000000
                   = 192.168.0.0
```

However, if we change any part of the network identifier, we will get a different result.
```c++
(D): 192.168.1.1   = 11000000 10101000 00000001 00000001
(B): 255.255.255.0 = 11111111 11111111 11111111 00000000

       (D) AND (B) = 11000000 10101000 00000001 00000000
                   = 192.168.1.0
```

For trying to solve the NetPractice problems, I found it useful to note that the sequecne ``0, 128, 192, 224, 240, 248, 252, 254, 255`` is the sum of the sequence ``128, 64, 32, 16, 8, 4, 2, 1``, which them sevles are the powers of 2: ``2^7, 2^6, 2^5, 2^4,2^3, 2^2, 2^1, 2^0``.

| Prefix | | Mask | | Addresses| |
|:--:| :----: | :-: | :------: | :-: | :-: |
| 32 | 32 - **0** | 255 | 254 + 1  |   1 | 2^**0** |
| 31 | 32 - **1** | 254 | 252 + 2  |   2 | 2^**1** |
| 30 | 32 - **2** | 252 | 248 + 4  |   4 | 2^**2** |
| 30 | 32 - **3** | 248 | 240 + 8  |   8 | 2^**3** |
| 29 | 32 - **4** | 240 | 224 + 16 |  16 | 2^**4** |
| 28 | 32 - **5** | 224 | 192 + 32 |  32 | 2^**5** |
| 27 | 32 - **6** | 192 | 128 + 64 |  64 | 2^**6** |
| 26 | 32 - **7** | 128 | 0 + 128  | 128 | 2^**7** |
| 25 | 32 - **8** |   0 | ...      | 256 | 2^**8** |

## What I learned
- Routers can interface with/between multiple different network groups 