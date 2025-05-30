.. zephyr:board:: m5stickc_plus2

Overview
********

M5StickC PLUS, one of the core devices in M5Stacks product series, is an ESP32-based development board.

M5StickC PLUS features the following integrated components:

- ESP32-PICO-D4 chip (240MHz dual core, 600 DMIPS, 520KB SRAM, Wi-Fi)
- ST7789v2, LCD TFT 1.14", 135x240 px screen
- IMU MPU-6886
- SPM-1423 microphone
- RTC BM8563
- PMU AXP192
- 120 mAh 3,7 V battery

Some of the ESP32 I/O pins are broken out to the board's pin headers for easy access.

Functional Description
**********************

The following table below describes the key components, interfaces, and controls
of the M5StickC PLUS board.

.. _ST7789v2: https://m5stack.oss-cn-shenzhen.aliyuncs.com/resource/docs/datasheet/core/ST7789V.pdf
.. _MPU-6886: https://m5stack.oss-cn-shenzhen.aliyuncs.com/resource/docs/datasheet/core/MPU-6886-000193%2Bv1.1_GHIC_en.pdf
.. _ESP32-PICO-D4: https://m5stack.oss-cn-shenzhen.aliyuncs.com/resource/docs/datasheet/core/esp32-pico-d4_datasheet_en.pdf
.. _SPM-1423: https://m5stack.oss-cn-shenzhen.aliyuncs.com/resource/docs/datasheet/core/SPM1423HM4H-B_datasheet_en.pdf

