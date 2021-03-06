---
title: Windows VM sistemine atanmış yönetilen bir kimlik kullanarak Azure Depolama'ya erişin | Microsoft Dokümanlar
description: Windows VM üzerinde bir sistem tarafından atanmış yönetilen kimlik kullanarak Azure Depolama'ya erişme işleminde size yol gösteren bir öğretici.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 182bf02bfaad598a447304cc9f2ed42f6221176d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75971955"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Öğretici: Azure Depolama’ya erişmek için Windows VM sistem tarafından atanan yönetilen kimlik kullanma

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Bu öğreticide, Azure Depolama'ya erişmek amacıyla, Windows sanal makinesi (VM) için sistem tarafından atanmış bir yönetilen kimliği nasıl kullanacağınız gösterilmektedir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Depolama hesabında bir blob kapsayıcı oluşturma
> * Windows VM’nizin sistem tarafından atanan yönetilen kimliğine depolama hesabı için erişim izni verin
> * Erişim elde edin ve bu erişimi Azure Depolama’yı çağırmak için kullanın

> [!NOTE]
> Azure Depolama için Azure Active Directory kimlik doğrulaması genel önizlemeye sunuldu.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]



## <a name="enable"></a>Etkinleştirme

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Erişim verme


### <a name="create-storage-account"></a>Depolama hesabı oluştur

Bu bölümde bir depolama hesabı oluşturursunuz.

