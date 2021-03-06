---
title: include dosyası
description: include dosyası
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 40e5a1bf940e46aed566a1e3fa6dcb4e6b2d9230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77135022"
---
## <a name="supported-operating-systems-and-drivers"></a>Desteklenen işletim sistemleri ve sürücüler

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) sürücüleri

NC, NCv2, NCv3, ND ve NDv2 serisi VM'ler (NV serisi için isteğe bağlı) için NVIDIA Tesla (CUDA) sürücüleri yalnızca aşağıdaki tabloda listelenen işletim sistemlerinde desteklenir. Sürücü indirme bağlantıları yayın sırasında geçerlidir. En son sürücüler için [NVIDIA](https://www.nvidia.com/) web sitesini ziyaret edin.

> [!TIP]
> Windows Server VM'de manuel CUDA sürücü yüklemesine alternatif olarak, Azure [Veri Bilimi Sanal Makine](../articles/machine-learning/data-science-virtual-machine/overview.md) görüntüsünü dağıtabilirsiniz. Windows Server 2016 için DSVM sürümleri nvidia CUDA sürücüleri, CUDA Derin Sinir Ağı Kitaplığı ve diğer araçları önceden yükler.


| İşletim Sistemi | Sürücü |
| -------- |------------- |
| Windows Server 2016 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID sürücüleri

Microsoft, sanal iş istasyonları olarak kullanılan NV ve NVv3 serisi VM'ler veya sanal uygulamalar için NVIDIA GRID sürücü yükleyicilerini yeniden dağıtır. Yalnızca aşağıdaki tabloda listelenen işletim sistemlerine yalnızca bu GRID sürücülerini Azure NV serisi VM'lere yükleyin. Bu sürücüler, Azure'daki GRID Virtual GPU Yazılımı için lisanslamayı içerir. Bir NVIDIA vGPU yazılım lisans sunucusu ayarlamanız gerekmez.

Nvidia uzantısı her zaman en son sürücüyü yükler. Eski bir sürüme bağımlı olan müşteriler için önceki sürüme bağlantılar salıyoruz.

Windows Server 2019, Windows Server 2016 ve Windows 10 (1909 oluşturmak için):
- [GRID 10.1 (442.06)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [GRID 10.0 (441.66)](https://download.microsoft.com/download/2/a/3/2a316e62-3be9-4ddb-ae8e-c04b6df6e22d/441.66_grid_win10_server2016_server2019_64bit_international.exe) (.exe) 

Windows Server 2012 R2, Windows Server 2008 R2, Windows 8 ve Windows 7 için: 
- [GRID 10.1 (442.06)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)
- [GRID 10.0 (441.66)](https://download.microsoft.com/download/d/8/0/d80091f8-0d55-47c2-958a-bacd136f432a/441.66_grid_win7_win8_server2008R2_server2012R2_64bit_international.exe) (.exe)  
