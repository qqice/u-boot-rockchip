.. SPDX-License-Identifier: GPL-2.0+ OR BSD-3-Clause
.. sectionauthor:: Neha Malcom Francis <n-francis@ti.com>

Keystone II EVM Generation
==========================

Summary
-------

This README has information on the U-Boot port for K2HK, K2E, and K2L EVM boards.
Documentation for this board can be found at:

 - http://www.advantech.com/Support/TI-EVM/EVMK2HX_sd.aspx
 - https://www.einfochips.com/index.php/partnerships/texas-instruments/k2e-evm.html
 - https://www.einfochips.com/index.php/partnerships/texas-instruments/k2l-evm.html

The K2HK board is based on Texas Instruments Keystone2 family of SoCs: K2H, K2K.
More details on these SoCs are available at company websites:

K2K: https://www.ti.com/product/tci6638k2k
K2H: https://www.ti.com/product/tci6638k2h

The K2E SoC details are available at
 https://www.ti.com/lit/ds/symlink/66ak2e05.pdf

The K2L SoC details are available at
 https://www.ti.com/lit/ds/symlink/tci6630k2l.pdf

The K2G SoC details are available at
 https://www.ti.com/lit/ds/symlink/66ak2g02.pdf

Board Configuration
-------------------

Some of the peripherals that are configured by U-Boot

.. list-table::
   :widths: 10 10 10 10 10 10 10 10
   :header-rows: 1

   * -
     - DDR3
     - NAND
     - MSM SRAM
     - ETH Ports
     - UART
     - I2C
     - SPI
   * - K2HK
     - 2
     - 512MB
     - 6MB
     - 4(2)
     - 2
     - 3
     - 3
   * - K2E
     - 4
     - 512MB
     - 2MB
     - 8(2)
     - 2
     - 3
     - 3
   * - K2L
     - 2
     - 512MB
     - 2MB
     - 4(2)
     - 4
     - 3
     - 3
   * - K2G
     - 2
     - 256MB
     - 1MB
     - 1
     - 1
     - 1
     - 1

There are only 2 eth port installed on the boards.

There are separate PLLs to drive clocks to Tetris ARM and Peripherals.
To bring up SMP Linux on this board, there is a boot monitor
code that will be installed in MSMC SRAM. There is command available
to install this image from U-Boot.

The port related files can be found at following folders:
 - keystone2 SoC related files: arch/arm/cpu/armv7/keystone/
 - EVMs board files: board/ti/k2s_evm/

Board configuration files:
 - include/configs/k2hk_evm.h
 - include/configs/k2e_evm.h
 - include/configs/k2l_evm.h
 - include/configs/k2g_evm.h

As U-Boot is migrating to Kconfig there is also board defconfig files
 - configs/k2e_evm_defconfig
 - configs/k2hk_evm_defconfig
 - configs/k2l_evm_defconfig
 - configs/k2g_evm_defconfig

Supported boot modes:
 - SPI NOR boot
 - AEMIF NAND boot (K2E, K2L and K2HK)
 - UART boot
 - MMC boot (Only on K2G)

Supported image formats:
 - u-boot.bin: for loading and running u-boot.bin through Texas Instruments Code
   Composer Studio (CCS) and for UART boot.
 - u-boot-spi.gph: gpimage for programming SPI NOR flash for SPI NOR boot
 - MLO: gpimage for programming NAND flash for NAND boot, MMC boot.

Build Instructions
------------------

Examples for k2hk, for k2e, k2l and k2g just replace k2hk prefix accordingly.
Don't forget to add CROSS_COMPILE.

To build u-boot.bin, u-boot-spi.gph, MLO:

.. prompt:: bash
   :prompts: $

   make k2hk_evm_defconfig
   make

Load and Run U-Boot on Keystone EVMs using CCS
----------------------------------------------

Need Code Composer Studio (CCS) installed on a PC to load and run u-boot.bin
on EVM? See instructions at below link for installing CCS on a Windows PC.

        `<http://processors.wiki.ti.com/index.php/MCSDK_UG_Chapter_Getting_Started#Installing_Code_Composer_Studio>`_

Use u-boot.bin from the build folder for loading and running U-Boot binary
on EVM. Follow instructions at:

 - K2HK http://processors.wiki.ti.com/index.php/EVMK2H_Hardware_Setup
 - K2E  http://processors.wiki.ti.com/index.php/EVMK2E_Hardware_Setup
 - K2L  http://processors.wiki.ti.com/index.php/TCIEVMK2L_Hardware_Setup
 - K2G  http://processors.wiki.ti.com/index.php/66AK2G02_GP_EVM_Hardware_Setup

to configure SW1 dip switch to use "No Boot/JTAG DSP Little Endian Boot Mode"
and Power ON the EVM.  Follow instructions to connect serial port of EVM to
PC and start TeraTerm or Hyper Terminal.

Start CCS on a Windows machine and Launch Target configuration as instructed at

        `<http://processors.wiki.ti.com/index.php/MCSDK_UG_Chapter_Exploring#Loading_and_Running_U-Boot_on_EVM_through_CCS>`_

The instructions provided in the above link uses a script for
loading the U-Boot binary on the target EVM. Instead do the following:-

#. Right click to "Texas Instruments XDS2xx USB Emulator_0/CortexA15_1 core (D
   is connected: Unknown)" at the debug window (This is created once Target
   configuration is launched) and select "Connect Target".
