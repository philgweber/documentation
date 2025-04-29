# Configuration Blob
The configuration blob data is owned by the OEM and allows custom actions to be taken on output parameters based on input channels and settings.

There are two files used to define configuration of the blob:
 - PocSpec.txt -  Defines Input and Output GUID's and actions to map input to outputs
 - PocIF.txt - For a given GUID maps valid ranges and mapping to ACPI output functions

Both these files live directly under the root folder, but will likely move in the future. The system needs to have "testsigning" enabled so MPTF would load the configuration blob.
<br><b>Note: </b> If these files are not present or found the MPTF Core driver will yellow bang.

 ## PocSpec.txt
This file refers to a specification file that defines various parameters and configurations for power and thermal management. The file includes detailed information about algorithms, input and output channels, and other settings that are used by MPTF to manage power and thermal conditions efficiently. If using an input or output from the OS you must use the existing UUID definitions found in the MPTF documentation.

In this case the output device is defined in the PocIF.txt but must be unique UUID if you are creating your own output channels.

```
//
// INPUT
   #macro I_OS_PWR_MODE_MPTF         {8945AB0A-35DD-4BEE-82A5-8138892C280D}_1
//
// OUTPUT
   #macro O_FAN1_ACTIVE_RPM           	{91F589E0-45F0-4C6E-A17D-24FD8E8CBDCE}_730

//
// DEMO-B - OS Power Mode Driven Fan RPM
//		Will monitor the OS PWR MODE and then in a lookup TABLE output a target RPM stored in O_FAN1_ACTIVE_RPM
1,0,0,%A_TABLE%,1,1,1,%I_OS_PWR_MODE_MPTF%,%O_FAN1_ACTIVE_RPM%,4,0,0,1,3,5,7,2,4,6,7,15,25,35,45
```

The last line monitors the input values selected in the OS from the power mode and maps this to output values for O_FAN1_ACTIVE_RPM. The last 4 values are the output values in this case in percentage for the fan speed 15,25,35,45.

## PocIF.txt
The PocIF file is the interface file that defines the input and output channel for MPTF. The example below maps an output channel to an ACPI function with several parameter.

```
// O_FAN1_ACTIVE_RPM
// GUID representing MPTF Customize Output - {91F589E0-45F0-4C6E-A17D-24FD8E8CBDCE}
// Channel ID - 730
// Configuration Input Format - 2 (Fixed do not change)
// Total Configuration - 5
// Configuration 1 (Version) - 1
// Configuration 2 (Max) - 60
// Configuration 3 (Min) - 10
// Configuration 4 (Default) - 20
// Configuration 5 (Function ID) - 2
// Device Bios Name - "\_SB.CIO1"
{91F589E0-45F0-4C6E-A17D-24FD8E8CBDCE}_730,2,5,1,60,10,20,2,"\_SB.CIO1"
```

Here the last two parameters maps this output to function 2 in the _DSM function of \_SB.CIO1 device in ACPI.

The value before (20) is the default value set if no value is set by the OS.

Previous two values 60,10 are the maximum and minimum valid values.

@Douglas to provide further details on the spec