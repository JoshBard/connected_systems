# Quest 2: CatTrack

## 2. Authors
Josh Bardwick, Noah Hathout, Cole Knutsen, Trieu Tran

## 3. Date

10 - 04 - 2024

# Cat Tracker

Authors: Joshua Bardwick, Trieu Tran, Noah Hathout, Cole Knutsen

Date: 2024-10-04

### Summary

The goal of this quest was to build a cat behavior tracking device that was locally displayed the behavior and wrote data to a web ui.

# Desired Behaviors
1. Measure and classify movement:
    Track movement of the cat with accelerometer and classify based on movement data.

2. Provide continuous reporting of time, temperature, and activity to laptop:
    Measure time in minutes, hours, and seconds. Calculate temperature in engineering units send classified behavior to computer.

3. Alphanumeric display shows cat name and current activity and time in activity and togglable with button press:
    Local display of name, classified behavior, and elapsed time. Button available to change displayed information.

4. Data at laptop plotted as stripcharts:
    Data sent to laptop is displayed on web ui in legible charts.

### Solution Design
We had four tasks in our design:
1. Accelerometer Task
    - Makes four measurements over two seconds and averages x, y, and z acceleration.
    - Calculate pitch and roll with x, y, and z.
    - Classify behavior based on those averages.
    - Send data to function that waits for temperature and state.

2. Button Task
    - Keeps track of button press and circles display mode if press is found.

3. Display Task
    - Displays name if state requires.
    - Displays elapsed time with xTaskGetTickCount() if state requires.
    - Displays cats activity level if state requires.
    - Uses the i2c bus to communicate, and scrolls the message if need be. 

4. Temperature Task
    - Measures adc reading from A2 pin.
    - Uses Steinhart-Hart equation to convert from resistance to kelvins then to fahrenheit.
    - Again, using semaphores to prevent print_status() race conditions. 


Circuit diagram:
![circuit-diagram](https://github.com/BU-EC444/Quest2-Team6-Bardwick-Hathout-Knutsen-Tran/blob/main/quest2/circuit-diagram1.png)

Images of circuit:
![circuit1](https://github.com/BU-EC444/Quest2-Team6-Bardwick-Hathout-Knutsen-Tran/blob/main/quest2/circuit-image1.png)
![circuit2](https://github.com/BU-EC444/Quest2-Team6-Bardwick-Hathout-Knutsen-Tran/blob/main/quest2/circuit-image2.png)

### Quest Summary
Our approach was succesful. We found that accelerometer measured movement accurately and we were able to distinguish between sleeping, walking, and standing with reasonable consistency. The OLED also worked well. One thing that we might change in the future is to have the clock on the OLED show the current time rather than elapsed time, that might be a more useful function. You could also add a phone number for the cat's owner in case it gets lost.

### Supporting Artifacts
Everything that we needed for this quest was covered by the skills and we did not need to use external resources except code from ChatGPT that was present in our skills as well.

Link to report video:
- [Link to video demo](https://drive.google.com/file/d/1MHmP07e8tH0pH1_1BzXr1exhsDJ_sImG/view?usp=sharing). 

Link to design demo:
- [Link to video demo](https://drive.google.com/file/d/1kXdl_pqsfS58n2JaBD2br86vPUoF4mpm/view?usp=sharing).

### AI and Open Source Code Assertions

- We have documented in our code readme.md and in our code any software that we have adopted from elsewhere
- We used AI for coding and this is documented in our code as indicated by comments "AI generated" 
