---
title: Öğretici - Azure Güvenlik Duvarı günlüklerini ve ölçümlerini izleme
description: Bu öğreticide Azure Güvenlik Duvarı günlükleri ile ölçümlerini etkinleştirmeyi ve yönetmeyi öğreneceksiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 10/24/2018
ms.author: victorh
ms.openlocfilehash: da46cf826da40658883d22692e5038b09d222907
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75974523"
---
# <a name="tutorial-monitor-azure-firewall-logs-and-metrics"></a>Öğretici: Azure Güvenlik Duvarı günlüklerini ve ölçümlerini izleme

Güvenlik duvarı günlüklerini kullanarak Azure Güvenlik Duvarı'nı izleyebilirsiniz. Ayrıca etkinlik günlüklerini kullanarak Azure Güvenlik Duvarı kaynaklarıyla ilgili işlemleri denetleyebilirsiniz. Ölçümleri kullanarak portalda performans sayaçlarını görüntüleyebilirsiniz.

Bu günlüklerden bazılarına portaldan erişebilirsiniz. Günlükler [Azure Monitor günlüklerine,](../azure-monitor/insights/azure-networking-analytics.md)Depolama ve Etkinlik Hub'larına gönderilebilir ve Azure Monitor günlüklerinde veya Excel ve Power BI gibi farklı araçlarla analiz edilebilir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Azure portaldan günlüğe kaydetmeyi etkinleştirme
> * PowerShell ile günlüğe kaydetmeyi etkinleştirme
> * Etkinlik günlüğünü görüntüleme ve analiz etme
> * Ağ ve uygulama kuralı günlüklerini görüntüleme ve analiz etme
> * Ölçümleri görüntüle


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiye başlamadan önce, Azure Güvenlik Duvarında kullanılabilen tanılama günlüklerine ve ölçümlere genel bir bakış için [Azure Güvenlik Duvarı günlükleri ve ölçümleri](logs-and-metrics.md) yazısını okumanız gerekir.


## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Azure portaldan tanılama günlüğüne kaydetmeyi etkinleştirme

Tanılama günlüğüne kaydetme işlemi etkinleştirildikten sonra verilerin günlükte görünmesi birkaç dakika sürebilir. İlk seferde görünen veri olmazsa birkaç dakika sonra tekrar deneyin.

1. Azure portalda güvenlik duvarı kaynak grubunuzu açın ve güvenlik duvarına tıklayın.
2. **İzleme**altında, **Tanılama ayarlarını**tıklatın.

   Azure Güvenlik Duvarı için hizmete özgü iki günlük vardır:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule

3. Veri toplamaya başlamak için **Tanılamayı aç**'a tıklayın.
4. **Tanılama ayarları** sayfasında tanılama günlükleriyle ilgili ayarlar bulunur.
5. Bu örnekte, Azure Monitor günlükleri depolar, bu nedenle ad için **Güvenlik Duvarı günlük analitiği** yazın.
6. Çalışma alanınızı yapılandırmak için **Log Analytics'e gönder**'e tıklayın. Tanılama günlüklerini kaydetmek için Event Hubs'ı veya depolama hesabını da kullanabilirsiniz.
7. **Log Analytics** bölümünde **Yapılandır**'a tıklayın.
8. Log Analytics çalışma alanları sayfasında **Yeni Çalışma Alanı Oluştur**'a tıklayın.
9. **Log Analytics çalışma alanı** sayfasında yeni **Log Analytics çalışma alanı** adı olarak **firewall-oms** yazın.
10. Aboneliğinizi seçin, var olan güvenlik duvarı kaynak grubunu (**Test-FW-RG**) kullanın, konum olarak **Doğu ABD** seçin ve **Ücretsiz** fiyatlandırma katmanını belirleyin.
11. **Tamam**'a tıklayın.
   ![Yapılandırma işleminin başlatılmasıyla][1] OMS çalışma alanları artık Log Analytics çalışma alanları olarak adlandırılır.  
12. **Günlük** bölümünde uygulama ve ağ kuralları için günlükleri toplamak için **AzureFirewallApplicationRule** ve **AzureFirewallNetworkRule** girişlerini seçin.
   ![Tanılama ayarlarını kaydetme][2]
13. **Kaydet**'e tıklayın.

## <a name="enable-logging-with-powershell"></a>PowerShell ile günlüğe kaydetmeyi etkinleştirme

