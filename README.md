# ESP32 Cam driver for Edge detection and Image Processing

## General Information

This fork of the ESP32 Camera driver is intended to be used for edge detection and image processing at specified resolution in JPEG.

### Supported Soc

- ESP32

### Supported (tested) Sensor

| model   | max resolution | color type | output format                                                | Len Size |
| ------- | -------------- | ---------- | ------------------------------------------------------------ | -------- |
| OV2640  | 1600 x 1200    | color      | YUV(422/420)/YCbCr422<br>RGB565/555<br>8-bit compressed data<br>8/10-bit Raw RGB data | 1/4"     |


## Important to Remember

- Except when using CIF or lower resolution with JPEG, the driver requires PSRAM to be installed and activated.
- Using YUV or RGB puts a lot of strain on the chip because writing to PSRAM is not particularly fast. The result is that image data might be missing. This is particularly true if WiFi is enabled. If you need RGB data, it is recommended that JPEG is captured and then turned into RGB using `fmt2rgb888` or `fmt2bmp`/`frame2bmp`.
- When 1 frame buffer is used, the driver will wait for the current frame to finish (VSYNC) and start I2S DMA. After the frame is acquired, I2S will be stopped and the frame buffer returned to the application. This approach gives more control over the system, but results in longer time to get the frame.
- When 2 or more frame bufers are used, I2S is running in continuous mode and each frame is pushed to a queue that the application can access. This approach puts more strain on the CPU/Memory, but allows for double the frame rate. Please use only with JPEG.

## Installation Instructions


### Using with ESP-IDF

- Add as a submodule
  ```bash
  git submodule add link-to-this-repo components/esp32-cam-driver
  ```
- Enable PSRAM in `menuconfig` (also set Flash and PSRAM frequiencies to 80MHz)
- Include `esp_camera.h` in your code

Enable PSRAM on `menuconfig` or type it direclty on `sdkconfig`. Check the [official doc](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/kconfig.html#config-esp32-spiram-support) for more info.

```
CONFIG_ESP32_SPIRAM_SUPPORT=y
```