---
title: Dv2 ve Dsv2 serisi - Azure Sanal Makineler
description: Dv2 ve Dsv2 serisi VM'ler için teknik özellikler.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 808b14f118e842cb9e52d110075f92ba25a343c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164432"
---
# <a name="dv2-and-dsv2-series"></a>Dv2 ve DSv2 serisi

Orijinal D serisinin bir devamı olan Dv2 ve Dsv2 serisi, daha güçlü bir Işlemci ve en iyi cpu-to-bellek yapılandırmasına sahiptir ve bunları çoğu üretim iş yükü için uygun hale getirir. Dv2 serisi D serisi yaklaşık% 35 daha hızlıdır. Dv2 serisi Intel Turbo Boost Technology 2.0 ile Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) veya Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) işlemcileri üzerinde çalışır. Dv2 Serisi, D Serisi ile aynı bellek ve disk yapılandırmalarına sahiptir.

## <a name="dv2-series"></a>Dv2 Serisi

Dv2 serisi boyutları Intel® Xeon® 8171M 2.1GHz (Skylake) veya Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) veya Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) işlemcileri ile Intel Turbo Boost Technology 2.0 ile çalışır.

ACU: 210-250

Premium Depolama: Desteklenmiyor

Premium Depolama önbelleğe alma: Desteklenmiyor

Canlı Geçiş: Desteklenen

Bellek Koruma Güncelleştirmeleri: Desteklenen

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum geçici depolama iş boku: IOPS/Okuma MBps/Yazma MBps | Maksimum veri diskleri | Kaynak: IOPS | Max NIC'ler/Beklenen ağ bant genişliği (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3,5 | 50  | 3000/46/23    | 4  | 4x500  | 2/750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8x500  | 2/1500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16x500 | 4/3000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32x500 | 8/6000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64x500 | 8/12000 |

## <a name="dsv2-series"></a>DSv2 serisi

DSv2 serisi boyutları Intel® Xeon® 8171M 2.1GHz (Skylake) veya Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) veya Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) işlemcileri Intel Turbo Boost Technology 2.0 ile çalışır ve premium depolama kullanır.

ACU: 210-250

Premium Depolama: Desteklenen

Premium Depolama önbelleğe alma: Desteklenen

Canlı Geçiş: Desteklenen

Bellek Koruma Güncelleştirmeleri: Desteklenen

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum önbelleğe alınmış ve geçici depolama işliği: IOPS/MBps (GiB'de önbellek boyutu) | Maksimum cached disk işçıktısı: IOPS/MBps | Max NIC'ler/Beklenen ağ bant genişliği (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1_v2 | 1  | 3,5 | 7   | 4  | 4000/32 (43)    | 3200/48   | 2/750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 (86)    | 6400/96   | 2/1500  |
| Standard_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 (172) | 12800/192 | 4/3000  |
| Standard_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 (344) | 25600/384 | 8/6000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 (688) | 51200/768 | 8/12000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Diğer boyutlar

- [Genel amaç](sizes-general.md)
- [Bellek için iyileştirilmiş](sizes-memory.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
- [GPU için iyileştirilmiş](sizes-gpu.md)
- [Yüksek performanslı işlem](sizes-hpc.md)
- [Önceki nesiller](sizes-previous-gen.md)

## <a name="next-steps"></a>Sonraki adımlar

Azure bilgi [işlem birimlerinin (ACU)](acu.md) Azure SK'leri genelinde bilgi işlem performansını karşılaştırmanıza nasıl yardımcı olabileceği hakkında daha fazla bilgi edinin.
