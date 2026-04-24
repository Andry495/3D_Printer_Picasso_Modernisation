# BTT TFT35 (EXP1 + EXP2) и Klipper: «не видит принтер»

## В чём путаница

Подключение **двумя шлейфами EXP1 / EXP2** на Octopus — это режим **эмуляции классического 128×64** (часто в меню BTT называется **Marlin mode** / **Simulator**).  
Экран **не** «видит Marlin» по отдельному протоколу: **рисует и опрашивает дисплей сам Klipper** на MCU, если в **`printer.cfg`** задан блок **`[display]`**.

Сообщение **«нет принтера» / «No printer attached»** на TFT при **уже** подключённых шлейфах чаще всего значит одно из:

1. **На Octopus не работает связь Klipper** (`[mcu]` в ошибке, нет USB и т.д.) — тогда **нет** активного Klipper на плате, **дисплей нечем кормить**.
2. В **`printer.cfg` нет секции `[display]`** под **emulated_st7920** и пины **EXP** — Klipper **не** выводит картинку на этот режим TFT.
3. На самой **TFT** выбран **не** тот режим (нужен режим **12864 / Marlin emulation**, а не только сенсорный **Touch** по UART).

Отдельная **«прошивка осьминога ради дисплея»** не заменяет пункты 1–2: на MCU должна быть **прошивка Klipper**, а **дисплей** настраивается **в том же `printer.cfg`**, что и остальной принтер.

## Что сделать по шагам

### 1) Сначала Octopus + Pi

- Питание логики **по документации BTT** (для Pro обычно **не ниже ~15 V** на «материнский» вход; **12 V** на этот вход часто **недостаточно** — плата может не поднимать USB и логику нормально).
- **USB данные:** USB‑A на Raspberry → USB‑C **MCU** на Octopus.
- На Pi: `lsusb` видит **STM**; в Mainsail **`[mcu]`** с **`serial:`** из `/dev/serial/by-id/` и **Printer is ready** (или хотя бы стабильное соединение с MCU после **`FIRMWARE_RESTART`**).

### 2) Добавить `[display]` в `printer.cfg`

Ориентир по пинам — пример BTT для Octopus в репозитории Klipper: файл  
[`config/generic-bigtreetech-octopus-pro-v1.0.cfg`](https://github.com/Klipper3d/klipper/blob/master/config/generic-bigtreetech-octopus-pro-v1.0.cfg)  
(внизу там **`[board_pins]`** с алиасами **`EXP1_*` / `EXP2_*`**).

Типовой блок для **BTT TFT в режиме эмуляции 12864** (из [README BTT Octopus + Klipper](https://github.com/bigtreetech/BIGTREETECH-OCTOPUS-V1.0/blob/master/Firmware/Klipper/README.md), раздел *BigTreeTech TFT TouchScreen emulated 12864 mode*), **после** того как в конфиге уже есть **`[board_pins]`** с EXP или используются те же имена пинов, что в примере Octopus Pro:

```ini
[display]
lcd_type: emulated_st7920
spi_software_miso_pin: PA6
spi_software_mosi_pin: EXP1_3
spi_software_sclk_pin: EXP1_5
en_pin: EXP1_4
encoder_pins: ^EXP2_5, ^EXP2_3
click_pin: ^!EXP1_2

[output_pin beeper]
pin: EXP1_1
```

**Важно:** для **ревизии платы** (Pro **v1.0 / v1.0.1** vs **v1.1**) пины EXP **нельзя** слепо копировать с чужой схемы — сверяйте **`board_pins`** в **`generic-bigtreetech-octopus-pro-v1.0.cfg`** и **не** используйте пример **v1.1**, если у вас **не** эта плата (в официальном примере для Pro v1.0 есть предупреждение про **v1.1**).

### 3) Режим на самом TFT

В меню дисплея выберите режим **эмуляции 12864 / Marlin** (название зависит от прошивки панели), соответствующий работе **по EXP1+EXP2**.

### 4) Прошивка **панели** TFT (опционально)

Если меню глючит — обновите прошивку экрана с карты в **слоте TFT** из репозитория [BIGTREETECH-TouchScreenFirmware](https://github.com/bigtreetech/BIGTREETECH-TouchScreenFirmware). Это **не** заменяет **`[display]`** в Klipper и **не** то же самое, что прошивка **Octopus**.

## Кратко

| Проблема | Действие |
|----------|----------|
| «Нет принтера» при EXP | Проверить **`[mcu]`** и питание/напряжение платы; затем добавить **`[display]`** + **`board_pins`** под ваш Octopus Pro. |
| Нужна «прошивка с конфигом» | На MCU — **Klipper** (`firmware.bin` / сборка `make`); в Mainsail — **`printer.cfg`**, включая **`[display]`**, а не отдельный «конфиг только для дисплея» на SD осьминога. |

Дата: **апрель 2026**.
