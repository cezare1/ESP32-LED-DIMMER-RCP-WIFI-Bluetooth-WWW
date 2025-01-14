# ESP32-LED-DIMMER-RCP-WIFI-Bluetooth-WWW
ESP32 LED DIMMER RCP WIFI Bluetooth WWW


### Overview

This program is designed to run on an ESP32 microcontroller, providing functionalities for WiFi connectivity, NTP time synchronization, motion detection, PWM control for LEDs, and Bluetooth communication. The program also includes a web server for managing settings and monitoring the device status.

### Technologies Used

1. **WiFi**: For connecting to a wireless network.
2. **NTPClient**: For synchronizing time with an NTP server.
3. **RTClib**: For managing the Real-Time Clock (RTC).
4. **WebServer**: For hosting a web server on the ESP32.
5. **EEPROM**: For storing persistent configuration data.
6. **BluetoothSerial**: For Bluetooth communication.
7. **NimBLE**: For Bluetooth Low Energy (BLE) functionalities.

### Program Logic

1. **Initialization**:
   - The program initializes the WiFi, NTP client, RTC, and EEPROM.
   - It sets up the PWM for LED control and configures the motion sensor pin.
   - It starts a web server to handle HTTP requests.
   - It initializes Bluetooth for serial communication.

2. **WiFi Connection**:
   - The program attempts to connect to a WiFi network using credentials stored in EEPROM.
   - If the connection fails, it retries after a short delay.

3. **NTP Synchronization**:
   - The program synchronizes the RTC with an NTP server at regular intervals.
   - If the NTP server is not reachable, it uses the RTC time.
   - It checks the difference between the RTC and NTP time and adjusts the RTC if the difference exceeds a specified threshold.

4. **Motion Detection**:
   - The program monitors a PIR motion sensor.
   - When motion is detected, it increases the PWM value to brighten the LED.
   - If no motion is detected for a specified period, it gradually dims the LED.

5. **Web Server**:
   - The web server provides an interface for viewing and updating configuration settings.
   - It displays the current time, WiFi status, and other relevant information.
   - It allows the user to reset the device or update settings via HTTP POST requests.

6. **Bluetooth Communication**:
   - The program listens for Bluetooth commands to update WiFi credentials or retrieve current settings.
   - It stores the new credentials in EEPROM and commits the changes.

### LED States and Modes

The LED control is divided into two main modes: **Day Mode** and **Night Mode**. The behavior of the LED is influenced by the state of the PIR motion sensor and the configured times for different states.

#### Day Mode
- **Active State**: When motion is detected by the PIR sensor, the LED brightens to the maximum PWM value configured for the day (`MAX_PWM_DZIENNE`).
- **Standby State**: If no motion is detected for a specified period (`CZAS_SWIECENIA_PO_WYKRYCIU_RUCHU_MS`), the LED dims to a standby PWM value (`PWM_STANDBY`).
- **Off State**: If no motion is detected for an extended period (`CZAS_SWIECENIA_W_STANDBY_MS`), the LED turns off.

#### Night Mode
- **Active State**: Similar to Day Mode, but the maximum PWM value is lower (`MAX_PWM_NOC`).
- **Standby State**: The LED dims to the standby PWM value (`PWM_STANDBY`).
- **Off State**: The LED turns off after the extended period of no motion detection.

### Configuration and Storage

The times and PWM values for the different states and modes are configurable via the web interface and stored in EEPROM. This ensures that the settings persist across device reboots.

### Day and Night Mode Configuration

- The user can configure the start and end times for day and night modes via the web interface.
- These times are compared with the current time obtained from the NTP server. If the NTP server is not available, the RTC time is used.
- The program switches between day and night modes based on these configured times.

### Security Measures

1. **Password Protection**:
   - The web server includes a form for resetting the device, which requires a correct login and password.

2. **EEPROM Validation**:
   - The program checks if the EEPROM contains valid data and sets default values if necessary.

3. **Bluetooth Command Validation**:
   - The program validates the length of the SSID and password received via Bluetooth to prevent buffer overflow.

### Functions

1. **setup()**:
   - Initializes all components and starts the web server and Bluetooth.

2. **loop()**:
   - Main loop that handles motion detection, NTP synchronization, Bluetooth commands, and web server requests.

3. **polaczZWIFI()**:
   - Connects to the WiFi network using stored credentials.

4. **synchronizujNTP()**:
   - Synchronizes the RTC with the NTP server.

5. **synchronizujCzasZNTP()**:
   - Forces an NTP update and adjusts the RTC.

6. **synchronizujRTCzNTP()**:
   - Checks the difference between RTC and NTP time and adjusts the RTC if necessary.

7. **wyslijDaneSerwerWWW()**:
   - Sends data to a web server at regular intervals.

