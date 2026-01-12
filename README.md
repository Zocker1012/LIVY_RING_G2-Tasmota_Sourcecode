Mein Versuch einer aktuellen Tasmota Version mit möglichst allen nützlichen Sensoren integriert!

PIN-Layout ESP32-WROOM-32X
https://tasmota.github.io/docs/Pinouts/#esp32-wroom-32x

## Was funktioniert?
- [Tasmota](https://github.com/arendst/Tasmota) und alle seinen Funktionen :)
- LED Steurung (Rot,Grün,Blau) [Sichtbar am PIR Sensor] 
- Der Reset-Knopf für beliebige Funktion
- Temperatur & Luftfeuchtigkeitssensor
- Piezo per PWM ansteuerbar
- Mikrofon-Lautstärkepegel auslesbar
- Bewegungssensor
- Gas-Sensor
- Auslesen des Spannungschips


## Was funktioniert nicht?
:construction_worker: Energiesparfunktionen <- werden evtl. nachgereicht  
:construction_worker: Gas-Sensor Anzeige in ppm <- Work in progress, da brauche ich Hilfe  
:construction_worker: Aus/Einschalten der AirQuality-Sensoren (bei der aktivierung werden diese nicht mehr im i2c bus erkannt)  
:x: RTC, der Zeitgeber um Uhrzeit auch ohne WLAN zu aktualisieren <- Wird von mir nicht umgesetzt 


## Den Ring öffnen
- Plastikschutzfolio an der Unterseite des "RING" entfernen
- Vier Kreuzschlitzschrauben entfernen (am besten die Löcher mit einem Schranubenzieher ertasten)
- Ring entfernen

~~ Flashen ~~  
- gpio0 mit GND verbinden (am einfachsten das Gehäuse des PushButton als GND nehmen)
- ESP32 starten (USB Kabel verbinden)
- Led bleibt dauerhaft "grün" <- Flashmodus aktiv
- gpio0 Verbindung zu GND trennen
- gpio1 mit dem TTL-Modul RX verbinden
- gpio3 mit dem TTL-Modul TX verbinden
- Mit beliebigem Flasher flashen


## Einstellungen Tasmota  
~~  Voreingestellt ~~  
[SetOption114](https://tasmota.github.io/docs/Commands/#setoption114)  -  eingeschaltet um Switches von Relays zu trennen    
  
 ~~ Konsolen-Kommandos  ~~  
Um den Buzzer zu aktivieren:
```
setoption111 1
```   
Für die Beschriftung der Buttons in der UI  
```
WEBBUTTON1 PIR  
WEBBUTTON2 AIRQ  
WEBBUTTON3 MIC  
WEBBUTTON4 LED 
``` 

![Screenshot](livyringtasmotized.png)


## gpios and sensor

~~ :heavy_check_mark: Buttons ~~  
gpio35  -  RESET  
 
~~ :heavy_check_mark: Motion (PIR) PYQ 1548/7660 ~~   
gpio32  -  serial IN       OUTPUT  
gpio2   -  DirectLink      INPUT  
gpio27  -  Power 3,3v Sensor ON/OFF   **[RELAY 1]**  
ToDo  -  Neue xsns Lib überarbeiten, evtl. GIT Push  

~~ :heavy_check_mark: LED ~~   **[RELAY 4]**  
gpio21  -  RED LED Inverted  
gpio22  -  BLUE LED Inverted  
gpio4   -  GREEN LED Inverted  

~~ :heavy_check_mark: PIEZO ~~   
gpio16  -  Funktioniert als PWM Output 
Bemerkung  -  Wird immer als letztes Relay angezeigt/hinzugefügt... warum auch immer :)  

~~ :heavy_check_mark: Mikrofon I2S PDM pk0641ht4h ~~   
gpio17  -  Clock (I2S In SLCT)  
gpio5  -  Data  (I2S In Data)  
gpio13  -  Power 3,3v Microphone ON/OFF   **[RELAY 3]**   
gpio15  -  Clock over DS1099 IC (Muss low sein)    

~~ :heavy_check_mark: GAS SENSOR CCS801 ~~  
Sensor  -  TLA2024 (?ADS1115?)  
gpio33  -  Power 3,3v Sensor ON/OFF   **[RELAY 2]**  
i2c  -  Heater über MCP4706  
ToDo  -  Rückgabewert in ppm umwandeln 

~~ :heavy_check_mark: HDC1080 Temperatur und Luftfeuchtigkeit ~~   
i2c  -  Gruppe1  
gpio33  -  Power 3,3v Sensor ON/OFF   **[RELAY 2]**  
  
~~ :heavy_check_mark: LiPO Spannungsanzeige [LC709203F] ~~  
i2c  -  Gruppe2  
gpio23 -  low power alarm  
low power alarm gpio finden und testen 

~~ :x: RTC Clock (MCP7940M)  
i2c  -  Gruppe2  
Komplizierte RTC, hier ist es der Aufwand nicht Wert, mit Tasmota haben wir NTP.  

~~ :heavy_check_mark: i2c GRUPPE 2 ~~  
gpio14  -  SDA    
gpio12  -  SCL   
Found Devices:  
{"I2CScan":"Device(s) found at 0x0b 0x6f"}  
0x0b = LC709203F (LiPo-SPannungsanzeige)  
0x6f = MCP7940M (RTC Clock)

~~ :heavy_check_mark: i2c GRUPPE 1 ~~   
gpio19  -  SDA  
gpio18  -  SCL   
Found Devices:  
{"I2CScan":"Device(s) found at 0x40 0x48 0x60"}  
0x40 = HDC1080 Temp&Feuchtigkeit  
0x48 = TLA2024 (?ADS1115?) Analog zu DigitalWandler  
0x60 = MCP4706(A0T-E/MA) ->(INA) MCP602 (OUTA)-> Heater für Gas Sensor  



© Schnup89
