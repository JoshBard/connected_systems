# 1. Personal Indoor Robot Assistant

## 2. Authors
Group 6: Josh Bardwick, Noah Hathout, Cole Knutsen, Trieu Tran

## 3. Date
12 - 10 - 2024

## 4. Summary
The quest involves designing and building a personal indoor robot assistant capable of navigating through predefined waypoints while avoiding obstacles. We use the ESP32 we have been using the whole semester with an indoor positioning system (Optitrack) for lcoating the robot. The robot kart also uses a sharp-IR sensor for obstacle detection. We use a NodeJS application to process positional data and overlook the communication between multiple robots connected over the same WiFi network. The ultimate goal was to encorporate autonomous navigation, real-time obstacle avoidance, as well as a multimodal option that would allow users to use WASD keyboard inputs. Finally, this project requires a graphic display of robots and waypoints in 2D via tingoDB and streamlit.

### Desired Behaviors

1. **Autonomous Navigation**
   - Navigate through a set of predefined waypoints using positional data from Optitrack.
   - Implement feedback control (PID) to keep our robot on a path within +/- 10cm.

2. **Obstacle Avoidance**
   -  Use sharp-IR sensors to detect and avoid collisions real-time. 
   - Implement priority-based sensor processing to ensure that the robot avoids collisions over staying on the path. 

3. **Coordinator Communication**
   - Use a NodeJS server to receive real-time coordinates (x, z, theta) from Optitrack.
   - Handle more than one simultaneous robots on the same network without problems.

4. **Remote Control (WASD) Mode**
   - Allow users to switch modes between 'automatic' and 'manual' modes, making the robot able to be controlled using WASD inputs on our NodeJS server. 

5. **Real-Time Monitoring and Visualization**
   - Create a graphical display of the robots' positions and waypoints on a laptop using Streamlit.
   - Store positional data in TingoDB for real-time updates 

### Solution Requirements

1. **Hardware Integration**
   - The ESP32 microcontroller serves as the central component, interfacing with IR transmitters and receivers, sharp-IR sensors, and GPIO peripherals (buttons, LEDs).
   - The robot requires precise integration with the Optitrack indoor positioning system for accurate navigation and real-time location updates.
   - Network hardware supports WiFi connectivity for communication between multiple robots and the NodeJS server.
   - Robust and low-latency obstacle detection is achieved through sharp-IR sensors.

2. **Network Communication**
   -  Use of UDP for message passing ensures minimal overhead, supporting real-time communication between all interfaces, TingoDB, Node.js, and streamlit. 
   -  The NodeJS server handles simultaneous robot connections on a shared WiFi network, receiving and processing location data and commands.
   -  Real-time updates to the database (TingoDB) ensure consistent monitoring of robots' status.

3. **Control Systems**
   -  Feedback control (PID) is implemented to maintain the robot's position within a +/- 10cm margin from predefined waypoints.
   -  Priority-based processing ensures obstacle avoidance takes precedence over maintaining path alignment during autonomous navigation.

4. **Software Components**
   - Visualization and control are achieved through a Streamlit-based interface for graphical representation of robot positions and waypoints.

5. **Concurrency and Synchonization**
   - Mutexes and semaphores prevent race conditions and ensure synchronized access to shared resources such as network sockets and the database.
   - FreeRTOS tasks ensure concurrent handling of operations, IR message transmission/reception, UDP communication.


## 5. Solution Design
This project leverages ESP32 devices for motor control, manual and autonomous navigation modes, and UDP communication to interact with external systems. It incorporates ADC-based distance sensing, PID control for directional correction, and waypoint navigation for autonomous mode.

1. **app_main:**
   - Configure WiFi for network communication.
   - Set up PWM (LEDC) and GPIO for motor control.
   - Initialize ADC for Sharp IR distance sensing.
   - Set up timers for periodic tasks.

2. **Create FreeRTOS Tasks**
   - Receives UDP messages for robot commands and destination updates.
   - Parses messages and handles manual (WASD) and mode-switch (P) commands.
   - Updates motor states based on parsed commands.
   - In autonomous mode, calculates distance to the next waypoint.

3. **Request Location Task (request_location)**
   - **Functionality**: 
     - Sends position requests to the external server via UDP.
     - Parses the server response to update robot position and heading.
     - In autonomous mode: Updates the destination waypoint, & Calculates heading errors and invokes PID control for navigation.