8. **obslugaRuchu()**:
   - Handles motion detection and updates the PWM value for the LED.

9. **aktualizujPWM()**:
   - Updates the PWM value based on the time since the last motion detection.

10. **logDebugInfo()**:
    - Logs debug information if debug mode is enabled.

11. **handleRoot()**:
    - Handles the root HTTP request and serves the main web page.

12. **handleReset()**:
    - Handles the HTTP request for resetting the device.

13. **handleFakeSetup()**:
    - Handles the HTTP request for a fake setup page.

14. **handleUpdate()**:
    - Handles the HTTP request for updating configuration settings.

15. **saveVariablesToEEPROM()**:
    - Saves configuration variables to EEPROM.

16. **readEEPROM()**:
    - Reads configuration variables from EEPROM.

17. **handleBluetoothCommands()**:
    - Handles Bluetooth commands for updating WiFi credentials and retrieving current settings.

18. **ustawCzasUruchomienia()**:
    - Sets the startup time of the system.

19. **Shine_from_current_point_within_5SEC()**:
    - Gradually brightens the LED to the maximum PWM value within 5 seconds.

20. **wyslijDaneNaSerwer()**:
    - Sends data to a specified web server endpoint.

### Detailed Analysis of LED Control Logic

The LED control logic is sophisticated and allows for different behaviors during day and night modes. The program ensures energy efficiency by dimming or turning off the LED when no motion is detected. The brightness and duration for each state are configurable and stored in EEPROM.

#### Day Mode
- **Active State**:
  - When motion is detected by the PIR sensor, the LED brightens to the maximum PWM value configured for the day (`MAX_PWM_DZIENNE`).
  - The duration for which the LED remains bright is configurable (`CZAS_SWIECENIA_PO_WYKRYCIU_RUCHU_MS`).
- **Standby State**:
  - If no motion is detected for the configured duration, the LED dims to a standby PWM value (`PWM_STANDBY`).
  - The duration for which the LED remains in standby mode is configurable (`CZAS_SWIECENIA_W_STANDBY_MS`).
- **Off State**:
  - If no motion is detected for the extended period, the LED turns off.

#### Night Mode
- **Active State**:
  - Similar to Day Mode, but the maximum PWM value is lower (`MAX_PWM_NOC`).
  - This ensures that the LED is not too bright during the night.
- **Standby State**:
  - The LED dims to the standby PWM value (`PWM_STANDBY`).
- **Off State**:
  - The LED turns off after the extended period of no motion detection.

### NTP and RTC Synchronization

The program ensures accurate timekeeping by synchronizing the RTC with an NTP server. If the NTP server is not reachable, the program uses the RTC time. The synchronization logic includes:
- Regular synchronization with the NTP server.
- Checking the difference between RTC and NTP time.
- Adjusting the RTC if the difference exceeds a specified threshold.

### Conclusion

This program provides a comprehensive solution for managing an IoT device with WiFi, NTP, motion detection, PWM control, and Bluetooth functionalities. It includes robust security measures and a user-friendly web interface for configuration and monitoring. The LED control logic is sophisticated, allowing for different behaviors during day and night modes, and ensuring energy efficiency by dimming or turning off the LED when no motion is detected. The program also ensures accurate timekeeping by synchronizing the RTC with an NTP server and adjusting the time if necessary. The user can configure the start and end times for day and night modes, and these times are compared with the current time obtained from the NTP server or RTC.

Przegląd
Ten program został zaprojektowany do pracy na mikrokontrolerze ESP32, oferując funkcje łączności WiFi, synchronizacji czasu NTP, detekcji ruchu, sterowania PWM dla diod LED oraz komunikacji Bluetooth. Program zawiera również serwer internetowy do zarządzania ustawieniami i monitorowania statusu urządzenia.

Wykorzystane Technologie
WiFi: Do łączenia się z siecią bezprzewodową.
NTPClient: Do synchronizacji czasu z serwerem NTP.
RTClib: Do zarządzania zegarem czasu rzeczywistego (RTC).
WebServer: Do hostowania serwera internetowego na ESP32.
EEPROM: Do przechowywania trwałych danych konfiguracyjnych.
BluetoothSerial: Do komunikacji Bluetooth.
NimBLE: Do obsługi Bluetooth Low Energy (BLE).
Logika Programu
Inicjalizacja:

Program inicjalizuje WiFi, klienta NTP, RTC i EEPROM.
Ustawia PWM do sterowania diodami LED i konfiguruje pin czujnika ruchu.
Uruchamia serwer internetowy do obsługi żądań HTTP.
Inicjalizuje Bluetooth do komunikacji szeregowej.
Łączenie z WiFi:

Program próbuje połączyć się z siecią WiFi za pomocą danych zapisanych w EEPROM.
W przypadku niepowodzenia ponawia próbę po krótkim czasie.
Synchronizacja NTP:

