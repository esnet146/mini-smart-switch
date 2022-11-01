# mini-smart-switch
Replacement the module WB2S with esp8285

Можно уйти от облачных сервисов без замены модуля! https://github.com/esnet146/mini-smart-switch2

Замена tuya модуля wb2s в переключателе под подрозетник на модуль esp-02s(esp8285) 
![photo1658734802](https://user-images.githubusercontent.com/64173457/180724032-31c21be3-d5d2-4619-838f-b1a06e06bd71.jpeg)

Модуль esp-02s можно заказать на али https://a.aliexpress.com/_Dnqn3gn

Было:
![photo1658733527](https://user-images.githubusercontent.com/64173457/180722581-d16b5f84-bb41-49df-99e4-ab74021093c8.jpeg)

Подключаем новый модуль к uart, gpio0 замыкаем на землю, подаем питание и прошиваем.
![photo1658733527 (1)](https://user-images.githubusercontent.com/64173457/180724598-77d4959f-9563-4868-887d-611c1a434c45.jpeg)


Выпаиваем модуль и припаиваем новый.
Стало:
![photo1658733527 (2)](https://user-images.githubusercontent.com/64173457/180724714-53df9a6b-9ded-4292-b609-d664916db38f.jpeg)

Код прошивки:
```

substitutions:
  board_name: "mini1"

esphome:
  name: $board_name
  platform: ESP8266
  board: esp01_1m
 

# disable logging
logger:
  baud_rate: 0

api:
  password: !secret passwordapi

ota:
  password: !secret passwordota

wifi:
  networks:
  - ssid: !secret wifi1
    password: !secret password1

web_server:
  port: 80
binary_sensor:
  - platform: gpio
    pin:
      number: GPIO14 # контакты выключателя
      mode: INPUT_PULLUP
      inverted: true
    name: switch_$board_name
    on_state:
    - light.toggle: light_1
    
  - platform: gpio
    pin:
      number: GPIO13 # кнопка
      mode: INPUT_PULLUP
      inverted: true
    name: key_$board_name
    on_press:
    - light.toggle: light_1

output:
  - platform: gpio
    pin: GPIO12 # релe
    id: relay

light:
  - platform: status_led
    name: Status_$board_name
    id: light_s
    internal: true
    pin:
      number: GPIO04 # индикатор
      inverted: true

  - platform: binary
    name: T1_$board_name
    id: light_1
    output: relay
    on_turn_on:
    - light.turn_off: light_s
    on_turn_off:
    - light.turn_on: light_s

button:
  - platform: restart
    name: Reset.$board_name
sensor:
  - platform: wifi_signal
    name: WiFi_Signal.$board_name
  - platform: uptime
    name: Uptime_Sensor_$board_name
    id: uptime_sensor

text_sensor:
  - platform: wifi_info
    ip_address:
      name: ESP IP Address.$board_name
    ssid:
      name: ESP Connected SSID.$board_name
    bssid:
      name: ESP Connected BSSID.$board_name
    mac_address:
      name: ESP Mac Wifi Address.$board_name
```
