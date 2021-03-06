---
title: Azure VM önyükleme yaparken KRITIK HİzMET BAŞARıSıZ OLDU | Microsoft Dokümanlar
description: Önyükleme yaparken oluşan "0x0000005A-CRITICAL SERVICE FAILED" hatasını nasıl gidereceklerini öğrenin | Microsoft Dokümanlar
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: 54ba87b681a055bb46b81ca81d2bcdd103491f27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921462"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>Windows, Azure VM önyükleme yaparken mavi ekranda "ÖNEMLİ HİzMET BAŞARıSıZ" gösterir
Bu makalede, Microsoft Azure'da bir Windows Sanal Makine (VM) önyükleme nizde karşılaşabileceğiniz "KRITIK HİzMET BAŞARıSıZ OLDU" hatası açıklanmaktadır. Sorunları çözmeye yardımcı olmak için sorun giderme adımları sağlar. 


## <a name="symptom"></a>Belirti 

Windows VM başlatılamıyor. [Önyükleme tanılamaönyükleme](./boot-diagnostics.md)ekran görüntüleri kontrol ettiğinizde, mavi ekranda aşağıdaki hata iletilerinden birini görürsünüz:

- "Bilgisayarınız bir sorunla karşılaştı ve yeniden başlatılması gerekiyor. Yeniden başlatabilirsiniz. Bu sorun ve olası düzeltmeler hakkında https://windows.com/stopcodedaha fazla bilgi için ziyaret edin. Bir destek kişi ararsanız, onlara şu bilgiyi verin: Dur kodu: ELEŞTİrİ HİzMETİ BAŞARDI" 
- "Bilgisayarınız bir sorunla karşılaştı ve yeniden başlatılması gerekiyor. Sadece bazı hata bilgileri topluyoruz ve sonra sizin için yeniden başlatAcağız. Daha fazla bilgi almak isterseniz, bu hatayı daha sonra çevrimiçi olarak arayabilirsiniz: CRITICAL_SERVICE_FAILED"

## <a name="cause"></a>Nedeni

Durdurma hatalarının çeşitli nedenleri vardır. En yaygın nedenleri şunlardır:
- Sürücüyle ilgili sorun
- Bozuk sistem dosyası veya bellek
- Uygulama belleğin yasak bir sektörüne erişiyor

## <a name="solution"></a>Çözüm 

Bu sorunu gidermek için [desteğe başvurun ve](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file)sorunu daha hızlı tanılamamıza veya aşağıdaki kendi kendine yardım çözümlerini denememize yardımcı olacak bir döküm dosyası gönderin.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Os diskini kurtarma VM'ine takın

