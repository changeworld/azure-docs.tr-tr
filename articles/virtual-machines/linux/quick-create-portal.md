---
title: Quickstart - Azure portalında Linux VM oluşturma
description: Bu hızlı başlangıçta, Bir Linux sanal makinesi oluşturmak için Azure portalını nasıl kullanacağınızı öğrenirsiniz.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: bc1dd56cd024ee65e29f227f4ec11cde436e388d
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80294769"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Hızlı başlangıç: Azure portalında Linux sanal makinesi oluşturma

Azure sanal makineleri (VM’ler), Azure portalı üzerinden oluşturulabilir. Azure portalı, Azure kaynakları oluşturmak için tarayıcı tabanlı bir kullanıcı arabirimidir. Bu hızlı başlangıç, Ubuntu 18.04 LTS çalıştıran bir Linux sanal makinesini (VM) dağıtmak için Azure portalını nasıl kullanacağınızı gösterir. Ayrıca VM'nizin çalıştığını görmek için SSH ile VM bağlantısı kurup NGINX web sunucusunu da yükleyeceksiniz.

Azure aboneliğiniz yoksa, başlamadan önce [ücretsiz](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) bir hesap oluşturun.

## <a name="create-ssh-key-pair"></a>SSH anahtar çifti oluşturma

Bu hızlı başlangıcı tamamlamak için bir SSH anahtar çifti gerekir. Bir SSH anahtar çiftiniz varsa bu adımı atlayabilirsiniz.