1. Azure portalın sol üst köşesinde bulunan **+ Kaynak oluştur** düğmesine tıklayın.
2. **Depolama**’ya ve sonra **Depolama hesabı - blob, dosya, tablo, kuyruk** öğesine tıklayın.
3. **Ad**’ın altında, depolama hesabı için bir ad girin.
4. **Dağıtım modeli** ve **Hesap türü**, **Kaynak yöneticisi** ve **Depolama (genel amaçlı v1)** olarak ayarlanmalıdır.
5. **Abonelik** ve **Kaynak Grubu** değerlerinin, önceki adımda VM'nizi oluştururken belirttiklerinizle eşleştiğinden emin olun.
6. **Oluştur'u**tıklatın.

    ![Yeni depolama hesabı oluşturma](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

### <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Bir blob kapsayıcı oluşturma ve depolama hesabına dosya yükleme

Dosyalar blob depolama alanı gerektirdiğinden dosyasının depolanacağı bir blob kapsayıcısı oluşturmanız gerekir. Ardından yeni depolama hesabındaki blob kapsayıcısına bir dosya yükleyin.

1. Yeni oluşturulan depolama hesabınıza geri gidin.
2. **Blob Hizmeti**’nin altında, **Kapsayıcılar**’a tıklayın.
3. Sayfanın üstündeki **+ Kapsayıcı** seçeneğine tıklayın.
4. **Yeni kapsayıcı**’nın altında, kapsayıcı için bir ad girin ve **Genel erişim düzeyi**’nin altında varsayılan değeri değiştirmeyin.

    ![Depolama kapsayıcısı oluşturma](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Tercih ettiğiniz bir düzenleyiciyi kullanarak yerel makinenizde *hello world.txt* başlıklı bir dosya oluşturun. Dosyayı açıp (tırnak işaretleri olmadan) "Hello world! :)" metnini ekleyin ve sonra kaydedin.
6. Kapsayıcı adına ve ardından **Karşıya yükle**’ye tıklayarak dosyayı yeni oluşturulan kapsayıcıya yükleyin
7. **Blobu karşıya yükle** bölmesinde **Dosyalar**’ın altında, klasör simgesine tıklayıp yerel makinenizde **hello_world.txt** dosyasına göz atın, dosyayı seçin ve ardından **Karşıya yükle**’ye tıklayın.
    ![Metin dosyasını karşıya yükleme](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

### <a name="grant-access"></a>Erişim verme

Bu bölümde, Bir Azure Depolama kapsayıcısına VM erişiminasıl verilen gösterilmektedir. Azure depolama blobundaki verileri almak için VM’nin sistem tarafından atanan yönetilen kimliğini kullanabilirsiniz.

1. Yeni oluşturulan depolama hesabınıza geri gidin.
2. Sol bölmedeki **Erişim denetimi (IAM)** bağlantısına tıklayın.
3. VM'iniz için yeni bir rol ataması eklemek için sayfanın üstüne **rol ataması** ekle seçeneğini tıklatın.
4. **Alt Rol**, açılır, **Depolama Blob Veri Okuyucu**seçin.
5. Sonraki açılan listede **Erişimin atanacağı hedef** öğesinin altında **Sanal Makine**’yi seçin.
6. Ardından, uygun aboneliğin **Abonelik**’te listelendiğinden emin olun ve sonra **Kaynak Grubu**’nu **Tüm kaynak grupları** olarak ayarlayın.
7. **Seçin**’in altında, VM'nizi belirleyin ve ardından **Kaydet**’e tıklayın.

    ![İzin atama](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="access-data"></a>Verilere erişme 

Azure Depolama, Azure AD kimlik doğrulamasını yerel olarak desteklediğinden yönetilen kimlik kullanılarak alınan erişim belirteçlerini doğrudan kabul eder. Bu, Azure Depolama’nın Azure AD tümleştirmesi kapsamındadır ve bağlantı dizesinde kimlik bilgileri sağlama işleminden farklıdır.

Erişim jetonu kullanarak Azure Depolama'ya bağlantı açmanın ve daha önce oluşturduğunuz dosyanın içeriğini okumaya ilişkin bir .NET kodu örneği aşağıda verilmiştir. Bu kodun, VM’nin yönetilen kimlik uç noktasına erişebilmesi için VM üzerinde çalıştırılması gerekir. .NET Framework 4.6 veya üzeri erişim belirteç yöntemini kullanmak için gereklidir. `<URI to blob file>` değerini uygun şekilde değiştirin. Blob depolama alanına oluşturup yüklediğiniz dosyaya giderek ve **Özellikler**’in altındaki **URL**’yi **Genel bakış** sayfasına kopyalayarak bu değeri alabilirsiniz.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IO;
using System.Net;
using System.Web.Script.Serialization;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;

namespace StorageOAuthToken
{
    class Program
    {
        static void Main(string[] args)
        {
            //get token
            string accessToken = GetMSIToken("https://storage.azure.com/");

            //create token credential
            TokenCredential tokenCredential = new TokenCredential(accessToken);

            //create storage credentials
            StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

            Uri blobAddress = new Uri("<URI to blob file>");

            //create block blob using storage credentials
            CloudBlockBlob blob = new CloudBlockBlob(blobAddress, storageCredentials);

            //retrieve blob contents
            Console.WriteLine(blob.DownloadText());
            Console.ReadLine();
        }

        static string GetMSIToken(string resourceID)
        {
            string accessToken = string.Empty;
            // Build request to acquire MSI token
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=" + resourceID);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";

            try
            {
                // Call /token endpoint
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader, and extract access token
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                JavaScriptSerializer j = new JavaScriptSerializer();
                Dictionary<string, string> list = (Dictionary<string, string>)j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
                accessToken = list["access_token"];
                return accessToken;
            }
            catch (Exception e)
            {
                string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                return accessToken;
            }
        }
    }
}
```

Yanıt, dosyanın içeriklerini kapsar:

`Hello world! :)`


## <a name="disable"></a>Devre Dışı Bırak

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide Azure Depolama’ya erişmek için Windows VM sistem tarafından atanan kimliğini etkinleştirmeyi öğrendiniz.  Azure Depolama hakkında daha fazla bilgi için bkz:

> [!div class="nextstepaction"]
> [Azure Depolama](/azure/storage/common/storage-introduction)
