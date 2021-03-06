---
title: Azure'da Linux VM'lere Genel Bakış
description: Azure'daki Linux sanal makinelerine genel bakış.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 69a9722436aea1cf794e6e3f3ce02ec79180cff3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80159481"
---
# <a name="linux-virtual-machines-in-azure"></a>Azure’da Linux sanal makineleri

Azure Virtual Machines (VM) Azure’un sunduğu [isteğe bağlı ve ölçeklenebilir işlem kaynağı türlerinden](/azure/architecture/guide/technology-choices/compute-decision-tree) biridir. Genellikle, sunulan diğer seçimlere göre bilgi işlem ortamınız üzerinde daha fazla denetime ihtiyacınız varsa, bir VM seçersiniz. Bu makalede VM oluşturmadan önce dikkat etmeniz gereken bilgilere, oluşturma yöntemine ve yönetim seçeneklerine yer verilmiştir.

Bir Azure VM, onu çalıştıran fiziksel donanımı satın almanıza ve muhafaza etmenize gerek kalmadan size sanallaştırma esnekliği sunar. Ancak yine de yapılandırma, düzeltme eki uygulama ve yazılım yükleme gibi görevleri gerçekleştirerek VM’nin bakımını yapmanız gerekir.

Azure sanal makineleri farklı amaçlarla kullanılabilir. Bazı örnekler şunlardır:

* **Geliştirme ve test etme** – Azure VM’leri bir uygulama için kod yazmak ve test yapmak için özel yapılandırmalara sahip bilgisayarları hızlıca oluşturmanızı sağlar.
* **Uygulamaları buluta taşıma** – Talep düzeyinde dalgalanmalar olan uygulamalarınızı Azure üzerindeki bir VM’de çalıştırmak mantıklıdır. İhtiyaç duyduğunuzda oluşturulan ek VM’ler için ödeme yapar, ihtiyaç kalmadığında bunları kapatırsınız.
* **Veri merkezini genişletme** – Bir Azure sanal ağı üzerindeki sanal makineleri kolayca kuruluşunuzun ağına bağlayabilirsiniz.

Uygulamanız tarafından kullanılan VM sayısı, ihtiyaçlarınıza göre ölçeklendirilebilir.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>VM oluşturmadan önce dikkat etmem gereken noktalar nelerdir?
Azure’da uygulama altyapısı oluştururken [dikkate almanız gereken tasarım ölçütleri](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/windows-vm) vardır. Başlamadan önce dikkat etmeniz gereken VM özellikleri şunlardır:

* Uygulama kaynaklarınızın adları
* Kaynakların depolanacağı konum
* VM’nin boyutu
* Oluşturulabilecek en fazla VM sayısı
* VM üzerinde çalışan işletim sistemi
* VM’nin başlatıldıktan sonraki yapılandırması
* VM’nin ihtiyaç duyduğu kaynaklar

