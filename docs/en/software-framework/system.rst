System
======

:link_to_translation:`zh_CN:[中文]`

.. raw:: html

   <style>
   body {counter-reset: h2}
     h2 {counter-reset: h3}
     h2:before {counter-increment: h2; content: counter(h2) ". "}
     h3:before {counter-increment: h3; content: counter(h2) "." counter(h3) ". "}
     h2.nocount:before, h3.nocount:before, { content: ""; counter-increment: none }
   </style>

--------------

Is it possible to compile the binaries in application layer and bottom layer separately?
--------------------------------------------------------------------------------------------

  No, they cannot be compiled separately.

--------------

My application does not really need the watchdog timer, can I disable it?
----------------------------------------------------------------------------

  The current SDK allows disabling the software watchdog only. The following methods can be taken to avoid watchdog reset when user program occupies CPU for too long:

  - If your routine needs a time frame of duration between software reset and hardware watchdog reset, you may use system_soft_wdt_stop () to disable the software watchdog. After the program has been executed, you can restart the software watchdog with system_soft_wdt_restart ().
  - You may feed the watchdog in between your codes by adding system_soft_wdt_feed () so that the watchdog is updated before it issues a reset.

  The hardware watchdog interrupt interval is 0.8*2048 ms, that is 1638.4 ms. The interrupt handling interval is 0.8*8192 ms, equal to 6553.6 ms. The interrupt handling interval is the time limit to feed the watchdog after the interrupt occurs. If the interrupt handling interval expires, it will trigger a hardware watchdog reset. As a result, in the cases where there is only hardware watchdog, if a program runs for over 6553.6 ms, then it could cause a hardware watchdog reset. If the program runs for over 8192 ms, then it will invoke a watchdog reset for sure.

  The software watchdog is based on MAC timer and task arrangement. The interrupt interval is 1600 ms, so is the interrupt handling interval. As a result, in the cases where there are both software and hardware watchdogs, if a program runs for over 1600 ms, it could cause a software watchdog reset. If the program runs for over 3200 ms, it will invoke a watchdog reset for sure.

--------------

What are the differences between RTOS SDK and Non-OS SDK?
-----------------------------------------------------------
  The main differences are as follows:

  - Non-OS SDK

     Non-OS SDK uses timers and callbacks as the main way to perform various functions - nested events and functions triggered by certain conditions. Non-OS SDK uses the espconn network interface; users need to develop their software according to the usage rules of the espconn interface.

  - RTOS SDK

     1. FreeRTOS SDK is based on FreeRTOS , a multi-tasking OS. You can use the standard FreeRTOS interfaces to realize resource management, recycling operations, execution delay, inter-task messaging and synchronization, and other task-oriented process design approaches. For the specifics of interface methods, please refer to the official website of FreeRTOS or the book USING THE FreeRTOS REAL TIME KERNEL-A Practical Guide.
     2. The network operation interface in RTOS SDK is the standard lwIP API. RTOS SDK provides a package which enables BSD Socket API interface. Users can directly use the socket API to develop software applications; and port other applications from other platforms using socket API to ESP8266, effectively reducing the learning and development cost arising from platform switch.
     3. RTOS SDK introduces cJSON library whose functions make it easier to parse JSON packets.
     4. RTOS is compatible with Non-OS SDK in Wi-Fi interfaces, SmartConfig interfaces, Sniffer related interfaces, system interfaces, timer interface, FOTA interfaces and peripheral driver interfaces, but does not support the AT implementation.

--------------

Why do I get compile errors when using IRAM_ATTR in Non-OS SDK?
-------------------------------------------------------------------

  The default function attribute is IRAM_ATTR in Non-OS SDK. Therefore, if you want the function to reside in IRAM, please leave out the ``ICACHE_FLASH_ATTR`` attribution in the function definition/declaration.

--------------

Where is main function in ESP8266?
-----------------------------------

  - ESP8266 SDK does not provide main function.
  - Main function is stored in first-stage bootloader in ROM, which is used to load second-stage bootloader.
  - The entry function of the second-stage bootloader is ets_main. After startup, the user_init in the user application will be loaded to lead the user to the program.