Synchronizuje RTC z serwerem NTP w regularnych odstępach czasu.
Jeśli serwer NTP jest niedostępny, używa czasu z RTC.
Sprawdza różnicę między czasem RTC a NTP i koryguje RTC, jeśli różnica przekracza określony próg.
Detekcja Ruchu:

Program monitoruje czujnik ruchu PIR.
Po wykryciu ruchu zwiększa wartość PWM, aby rozjaśnić diodę LED.
Jeśli ruch nie zostanie wykryty przez określony czas, stopniowo przyciemnia diodę LED.
Serwer Internetowy:

Udostępnia interfejs do przeglądania i aktualizacji ustawień konfiguracyjnych.
Wyświetla aktualny czas, status WiFi i inne informacje.
Pozwala użytkownikowi resetować urządzenie lub aktualizować ustawienia za pomocą żądań HTTP POST.
Komunikacja Bluetooth:

Odbiera polecenia Bluetooth do aktualizacji danych WiFi lub pobierania aktualnych ustawień.
Zapisuje nowe dane w EEPROM i zatwierdza zmiany.
Tryby i Stany Diod LED
Sterowanie diodą LED jest podzielone na dwa główne tryby: Tryb Dzienny i Tryb Nocny. Zachowanie diody LED zależy od stanu czujnika ruchu PIR oraz skonfigurowanych czasów dla różnych stanów.

Tryb Dzienny
Stan Aktywny: Gdy czujnik PIR wykryje ruch, dioda LED rozjaśnia się do maksymalnej wartości PWM skonfigurowanej dla dnia (MAX_PWM_DZIENNE).
Stan Oczekiwania: Jeśli ruch nie zostanie wykryty przez określony czas (CZAS_SWIECENIA_PO_WYKRYCIU_RUCHU_MS), dioda LED przyciemnia się do wartości PWM_STANDBY.
Stan Wyłączony: Jeśli ruch nie zostanie wykryty przez dłuższy czas (CZAS_SWIECENIA_W_STANDBY_MS), dioda LED wyłącza się.
Tryb Nocny
Stan Aktywny: Podobnie jak w Trybie Dziennym, ale maksymalna wartość PWM jest niższa (MAX_PWM_NOC).
Stan Oczekiwania: Dioda LED przyciemnia się do wartości PWM_STANDBY.
Stan Wyłączony: Dioda LED wyłącza się po przedłużonym braku ruchu.
Konfiguracja i Przechowywanie
Czasy oraz wartości PWM dla różnych stanów i trybów są konfigurowalne za pomocą interfejsu internetowego i przechowywane w EEPROM. Dzięki temu ustawienia są zachowywane po ponownym uruchomieniu urządzenia.

Konfiguracja Trybów Dnia i Nocy
Użytkownik może skonfigurować godziny rozpoczęcia i zakończenia trybów dnia i nocy za pomocą interfejsu internetowego.
Czas uzyskany z serwera NTP (lub RTC w przypadku braku NTP) jest porównywany z tymi ustawieniami.
Program automatycznie przełącza tryby na podstawie skonfigurowanych godzin.
Środki Bezpieczeństwa
Ochrona Hasłem:

Formularz resetu urządzenia w serwerze internetowym wymaga poprawnego logowania i hasła.
Walidacja EEPROM:

Program sprawdza poprawność danych w EEPROM i ustawia wartości domyślne, jeśli jest to konieczne.
Walidacja Poleceń Bluetooth:

Program sprawdza długość SSID i hasła odbieranych przez Bluetooth, aby zapobiec przepełnieniu bufora.
Funkcje
setup(): Inicjalizuje wszystkie komponenty oraz uruchamia serwer internetowy i Bluetooth.
loop(): Główna pętla obsługująca detekcję ruchu, synchronizację NTP, polecenia Bluetooth i żądania HTTP.
polaczZWIFI(): Łączy z siecią WiFi za pomocą zapisanych danych.
synchronizujNTP(): Synchronizuje RTC z serwerem NTP.
obslugaRuchu(): Obsługuje detekcję ruchu i aktualizuje wartość PWM dla diody LED.
aktualizujPWM(): Aktualizuje wartość PWM w zależności od czasu od ostatniej detekcji ruchu.
logDebugInfo(): Loguje informacje debugowe, jeśli tryb debugowania jest włączony.
Podsumowanie
Program oferuje kompleksowe rozwiązanie do zarządzania urządzeniem IoT z funkcjami WiFi, NTP, detekcji ruchu, sterowania PWM oraz Bluetooth. Dzięki zaawansowanej logice sterowania diodami LED program zapewnia oszczędność energii oraz dostosowuje zachowanie urządzenia do pory dnia.