+------------------+-------------------------------------------------------------------------+
| Key Component    | Description                                                             |
+==================+=========================================================================+
| 32.768 kHz RTC   | External precision 32.768 kHz crystal oscillator serves as a clock with |
|                  | low-power consumption while the chip is in Deep-sleep mode.             |
+------------------+-------------------------------------------------------------------------+
| ESP32-PICO-D4    | This `ESP32-PICO-D4`_ module provides complete Wi-Fi and Bluetooth      |
| module           | functionalities and integrates a 4-MB SPI flash.                        |
+------------------+-------------------------------------------------------------------------+
| Diagnostic LED   | One user LED connected to the GPIO pin.                                 |
+------------------+-------------------------------------------------------------------------+
| USB Port         | USB interface. Power supply for the board as well as the                |
|                  | communication interface between a computer and the board.               |
|                  | Contains: TypeC x 1, GROVE(I2C+I/O+UART) x 1                            |
+------------------+-------------------------------------------------------------------------+
| Power Switch     | Power on/off button.                                                    |
+------------------+-------------------------------------------------------------------------+
| A/B user buttons | Two push buttons intended for any user use.                             |
+------------------+-------------------------------------------------------------------------+
| LCD screen       | Built-in LCD TFT display \(`ST7789v2`_, 1.14", 135x240 px\) controlled  |
|                  | by the SPI interface                                                    |
+------------------+-------------------------------------------------------------------------+
| MPU-6886         | The `MPU-6886`_ is a 6-axis MotionTracking device that combines a       |
|                  | 3-axis gyroscope and a 3-axis accelerometer.                            |
+------------------+-------------------------------------------------------------------------+
| Built-in         | The `SPM-1423`_ I2S driven microphone.                                  |
| microphone       |                                                                         |
+------------------+-------------------------------------------------------------------------+


Start Application Development
*****************************

Before powering up your M5StickC PLUS, please make sure that the board is in good
condition with no obvious signs of damage.

System requirements
===================

Prerequisites
-------------

Espressif HAL requires WiFi and Bluetooth binary blobs in order work. Run the command
below to retrieve those files.

.. code-block:: console

   west blobs fetch hal_espressif

.. note::

   It is recommended running the command above after :file:`west update`.

Building & Flashing
*******************

.. zephyr:board-supported-runners::

Simple boot
===========

The board could be loaded using the single binary image, without 2nd stage bootloader.
It is the default option when building the application without additional configuration.

.. note::

   Simple boot does not provide any security features nor OTA updates.

MCUboot bootloader
==================

User may choose to use MCUboot bootloader instead. In that case the bootloader
must be built (and flashed) at least once.

There are two options to be used when building an application:

1. Sysbuild
2. Manual build

.. note::

   User can select the MCUboot bootloader by adding the following line
   to the board default configuration file.

   .. code:: cfg

      CONFIG_BOOTLOADER_MCUBOOT=y

Sysbuild
========

The sysbuild makes possible to build and flash all necessary images needed to
bootstrap the board with the ESP32 SoC.

To build the sample application using sysbuild use the command:

.. zephyr-app-commands::
   :tool: west
   :zephyr-app: samples/hello_world
   :board: m5stickc_plus2
   :goals: build
   :west-args: --sysbuild
   :compact:

By default, the ESP32 sysbuild creates bootloader (MCUboot) and application
images. But it can be configured to create other kind of images.

Build directory structure created by sysbuild is different from traditional
Zephyr build. Output is structured by the domain subdirectories:

.. code-block::

  build/
  ├── hello_world
  │  └── zephyr
  │       ├── zephyr.elf
  │       └── zephyr.bin
  ├── mcuboot
  │    └── zephyr
  │       ├── zephyr.elf
  │       └── zephyr.bin
  └── domains.yaml

.. note::

   With ``--sysbuild`` option the bootloader will be re-build and re-flash
   every time the pristine build is used.

For more information about the system build please read the :ref:`sysbuild` documentation.

Manual build
============

During the development cycle, it is intended to build & flash as quickly possible.
For that reason, images can be built one at a time using traditional build.

The instructions following are relevant for both manual build and sysbuild.
The only difference is the structure of the build directory.

.. note::

   Remember that bootloader (MCUboot) needs to be flash at least once.

Build and flash applications as usual (see :ref:`build_an_application` and
:ref:`application_run` for more details).

.. zephyr-app-commands::
   :zephyr-app: samples/hello_world
   :board: m5stickc_plus2/esp32/procpu
   :goals: build

The usual ``flash`` target will work with the ``m5stickc_plus2`` board
configuration. Here is an example for the :zephyr:code-sample:`hello_world`
application.

.. zephyr-app-commands::
   :zephyr-app: samples/hello_world
   :board: m5stickc_plus2/esp32/procpu
   :goals: flash

The default baud rate for the M5StickC PLUS is set to 1500000bps. If experiencing issues when flashing,
try using different values by using ``--esp-baud-rate <BAUD>`` option during
``west flash`` (e.g. ``west flash --esp-baud-rate 115200``).

You can also open the serial monitor using the following command:

.. code-block:: shell

   west espressif monitor

After the board has automatically reset and booted, you should see the following
message in the monitor:

.. code-block:: console

   ***** Booting Zephyr OS vx.x.x-xxx-gxxxxxxxxxxxx *****
   Hello World! m5stickc_plus2

Debugging
*********

M5StickC PLUS debugging is not supported due to pinout limitations.

Related Documents
*****************

- `M5StickC PLUS schematic <https://static-cdn.m5stack.com/resource/docs/products/core/m5stickc_plus2/m5stickc_plus_sch_03.webp>`_ (WEBP)
- `ESP32-PICO-D4 Datasheet <https://www.espressif.com/sites/default/files/documentation/esp32-pico-d4_datasheet_en.pdf>`_ (PDF)
- `M5StickC PLUS docs <https://docs.m5stack.com/en/core/m5stickc_plus2>`_
- `ESP32 Datasheet <https://www.espressif.com/sites/default/files/documentation/esp32_datasheet_en.pdf>`_ (PDF)
- `ESP32 Hardware Reference <https://docs.espressif.com/projects/esp-idf/en/latest/esp32/hw-reference/index.html>`_