SSH anahtar çifti oluşturmak için bir Bash kabuğu açın ve [ssh-keygen](https://www.ssh.com/ssh/keygen/) komutunu kullanın. Yerel bilgisayarınızda Bash kabuğunuz yoksa [Azure Cloud Shell](https://shell.azure.com/bash)'i kullanabilirsiniz.


1. [Azure portalında](https://portal.azure.com)oturum açın.
1. Sayfanın üst kısmındaki menüde Bulut `>_` Kabuğu'nu açmak için simgeyi seçin.
1. CloudShell'in sol üstte **Bash** dediğine emin ol. PowerShell diyorsa, **Bash'i** seçmek için açılır açılır ı kullanın ve Bash kabuğuna değiştirmek için **Onayla'yı** seçin.
1. ssh tuşunu oluşturmak için yazın. `ssh-keygen -t rsa -b 2048` 
1. Anahtar çiftini kaydetmek için bir dosya girmeniz istenir. Parantez içinde listelenen varsayılan konumda kaydetmek için **Enter** tuşuna basmaniz gerekiyor. 
1. Bir parola girmeniz istenir. SSH anahtarınız için bir parola yazabilir veya geçiş cümlesi olmadan devam etmek için **Enter** tuşuna basabilirsiniz.
1. Komut, `ssh-keygen` varsayılan adı `id_rsa` .'de olan ortak `~/.ssh directory`ve özel anahtarlar oluşturur. Komut, ortak anahtarın tam yolunu döndürür. İçeriğini `cat` yazarak `cat ~/.ssh/id_rsa.pub`görüntülemek için ortak anahtara giden yolu kullanın.
1. Bu komutun çıktısını kopyalayın ve bu makalenin ilerleyen saatlerinde kullanmak üzere bir yere kaydedin. Bu ortak anahtarınızdır ve yönetici hesabınızı VM'nizde oturum açacak şekilde yapılandırırken buna ihtiyacınız olacaktır.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure [portalında](https://portal.azure.com) oturum açın.

## <a name="create-virtual-machine"></a>Sanal makine oluşturma

1. Aramada **sanal makineler** yazın.
1. **Hizmetler**altında, **Sanal makineleri**seçin.
1. Sanal **makineler** sayfasında **Ekle'yi**seçin. **Sanal makine oluştur** sayfası açılır.
1. **Temel Bilgiler** sekmesinde, **Proje ayrıntıları** altında, doğru aboneliğin seçildiğinden emin olun, ardından **Yeni oluştur** kaynak grubunu seçin. Ad için *myResourceGroup* yazın.*. 

    ![VM'niz için yeni bir kaynak grubu oluşturma](./media/quick-create-portal/project-details.png)

1. **Örnek ayrıntıları**altında , Sanal **makine adı**için *myVM* yazın, **Bölgeniz**için *Doğu ABD'yi* seçin ve **Görüntünüz**için *Ubuntu 18.04 LTS'yi* seçin. Diğer varsayılan değerleri bırakın.

    ![Örnek ayrıntıları bölümü](./media/quick-create-portal/instance-details.png)

1. **Yönetici hesabı**altında, **SSH ortak anahtarını**seçin, kullanıcı adınızı yazın ve ardından ortak anahtarınıza yapıştırın. Ortak anahtarınızda varsa baştaki ve sondaki tüm boşlukları kaldırın.

    ![Yönetici hesabı](./media/quick-create-portal/administrator-account.png)

1. **Gelen bağlantı noktası kuralları** > altında**Ortak gelen bağlantı noktaları,** **seçili bağlantı noktalarına izin ver'i** seçin ve ardından açılan bağlantı noktasından **SSH (22)** ve **HTTP (80)** seçeneğini belirleyin. 

    ![RDP ve HTTP için bağlantı noktaları açma](./media/quick-create-portal/inbound-port-rules.png)

1. Kalan varsayılan ayarları bırakın, ardından sayfanın alt kısmındaki **Gözden geçir + oluştur** düğmesini seçin.

1. **Sanal makine oluştur** sayfasında oluşturmak üzere olduğunuz VM'nin ayrıntılarını görüntüleyebilirsiniz. Hazır olduğunuzda **Oluştur**'u seçin.

VM'nizin dağıtılması birkaç dakika sürer. Dağıtım tamamlandıktan sonra bir sonraki bölüme geçin.

    
## <a name="connect-to-virtual-machine"></a>Sanal makineye bağlanma

VM ile bir SSH bağlantısı oluşturun.

1. VM’nizin genel bakış sayfasından **Bağlan** düğmesini seçin. 

    ![Portal 9](./media/quick-create-portal/portal-quick-start-9.png)

2. **Sanal makineye bağlan** sayfasında, 22 numaralı bağlantı noktası üzerinden IP adresine göre bağlanmak için varsayılan seçenekleri olduğu gibi bırakın. **VM yerel hesabı kullanarak oturum açın** bölümünde bir bağlantı komutu gösterilir. Komutu kopyalamak için düğmeyi seçin. Aşağıdaki örnekte SSH bağlantı komutunun nasıl göründüğü gösterilmiştir:

    ```bash
    ssh azureuser@10.111.12.123
    ```

3. SSH anahtar çiftinizi oluşturmak için kullandığınız aynı bash kabuğunu kullanarak (yeniden `>_` seçerek `https://shell.azure.com/bash`veya devam ederek Bulut Kabuğunu yeniden açabilirsiniz), SSH oturumu oluşturmak için SSH bağlantı komutunu kabuğuna yapıştırın.

## <a name="install-web-server"></a>Web sunucusunu yükleme

Sanal makinenizin çalıştığını görmek için NGINX web sunucusunu yükleyin. SSH oturumunuzdan paket kaynaklarınızı güncelleştirip en son NGINX paketini yükleyin.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

İşlemi tamamladığınızda `exit` yazarak SSH oturumunu kapatın.


## <a name="view-the-web-server-in-action"></a>Web sunucusunun çalıştığını görme

İstediğiniz web tarayıcısını kullanarak varsayılan NGINX karşılama sayfasını görüntüleyin. VM'nin genel IP adresini web adresi olarak yazın. Genel IP adresini VM genel bakış sayfasında veya önceden kullandığınız SSH bağlantı dizesinde bulabilirsiniz.

![Varsayılan NGINX sitesi](./media/quick-create-portal/nginx.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, sanal makineyi ve tüm ilişkili kaynakları silebilirsiniz. Bunu yapmak için, sanal makinenin kaynak grubunu ve **Sil**’i seçin, ardından silinecek kaynak grubunun adını onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta basit bir sanal makine dağıttınız, bir Ağ Güvenlik Grubu ve kuralı oluşturdunuz ve temel bir web sunucusu yüklediniz. Azure sanal makineleri hakkında daha fazla bilgi için Linux VM’lerine yönelik öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Linux sanal makine öğreticileri](./tutorial-manage-vm.md)
