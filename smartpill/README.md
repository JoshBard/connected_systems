# 1. SmartPill Ingestible Sensor

## 2. Authors
Josh Bardwick, Noah Hathout, Cole Knutsen, Trieu Tran

## 3. Date
09 - 20 - 2024

## 4. Summary
The quest involves designing and building the basic functionality of an ingestible "Smart Pill," which will capture, log, and transmit "biometric" data while passing through the body. The primary focus is to read sensor data periodically, report it in engineering units, and display the results on a console interface.

### Desired Behaviors

1. **Temperature Measurement**
   - Read and report temperature in engineering units (e.g., °C or °F).

2. **Light Measurement**
   - Measure and report light levels in Lux using a light sensor.

3. **Battery Level Measurement**
   - Read and report the battery voltage level, requiring a voltage divider circuit.

4. **Tilt Measurement**
   - Report tilt as a logical value (on/off) to detect the orientation of the device.

5. **LED Status Indicators**
   - **Green LED**: Device is in "ready-to-swallow" mode (light sensor detects light).
   - **Blue LED**: Normal sensing mode (light sensor detects darkness) and blinks every 2 seconds.
   - **Red LED**: Done sensing (light sensor detects light again, indicating the device has exited the body).

6. **Reporting Interval**
   - Report sensor data to the console every 2 seconds.
   - Console output should include all sensor data in engineering units (e.g., temperature, Lux, battery level, tilt status).

### Solution Requirements

- **Start-up**: Device starts via button press or power reset in "ready-to-swallow" mode.
- **Sensing Cycle**: A periodic cycle of 2 seconds, where sensor data is captured and reported.
- **Console Output**: Sensor data should be displayed as text in engineering units on the console I/O (via serial interface from the ESP32 to a laptop console window).

### Assignment
1. **Model the Behavior**: Develop a model based on the desired behaviors and functionality outlined.
2. **Design and Build the Solution**: Implement the solution to meet the specified criteria.
3. **Demonstration**: Present the working prototype.
4. **Reporting**: Follow quest reporting instructions to submit the solution.
5. **Self-Assessment Rubric**: Complete a self-assessment based on the criteria specified.


## 5. Solution Design

### START -> app_main
- **Create "Sensor Task"**
- **Create "Task Tilt Button"**
- **Create "Task Reset Button"**


#### Task Reset Button
---
- **Loop: Poll Button**
  - **IF button pressed**:
    - Set `pill_state = STATE_READY`
    - Turn ON **Green LED**
    - Log: *"Reset button pressed"*
  - **ELSE**: Keep polling


#### Task Tilt Button
---
- **Loop: Poll Tilt Sensor**
  - **IF button pressed**:
    - Toggle `tilt_orientation` (Vertical/Horizontal)
  - **ELSE**: Keep polling


#### Sensor Task
---
- **Initialize GPIO and ADC Channels**
- **Loop every 2 seconds**:
  - Read **temperature** from thermistor
  - Read **battery voltage**
  - Read **light intensity**
  - Calculate **lux** from light sensor
  - **SWITCH (`pill_state`)**:
    - **IF `STATE_READY`**:
      - Turn ON **Green LED**
      - **IF `lux < LIGHT_THRESHOLD`**: 
        - Transition to `STATE_SENSING`
    - **IF `STATE_SENSING`**:
      - Blink **Blue LED**
      - **IF `lux > LIGHT_THRESHOLD`**: 
        - Transition to `STATE_DONE`
    - **IF `STATE_DONE`**:
      - Turn ON **Red LED**
  - **Log sensor data**:
    - lux
    - temperature
    - battery voltage
    - tilt orientation
  - Delay 2 seconds and repeat

## 6. Summary

### Potential Improvements
For our reset button, we used a polling task. To reduce CPU usage, we would get rid of the spin lock created by polling and implement the reset with a hardware interrupt as well. We also had too many jumper cables and we could have reduced the complexity of our circuit.

### Results
The Smart Pill successfully captured and reported "biometric" data, including temperature, light levels in Lux, battery voltage, and tilt orientation. LED status indicators behaved as expected, with data reported every 2 seconds via the serial console.

### Challenges
- **Tilt Sensor Integration**: Difficulty using tilt detection, as we didn't have the right sensors to actually implement it.
- **Light Sensor Threshold**: Fine-tuning the light threshold for transitioning between states required many adjustments in different light settings, so at time it was very fustrating to fine tune the M and B constants of the photocell.

## 7. Artifacts
Everything that we needed for this quest was covered by the skills and we did not need to use external resources except code from ChatGPT that was present in our skills as well.

Link to report video:
- [Link to video demo](https://drive.google.com/file/d/1CU57YWfw8BjZGP4tDwL273PEkrWFR-8J/view?usp=sharing). 

Link to design demo:
- [Link to video demo](https://drive.google.com/file/d/11GS_PD7nRqs_4I9aimhMB19hdrCAkj27/view?usp=sharing).
  
## 9. AI Code Assertions

All code is labeled appropriately "AI Generated".
