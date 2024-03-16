# Ethernet_mac
Implementing Ethernet mac on Virtex-6 FPGA. This contains the opensource resources and how to use them. Its like a personal blog for implementing step by step.

## 19-02-2024
### Opencore resources
[Freecores](https://freecores.github.io/) is project that contains all the projects from the [Opencores](opencores.org).

I will start with [ethamc](https://github.com/freecores/ethmac) from opencores which can support 10/100Mbps.

## 06-03-2024
### Finding the MAC address
In windows type the following in cmd and find the appropriate MAC address.
```
ipconfig /all
```
**MAC address** of the FPGA is mentioned over it.

## Using scapy to check the ethernet
```
>>> IP()
<IP  |>
>>> raw(IP())
b'E\x00\x00\x14\x00\x01\x00\x00@\x00|\xe7\x7f\x00\x00\x01\x7f\x00\x00\x01'
>>> hexdump(IP())
0000  45 00 00 14 00 01 00 00 40 00 7C E7 7F 00 00 01  E.......@.|.....
0010  7F 00 00 01                                      ....
>>> IP(_)
<IP  version=4 ihl=5 tos=0x0 len=20 id=1 flags= frag=0 ttl=64 proto=ip chksum=0x7ce7 src=127.0.0.1 dst=127.0.0.1 |>
>>> a = Ether()/IP(src = '192.168.5.1',dst='192.168.5.9')/TCP()/"Hello world..!"
>>> a
<Ether  type=IPv4 |<IP  frag=0 proto=tcp src=192.168.5.1 dst=192.168.5.9 |<TCP  |<Raw  load='Hello world..!' |>>>>
>>> hexdump(a)
0000  00 00 5E 00 01 01 14 13 33 D7 D5 25 08 00 45 00  ..^.....3..%..E.
0010  00 36 00 01 00 00 40 06 EF 66 C0 A8 05 01 C0 A8  .6....@..f......
0020  05 09 00 14 00 50 00 00 00 00 00 00 00 00 50 02  .....P........P.
0030  20 00 63 F8 00 00 48 65 6C 6C 6F 20 77 6F 72 6C   .c...Hello worl
0040  64 2E 2E 21                                      d..!
>>> b=raw(a)
>>> b
b'\x00\x00^\x00\x01\x01\x14\x133\xd7\xd5%\x08\x00E\x00\x006\x00\x01\x00\x00@\x06\xeff\xc0\xa8\x05\x01\xc0\xa8\x05\t\x00\x14\x00P\x00\x00\x00\x00\x00\x00\x00\x00P\x02 \x00c\xf8\x00\x00Hello world..!'
>>> c = Ether(b)
>>> c
<Ether  dst=00:00:5e:00:01:01 src=14:13:33:d7:d5:25 type=IPv4 |<IP  version=4 ihl=5 tos=0x0 len=54 id=1 flags= frag=0 ttl=64 proto=tcp chksum=0xef66 src=192.168.5.1 dst=192.168.5.9 |<TCP  sport=ftp_data dport=http seq=0 ack=0 dataofs=5 reserved=0 flags=S window=8192 chksum=0x63f8 urgptr=0 |<Raw  load='Hello world..!' |>>>>
```

## PHY Default Interface Mode

| Mode | J66 | J67 | J68 |
| ------------- | ------------ | ----------- | ----------- | 
|GMII/MII to copper (default) | Jumper over pins 1-2 | Jumper over pins 1-2 | No jumper |
|SGMII to copper,no clock | Jumper over pins 2-3 | Jumper over pins 2-3 | No jumper |
| RGMII | Jumper over pins 1-2 | No jumper | Jumper on |

## Ethernet PHY connections
| U1 FPGA Pin | Schematic Net Name | Net in model |
| ----------- | ------------------ | ------------ |
|AH13| PHY_RESET|  |
|AL13| PHY_CRS| MII_CRS |
|AK13 |PHY_COL| MII_COL |
|AP11| PHY_RXCLK | MII_RX_CLK |
|AG12 |PHY_RXER| MII_RX_ER |
|AM13| PHY_RXCTL_RXDV| MII_RX_DV |
|AN13| PHY_RXD0| MII_RXD[0] |
|AF14| PHY_RXD1 | MII_RXD[1] |
|AE14| PHY_RXD2 | MII_RXD[2] |
|AN12| PHY_RXD3| MII_RXD[3] |
|AD12| PHY_TXCLK| MII_TX_CLK |
|AH10| PHY_TXER| MII_TX_ER |
|AJ10 |PHY_TXCTL_TXEN| MII_TX_EN |
|AM11| PHY_TXD0 | MII_TXD[0] |
|AL11| PHY_TXD1| MII_TXD[1] |
|AG10| PHY_TXD2| MII_TXD[2] |
|AG11| PHY_TXD3| MII_TXD[3] |
