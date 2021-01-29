## Programming info0 binary

Note, to program info0 properly, do these things:

1. Clone this repo.
2. Open a CMD window.
3. Navigate to the bootloader directory of this repo.
4. Run JLINK with the included commandfile like so:
    "C:\Program Files (x86)\SEGGER\JLink\jlink.exe" -CommanderScript jlink-prog-info0.txt


## Special notes about programming

* You cannot effectively program info0 binaries using J-flash Light GUI. You must use JLINK commander with a commandfile. A command file can include calls to "ROM helper functions", which are essential to correctly (actually) programming the info0 memory locations on the MCU. These are the commands to the ROM helper functions:

* You can call Jlink.exe from command line with the commandfile as an argument. Note, you may have to include the complete paths to jlink.exe and the commanderfile. Also note, you need to navigate your CMD into this directory first.

C:\Users\pete.lewis\Documents\GitHub\SparkFun_LoRa_Thing_Plus_expLoRaBLE\Bootloader>"C:\Program Files (x86)\SEGGER\JLink\jlink.exe" -CommanderScript jlink-prog-info0.txt

This is the readout you should see:

    C:\Users\pete.lewis\Documents\GitHub\SparkFun_LoRa_Thing_Plus_expLoRaBLE\Bootloader>"C:\Program Files (x86)\SEGGER\JLink\jlink.exe" -CommanderScript jlink-prog-info0.txt
    SEGGER J-Link Commander V6.92 (Compiled Dec 18 2020 13:13:50)
    DLL version V6.92, compiled Dec 18 2020 13:12:28


    J-Link Command File read successfully.
    Processing script file...

    J-Link connection not established yet but required for command.
    Connecting to J-Link via USB...O.K.
    Firmware: J-Link EDU Mini V1 compiled Nov 12 2020 13:31:42
    Hardware version: V1.00
    S/N: 801028773
    License(s): FlashBP, GDB
    VTref=3.283V

    Selecting SWD as current target interface.

    Selecting 1000 kHz as target interface speed

    Target connection not established yet but required for command.
    Device "AMA3B1KK-KBR" selected.


    Connecting to target via SWD
    Found SW-DP with ID 0x2BA01477
    DPIDR: 0x2BA01477
    Scanning AP map to find all available APs
    AP[1]: Stopped AP scan as end of AP map has been reached
    AP[0]: AHB-AP (IDR: 0x24770011)
    Iterating through AP map to find AHB-AP to use
    AP[0]: Core found
    AP[0]: AHB-AP ROM base: 0xE00FF000
    CPUID register: 0x410FC241. Implementer code: 0x41 (ARM)
    Found Cortex-M4 r0p1, Little endian.
    FPUnit: 6 code (BP) slots and 2 literal slots
    CoreSight components:
    ROMTbl[0] @ E00FF000
    ROMTbl[0][0]: E000E000, CID: B105E00D, PID: 000BB00C SCS-M7
    ROMTbl[0][1]: E0001000, CID: B105E00D, PID: 003BB002 DWT
    ROMTbl[0][2]: E0002000, CID: B105E00D, PID: 002BB003 FPB
    ROMTbl[0][3]: E0000000, CID: B105E00D, PID: 003BB001 ITM
    ROMTbl[0][4]: E0040000, CID: B105900D, PID: 000BB9A1 TPIU
    Cortex-M4 identified.
    Reset delay: 0 ms
    Reset type NORMAL: Resets core & peripherals via SYSRESETREQ & VECTRESET bit.
    ResetTarget() start
    JDEC PID 0x000000CF
    Ambiq Apollo3 ResetTarget
    Bootldr = 0x04000000
    Secure Part.
    Secure Chip. Bootloader needs to run which will then halt when finish.
    CPU halted after reset. TryCount = 0x00000000
    ResetTarget() end

    Sleep(10)

    MSP = 0x10000100

    Writing 00000000 -> 10000000

    Writing D894E09E -> 10000004

    Writing FFFFFFFF -> 10000008



    Sleep(50)

    10000008 = 00000000

    Writing 00000000 -> 10000000

    Writing 00000000 -> 10000004

    Writing 00000800 -> 10000008

    Writing D894E09E -> 1000000C

    Writing FFFFFFFF -> 10000010

    Downloading file [info0_115200_bootGPIO22_active_high.bin]...
    O.K.



    Sleep(50)

    10000010 = 00000000

    Writing 0000001B -> 40000004


    Script processing completed.
    



## Info0 binary creation info

This was used to create the info0 file to work with the Ambiq Secure Bootloader in the expLoRaBLE design:

    python create_info0.py --valid 1 info0_115200_gpio22_active_high --pl 1 --u0 0x1c200c0 --u1 0xFFFF3031 --u2 0x2 --u3 0x0 --u4 0x0 --u5 0x0 --main 0xC000 --gpio 0x16 --version 0 --wTO 2500 --gpiolvl 1 --chip apollo3


Where:
--u0 0x1C200c0 = 115200 baud (0x1c200)
--main 0xC000 = main image location (0xC000)
-- u1 0xFFFF3031 = UART-TX (0x30 - pin 48) and UART-RX (0x31 - pin 49)
--gpio 0x16 = GPIO override *for the boot pin* (0x16, GPIO22)
--gpiolvl 1 = active high boot setup (note, the default is active low, and the only SFE design as of 1/29/2021 that had active low was the edge, all others (e.g. the artemis and expLoRaBLE) are active high setups)
--wTO 2500 = timeout failures set to 2500ms