#. Once target connect is successful, choose Tools->Load Memory option from the
   top level menu. At the Load Memory window, choose the file u-boot.bin
   through "Browse" button and click "next >" button. In the next window, enter
   Start address as 0xc000000, choose Type-size "32 bits" and click "Finish"
   button.
#. Click View -> Registers from the top level menu to view registers window.
#. From Registers, window expand "Core Registers" to view PC. Edit PC value
   to be 0xc000000. From the "Run" top level menu, select "Free Run"
#. The U-Boot prompt is shown at the Tera Term/ Hyper terminal console as
   below and type any key to stop autoboot as instructed.

.. code-block:: bash

  U-Boot 2014.04-rc1-00201-gc215b5a (Mar 21 2014 - 12:47:59)

  I2C:   ready
  Detected SO-DIMM [SQR-SD3T-2G1333SED]
  DRAM:  1.1 GiB
  NAND:  512 MiB
  Net:   K2HK_EMAC
  Warning: K2HK_EMAC using MAC address from net device
  , K2HK_EMAC1, K2HK_EMAC2, K2HK_EMAC3
  Hit any key to stop autoboot:  0

SPI NOR Flash Programming Instructions
--------------------------------------

U-Boot image can be flashed to first 512KB of the NOR flash using following
instructions:

1. Start CCS and run U-Boot as described above.
2. Suspend Target. Select Run -> Suspend from top level menu
   CortexA15_1 (Free Running)"
3. Load u-boot-spi.gph binary from build folder on to DDR address 0x87000000
   through CCS as described in step 2 of "Load and Run U-Boot on K2HK/K2E/K2L
   EVM using CCS", but using address 0x87000000.
4. Free Run the target as described earlier (step 4) to get U-Boot prompt
5. At the U-Boot console type following to setup U-Boot environment variables.

.. prompt:: bash
   :prompts: =>

   setenv addr_uboot 0x87000000
   setenv filesize <size in hex of u-boot-spi.gph rounded to hex 0x10000>
   run burn_uboot_spi

Once U-Boot prompt is available, power off the EVM. Set the SW1 dip switch to
"SPI Little Endian Boot mode" as per instruction at

        `<http://processors.wiki.ti.com/index.php/*_Hardware_Setup>`_

6. Power ON the EVM. The EVM now boots with U-Boot image on the NOR flash.

AEMIF NAND Flash programming instructions
-----------------------------------------

U-Boot image can be flashed to first 1024KB of the NAND flash using following
instructions:

1. Start CCS and run U-Boot as described above.
2. Suspend Target. Select Run -> Suspend from top level menu
   CortexA15_1 (Free Running)"
3. Load MLO binary from build folder on to DDR address 0x87000000
   through CCS as described in step 2 of "Load and Run U-Boot on K2HK EVM
   using CCS", but using address 0x87000000.
4. Free Run the target as described earlier (step 4) to get U-Boot prompt
5. At the U-Boot console type following to setup U-Boot environment variables.

.. prompt:: bash
   :prompts: =>

   setenv filesize <size in hex of MLO rounded to hex 0x10000>
   run burn_uboot_nand

Once U-Boot prompt is available, Power OFF the EVM. Set the SW1 dip switch to
"ARM NAND Boot mode" as per instruction at

        `<http://processors.wiki.ti.com/index.php/>`_

under Hardware Setup

6. Power ON the EVM. The EVM now boots with U-Boot image on the NAND flash.

Load and Run U-Boot on keystone EVMs using UART download
--------------------------------------------------------

Open BMC and regular UART terminals.

1. On the regular UART port start xmodem transfer of the u-boot.bin
2. Using BMC terminal set the ARM-UART bootmode and reboot the EVM

.. prompt:: bash

   BMC> bootmode #4
   MBC> reboot

3. When xmodem is complete you should see the U-Boot starts on the UART port

Load and Run U-Boot on K2G EVMs using MMC
-----------------------------------------

Open BMC and regular UART terminals.

1. Set the SW3 dip switch to "ARM MMC Boot mode" as per instruction at

        `<http://processors.wiki.ti.com/index.php/66AK2G02_GP_EVM_Hardware_Setup>`_

2. Create SD card partitions as per steps given in Hardware Setup Guide.
3. Copy MLO to Boot Partition.
4. Insert SD card and Power on the EVM.
   The EVM now boots with U-Boot image from SD card.

Secure Boot
-----------

.. include:: am335x_evm.rst
   :start-after: .. secure_boot_include_start_config_ti_secure_device
   :end-before: .. secure_boot_include_end_config_ti_secure_device

.. include:: am335x_evm.rst
   :start-after: .. secure_boot_include_start_spl_boot
   :end-before: .. secure_boot_include_end_spl_boot

<IMAGE_FLAG> is currently ignored and reserved for future use.

<INPUT_FILE> is the full path and filename of the public world boot
loader binary file (only u-boot.bin is currently supported on
Keystone2 devices, u-boot-spl.bin is not currently supported).

<OUTPUT_FILE> is the full path and filename of the final secure image.
The output binary images should be used in place of the standard
non-secure binary images (see the platform-specific user's guides
and releases notes for how the non-secure images are typically used)

.. list-table::
   :widths: 10 20
   :header-rows: 0

   * - u-boot_HS_MLO
     - signed and encrypted boot image that can be used to
       boot from all media. Secure boot from SPI NOR flash is not
       currently supported.

.. include:: am335x_evm.rst
   :start-after: .. secure_boot_include_start_primary_u_boot
   :end-before: .. secure_boot_include_end_primary_u_boot
