# ESPHome Pellet Level Sensor Setup (Mac)

Guida per installare **ESPHome da zero su macOS** e caricare il firmware
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

![Collegamenti sensore](wemos_vl53l0x.jpg)

  D1 Mini   VL53L0X
  --------- ---------
  5V        VIN
  GND       GND
  D1        SCL
  D2        SDA

------------------------------------------------------------------------

# 1 Installazione ESPHome su Mac

Installare pipx:

``` bash
brew install pipx
```

Installare ESPHome:

``` bash
pipx install esphome
```

Aggiungere pipx al PATH:

``` bash
pipx ensurepath
```

Ricaricare la shell:

``` bash
exec $SHELL
```

Verificare installazione:

``` bash
esphome version
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
joe pellet.yaml
```

Inserire la configurazione sensore VL53L0X.

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
