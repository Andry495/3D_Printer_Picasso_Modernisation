# Модернизация 3D принтера Picasso

Публичный репозиторий документации: [github.com/Andry495/3D_Printer_Picasso_Modernisation](https://github.com/Andry495/3D_Printer_Picasso_Modernisation)

Проект по модернизации принтера **Picaso 3D Designer XL**:

- переход на современную электронику (**BigTreeTech Octopus Pro**, в т.ч. ревизия **V1.0 / V1.0.1**);
- хост **Raspberry Pi 4B** и прошивка **Klipper**;
- конфигурация с **одним хотэндом**;
- сеть **Ethernet + Wi‑Fi**, при необходимости **USB‑камера**;
- список комплектующих, датчиков и поэтапная закупка.

## Состав репозитория

| Файл | Содержание |
|------|------------|
| [Комплект_электроники_3D_принтер.md](Комплект_электроники_3D_принтер.md) | Техническая спецификация, схемы подключения, Klipper, совместимость, BLTouch, MAX31855, ADXL345 (SPI), TFT35, ссылки на BTT/Klipper. |
| [Список_покупок_электроники.md](Список_покупок_электроники.md) | Полный BOM, модули **M0–M6**, ориентиры по ценам (актуальная оценка — внутри файла). |
| [Где_купить_компоненты.md](Где_купить_компоненты.md) | Поисковые запросы и магазины (РФ, AliExpress). |
| [Git_и_синхронизация_с_GitHub.md](Git_и_синхронизация_с_GitHub.md) | Где лежит проект (`D:\project\3D_printer`), команды `git pull` / `push`, временная папка-клон, отличие от корня `D:\project`. |
| [TFT35_EXP1_EXP2_Klipper.md](TFT35_EXP1_EXP2_Klipper.md) | Почему TFT пишет «нет принтера» при EXP1/EXP2: **`[display]`** в Klipper, питание MCU, ссылка на пример Octopus Pro. |

## Текущая конфигурация (сводка)

- **Плата:** BigTreeTech **Octopus Pro** (STM32F446; ревизии **V1.0 / V1.0.1** — сверять `printer.cfg` и **PINS.pdf** с GitHub BTT).
- **Хост:** Raspberry Pi **4B** (в т.ч. 4 ГБ ОЗУ).
- **Прошивка:** **Klipper** (MCU на Octopus + хост на Pi); веб — **Moonraker** + **Fluidd** или **Mainsail**.
- **Головка:** один хотэнд (**E3D V6 / Volcano** или аналог), желательно **24 V**, под **термопары K** и **MAX31855** (см. список покупок §5–§6, §10).
- **Дисплей:** BTT **TFT35 E3 V3.0** — **EXP1 + EXP2**; сенсорный режим — жгут **RS232** в **5‑пиновый разъём TFT** (на Octopus Pro обычно **слева от EXP1**, см. **PINS.pdf** в [BIGTREETECH-OCTOPUS-PRO](https://github.com/bigtreetech/BIGTREETECH-OCTOPUS-PRO)).
- **Датчики:**
  - **BLTouch** (или клон 3D Touch) — mesh стола и нуль по Z на стекле;
  - **ADXL345 по SPI на Octopus** — Input Shaper (см. основной файл);
  - **MAX31855 ×2** + **термопары K ×2** — стол и хотэнд;
  - оптические концевики **×6** (на первое время допустимы концевики Picaso — см. §8 списка);
  - датчик окончания филамента (опционально).

## Оценка бюджета

Ориентир по полному списку — в файле [Список_покупок_электроники.md](Список_покупок_электроники.md) (шапка документа и итоговая таблица по разделам).

## Первый запуск Klipper (краткий чеклист)

1. Образ **MainsailOS** / **FluiddPi** или **Raspberry Pi OS Lite** + [KIAUH](https://github.com/dw-0/kiauh) / [установка Klipper](https://www.klipper3d.org/Installation.html).
2. Сборка и прошивка **MCU Klipper** на Octopus ([документация Klipper](https://www.klipper3d.org/), примеры `config/` для Octopus Pro).
3. Базовый **`printer.cfg`** от примера BTT + правки под Picaso; калибровка **BLTouch**; проверка концевиков.
4. Подключение **MAX31855** и калибровка температур; затем **Input Shaper** с **ADXL345** (после опор, если принтер на колёсах).
5. Проверка датчика филамента (если установлен).

## Полезные ссылки

- [Klipper](https://www.klipper3d.org/) · [Moonraker](https://moonraker.readthedocs.io/) · [Mainsail](https://docs.mainsail.xyz/)
- [Репозиторий BTT Octopus Pro](https://github.com/bigtreetech/BIGTREETECH-OCTOPUS-PRO) (схемы, **PINS.pdf**)
- [Образы MainsailOS](https://github.com/mainsail-crew/mainsailOS/releases)

## Синхронизация с GitHub

Рабочий каталог с Git: **`D:\project\3D_printer`** (ветка **`main`**, remote на [3D_Printer_Picasso_Modernisation](https://github.com/Andry495/3D_Printer_Picasso_Modernisation)). Подробные команды и история (в т.ч. про папку `_3D_Printer_repo_work` и корень `D:\project`) — в [Git_и_синхронизация_с_GitHub.md](Git_и_синхронизация_с_GitHub.md).

Документация обновляется по мере сборки; дата правок в шапках `.md` — **апрель 2026** (где указано).