1. Yedek olarak etkilenen VM'nin işletim sistemi diskinin anlık görüntüsünü alın. Daha fazla bilgi için [bir diskanlık anlık görüntüsüne](../windows/snapshot-copy-managed-disk.md)bakın.
2. [Os diskini kurtarma VM'sine takın.](./troubleshoot-recovery-disks-portal-windows.md) 
3. Kurtarma VM'sine uzak bir masaüstü bağlantısı kurun.

### <a name="enable-dump-logs-and-serial-console"></a>Döküm günlüklerini ve Seri Konsolu etkinleştirme

Döküm günlük ve [Seri Konsol](./serial-console-windows.md) bize daha fazla sorun giderme yapmanıza yardımcı olacaktır.

Döküm günlüklerini ve Seri Konsolu etkinleştirmek için aşağıdaki komut dosyasını çalıştırın.

1. Yükseltilmiş bir komut istemi oturumu açın (yönetici olarak çalıştırın).
2. Şu betiği çalıştırın:

    Bu komut dosyasında, ekli işletim sistemi diskine atanan sürücü harfinin F olduğunu varsayıyoruz. VM'iniz için uygun değerle değiştirmelisiniz.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 10
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

### <a name="replace-the-unsigned-drivers"></a>İmzalanmamış sürücüleri değiştirme

1. Kurtarma VM'inde, aşağıdaki komutu yükseltilmiş bir komut isteminden çalıştırın. Bu komut, etkilenen işletim sistemi diskini bir sonraki önyüklemede güvenli moda başlamak üzere ayarlar:

        bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal

    Örneğin, iliştirdiğiniz işletim sistemi diski F sürücüsüise aşağıdaki komutu çalıştırın:

        bcdedit /store F: boot\bcd /set {default} safeboot minimal

2. [Os diskini ayırın ve işletim sistemi diskini etkilenen VM'ye yeniden takın.](troubleshoot-recovery-disks-portal-windows.md) VM Güvenli moduna önyükleme yapacaktır. Hatayı yaşamaya devam ederseniz, isteğe bağlı adıma gidin.
3. Sürücü Doğrulayıcı Yöneticisi aracını başlatmak için **Çalıştır** kutusunu açın ve **doğrulayıcıyı** çalıştırın.
4. **İmzalanmamış sürücüleri otomatik olarak seçin**ve sonra **İleri'yi**tıklatın.
5. İmzalanmamış sürücü dosyalarının listesini alırsınız. Dosya adlarını hatırlayın.
6. Çalışan bir VM'den bu dosyaların aynı sürümlerini kopyalayın ve ardından bu imzalanmamış dosyaları değiştirin. 

7. Güvenli önyükleme ayarlarını kaldırın:

        bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
8.  VM’yi yeniden başlatın. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>İsteğe bağlı: Dökümü Kilitlenme modunda döküm günlükleri analiz

Döküm günlüklerini kendiniz analiz etmek için aşağıdaki adımları izleyin:

1. İşletim sistemi diskini bir kurtarma sanal makinesine ekleyin.
2. Eklediğiniz işletim sistemi diskinde **\windows\system32\config'e**göz atın. Geri alma gerekirse tüm dosyaları yedek olarak kopyalayın.
3. **Kayıt Defteri Düzenleyicisi'ni** başlatın (regedit.exe).
4. **HKEY_LOCAL_MACHINE** tuşunu seçin. Menüde **Dosya** > **Yük Kovanı'nı**seçin.
5. Eklediğiniz işletim sistemi diskindeki **\windows\system32\config\SYSTEM** klasörüne göz atın. Kovanın adı için **BROKENSYSTEM'i**girin. Yeni kayıt kovanı **HKEY_LOCAL_MACHINE** anahtarının altında görüntülenir.
6. **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl'e** göz atın ve aşağıdaki değişiklikleri yapın:

    Autoreboot = 0

    CrashDumpEnabled = 2
7.  **BROKENSYSTEM'i**seçin. Menüden **Dosya** > **Boşalt Kovanı'nı**seçin.
8.  BCD kurulumunu hata ayıklama moduna önyükleme yapmak için değiştirin. Yükseltilmiş bir komut isteminden aşağıdaki komutları çalıştırın:

    ```cmd
    REM Setup some debugging flags on the boot manager
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} default {<IDENTIFIER OF THE WINDOWS BOOT LOADER>}
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices disable

    REM Setup some debugging flags on the boot loader
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootlog yes
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy displayallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} testsigning off
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservices disable
    ```
9. [Os diskini ayırın ve işletim sistemi diskini etkilenen VM'ye yeniden takın.](troubleshoot-recovery-disks-portal-windows.md)
10. Döküm çözümlemesi gösterip gösterolmadığını görmek için VM'yi önyükleme. Yüklenmeyen dosyayı bulun. Bu dosyayı çalışan VM'den bir dosyayla değiştirmeniz gerekir. 

    Aşağıda döküm analizi örneğidir. **FAILURE** filecrypt.sys üzerinde olduğunu görebilirsiniz: "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys".

    ```
    kd> !analyze -v 

    ******************************************************************************* 
    * * * Bugcheck Analysis * * * 

    ******************************************************************************* 
    CRITICAL_SERVICE_FAILED (5a) Arguments: Arg1: 0000000000000001 Arg2: ffffd80f4bfe7070 Arg3: ffffb00b0513d320 Arg4: ffffffffc0000428 Debugging 
    Details: ------------------ 
    DUMP_CLASS: 1 DUMP_QUALIFIER: 401 BUILD_VERSION_STRING: 10.0.14393.1770 (rs1_release.170917-1700) 
    DUMP_TYPE: 1 BUGCHECK_P1: 1 BUGCHECK_P2: ffffd80f4bfe7070 BUGCHECK_P3: ffffb00b0513d320 BUGCHECK_P4: ffffffffc0000428 BUGCHECK_STR: 0x5A_c0000428 
    CPU_COUNT: 1 CPU_MHZ: a22 CPU_VENDOR: GenuineIntel CPU_FAMILY: 6 CPU_MODEL: 3d CPU_STEPPING: 4 DEFAULT_BUCKET_ID: WIN8_DRIVER_FAULT 
    PROCESS_NAME: System CURRENT_IRQL: 0 ANALYSIS_SESSION_HOST: MININT-6RMM091 ANALYSIS_SESSION_TIME: 11-15-2017 19:32:42.0841 
    ANALYSIS_VERSION: 10.0.16361.1001 amd64fre STACK_TEXT: ffffc701`1dc74948 fffff803`b2ff4b4a : 00000000`0000005a 00000000`00000001 ffffd80f`4bfe7070 ffffb00b`0513d320 : nt!KeBugCheckEx [d:\rs1\minkernel\ntos\ke\amd64\procstat.asm @ 127] ffffc701`1dc74950 fffff803`b3205df3 : ffffd80f`4bba9f58 ffffd80f`4bba9f58 ffffc701`1dc74b80 ffffd80f`00000006 : nt!IopLoadDriver+0x19f8e6 [d:\rs1\minkernel\ntos\ke\amd64\threadbg.asm @ 81] 
    RETRACER_ANALYSIS_TAG_STATUS: DEBUG_FLR_FAULTING_IP is not found THREAD_SHA1_HASH_MOD_FUNC: eb79608c07faa1af62c0e61f25ff6bc1d6dfdb25 THREAD_SHA1_HASH_MOD_FUNC_OFFSET: 96a3a314834bb4e8443a8b7201525fc5dfc1878b THREAD_SHA1_HASH_MOD: 30a3e915496deaace47137d5b90c3ecc03746bf6 FOLLOWUP_NAME: wintriag
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: https://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. VM normal çalışıp önyükleme yaptıktan sonra, Döküm ayarlarını kaldırın:

    ```cmd
    REM Restore the boot manager to default values
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {bootmgr} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices enable

    REM Restore the boot loader to default values for an azure VM
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} bootlog
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy ignoreallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} testsigning
    bcddit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservicesenable
    ```
