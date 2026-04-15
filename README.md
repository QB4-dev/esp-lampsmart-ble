# esp-lampsmart_ble (ESP-IDF component)

ESP-IDF component for pairing and controlling LampSmart BLE CCT lights using BLE advertisements.

## Features

- Pair and unpair command support
- Turn on and turn off support
- Cold/warm white level control (0-255)
- Supports protocol variants: V3, V2, V1A, V1B

## Installation

### Using ESP Component Registry

[![Component Registry](https://components.espressif.com/components/qb4-dev/esp-lampsmart-ble/badge.svg)](https://components.espressif.com/components/qb4-dev/esp-lampsmart-ble)

```bash
idf.py add-dependency "qb4-dev/esp-lampsmart-ble=*"
```

### Manual Installation

Clone this repository into your project's `components` directory:

```bash
cd your_project/components
git clone https://github.com/QB4-dev/esp-lampsmart-ble.git esp-lampsmart-ble
```

## ESP32 quick start

1. In your ESP-IDF project, set the target:

```bash
idf.py set-target esp32
```

2. Initialize and pair in your `app_main()`.
3. Build and flash:

```bash
idf.py -p /dev/ttyUSB0 flash monitor
```

## Minimal usage

```c
#include "lampsmart_ble.h"

void app_main(void) {
  lampsmart_ble_t light = NULL;
  lampsmart_ble_config_t cfg = LAMPSMART_BLE_CONFIG_DEFAULT();

  cfg.group_id = 0x12345678;          // shared group id used by your lamp(s)
  cfg.variant = LAMPSMART_VARIANT_3;  // try other variants if needed
  cfg.tx_duration_ms = 1000;

  ESP_ERROR_CHECK(lampsmart_ble_init(&light, &cfg));

  // Put the lamp in pairing mode (usually power-cycle and pair within a short window)
  ESP_ERROR_CHECK(lampsmart_ble_pair(light));

  ESP_ERROR_CHECK(lampsmart_ble_set_levels(light, 180, 30));
  vTaskDelay(pdMS_TO_TICKS(3000));

  ESP_ERROR_CHECK(lampsmart_ble_turn_off(light));
}
```

## Notes

- This component sends non-connectable BLE advertisements and does not open a BLE GATT connection.
- BLE commands are queued internally and transmitted asynchronously, so control calls return once the advertisement job is queued.
- Group addressing is used by this protocol, so multiple lamps in the same group can react together.
- If your lamp responds with swapped warm/cold channels, set `reversed_channels = true` in the config.

## References

Thanks to all people from this topic:
https://community.home-assistant.io/t/controlling-ble-ceiling-light-with-ha
