# ESPHome Pellet Level Sensor Setup (macOS / Windows / Linux)

Guida per installare **ESPHome da zero su macOS, Windows o Linux** e caricare il firmware
su:

-   AZDelivery **D1 Mini ESP8266**
-   AZDelivery **VL53L0X ToF distance sensor**

## Hardware usato

-   D1 Mini ESP8266\
    https://www.amazon.it/dp/B01N9RXGHY

-   VL53L0X ToF sensor\
    https://www.amazon.it/dp/B086V37JJ7

------------------------------------------------------------------------

# Collegamenti hardware

![Collegamenti sensore](Collegamenti.jpeg)

| Pin D1 Mini | Pin sensore VL53L0X | Significato |
| --- | --- | --- |
| 5V | VIN | Alimentazione positiva a 5 volt |
| GND | GND | Massa / negativo dell'alimentazione |
| D1 | SCL | Clock del bus I²C (linea di sincronizzazione) |
| D2 | SDA | Dati del bus I²C (linea dati) |

------------------------------------------------------------------------

# 1 Installazione ESPHome (macOS / Windows / Linux)

ESPHome serve per:

- creare la configurazione firmware tramite file YAML
- compilare firmware per ESP8266/ESP32
- caricare il firmware via USB e fare aggiornamenti OTA
- esporre sensori e dispositivi in Home Assistant

## macOS

Installare `pipx`:

``` bash
brew install pipx
```

Aggiungere `pipx` al PATH:

``` bash
pipx ensurepath
exec $SHELL
```

Installare ESPHome e verificare:

``` bash
pipx install esphome
esphome version
```

## Windows (PowerShell)

Installare `pipx`:

``` powershell
py -m pip install --user pipx
py -m pipx ensurepath
```

Chiudere e riaprire PowerShell, poi installare ESPHome:

``` powershell
pipx install esphome
esphome version
```

## Linux

Installare `pipx` (Debian/Ubuntu):

``` bash
sudo apt update
sudo apt install -y pipx
```

Aggiungere `pipx` al PATH:

``` bash
pipx ensurepath
exec $SHELL
```

Installare ESPHome e verificare:

``` bash
pipx install esphome
esphome version
```

Se la tua distro non ha `pipx` nel package manager:

``` bash
python3 -m pip install --user pipx
python3 -m pipx ensurepath
```

------------------------------------------------------------------------

# 2 Creare cartella progetto

``` bash
mkdir -p ~/Documents/GitHub/esphome
```

``` bash
cd ~/Documents/GitHub/esphome
```

------------------------------------------------------------------------

# 3 Creare configurazione ESPHome

Lanciare il wizard:

``` bash
esphome wizard pellet.yaml
```

Durante il wizard inserire:

    board: d1_mini
    wifi ssid
    wifi password

------------------------------------------------------------------------

# 4 Collegare il D1 Mini via USB

Verificare la porta seriale:

``` bash
ls /dev/cu.*
```

Esempio output:

    /dev/cu.usbserial-11320

------------------------------------------------------------------------

# 5 Modificare configurazione

Aprire il file:

``` bash
vim pellet.yaml
```

Inserire questa configurazione:

``` yaml
i2c:
  sda: D2
  scl: D1
  scan: true
  id: bus_a

sensor:
  - platform: vl53l0x
    id: distance_sensor
    name: "Livello Pellet"
    address: 0x29
    update_interval: 10s
    long_range: false

  - platform: template
    id: livello
    name: livello pellet
    unit_of_measurement: '%'
    update_interval: 1s
    lambda: |-
      if (isnan(id(distance_sensor).state)) return 0;
      auto r = (id(distance_sensor).state - 0.8) * (100.0 - 0.0) / (0.1 - 0.8) + 0.0;
      if (r > 100) return 100;
      if (r < 0) return 0;
      return r;
```

------------------------------------------------------------------------

# 6 Compilare e caricare firmware

``` bash
esphome run pellet.yaml
```

Quando richiesto scegliere la porta USB:

    1

Esempio:

    [1] /dev/cu.usbserial-11320

------------------------------------------------------------------------

# 7 Verifica funzionamento

Nel log ESPHome dovresti vedere valori simili:

    Distanza pellet: 0.19 m
    Livello pellet: 86 %

------------------------------------------------------------------------

# 8 Integrazione con Home Assistant

Una volta connesso al WiFi:

    Impostazioni
    Dispositivi e Servizi
    ESPHome
    Aggiungi dispositivo

Inserire la **API key generata dal wizard**.

------------------------------------------------------------------------

# Sensori disponibili in Home Assistant

    sensor.pellet_distanza_pellet
    sensor.pellet_livello_pellet

------------------------------------------------------------------------

# Dashboard esempio

    gauge → livello pellet
    tile → distanza pellet
    history graph → consumo pellet