### <a name="locations"></a>Konumlar
Azure’da oluşturulan tüm kaynaklar, dünyanın farklı yerindeki çeşitli [coğrafi bölgelere](https://azure.microsoft.com/regions/) dağıtılır. VM oluştururken bu bölgeler genelde **konum** olarak adlandırılır. Bir VM için konum, sanal sabit disklerin depolandığı yeri belirtir.

Bu tabloda, kullanılabilen konumların listesini edinme yöntemlerinden bazıları gösterilmektedir.

| Yöntem | Açıklama |
| --- | --- |
| Azure portalında |VM oluştururken listeden konum seçin. |
| Azure PowerShell |[Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) komutunu kullanın. |
| REST API |[List locations](https://docs.microsoft.com/rest/api/resources/subscriptions) işlemini kullanın. |
| Azure CLI |[az account list-locations](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest) işlemini kullanın. |

## <a name="availability"></a>Kullanılabilirlik
Azure, sanal makineyi tüm diskler için premium depolamayla dağıtmanız koşuluyla, tek örnekli sanal makinelerde endüstri lideri %99,9 kullanılabilirlik Hizmet Düzeyi Sözleşmesi'nin duyurusunu yaptı.  Dağıtımınızın standart %99,95 VM Hizmet Düzeyi Sözleşmesinin kapsamına girebilmesi için iş yükünüzü çalıştıran iki veya daha fazla VM’yi yine bir kullanılabilirlik kümesi içinde dağıtmanız gerekir. Bir kullanılabilirlik kümesi, VM’lerinizin Azure veri merkezlerinde birden çok hata etki alanına dağıtılmasını ve aynı zamanda dağıtımlarının farklı bakım aralıklarına sahip konaklara yapılmasını sağlar. [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) şartları, Azure’un tamamının kullanılabilirlik garantisini açıklamaktadır.

## <a name="vm-size"></a>VM Boyutu
Kullandığınız VM’nin [boyutu](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), çalıştırmak istediğiniz iş yüküne göre belirlenir. Seçtiğiniz boyut işlemci gücü, bellek ve depolama kapasitesi gibi ölçütleri belirler. Azure çok sayıda kullanım türünü desteklemek için büyük çeşitlilikteki boyutları sunar.

Azure, VM'nin boyutuna ve işletim sistemine bağlı olarak saatlik bir [fiyat](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ücrete dahil eder. Kısmi saatler için, Azure yalnızca kullanılan dakikaları ücretlendirir. Depolama ayrı olarak fiyatlandırılır ve ücretlendirilir.

## <a name="vm-limits"></a>VM Sınırları
Aboneliğinizde, projeniz için birden fazla VM dağıtımını etkileyebilecek varsayılan [kota sınırları](../../azure-resource-manager/management/azure-subscription-service-limits.md) vardır. Geçerli sınırlar abonelik başına her bölge için 20 VM olarak belirlenmiştir. Sınırların [yükseltilmesini talep etmek için destek bileti oluşturabilirsiniz](../../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="managed-disks"></a>Yönetilen Diskler

Yönetilen Diskler, Azure Depolama hesabı oluşturma ve yönetme işlemini arka planda gerçekleştirir ve depolama hesabının ölçeklenebilirlik sınırları hakkında endişe etmeniz gerekmez. Azure’ın diski oluşturup yönetebilmesi için disk boyutunu ve performans katmanını (Standart veya Premium) belirtmeniz yeterlidir. Disk eklediğinizde veya VM ölçeğini artırıp azalttığınızda kullanılan depolama alanı konusunda endişelenmeniz gerekmez. Yeni VM'ler oluşturuyorsanız, VM'leri Yönetilen işletim sistemi ve veri diskleriyle oluşturmak için [Azure CLI](quick-create-cli.md) veya Azure portalını kullanın. Yönetilmeyen diskleri olan VM'leriniz varsa, [VM'leri Yönetilen Disklerle desteklenecek şekilde dönüştürebilirsiniz](convert-unmanaged-to-managed-disks.md).

Ayrıca, her Azure bölgesinde bir depolama hesabındaki özel görüntülerinizi yönetebilir ve aynı abonelikte yüzlerce VM oluşturmak için kullanabilirsiniz. Yönetilen Diskler hakkında daha fazla bilgi için bkz. [Yönetilen Disklere Genel Bakış](../linux/managed-disks-overview.md).

## <a name="distributions"></a>Dağıtım 
Microsoft Azure, çeşitli iş ortakları tarafından sağlanan ve bakımı yapılan bir dizi popüler Linux dağıtımının çalıştırılmasını destekler.  Azure Market'te Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD gibi daha birçok dağıtım bulabilirsiniz. Microsoft, [Azure destekli Linux Dağıtımları](endorsed-distros.md) listesine daha da fazla çeşitleme katmak için çeşitli Linux topluluklarıyla etkin bir çalışma sürdürüyor.

Tercih ettiğiniz Linux dağıtımı şu anda galeride yoksa, [Azure'da Linux VHD'si oluşturma ve karşıya yükleme](create-upload-generic.md) yoluyla "Kendi Linux VM'inizi getirebilirsiniz".

Microsoft, sağlanan görüntülerin güncelleştirilmiş ve Azure çalışma zamanı için iyileştirilmiş olduğundan emin olmak için iş ortaklarıyla yakın bir çalışma sürdürür.  Azure iş ortakları hakkında daha fazla bilgi için şu bağlantılara göz atın:

* Azure[ Destekli Dağıtımlarda](endorsed-distros.md) Linux
* SUSE - [Azure Market - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SLES?tab=Overview)
* Red Hat - [Azure Market - Red Hat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical - [Azure Market - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian - [Azure Market - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD - [Azure Marketi - FreeBSD 10.4](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
* CoreOS - [Azure Market - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS - [Azure Market - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami - [Azure için Bitnami Kitaplığı](https://azure.bitnami.com/)
* Mesosphere - [Azure Market - Azure'da Mesosphere DC/OS](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker - [Azure Market - Docker Swarm ile Azure Container Service](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins - [Azure Market - CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudbees.cloudbees-core-contact)


## <a name="cloud-init"></a>Cloud-init 

Düzgün bir DevOps kültürünü başarmak için tüm altyapı kod olmalıdır.  Tüm altyapı kod içinde yaşadığında kolayca yeniden oluşturulabilir.  Azure tüm önemli otomasyon araçlarıyla, örneğin Ansible, Chef, SaltStack ve Puppet ile çalışır.  Azure'un ayrıca kendi otomasyon aracı da vardır:

* [Azure Şablonları](create-ssh-secured-vm-from-template.md)
* [Azure VMAccess](using-vmaccess-extension.md)

Azure, bulut ların karşısına [çıkan](https://cloud-init.io/) ve onu destekleyen çoğu Linux Dağıtımcısı'na yönelik destekler.  Azure pazarda bulut ekleme özellikli görüntülerin kullanılabilmesi için onaylanan Linux dağıtım ortaklarımızla aktif olarak çalışıyoruz. Bu görüntüler, bulut-init dağıtımlarınızın ve yapılandırmalarınızın VM'ler ve sanal makine ölçek kümeleriyle sorunsuz çalışmasını sağlar.

* [Azure Linux VM'lerinde cloud-init kullanma](using-cloud-init.md)

## <a name="storage"></a>Depolama
* [Microsoft Azure Depolama'ya Giriş](../../storage/common/storage-introduction.md)
* [azure-cli kullanarak Linux VM'sine disk ekleme](add-disk.md)
* [Azure Portal’da Linux VM’sine veri diski ekleme](attach-disk-portal.md)

## <a name="networking"></a>Ağ Oluşturma
* [Sanal Ağ’a Genel Bakış](../../virtual-network/virtual-networks-overview.md)
* [Azure'da IP adresleri](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Azure'da Linux VM'sine bağlantı noktalarını açma](nsg-quickstart.md)
* [Azure portalda Tam Etki Alanı Adı oluşturma](portal-create-fqdn.md)


## <a name="next-steps"></a>Sonraki adımlar

İlk VM'inizi oluşturun!

- [Portal](quick-create-portal.md)
- [Azure CLI](quick-create-cli.md)
- [Powershell](quick-create-powershell.md)

