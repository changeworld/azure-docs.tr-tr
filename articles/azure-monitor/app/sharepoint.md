---
title: Bir SharePoint sitesini Application Insights ile izleme
description: Yeni bir izleme anahtarı ile yeni bir uygulamayı izlemeye başlama
ms.topic: conceptual
ms.date: 07/11/2018
ms.openlocfilehash: 395e8d667985318f4a084428c6fd4c395ee8b956
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671452"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Bir SharePoint sitesini Application Insights ile izleme
Azure Application Insights, uygulamalarınızın kullanılabilirliğini, performansını ve kullanımını izler. Burada, bir SharePoint sitesi için nasıl ayarlayacağınızı öğreneceksiniz.

## <a name="create-an-application-insights-resource"></a>Application Insights kaynağı oluşturma
[Azure portalında](https://portal.azure.com) yeni bir Application Insights kaynağı oluşturun. Uygulama türü olarak ASP.NET’i seçin.

![Özellikler'e tıklayın, anahtarı seçin ve ctrl + C tuşlarına basın](./media/sharepoint/001.png)

Uygulamanızla ilgili performans ve kullanım verilerini açılan pencerede görürsünüz. Azure’da bir sonraki oturum açışınızda bu pencereye dönmek için başlangıç ekranında bunun bir kutucuğunu bulmanız gerekir. Alternatif olarak, pencereyi bulmak için Gözat'a tıklayın.

## <a name="add-the-script-to-your-web-pages"></a>Betiği web sayfalarınıza ekleme

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

Komut dosyasını izlemek &lt;istediğiniz&gt; her sayfanın /head etiketinden hemen önce ekleyin. Web sitenizde bir ana sayfa varsa, komut dosyasını oraya koyabilirsiniz. Örneğin, bir ASP.NET MVC projesinde View\Shared\_Layout.cshtml’ye koyarsınız

Betikte, telemetriyi Application Insights kaynağınıza yönlendiren izleme anahtarı bulunur.

### <a name="add-the-code-to-your-site-pages"></a>Kodu site sayfalarınıza ekleme
#### <a name="on-the-master-page"></a>Ana sayfada
Sitenin ana sayfasını düzenleyebiliyorsanız, bu sayede sitedeki her sayfa için izleme olanağına sahip olursunuz.

Ana sayfayı inceleyin ve SharePoint Designer’ı veya başka bir düzenleyiciyi kullanarak düzenleyin.

![](./media/sharepoint/03-master.png)

Kodu </head> etiketinin hemen önüne ekleyin. 

![](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>Veya belirli sayfalarda
Sınırlı sayıda sayfayı izlemek için betiği her sayfaya ayrı ayrı ekleyin. 

Bir web parçası ekleyip kod parçacığını buna ekleyin.

![](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Uygulamanızla ilgili verileri görüntüleme
Uygulamanızı yeniden dağıtın.

Uygulamanızın [Azure portalındaki](https://portal.azure.com) dikey penceresine dönün.

İlk olaylar Arama’da görünür. 

![](./media/sharepoint/09-search.png)

Daha fazla veri bekliyorsanız, birkaç saniye geçtikten sonra Yenile’ye tıklayın.

## <a name="capturing-user-id"></a>Kullanıcı Kimliğini Yakalama
Standart web sayfası kod parçacığı SharePoint’ten kullanıcı kimliğini yakalamaz, ancak küçük bir değişiklikle bunu yapabilirsiniz.

1. Application Insights’taki Temel Bileşenler açılan penceresinden uygulamanızın izleme anahtarını kopyalayın. 

    ![](./media/sharepoint/02-props.png)

1. Aşağıdaki kod parçacığında geçen 'XXXX' ifadesini izleme anahtarıyla değiştirin. 
2. Betiği portaldan edindiğiniz kod parçacığı yerine SharePoint uygulamanıza ekleyin.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>Sonraki Adımlar
* Sitenizin kullanılabilirliğini izlemek için [web testleri](../../azure-monitor/app/monitor-web-app-availability.md).
* Diğer uygulama türleri için [Application Insights](../../azure-monitor/app/app-insights-overview.md).

<!--Link references-->