Etkinlik günlüğü tüm Kaynak Yöneticisi kaynakları için otomatik olarak etkinleştirilir. Bu günlükler aracılığıyla sunulan verileri toplamaya başlamak için tanılama günlüğüne kaydetme işlevinin etkinleştirilmesi gerekir.

Tanılama günlüğüne kaydetmeyi etkinleştirmek için aşağıdaki adımları izleyin:

1. Günlük verilerinin depolandığı depolama hesabınızın kaynak kimliğini not edin. Bu değer şu biçimdedir: */subscriptions/\<abonelik kimliği\>/resourceGroups/\<kaynak grubu adı\>/providers/Microsoft.Storage/storageAccounts/\<depolama hesabı adı\>*.

   Aboneliğinizdeki herhangi bir depolama hesabını kullanabilirsiniz. Bu bilgileri Azure portalda bulabilirsiniz. Bu bilgiler kaynağın **Özellik** sayfasında yer alır.

2. Günlüğe kaydetmenin etkinleştirildiği güvenlik duvarının kaynak kimliğini not edin. Bu değer şu biçimdedir: */subscriptions/\<abonelik kimliği\>/resourceGroups/\<kaynak grubu adı\>/providers/Microsoft.Network/azureFirewalls/\<Güvenlik duvarı adı\>*.

   Bu bilgileri portalda bulabilirsiniz.

3. Tanılama günlüğüne kaydetmeyi etkinleştirmek için aşağıdaki PowerShell cmdlet'ini kullanın:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```

> [!TIP]
>Tanılama günlükleri için ayrı bir depolama hesabına gerek yoktur. Depolama alanının erişim ve performans günlüğü kaydı için kullanılması durumunda hizmet ücreti tahsil edilir.

## <a name="view-and-analyze-the-activity-log"></a>Etkinlik günlüğünü görüntüleme ve analiz etme

Aşağıdaki yöntemlerden birini kullanarak etkinlik günlüğü verilerini görüntüleyebilir ve analiz edebilirsiniz:

* **Azure araçları**: Etkinlik günlüğü verilerini Azure PowerShell, Azure CLI, Azure REST API veya Azure portal üzerinden alabilirsiniz. Her yöntemle ilgili ayrıntılı adımlar [Kaynak Yöneticisi etkinlik işlemleri](../azure-resource-manager/management/view-activity-logs.md) makalesinde ayrıntılı bir şekilde anlatılmıştır.
* **Power BI**: [Power BI](https://powerbi.microsoft.com/pricing) hesabınız yoksa ücretsiz oluşturabilirsiniz. [Power BI için Azure Activity Logs içerik paketi](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) ile verilerinizi önceden yapılandırılmış panoları olduğu gibi veya değiştirerek kullanarak analiz edebilirsiniz.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Ağ ve uygulama kuralı günlüklerini görüntüleme ve analiz etme

[Azure Monitor günlükleri](../azure-monitor/insights/azure-networking-analytics.md) sayaç ve olay günlüğü dosyalarını toplar. Günlüklerinizi analiz etmek için görselleştirmelere ve güçlü arama özelliklerine sahiptir.

Azure Güvenlik Duvarı günlük analizi örnek sorguları için Bkz. [Azure Güvenlik Duvarı günlük analizi örnekleri.](log-analytics-samples.md)

Dilerseniz depolama hesabınıza bağlanabilir ve JSON erişim günlüklerini ve performans günlüklerini alabilirsiniz. İndirdiğiniz JSON dosyalarını CSV biçimine dönüştürebilir ve Excel, Power BI veya diğer veri görselleştirme araçlarında görüntüleyebilirsiniz.

> [!TIP]
> Visual Studio ve C# ile sabit ve değişken değerlerini değiştirme konusunda temel kavramlara hakimseniz GitHub'daki [günlük dönüştürücü araçlarını](https://github.com/Azure-Samples/networking-dotnet-log-converter) kullanabilirsiniz.

## <a name="view-metrics"></a>Ölçümleri görüntüle
Bir Azure Güvenlik Duvarına gidin, **İzleme** bölümünde **Ölçümler**’e tıklayın. Kullanılabilir değerleri görüntülemek için **ÖLÇÜM** açılan listesini seçin.

## <a name="next-steps"></a>Sonraki adımlar

Güvenlik duvarınızı günlükleri toplayacak şekilde yapılandırdığınıza göre, verilerinizi görüntülemek için Azure Monitor günlüklerini keşfedebilirsiniz.

> [!div class="nextstepaction"]
> [Azure Monitor günlüklerinde ağ izleme çözümleri](../azure-monitor/insights/azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
