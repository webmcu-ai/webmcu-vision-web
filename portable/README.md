The main webpage has life include files for tensroflowjs, zip and the esptool, so it might work offline
if ran once, but might not. This index.html has assess to the include files and should work offline.

Note the flash programs will need you to find the flash.ino.bin file using the file picker and go up a directory to allow the security to OK the installation. 

Like always you could just use the Arduino IDE and the firmware.ino to install the XIAO ML kit code.

If you want to bundle this code with the flassh.ino.bin and flash.ino.merge.bin  files and zip them and send them to someone or share with a thumb drive that would make sense for no internet use of this work.  Note I would also include the firmware.ino file as well.





for the esp tool I changed at the very bottom of the code, to switch it from a module

```
export{Fe as ClassicReset,Pe as CustomReset,Ai as ESPLoader,Te as HardReset,pe as ROM,Me as Transport,fe as UsbJtagSerialReset,ue as decodeBase64Data,Oe as getStubJsonByChipName,Ue as validateCustomResetStringSequence};

```

```
window.esptool = {
    ClassicReset: Fe,
    CustomReset: Pe,
    ESPLoader: Ai,
    HardReset: Te,
    ROM: pe,
    Transport: Me,
    UsbJtagSerialReset: fe,
    decodeBase64Data: ue,
    getStubJsonByChipName: Oe,
    validateCustomResetStringSequence: Ue
};
```


