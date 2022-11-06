# IONA - JVS IO Board by Arduino Nano

This program makes an Arduino Nano work as a JVS IO Board that allows you
to connect a legacy JAMMA joystick to a JVS based arcade system, such as NAOMI.

Since now underlying [jvsio](https://github.com/toyoshim/jvsio) library
supports several boards and devices, you would be able to run this on
other boards with minimal hacks.

Also there is [another example](https://github.com/toyoshim/iona-js)
to run iona compatible functionalities without the Arduino firmware.

## Schematic
```
                         o TX1  VIN o
JVS Data+ o--------------o RX0  GND o
                         o RST  RST o
                         o GND   5V o--------------------------o (JAMMA c)
                                                         / DIPSW1
JVS Data- o--------------o D2    A7 o-o---o---|==|-------o o---o 5V
                                      |   |   R Ohm
                                      _   |              / DIPSW2
                                R Ohm| |  +---|======|---o o---o 5V
                                     |_|       2R Ohm
                                      |
                                    -----
                                    /////                / DIPSW3
JVS Sense o---o---VVVV---o D3    A6 o-o---o---|==|-------o o---o 5V
              |  100 Ohm              |   |   R Ohm
       100 nF =                       -   |              / DIPSW4
              |  RC-LPF         R Ohm| |  +---|======|---o o---o 5V
            -----                    |_|       2R Ohm
            /////                     |
                                    -----
                                    /////
 JAMMA 18 o--------------o D4    A5 o
 JAMMA 19 o--------------o D5    A4 o
 JAMMA 20 o--------------o D6    A3 o--------------------------o JAMMA 16
 JAMMA 21 o--------------o D7    A2 o--------------------------o JAMMA 17
 JAMMA 22 o--------------o D8    A1 o--------------------------o (button 5)
 JAMMA 23 o--------------o D9    A0 o--------------------------o (button 6)
 JAMMA 24 o--------------o D10 AREF o--------------------------o 5V
 JAMMA 25 o--------------o D11  3V3 o
 JAMMA 15 o--------------o D12  D13 o--------------------------o LED on Nano
```
Of course, all GNDs should be connected together.
```

1P input = Arduino Pin = DB15 Pin
UP = D4 = 15
DOWN  = D5 = 17
LEFT = D6 = 14
RIGHT = D7 = 6
B1 = D8 = 13
B2 = D14 = 5
B3 = D10 = 12
B4 = D11 = 4
Test = D12 = 9 (Not Used)
Coin = A3 = 3
Start = A2 = 11
B5 = A1 = 10
B6 = A0 = 2
5V = 5V/AREF = 8
GND = GND = 1



2P input = Arduino Pin = DB15 Pin
UP = D22 = 15
DOWN  = 15 = 17
LEFT = D16 = 14
RIGHT = D17 = 6
B1 = D18 = 13
B2 = D19 = 5
B3 = D20 = 12
B4 = D21 = 4
Test = D12 = 9 (Not Used)
Coin = A3 = 3
Start = D24 = 11
B5 = D2 = 10
B6 = D3 = 2
5V = 5V/AREF = 8
GND = GND = 1 



JVS GND o---o---o JAMMA GND
             |
      Arduino Nano  GND
```
VIN from JAMMA 5V is optinoal. Instead, you can provide power to the Arduino
Nano from the USB mini on the board as usually you do to program it.

DIPSW1-4 are also optional, and not used yet. But probably will be used to have
rapid fire switches, and mode select to support the Marjong connector.

Button 5 and 6 can be connected to an extended connector that support extra
buttons, like aka kick harness.

Note: JVS uses the USB connector, but it isn't the real USB at all.
The protocol is completely different, and it does not provide the 5V power.

To support multiple nodes, you can use NanoSenseClientSupportingDaisyChain
instead of NanoSenseClient, and use the A5 pin to connect with the secondary
sense signal.
```
JVS Data+ o----o--------------------------------------o    Data+/- are shared
               |                                      |       among all nodes
JVS Data- o-------o--------------------------------------o
               |  |                                   |  |
JVS Sense o----------o     o--------------------------------o  Terminated by
               |  |  |     | Sense must be isolated   |  |  |   NanoSenseClient
               |  |  |     |  per node:node link      |  |  |
               R  R LPF    |                          R  R LPF
               |  |  |     |                          |  |  |
               o  o  o     o                          o  o  o
              RX0 D2 D3    A5                        RX0 D2 D3
         Nano for the primary node              Nano for the secondary node
with NanoSenseClientSupportingDaisyChain            with NanoSenseClient
```
RC Low Pass Filter is still needed to generate the intermediate voltage between
D3 and Sense as we do for the single node circuit.
You may want to insert small Rs for each RX0 and D2 to protect circuits.


