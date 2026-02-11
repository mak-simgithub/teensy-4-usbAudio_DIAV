# Extension of the T4.x USB audio input and output. #
This suite of files enables 2-, 4-, 6- or 8-channel USB I/O to be used with the Teensy 4.x Audio library. It has been developed based on Teensyduino 1.59, so no representations are made as to whether it might work starting from older versions!

## Installation ##
* Locate the core files folder, e.g. for Arduino 1.8.19 on Linux `/.arduino15/packages/teensy/hardware/avr/1.59.0/`

and copy repo into it.

WHEN USING MAKE SURE LINUX DID NOT MUTE THE TEENSY!


## In use ##
* In the updated Design Tool, place one usb, usb_quad, usb_hex or usb_oct input and/or output object(s) in your design, and export as usual
* In the Tools menu, ensure you have configured the `USB Type` to include Audio, and select the correct number of `USB channels`
* If your sketch uses a USB I/O object that is wider than configured, you will get a compile-time error - usually *many* errors
* Windows is very bad at detecting changes to the USB descriptor: see the examples for how to use the `set_usb_string_serial_number.h` file to change the serial number according to the channel count and sample rate, which seems to force Windows to recognise the changes
* In addition to the USB channel count, the Tools menu also has options to
  * set the sample rate to 44.1kHz, 48kHz or 96kHz: these seem to work OK, but may not be supported by all audio hardware
  * set the audio block sample count to 128 (normal), 16, or 256 samples. This is highly experimental, and many audio objects work badly if the sample count is changed. It is hoped that future revisions of the Audio library will be more resilient to changing this parameter, which will be of use for (a) low-latency applications using 16-sample blocks, or (b) keeping the audio interrupt rate reasonable at 96kHz  sample rate by using 256-sample blocks

## Examples ##  
Examples can be found in `main_usbOutput.cpp` and `main_usbInput.cpp` 

## Technical details ## 
Main features are:
- Switched from UAC1 to UAC2 standard.
- 8 channels can be streamed from and to the USB host. (Change `USB_AUDIO_NO_CHANNELS_480` in `usb_desc.h` if you can't get the updated Tools options to work. For more than 8 channels one need to extend `CHANNEL_CONFIG_480` and the feature unit descriptor in `usb_desc.c`)
- 16 or 24bit pcm audio can be streamed. (Change `AUDIO_SUBSLOT_SIZE` in `usb_desc.h` if needed. `AUDIO_SUBSLOT_SIZE 2` means each sample is 2bytes large, `AUDIO_SUBSLOT_SIZE 3` changes the sample size to 3bytes/24bit)
- Feedback of the usb input to the host improved in order to prevent buffer under- and overruns
- USB output is able to duplicate or skip single samples in order to prevent buffer under- and overruns. This is not a perfect solution, but is an improvement to the current implementation.
- USB input: Parameters of the PI controller that computes the feedback can optionally be set at the constructor.
- USB input and output: The target number of buffered samples can be configured. (Can e.g. be increased if buffer under- or overruns occur.)
- USB input and ouput provide information about their status (getStatus) like if and how many buffer over- and under-runs occurred.


Tested with Teensyduino 1.59 + Arduino IDE being 1.8.19 and Visual Studio Code + Platformio/Teensy platform version 5.0.0

