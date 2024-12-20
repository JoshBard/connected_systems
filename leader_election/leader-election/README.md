# IR TX-RX Code Example n

Infrared IR/UART beacon code example
Updated 4/10/23 using ESP-IDF 5.0

Due to an unresolved sender/receiver conflict bug, this is really two programs that are configured
by uncommenting out the tx or rx part (but not both). 

### Features:

Transmit mode:
- Sends UART payload -- | START | myColor | myID | Checksum |
- Outputs 38kHz using PWM for IR transmission
- Onboard LED blinks corresponding to device ID value (myID)
- Button press to change device ID
- RGB LED shows traffic light state (red, green, yellow)
- Timer controls traffic light state (r - 10s, g - 10s, y - 10s)
- Runs at 1200 Baud

Receive mode:
- Uses three-pin IR receiver that strips 38kHz signal
- UART Receiver on pin 34 -- A5
- Uses checksum to validate message
- Message sent to console when using Monitor

***Note:*** this code is unreliable when both the RX and TX are both enabled. 


|Indicator Color|Color ID|
|---|---|
|Red|'R'|
|Yellow|'Y'|
|Green|'G'|

Resistors are fine-tuned for a special RGB LED (behavior will differ for if using individual LEDs)

### Wiring

|Function|Pin|
|---|---|
|PWM Pulse          |pin 26 -- A0|
|UART Transmitter   |pin 25 -- A1|
|UART Receiver      |pin 34 -- A2|
|Hardware interrupt |pin 4 -- A5|
|ID Indicator       |pin 13 -- Onboard LED|
|Red LED            |pin 33|
|Green LED          |pin 32|
|Blue LED           |Pin 14|
