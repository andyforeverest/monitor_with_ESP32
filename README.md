# EMonitor - Environment Monitor Device

*by Anju Chamantha*



## Content

[TOC]

## 1) EMonitor Device : features & specifications

<Photo of EMonitor Device >

<Solidworks design>



> *Scope of the Project*

EMonitor is a [ESP32](https://www.espressif.com/en/products/socs/esp32) microcontroller based device which has Wi-Fi inbuilt. With its Xtensa LX6 microprocessor operating at 240 MHz and memory of 520 KiB SRAM, EMonitor is more than capable of capturing the environment and sending data to the server after doing the necessary calculations seamlessly.  It runs on [FreeRTOS](https://www.iottechtrends.com/best-operating-systems-arduino/), a leading operating system supported by Arduino which can be working with the relevant peripherals smoothly.

### 1.1) Measuring Parameters

- Temperature (°C) 	- DHT11 Sensor module
- Humidity (%) 			- DHT11 Sensor module
- Pressure (Pa) 			- BMP180 Sensor module
- Light Level (%) 		  - LDR module

### 1.2) Power

The device can be powered using a Micro USB cable which is a very reliable mode of powering the ESP32 microcontroller. Since pretty much every standard mobile phone charging adapter outputs 5V, this device can be powered using the same way which means there's nothing to worry about power. Also EMonitor can be powered using any mobile phone charging power-bank which makes the device portable. 

The ESP32 is a low power consuming microcontroller and has adaptive power controlling mechanisms which can handle some basic power related matters that makes the EMonitor very reliable when powering with Micro USB cable.

### 1.3) Communication Protocol

Data is transmitted from the EMonitor device to the cloud hosted [EMonitor server](http://e-monitor.herokuapp.com/) using [CAP](http://docs.oasis-open.org/emergency/cap/v1.2/CAP-v1.2-os.html) (Common Alerting Protocol) over internet. CAP is an XML based data format for exchanging public warnings and emergencies between alerting technologies. 

### 1.4) Sensor Readings & Data sending to server

The sensor data (temperature, humidity, pressure, light level) are read at **T** time intervals and after **N** number of sensor readings the Mean & Standard Deviation of those N number of data are sent to the server using CAP protocol. So the data is sent to the server at **TN** time intervals. (There can be an average 2-3s delay other than TN due to the other logics and calculations)

*Example : Take N=15 & T=60s => The a sensor reading is taken for every 1 minute and after 15 rounds (after 15 minutes) the mean and standard deviation of those 15 samples are send to the server.*

Following are the basic data sent to the server as a message which is referred as `MSG` hereafter.

`msg_id, timestamp, temperature, humidity, pressure, light, temperature_sd, humidity_sd, pressure_sd, light_sd`

Other than these data, some constant data that are mandatory for CAP like sender,msg type, category, urgency etc are sent to the server.



### 1.5)Connection Lost Self-Recovery

The EMonitor device is capable of withstand the following connection lost scenarios,

- Wi-Fi connection Lost 
- Lost of connection to the EMonitor server

The device can self re-connect to Wi-Fi or self re-connect to server after any of the above cases. But the data is secure even after such connection issue.

In both above cases EMonitor detects the connection issue and cash the raw data which were not able to send to the server due to connection lost to a buffer. After each TN time interval, the device checks the connection and if the connection is stable, it sends all the data which were buffered to the server and clears the buffer. (During this process also if the connection is lost, it keeps the data safely in the buffer)



## 2) Server & Database : features & specifications

**Deployed EMonitor website** : **[http://e-monitor.herokuapp.com/](http://e-monitor.herokuapp.com/)**

The backend server is developed using [Flask](https://flask.palletsprojects.com/en/1.1.x/) which is a Python based web framework. It supports all the existing python libraries and tools and hence it is very much scalable and extensible. 

The database used is [PostgreSQL](https://www.postgresql.org/) and all the data that comes to the server from the EMonitor device are saved in a database table of values, `msg_id, timestamp, temperature, humidity, pressure, light, temperature_sd, humidity_sd, pressure_sd, light_sd`

Backend supports the following end-points to communicate with it.

- [POST] : /data 
- [GET] : /

The EMonitor device uses `/data` end-point to send data to the server using CAP protocol. 

`/` endpoint is used to access the main dashboard of the EMonitor website, which shows the current(last received) readings as well as charts and the data of last 20 readings received.

<Screenshot of Dashboard>



## 3) Special features

### 3.1) OLED Display to view Readings and Connection States

<Photo of OLED display>

OLED Display shows the temperature, humidity, pressure and light level values which are sent to the server with the `msg_id` after each TN time interval. Also it shows the Wi-Fi and Server connection statuses.(0 - Not connected, 1 - Connected)

 It also shows if a `MSG` is not sent to the server and buffered (using a `*` symbol)

### 3.2) Automatic warning Email sending through EMonitor Device

<Screenshot of EMail>

The EMonitor device sends a WARNING e-mail to a pre defined  e-mail address if a value of a `MSG` exceeds a given threshold limit. 

*Currently if the temperature mean exceeds 40°C it sends a warning e-mail to my personal email.*



## 4) Design Architecture & Diagram

<high level architecture diagram : modules components server db>

## 5) Schematic diagram of EMonitor Device

<schematic diagram of device>



## 6) Fault Recovery Implementation

As mentioned in *1.5) Connection Lost Self-Recovery*, EMonitor is capable of self connecting to Wi-Fi & Server after a connection lost. During the connection lost period, the MSGs are buffered and when the connection is established back, it sends the buffered MSGs to the server.

### 6.1) Auto connect to Wi-Fi

<Pseodo code of wifi auto connect>



### 6.2) MSG Buffering

<Pseodo code of buffering process>



## 7) List of components and their cost 

- NodeMCU ESP-32S Microcontroller (LKR 1,050.00)
- BMP180 Digital Barometric Pressure Sensor (LKR 185.00)
- DHT11 Temperature & Relative Humidity Sensor (LKR 250.00)
- LDR (LKR 10)
- OLED Display 0.96" (LKR 600.00)
- Resistor 220Ohm (LKR 1.00)
- Capacitor 10 micro F (LKR 20)
- Vero Board & Circuit Wires (LKR 150)



## 8) Algorithm used for device and server (Pseudo code)

### 8.1) EMonitor Device Algorithm



### 8.2) Backend Server Logic



## 9) Full source code (an Annexure)