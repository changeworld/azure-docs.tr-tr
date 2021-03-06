---
title: "Öğretici: Bing Görsel Arama API'sını kullanarak görüntü yükleme"
titleSuffix: Azure Cognitive Services
description: Bing'e nasıl resim yükleyip, bu resim hakkında bilgi edinmeyi, yanıtı görüntülemeyi öğrenin.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: scottwhi
ms.openlocfilehash: ecd1ab5e613bb326b65f6aa50f3f85172bc334ac
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477926"
---
# <a name="tutorial-upload-images-to-the-bing-visual-search-api"></a>Öğretici: Bing Görsel Arama API'sine resim yükleme

Bing Görsel Arama API'si, yüklediğiniz resimlere benzer görüntüler için web'de arama yapmanızı sağlar. API'ye görüntü gönderebilen bir web uygulaması oluşturmak ve web sayfası içinde döndürdettiği bilgileri görüntülemek için bu öğreticiyi kullanın. Bu uygulamanın API'yi kullanmak için tüm [Bing Kullanımı ve Görüntüleme Gereksinimlerine](../bing-web-search/use-display-requirements.md) uymadığını unutmayın.

Bu örnek için tam kaynak [kodunu, github'da](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html)ek hata işleme ve ek açıklamalarla bulabilirsiniz.

Öğretici uygulaması şunları gösterir:

> [!div class="checklist"]
> * Bing Görsel Arama API'sine resim yükleme
> * Görüntü arama sonuçlarını bir web uygulamasında görüntüleme
> * API tarafından sağlanan farklı öngörüleri keşfedin

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>Web sayfasını oluşturma ve yapılandırma

Bing Görsel Arama API'sine görüntü gönderen, öngörüler alan ve bunları görüntüleyen bir HTML sayfası oluşturun. Sık kullanılan düzenleyicinizde veya IDE'de "uploaddemo.html" adlı bir dosya oluşturun. Dosyaya aşağıdaki temel HTML yapısını ekleyin:

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Visual Search Upload Demo</title>
    </head>

    <body>
    </body>
</html>
```

Sayfayı, kullanıcının istek için gerekli tüm bilgileri sağladığı bir istek bölümüne ve öngörülerin görüntülendiği bir yanıt bölümüne bölün. Aşağıdaki `<div>` etiketleri `<body>`ekleyin. Etiket, `<hr>` istek bölümünü yanıt bölümünden görsel olarak ayırır:

```html
<div id="requestSection"></div>
<hr />
<div id="responseSection"></div>
```

Uygulama `<script>` için `<head>` JavaScript'i içerecek şekilde etikete bir etiket ekleyin:

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>Yükleme dosyasını alma

Kullanıcının yüklemek için bir resim seçmesine `<input>` izin vermek için, `file`uygulama türü özniteliği 'ne ayarlanmış etiketi kullanır. Kullanıcı Arabirimi'nin, uygulamanın arama sonuçlarını almak için Bing kullandığını açıkça belirtmesi gerekir.

Aşağıdakileri `<div>` `requestSection` `<div>`ekleyin. Dosya girişi, herhangi bir resim türünde tek dosya kabul eder (örneğin, .jpg, .gif, .png). `onchange` olayı, kullanıcı dosyayı seçtiğinde çağrılan işleyiciyi belirtir.

Etiket, `<output>` seçili resmin küçük resmini görüntülemek için kullanılır:

```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>Dosya işleyicisi oluşturma

Yüklemek istediğiniz resimde okuyabilen bir işleyici işlevi oluşturun. `FileList` Nesnedeki dosyaları yinelerken, işleyici seçili dosyanın bir görüntü dosyası olduğundan ve boyutunun 1 MB veya daha az olduğundan emin olmalıdır. Görüntü daha büyükse, yüklemeden önce boyutunu azaltmanız gerekir. Son olarak, işleyici görüntünün bir küçük resmini görüntüler:

```javascript
function handleFileSelect(selector) {

    var files = document.getElementById(selector).files; // A FileList object

    for (var i = 0, f; f = files[i]; i++) {

        // Ensure the file is an image file.
        if (!f.type.match('image.*')) {
            alert("Selected file must be an image file.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        // Image must be <= 1 MB and should be about 1500px.
        if (f.size > 1000000) {
            alert("Image must be less than 1 MB.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        var reader = new FileReader();

        // Capture the file information.
        reader.onload = (function(theFile) {
            return function(e) {
                var fileOutput = document.getElementById('thumbnail');

                if (fileOutput.childElementCount > 0) {
                    fileOutput.removeChild(fileOutput.lastChild);  // Remove the current pic, if it exists
                }

                // Render thumbnail.
                var span = document.createElement('span');
                span.innerHTML = ['<img class="thumb" src="', e.target.result,
                                    '" title="', escape(theFile.name), '"/>'].join('');
                fileOutput.insertBefore(span, null);
            };
        })(f);

        // Read in the image file as a data URL.
        reader.readAsDataURL(f);
    }
}
```

## <a name="add-and-store-a-subscription-key"></a>Abonelik anahtarı ekleme ve depolama

Uygulama, Bing Görsel Arama API'sine arama yapmak için bir abonelik anahtarı gerektirir. Bu öğretici için, ui bunu sağlarsınız. Dosya `<input>` `<output>` etiketinin `<body>` hemen altına aşağıdaki etiketi (metin için tür özniteliği ayarlanmış şekilde) ekleyin:

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

Görüntü ve abonelik anahtarıyla, görüntü hakkında bilgi almak için Bing Görsel Arama'yı arayabilirsiniz. Bu öğreticide, arama varsayılan pazar`en-us`( ) ve`moderate`güvenli arama değeri ( ) kullanır.

Bu uygulama, bu değerleri değiştirmek için bir seçenek vardır. Abonelik anahtarının `<div>` `<div>`altına aşağıdakileri ekleyin. Uygulama, pazar `<select>` ve güvenli arama değerleri için açılır liste sağlamak için bir etiket kullanır. Her iki liste de varsayılan değeri görüntüler.

```html
<div>
    <p><a href="#" onclick="expandCollapse(options.id)">Query options</a></p>

    <div id="options" style="display:none">
        <p style="margin-left: 20px">Market: 
            <select id="mkt">
                <option value="es-AR">Argentina (Spanish)</option>
                <option value="en-AU">Australia (English)</option>
                <option value="de-AT">Austria (German)</option>
                <option value="nl-BE">Belgium (Dutch)</option>
                <option value="fr-BE">Belgium (French)</option>
                <option value="pt-BR">Brazil (Portuguese)</option>
                <option value="en-CA">Canada (English)</option>
                <option value="fr-CA">Canada (French)</option>
                <option value="es-CL">Chile (Spanish)</option>
                <option value="da-DK">Denmark (Danish)</option>
                <option value="fi-FI">Finland (Finnish)</option>
                <option value="fr-FR">France (French)</option>
                <option value="de-DE">Germany (German)</option>
                <option value="zh-HK">Hong Kong SAR(Traditional Chinese)</option>
                <option value="en-IN">India (English)</option>
                <option value="en-ID">Indonesia (English)</option>
                <option value="it-IT">Italy (Italian)</option>
                <option value="ja-JP">Japan (Japanese)</option>
                <option value="ko-KR">Korea (Korean)</option>
                <option value="en-MY">Malaysia (English)</option>
                <option value="es-MX">Mexico (Spanish)</option>
                <option value="nl-NL">Netherlands (Dutch)</option>
                <option value="en-NZ">New Zealand (English)</option>
                <option value="no-NO">Norway (Norwegian)</option>
                <option value="zh-CN">People's Republic of China (Chinese)</option>
                <option value="pl-PL">Poland (Polish)</option>
                <option value="pt-PT">Portugal (Portuguese)</option>
                <option value="en-PH">Philippines (English)</option>
                <option value="ru-RU">Russia (Russian)</option>
                <option value="ar-SA">Saudi Arabia (Arabic)</option>
                <option value="en-ZA">South Africa (English)</option>
                <option value="es-ES">Spain (Spanish)</option>
                <option value="sv-SE">Sweden (Swedish)</option>
                <option value="fr-CH">Switzerland (French)</option>
                <option value="de-CH">Switzerland (German)</option>
                <option value="zh-TW">Taiwan (Traditional Chinese)</option>
                <option value="tr-TR">Turkey (Turkish)</option>
                <option value="en-GB">United Kingdom (English)</option>
                <option value="en-US" selected>United States (English)</option>
                <option value="es-US">United States (Spanish)</option>
            </select>
        </p>
        <p style="margin-left: 20px">Safe search: 
            <select id="safesearch">
                <option value="moderate" selected>Moderate</option>
                <option value="strict">Strict</option>
                <option value="off">off</option>
            </select>
        </p>
    </div>
</div>
```

## <a name="add-search-options-to-the-webpage"></a>Web sayfasına arama seçenekleri ekleme

Uygulama, listeleri Sorgu seçenekleri bağlantısı `<div>` tarafından denetlenebilen bir daraltılabilir olarak gizler. Sorgu seçenekleri bağlantısını tıklattığınızda, sorgu seçeneklerini görebilmeniz ve değiştirebilmeniz için `<div>` genişletir. Sorgu seçenekleri bağlantısını yeniden tıklattığınızda, `<div>` daraltılır ve gizlenir. Aşağıdaki snippet Sorgu seçenekleri bağlantısının `onclick` işleyicisini gösterir. İşleyici genişletilmiş veya `<div>` daraltılmış olup olmadığını denetler. Bu işleyiciyi `<script>` bölüme ekleyin. Işleyici, demodaki tüm `<div>` katlanabilir bölümler tarafından kullanılır.

```javascript
// Contains the toggle state of divs.
var divToggleMap = {};  // divToggleMap['foo'] = 0;  // 1 = show, 0 = hide


// Toggles between showing and hiding the specified div.
function expandCollapse(divToToggle) {
    var div = document.getElementById(divToToggle);

    if (divToggleMap[divToToggle] == 1) {   // if div is expanded
        div.style.display = "none";
        divToggleMap[divToToggle] = 0;
    }
    else {                                  // if div is collapsed
        div.style.display = "inline-block";
        divToggleMap[divToToggle] = 1;
    }
}
```

## <a name="call-the-onclick-handler"></a>Işleyiciyi çağırın `onclick`

Gövdedeki `"Get insights"` seçeneklerin `<div>` altına aşağıdaki düğmeyi ekleyin. Düğme aramayı başlatmanızı sağlar. Düğme tıklatıldığında imleç dönen bekleme imleci olarak değiştirilir ve `onclick` işleyici çağrılır.

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Düğmenin `onclick` işleyicisini `handleQuery()` `<script>` etikete ekleyin.

## <a name="handle-the-query"></a>Sorguyu işleme

İşleyici, `handleQuery()` abonelik anahtarının mevcut olmasını ve 32 karakter uzunluğunda olmasını ve görüntünün seçilmesini sağlar. Ayrıca önceki sorgudan talan tüm içgörüleri de temizler. Daha sonra, `sendRequest()` arama yapmak için işlevi çağırır.

```javascript
function handleQuery() {
    var subscriptionKey = document.getElementById('key').value;

    // Make sure user provided a subscription key and image.
    // For this demo, the user provides the key but typically you'd
    // get it from secured storage.
    if (subscriptionKey.length !== 32) {
        alert("Subscription key length is not valid. Enter a valid key.");
        document.getElementById('key').focus();
        return;
    }

    var imagePath = document.getElementById('uploadImage').value;

    if (imagePath.length === 0)
    {
        alert("Please select an image to upload.");
        document.getElementById('uploadImage').focus();
        return;
    }

    var responseDiv = document.getElementById('responseSection');

    // Clear out the response from the last query.
    while (responseDiv.childElementCount > 0) {
        responseDiv.removeChild(responseDiv.lastChild);
    }

    // Send the request to Bing to get insights about the image.
    var f = document.getElementById('uploadImage').files[0];
    sendRequest(f, subscriptionKey);
}
```

## <a name="send-the-search-request"></a>Arama isteğini gönderme

İşlev `sendRequest()` uç nokta URL'sini `Ocp-Apim-Subscription-Key` biçimlendiriyor, üstbilgiyi abonelik anahtarına ayarlar, yüklemek için görüntünün ikilisini ekler, yanıt işleyicisini belirtir ve aramayı yapar:

```javascript
function sendRequest(file, key) {
    var market = document.getElementById('mkt').value;
    var safeSearch = document.getElementById('safesearch').value;
    var baseUri = `https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=${market}&safesearch=${safeSearch}`;

    var form = new FormData();
    form.append("image", file);

    var request = new XMLHttpRequest();

    request.open("POST", baseUri);
    request.setRequestHeader('Ocp-Apim-Subscription-Key', key);
    request.addEventListener('load', handleResponse);
    request.send(form);
}
```

## <a name="get-and-handle-the-api-response"></a>API yanıtını alın ve işleme

İşlev, `handleResponse()` aramadan Bing Görsel Arama'ya gelen yanıtı işler. Çağrı başarılı olursa, JSON yanıtını içgörüleri içeren tek tek etiketlere ayrıştırır. Ardından, arama sonuçlarını sayfaya ekler. Uygulama daha sonra, ne `<div>` kadar veri görüntüleneceğini yönetmek için her etiket için bir katlanabilir oluşturur. `<script>` Işleyiciyi bölüme ekleyin.

```javascript
function handleResponse() {
    if(this.status !== 200){
        alert("Error calling Bing Visual Search. See console log for details.");
        console.log(this.responseText);
        return;
    }

    var tags = parseResponse(JSON.parse(this.responseText));
    var h4 = document.createElement('h4');
    h4.textContent = 'Bing internet search results';
    document.getElementById('responseSection').appendChild(h4);
    buildTagSections(tags);

    document.body.style.cursor = 'default'; // reset the wait cursor set by query insights button
}
```

### <a name="parse-the-response"></a>Yanıtı ayrıştırma

İşlev, `parseResponse` JSON yanıtını bir sözlük nesnesine `json.tags`dönüştürerek.

```javascript
function parseResponse(json) {
    var dict = {};

    for (var i =0; i < json.tags.length; i++) {
        var tag = json.tags[i];

        if (tag.displayName === '') {
            dict['Default'] = JSON.stringify(tag);
        }
        else {
            dict[tag.displayName] = JSON.stringify(tag);
        }
    }

    return(dict);
}
```

### <a name="build-a-tag-section"></a>Etiket bölümü oluşturma

İşlev `buildTagSections()` ayrışmış JSON etiketleri aracılığıyla yinelenir ve `<div>` her etiket için bir işlev oluşturmak için `buildDiv()` işlevi çağırır. Her etiket bir bağlantı olarak görüntülenir. Bağlantı tıklandığında, etiketle ilişkili öngörüleri gösteren etiket genişler. Bağlantıyı yeniden tıklatmak bölümün çökmesine neden olur.

```javascript
function buildTagSections(tags) {
    for (var tag in tags) {
        if (tags.hasOwnProperty(tag)) {
            var tagSection = buildDiv(tags, tag);
            document.getElementById('responseSection').appendChild(tagSection);
        }
    }  
}

function buildDiv(tags, tag) {
    var tagSection = document.createElement('div');
    tagSection.setAttribute('class', 'subSection');

    var link = document.createElement('a');
    link.setAttribute('href', '#');
    link.setAttribute('style', 'float: left;')
    link.text = tag;
    tagSection.appendChild(link);

    var contentDiv = document.createElement('div');
    contentDiv.setAttribute('id', tag);
    contentDiv.setAttribute('style', 'clear: left;')
    contentDiv.setAttribute('class', 'section');
    tagSection.appendChild(contentDiv);

    link.setAttribute('onclick', `expandCollapse("${tag}")`);
    divToggleMap[tag] = 0;  // 1 = show, 0 = hide

    addDivContent(contentDiv, tag, tags[tag]);

    return tagSection;
}
```

## <a name="display-the-search-results-in-the-webpage"></a>Arama sonuçlarını web sayfasında görüntüleme

İşlev, `buildDiv()` `addDivContent` her etiketin katlanabilir `<div>`içeriğini oluşturmak için işlevi çağırır.

Etiketin içeriğinde, etiket için yanıttan gelen JSON yer alır. Başlangıçta, JSON sadece ilk 100 karakter gösterilir, ancak tüm JSON göstermek için JSON dizesini tıklatabilirsiniz. Yeniden tıklarsanız, JSON dizesi tekrar 100 karaktere daraltılır.

Ardından, etikette bulunan eylem türlerini ekleyin. Her eylem türü için, öngörülerini eklemek için uygun işlevleri arayın:

```javascript
function addDivContent(div, tag, json) {

    // Adds the first 100 characters of the json that contains
    // the tag's data. The user can click the text to show the
    // full json. They can click it again to collapse the json.
    var para = document.createElement('p');
    para.textContent = String(json).substr(0, 100) + '...';
    para.setAttribute('title', 'click to expand');
    para.setAttribute('style', 'cursor: pointer;')
    para.setAttribute('data-json', json);
    para.addEventListener('click', function(e) {
        var json = e.target.getAttribute('data-json');

        if (e.target.textContent.length <= 103) {  // 100 + '...'
            e.target.textContent = json;
            para.setAttribute('title', 'click to collapse');
        }
        else {
            para.textContent = String(json).substr(0, 100) + '...';
            para.setAttribute('title', 'click to expand');
        }
    });
    div.appendChild(para); 

    var parsedJson = JSON.parse(json);

    // Loop through all the actions in the tag and display them.
    for (var j = 0; j < parsedJson.actions.length; j++) {
        var action = parsedJson.actions[j];

        var subSectionDiv = document.createElement('div');
        subSectionDiv.setAttribute('class', 'subSection');
        div.appendChild(subSectionDiv);

        var h4 = document.createElement('h4');
        h4.innerHTML = action.actionType;
        subSectionDiv.appendChild(h4);

        if (action.actionType === 'ImageResults') {
            addImageWithWebSearchUrl(subSectionDiv, parsedJson.image, action);
        }
        else if (action.actionType === 'DocumentLevelSuggestions') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'RelatedSearches') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'PagesIncluding') {
            addPagesIncluding(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'VisualSearch') {
            addRelatedImages(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'Recipes') {
            addRecipes(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'ShoppingSources') {
            addShopping(subSectionDiv, action.data.offers);
        }
        else if (action.actionType === 'ProductVisualSearch') {
            addProducts(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'TextResults') {
            addTextResult(subSectionDiv, action);
        }
        else if (action.actionType === 'Entity') {
            addEntity(subSectionDiv, action);
        }
    }
}
```

## <a name="display-insights-for-different-actions"></a>Farklı eylemler için öngörüler görüntüleyin

Aşağıdaki işlevler farklı eylemler için öngörüler görüntüler. İşlevler, görüntü hakkında daha fazla bilgi içeren bir web sayfasına sizi gönderen tıklanabilir bir resim veya tıklanabilir bir bağlantı sağlar. Bu sayfa ya Bing.com veya resmin orijinal web sitesi tarafından barındırılan. Bu uygulamada öngörülerin tüm verileri görüntülenmez. Bir kavrayış için kullanılabilen tüm alanları görmek için [Görseller - Görsel Arama](https://aka.ms/bingvisualsearchreferencedoc) başvurusuna bakın.

> [!NOTE]
> Sayfada görüntülemeniz gereken en az miktarda içgörü bilgisi vardır. Bing [Arama API kullanımı ve](../bing-web-search/use-display-requirements.md) daha fazlası için gereksinimleri görüntüleme ye bakın.

### <a name="relatedimages-insights"></a>RelatedImages görüşleri

Bu `addRelatedImages()` işlev, eylemlerin listesini yineleyerek ve her biri için dışa `RelatedImages` `<img>` `<div>` bir etiket ekleyerek ilgili resmi barındıran web sitelerinin her biri için bir başlık oluşturur:

```javascript
    function addRelatedImages(div, images) {
        var length = (images.length > 10) ? 10 : images.length;

        // Set the title to the website that hosts the image. The title displays
        // when the user hovers over the image.

        // Make the image clickable. If the user clicks the image, they're taken
        // to the image in Bing.com.

        for (var j = 0; j < length; j++) {
            var img = document.createElement('img');
            img.setAttribute('src', images[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('title', images[j].hostPageDisplayUrl);
            img.setAttribute('data-webSearchUrl', images[j].webSearchUrl)

            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })

            div.appendChild(img);
        }
    }
```

### <a name="pagesincluding-insights"></a>Sayfalarİçeriş Bilgileri Dahil

Bu `addPagesIncluding()` işlev, yüklenen görüntüyü barındıran web sitelerinin her biri `PagesIncluding` için, eylemler listesi aracılığıyla `<img>` yineleyerek ve `<div>` her biri için dışa bir etiket ekleyerek bir bağlantı oluşturur:

```javascript

    // Display links to the first 5 webpages that include the image.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addPagesIncluding(div, pages) {
        var length = (pages.length > 5) ? 5 : pages.length;

        for (var j = 0; j < length; j++) {
            var page = document.createElement('a');
            page.text = pages[j].name;
            page.setAttribute('href', pages[j].hostPageUrl);
            page.setAttribute('style', 'margin: 20px 20px 0 0');
            page.setAttribute('target', '_blank')
            div.appendChild(page);

            div.appendChild(document.createElement('br'));
        }
    }
```

### <a name="relatedsearches-insights"></a>RelatedAramalar anlayışlar

İşlev, `addRelatedSearches()` `RelatedSearches` eylemler listesini yineleyerek ve her biri için dışa `<img>` `<div>` bir etiket ekleyerek, görüntüyü barındıran web sitesi için bir bağlantı oluşturur:

```javascript

    // Display the first 10 related searches. Include a link with the image
    // that when clicked, takes the user to Bing.com and displays the 
    // related search results.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRelatedSearches(div, relatedSearches) {
        var length = (relatedSearches.length > 10) ? 10 : relatedSearches.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', relatedSearches[j].thumbnail.url + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0;');
            childDiv.appendChild(img);

            var relatedSearch = document.createElement('a');
            relatedSearch.text = relatedSearches[j].displayText;
            relatedSearch.setAttribute('href', relatedSearches[j].webSearchUrl);
            relatedSearch.setAttribute('target', '_blank');
            childDiv.appendChild(relatedSearch);

        }
    }
```

### <a name="recipes-insights"></a>Yemek tarifleri anlayışlar

İşlev, `addRecipes()` `Recipes` eylemler listesi aracılığıyla yineleyerek döndürülen her bir yemek tarifi için `<img>` bir bağlantı `<div>` oluşturur ve her biri için bir etiketi dışa ekler:

```javascript
    // Display links to the first 10 recipes. Include the recipe's rating,
    // if available.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRecipes(div, recipes) {
        var length = (recipes.length > 10) ? 10 : recipes.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var recipe = document.createElement('a');
            recipe.text = recipes[j].name;
            recipe.setAttribute('href', recipes[j].url);
            recipe.setAttribute('style', 'margin: 20px 20px 0 0');
            recipe.setAttribute('target', '_blank')
            para.appendChild(recipe);

            if (recipes[j].hasOwnProperty('aggregateRating')) {
                var span = document.createElement('span');
                span.textContent = 'rating: ' + recipes[j].aggregateRating.text;
                para.appendChild(span);
            }

            div.appendChild(para);
        }
    }
```

### <a name="shopping-insights"></a>Alışveriş anlayışları

İşlev, `addShopping()` `RelatedImages` eylemlerin listesini yineleyerek ve her biri için dışa `<img>` `<div>` bir etiket ekleyerek döndürülen alışveriş sonuçları için bir bağlantı oluşturur:

```javascript
    // Display links for the first 10 shopping offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addShopping(div, offers) {
        var length = (offers.length > 10) ? 10 : offers.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var offer = document.createElement('a');
            offer.text = offers[j].name;
            offer.setAttribute('href', offers[j].url);
            offer.setAttribute('style', 'margin: 20px 20px 0 0');
            offer.setAttribute('target', '_blank')
            para.appendChild(offer);

            var span = document.createElement('span');
            span.textContent = 'by ' + offers[j].seller.name + ' | ' + offers[j].price + ' ' + offers[j].priceCurrency;
            para.appendChild(span);

            div.appendChild(para);
        }
    }
```

### <a name="products-insights"></a>Ürün öngörüleri

İşlev, `addProducts()` `Products` eylemler listesini yineleyerek ve her biri için dışa `<img>` `<div>` bir etiket ekleyerek döndürülen ürünler için bir bağlantı oluşturur:

```javascript

    // Display the first 10 related products. Display a clickable image of the
    // product that takes the user to Bing.com search results for the product.
    // If there are any offers associated with the product, provide links to the offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addProducts(div, products) {
        var length = (products.length > 10) ? 10 : products.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', products[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('title', products[j].name);
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('data-webSearchUrl', products[j].webSearchUrl)
            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })
            childDiv.appendChild(img);

            if (products[j].insightsMetadata.hasOwnProperty('aggregateOffer')) {
                if (products[j].insightsMetadata.aggregateOffer.offerCount > 0) {
                    var offers = products[j].insightsMetadata.aggregateOffer.offers;

                    // Show all the offers. Not all markets provide links to offers.
                    for (var i = 0; i < offers.length; i++) {  
                        var para = document.createElement('p');

                        var offer = document.createElement('a');
                        offer.text = offers[i].name;
                        offer.setAttribute('href', offers[i].url);
                        offer.setAttribute('style', 'margin: 20px 20px 0 0');
                        offer.setAttribute('target', '_blank')
                        para.appendChild(offer);

                        var span = document.createElement('span');
                        span.textContent = 'by ' + offers[i].seller.name + ' | ' + offers[i].price + ' ' + offers[i].priceCurrency;
                        para.appendChild(span);

                        childDiv.appendChild(para);
                    }
                }
                else {  // Otherwise, just show the lowest price that Bing found.
                    var offer = products[j].insightsMetadata.aggregateOffer;

                    var para = document.createElement('p');
                    para.textContent = `${offer.name} | ${offer.lowPrice} ${offer.priceCurrency}`; 

                    childDiv.appendChild(para);
                }
            }
        }
    }
```

### <a name="textresult-insights"></a>TextResult öngörüleri

İşlev, `addTextResult()` resimde tanınan herhangi bir metni görüntüler:

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

İşlev, `addEntity()` kullanıcıyı görüntüdeki varlık türü yle ilgili ayrıntıları alabilecekleri Bing.com götüren bir bağlantı görüntüler:

```javascript
    // If the image is of a person, the tag might include an entity
    // action type. Display a link that takes the user to Bing.com
    // where they can get details about the entity.
    function addEntity(div, action) {
        var entity = document.createElement('a');
        entity.text = action.displayName;
        entity.setAttribute('href', action.webSearchUrl);
        entity.setAttribute('style', 'margin: 20px 20px 0 0');
        entity.setAttribute('target', '_blank');
        div.appendChild(entity);
    }
```

İşlev, `addImageWithWebSearchUrl()` kullanıcıyı `<div>` Bing.com sonuçları aramaya götüren tıklatılabilir bir görüntü görüntüler:

```javascript
    function addImageWithWebSearchUrl(div, image, action) {
        var img = document.createElement('img');
        img.setAttribute('src', image.thumbnailUrl + '&w=120&h=120');
        img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
        img.setAttribute('data-webSearchUrl', action.webSearchUrl);
        img.addEventListener('click', function(e) {
            var url = e.target.getAttribute('data-webSearchUrl');
            window.open(url, 'foo');
        })
        div.appendChild(img);
    }

```

## <a name="add-a-css-style"></a>CSS stili ekleme

Web sayfasının düzenini `<style>` `<head>` düzenlemek için etikete aşağıdaki bölümü ekleyin:

```html
        <style>

            .thumb {
                height: 75px;
                border: 1px solid #000;
            }

            .stackLink {
                width:180px;
                min-height:210px;
                display:inline-block;
            }
            .stackLink a {
                float:left;
                clear:left;
            }

            .section {
                float:left;
                display:none;
            }

            .subSection {
                clear:left;
                float:left;
            }

        </style>
```

## <a name="next-steps"></a>Sonraki adımlar

>[!div class="nextstepaction"]
> [Öğretici: ImageInsightsToken'i kullanarak önceki aramalardan benzer görüntüler bulun](./tutorial-visual-search-insights-token.md)
