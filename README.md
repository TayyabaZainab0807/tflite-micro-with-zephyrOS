# ZephyrOS with Updated TFLM

This guide explains how to download and set up ZephyrOS with the latest TFLM (TensorFlow Lite for Microcontrollers) on your system. Follow the steps below:

## Downloading ZephyrOS

1. Download Zephyr by following the instructions provided [here](https://docs.zephyrproject.org/latest/develop/getting_started/index.html).
2. Once downloaded, run the Blinky example to ensure everything is working correctly.

## Downloading the Latest TFLM

1. Install `gmake` using Homebrew:
`brew install gmake`
2. Clone the TFLM repository:
  `git clone https://www.github.com/tensorflow/tflite-micro.git`
3. Navigate to the TFLM directory:
`cd tflite-micro`
4. Build TFLM using the provided Makefile:
  `gmake -f tensorflow/lite/micro/tools/make/Makefile`

If you want to build TFLM for the Cortex-M4 processor:
- Build TFLM with the specified target and target architecture: `gmake -f tensorflow/lite/micro/tools/make/Makefile TARGET=cortex_m_generic TARGET_ARCH=cortex-m4 third_party_downloads`

- `gmake -f tensorflow/lite/micro/tools/make/Makefile TARGET=cortex_m_generic TARGET_ARCH=cortex-m4 OPTIMIZED_KERNEL_DIR=cmsis_nn microlite`

replace m4 by m33 if you want to build for nrf53

## Building and Deploying ZephyrOS with TFLM

1. Copy the `hello_world` folder from `hello_world_1.1` and place it in the following directory: `zephyrproject/zephyr/samples/basic`.
2. Build the project using the following commands:
   
   `cd ~/zephyrproject`   
   `west build -p always -b nrf5340dk_nrf5340_cpuapp zephyr/samples/basic/hello_world/`

3. Make sure to edit the path to your latest TFLM in the `CMakeLists.txt` file and check the `target_arch` (for nRF5340 or nRF52, it should be `cortex-m33` or `cortex-m4`).
4. Deploy the built project using the following command: `west flash`
 
Once the above steps are completed successfully, follow these additional instructions to replace the TFLM image with the latest version:

1. Copy the latest TFLM image and replace the existing one in the following directory: `zephyrproject/modules/lib/tflite-micro`.

2. Copy the `hello_world_1.2` file and place it in the following directory: `zephyrproject/zephyr/samples/basic`.
3. Build the updated project using the following command: `west build -p always -b nrf5340dk_nrf5340_cpuapp zephyr/samples/basic/hello_world_1.2/`
4. You may see an error for fixedpoint.h:

- copy fixedpoint folder and detect_platform.h from tflite-micro/tensorflow/lite/micro/tools/make/downloads/gemmlowp/internal and copy it to tflite-micro/tensorflow/lite/kernels/internal
- now open the fixedpoint/fixedpoint.h and change the path of detect_platform.h from ../internal/detect_platform.h to ../detect_platform.h

 

5. You may see an error for DWT not defined:
- something is wrong witht he gmake, clean the tflite-micro build and repeat the following commands:
  `gmake -f tensorflow/lite/micro/tools/make/Makefile`
  `gmake -f tensorflow/lite/micro/tools/make/Makefile TARGET=cortex_m_generic TARGET_ARCH=cortex-m4 third_party_downloads`
  `gmake -f tensorflow/lite/micro/tools/make/Makefile TARGET=cortex_m_generic TARGET_ARCH=cortex-m4 OPTIMIZED_KERNEL_DIR=cmsis_nn microlite`

 
6. Make sure to edit the path to your latest TFLM in the `CMakeLists.txt` file and check the `target_arch` (for nRF5340 or nRF52, it should be `cortex-m4`).

7. Deploy the updated project using the following command: `west flash`
   
## Viewing the Output

To view the output, you can use the `picocom` tool:

1. Install `picocom` using Homebrew: `brew install picocom`
2. Run `picocom` with the appropriate settings: `picocom -fh -b 115200 --imap lfcrlf /dev/tty…`

## Building TFLM for Cortex-M7 

If you want to build TFLM for the Cortex-M7 processor:

1. Navigate to the TFLM directory:
   `cd ~ `
    `cd tflite-micro`

2. Clean the previous build (if necessary): `gmake -f tensorflow/lite/micro/tools/make/Makefile clean`
  
3. Build TFLM with the specified target, target architecture, and CMSIS-NN enabled: `gmake -f tensorflow/lite/micro/tools/make/Makefile TARGET=cortex_m_generic OPTIMIZED_KERNEL_DIR=cmsis_nn TARGET_ARCH=cortex-m7 microlite`

Note: If using the latest TFLM and building without CMSIS-NN, make sure to comment out the line `OPTIMIZED_KERNEL_DIR=cmsis_nn` in CMakeLists.txt
