**3. Motor Control**
- **Finite State Machine (FSM)**: Implements predefined states for motor direction and speed:
  - `STATE_MOVE_FORWARD`: Both motors move forward.
  - `STATE_GO_LEFT`: Left motor stops, right motor moves forward.
  - `STATE_GO_RIGHT`: Right motor stops, left motor moves forward.
  - `STATE_MOVE_BACKWARD`: Both motors move backward.
  - `STATE_STOP`: Both motors stop.
  - `STATE_SPIN_LEFT`: Left motor moves backward, right motor moves forward.
  - `STATE_SPIN_RIGHT`: Left motor moves forward, right motor moves backward.
- **PWM Control**: Uses LEDC to control motor speeds dynamically based on commands or PID output.

---

**4. Autonomous Mode**
- **Waypoint Navigation**:
  - Predefined waypoints guide the robot.
  - Calculates distance to the current waypoint using the robot’s position.
  - Advances to the next waypoint upon reaching the threshold distance.
- **PID Control**:
  - Dynamically adjusts motor speeds to correct heading errors.
  - Uses proportional, integral, and derivative gains to compute corrections.
  - Ensures smooth navigation towards the destination.

---

**5. Sensor Integration**
- **Sharp IR Distance Sensor**:
  - Configured using ADC with calibration.
  - Reads analog voltages and converts them into distance in millimeters.
  - Provides obstacle detection for autonomous navigation.

---

**6. Network Communication**
- **WiFi Initialization**:
  - Connects to the network using specified SSID and password.
  - Handles IP address acquisition and reconnection upon disconnection.
- **UDP Socket Communication**:
  - Receives control commands and position updates.
  - Sends position requests to the external server.

---

**7. Timers**
- **Periodic Timer**:
  - Configured for 100ms intervals.
  - Triggers tasks like distance sensing and PID corrections.

---

**8. Key Functional Blocks**
1. **Manual Mode**:
   - Processes `W`, `A`, `S`, `D` commands for directional control.
   - `P` toggles between manual and autonomous modes.
2. **Autonomous Mode**:
   - Calculates heading and distance to the target waypoint.
   - Uses PID control for steering corrections.

---

**9. External Communication**
- **Data Upload**:
  - Sends robot position and status to an external HTTP server using REST API.
- **Position Requests**:
  - Periodically queries the external system for updated position and heading.

Circuit diagram:
![circuit-diagram](/remote-control/public/Quest5-circuit-diagram.jpg)

## 6. Summary

**Potential Improvements**
- **Hardware Interrupts**: Use hardware interrupts for Sharp-IR sensor readings to enhance real-time performance and reduce CPU load compared to polling-based detection.
- **Network Optimization**: Improve the reliability of UDP message passing between the robot and the NodeJS server to minimize latency in position updates and command handling.
- **Data Integrity**: Implement robust error-checking mechanisms for UDP communication to ensure accurate position updates and reliable obstacle avoidance.
- **Enhanced Visualization**: Add real-time 3D visualization of the robot's environment to complement the 2D Streamlit interface.

**Results**
The project successfully developed a personal indoor robot assistant capable of navigating through predefined waypoints and avoiding obstacles in real time. Using positional data from the Optitrack system and Sharp-IR sensors for obstacle detection, the robot autonomously navigates or can be manually controlled via WASD inputs. The NodeJS server facilitates real-time communication and data management, while Streamlit provides a graphical interface for monitoring robot status and waypoints. TingoDB ensures data persistence for positional tracking and historical analysis.

**Challenges**
- **Real-Time Communication**: Maintaining seamless UDP communication with multiple robots on the same WiFi network required significant effort to handle packet collisions and data loss.
- **Obstacle Detection**: Fine-tuning the Sharp-IR sensor calibration to work in dynamic environments with varied obstacles and lighting conditions was challenging.
- **PID Control Optimization**: Balancing the proportional, integral, and derivative gains for stable and responsive navigation required iterative adjustments to avoid oscillations or overshoots.

## 7. Artifacts

- **ESP32 Firmware**: Implemented in C using FreeRTOS, including tasks for motor control, PID navigation, obstacle avoidance, and UDP communication.
- **NodeJS Server**: Handles robot-to-server communication, processes location updates, and streams commands.
- **TingoDB Integration**: Provides a local database for storing positional data and ensuring real-time updates for monitoring.
- **Streamlit Interface**: A 2D graphical interface displaying robot positions, waypoints, and system status, accessible from a web browser.
- **Optitrack System**: Used for precise indoor localization of the robot.


Link to report video:
- [Link to video demo](https://drive.google.com/file/d/1IIHNn9ovCAJhkweRPx5khYDsRs2hRVlT/view?usp=sharing). 

Link to design demo:
- [Link to video demo](https://drive.google.com/file/d/1VPvRS4MaWk1iu6lj2RG2DcL4Mfvxja_7/view?usp=sharing).

 ## 9. AI Code Assertions

All code is labeled appropriately "AI Generated".
