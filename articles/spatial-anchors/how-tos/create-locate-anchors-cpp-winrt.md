---
title: C++/WinRT'de bağlantı & bulma
description: C++/WinRT'de Azure Uzamsal Bağlantılarını kullanarak çapaların nasıl oluşturulup bulunabildiğini ayrıntılı olarak açıklama.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 084058edca59eda776c47a3e20bb49178de78681
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74790074"
---
# <a name="how-to-create-and-locate-anchors-using-azure-spatial-anchors-in-cwinrt"></a>C++/WinRT'de Azure Uzamsal Bağlantılarını kullanarak bağlantı oluşturma ve bulma

> [!div  class="op_single_selector"]
> * [Unity](create-locate-anchors-unity.md)
> * [Objective-C](create-locate-anchors-objc.md)
> * [Swift](create-locate-anchors-swift.md)
> * [Android Java](create-locate-anchors-java.md)
> * [C++/NDK](create-locate-anchors-cpp-ndk.md)
> * [C++/WinRT](create-locate-anchors-cpp-winrt.md)

Azure Uzamsal Çapalar, dünyadaki çapaları farklı aygıtlar arasında paylaşmanıza olanak tanır. Birkaç farklı geliştirme ortamını destekler. Bu makalede, C++/WinRT'de Azure Uzamsal Çapalar SDK'nın nasıl kullanılacağına aşağıdakileri yapacağız:

- Azure Uzamsal Çapalar oturumunu doğru şekilde ayarlayın ve yönetin.
- Yerel çapalarda özellikler oluşturun ve ayarlayın.
- Buluta yükleyin.
- Bulut uzamsal bağlantı larını bulun ve silin.

## <a name="prerequisites"></a>Ön koşullar

Bu kılavuzu tamamlamak için şunları yaptığınızdan emin olun:

- [Azure Uzamsal Çapalar genel görünümünü](../overview.md)okuyun.
- 5 dakikalık [Quickstarts](../index.yml)biri tamamlandı.
- C++ ve Windows <a href="https://docs.microsoft.com/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt" target="_blank">Runtime API'leri</a>hakkında temel bilgiler.

[!INCLUDE [Start](../../../includes/spatial-anchors-create-locate-anchors-start.md)]

[CloudSpatialAnchorSession](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession) sınıfı hakkında daha fazla bilgi edinin.

```cpp
    CloudSpatialAnchorSession m_cloudSession{ nullptr };

    m_cloudSession = CloudSpatialAnchorSession();
```

[!INCLUDE [Account Keys](../../../includes/spatial-anchors-create-locate-anchors-account-keys.md)]

[SessionConfiguration](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/sessionconfiguration) sınıfı hakkında daha fazla bilgi edinin.

```cpp
    auto configuration = m_cloudSession.Configuration();
    configuration.AccountKey(LR"(MyAccountKey)");
```

[!INCLUDE [Access Tokens](../../../includes/spatial-anchors-create-locate-anchors-access-tokens.md)]

```cpp
    auto configuration = m_cloudSession.Configuration();
    configuration.AccessToken(LR"(MyAccessToken)");
```

[!INCLUDE [Access Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-access-tokens-event.md)]

[TokenRequiredDelegate](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/tokenrequireddelegate) temsilcisi hakkında daha fazla bilgi edinin.

```cpp
    m_accessTokenRequiredToken = m_cloudSession.TokenRequired(winrt::auto_revoke, [](auto&&, auto&& args) {
        args.AccessToken(LR"(MyAccessToken)");
    });
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```cpp
    m_accessTokenRequiredToken = m_cloudSession.TokenRequired(winrt::auto_revoke, [this](auto&&, auto&& args) {
        auto deferral = args.GetDeferral();
        MyGetTokenAsync([&deferral, &args](winrt::hstring const& myToken) {
            if (!myToken.empty()) args.AccessToken(myToken);
            deferral.Complete();
        });
    });
```

[!INCLUDE [Azure AD Tokens](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens.md)]

```cpp
    auto configuration = m_cloudSession.Configuration();
    configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

[!INCLUDE [Azure AD Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens-event.md)]

```cpp
    m_accessTokenRequiredToken = m_cloudSession.TokenRequired(winrt::auto_revoke, [](auto&&, auto&& args) {
        args.AuthenticationToken(LR"(MyAuthenticationToken)");
    });
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```cpp
    m_accessTokenRequiredToken = m_cloudSession.TokenRequired(winrt::auto_revoke, [this](auto&&, auto&& args) {
        auto deferral = args.GetDeferral();
        MyGetTokenAsync([&deferral, &args](winrt::hstring const& myToken) {
            if (!myToken.empty()) args.AuthenticationToken(myToken);
            deferral.Complete();
        });
    });
```

[!INCLUDE [Setup](../../../includes/spatial-anchors-create-locate-anchors-setup-non-ios.md)]

[Başlangıç](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#start) yöntemi hakkında daha fazla bilgi edinin.

```cpp
    m_cloudSession.Start();
```

[!INCLUDE [Frames](../../../includes/spatial-anchors-create-locate-anchors-frames.md)]

[ProcessFrame](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession) yöntemi hakkında daha fazla bilgi edinin.

```cpp
    m_cloudSession->ProcessFrame(ar_frame_);
```

[!INCLUDE [Feedback](../../../includes/spatial-anchors-create-locate-anchors-feedback.md)]

[SessionUpdatedDelegate](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/sessionupdateddelegate) temsilcisi hakkında daha fazla bilgi edinin.

```cpp
    m_sessionUpdatedToken = m_cloudSession.SessionUpdated(winrt::auto_revoke, [this](auto&&, auto&& args)
    {
        auto status = args.Status();
        if (status.UserFeedback() == SessionUserFeedback::None) return;
        m_feedback = LR"(Feedback: )" + FeedbackToString(status.UserFeedback()) + LR"( -)" +
            LR"( Recommend Create=)" + FormatPercent(status.RecommendedForCreateProgress() * 100.f);
    });
```

[!INCLUDE [Creating](../../../includes/spatial-anchors-create-locate-anchors-creating.md)]

[CloudSpatialAnchor](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchor) sınıfı hakkında daha fazla bilgi edinin.

```cpp
    // Initialization
    SpatialStationaryFrameOfReference m_stationaryReferenceFrame = nullptr;
    HolographicDisplay defaultHolographicDisplay = HolographicDisplay::GetDefault();
    SpatialLocator spatialLocator = defaultHolographicDisplay.SpatialLocator();
    m_stationaryReferenceFrame = spatialLocator.CreateStationaryFrameOfReferenceAtCurrentLocation();

    // Create a local anchor, perhaps by positioning a SpatialAnchor a few meters in front of the user
    SpatialAnchor localAnchor{ nullptr };
    PerceptionTimestamp timestamp = PerceptionTimestampHelper::FromHistoricalTargetTime(DateTime::clock::now());
    SpatialCoordinateSystem currentCoordinateSystem = m_attachedReferenceFrame.GetStationaryCoordinateSystemAtTimestamp(timestamp);
    SpatialPointerPose pose = SpatialPointerPose::TryGetAtTimestamp(currentCoordinateSystem, timestamp);

    // Get the gaze direction relative to the given coordinate system.
    const float3 headPosition = pose.Head().Position();
    const float3 headDirection = pose.Head().ForwardDirection();

    // The anchor is positioned two meter(s) along the user's gaze direction.
    constexpr float distanceFromUser = 2.0f; // meters
    const float3 gazeAtTwoMeters = headPosition + (distanceFromUser * headDirection);

    localAnchor = SpatialAnchor::TryCreateRelativeTo(currentCoordinateSystem, gazeAtTwoMeters);

    // If the user is placing some application content in their environment,
    // you might show content at this anchor for a while, then save when
    // the user confirms placement.
    CloudSpatialAnchor cloudAnchor = CloudSpatialAnchor();
    cloudAnchor.LocalAnchor(localAnchor);
    co_await m_cloudSession.CreateAnchorAsync(cloudAnchor);
    m_feedback = LR"(Created a cloud anchor with ID=)" + cloudAnchor.Identifier();
```

[!INCLUDE [Session Status](../../../includes/spatial-anchors-create-locate-anchors-session-status.md)]

[GetSessionStatusAsync](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#getsessionstatusasync) yöntemi hakkında daha fazla bilgi edinin.

```cpp
    SessionStatus status = co_await m_cloudSession.GetSessionStatusAsync();
    if (value.RecommendedForCreateProgress() < 1.0f) return;
    // Issue the creation request ...
```

[!INCLUDE [Setting Properties](../../../includes/spatial-anchors-create-locate-anchors-setting-properties.md)]

[AppProperties](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchor#appproperties) yöntemi hakkında daha fazla bilgi edinin.

```cpp
    CloudSpatialAnchor cloudAnchor = CloudSpatialAnchor();
    cloudAnchor.LocalAnchor(localAnchor);
    auto properties = m_cloudAnchor.AppProperties();
    properties.Insert(LR"(model-type)", LR"(frame)");
    properties.Insert(LR"(label)", LR"(my latest picture)");
    co_await m_cloudSession.CreateAnchorAsync(cloudAnchor);
```

[!INCLUDE [Update Anchor Properties](../../../includes/spatial-anchors-create-locate-anchors-updating-properties.md)]

[UpdateAnchorPropertiesAsync](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#updateanchorpropertiesasync) yöntemi hakkında daha fazla bilgi edinin.

```cpp
    CloudSpatialAnchor anchor = /* locate your anchor */;
    anchor.AppProperties().Insert(LR"(last-user-access)", LR"(just now)");
    co_await m_cloudSession.UpdateAnchorPropertiesAsync(anchor);
```

[!INCLUDE [Getting Properties](../../../includes/spatial-anchors-create-locate-anchors-getting-properties.md)]

[GetAnchorPropertiesAsync](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#getanchorpropertiesasync) yöntemi hakkında daha fazla bilgi edinin.

```cpp
    CloudSpatialAnchor anchor = co_await m_cloudSession.GetAnchorPropertiesAsync(LR"(anchorId)");
    if (anchor != nullptr)
    {
        anchor.AppProperties().Insert(LR"(last-user-access)", LR"(just now)");
        co_await m_cloudSession.UpdateAnchorPropertiesAsync(anchor);
    }
```

[!INCLUDE [Expiration](../../../includes/spatial-anchors-create-locate-anchors-expiration.md)]

[Sona Erme](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchor#expiration) yöntemi hakkında daha fazla bilgi edinin.

```cpp
    const int64_t oneWeekFromNowInHours = 7 * 24;
    const DateTime oneWeekFromNow = DateTime::clock::now() + std::chrono::hours(oneWeekFromNowInHours);
    cloudAnchor.Expiration(oneWeekFromNow);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating.md)]

[CreateWatcher](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#createwatcher) yöntemi hakkında daha fazla bilgi edinin.

```cpp
    AnchorLocateCriteria criteria = AnchorLocateCriteria();
    criteria.Identifiers({ LR"(id1)", LR"(id2)", LR"(id3)" });
    auto cloudSpatialAnchorWatcher = m_cloudSession.CreateWatcher(criteria);
```

[!INCLUDE [Locate Events](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[AnchorLocatedDelegate](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/anchorlocateddelegate) temsilcisi hakkında daha fazla bilgi edinin.

```cpp
    m_anchorLocatedToken = m_cloudSession.AnchorLocated(winrt::auto_revoke, [this](auto&&, auto&& args)
    {
        switch (args.Status())
        {
            case LocateAnchorStatus::Located:
            {
                CloudSpatialAnchor foundAnchor = args.Anchor();
            }
                break;
            case LocateAnchorStatus::AlreadyTracked:
                // This anchor has already been reported and is being tracked
                break;
            case LocateAnchorStatus::NotLocatedAnchorDoesNotExist:
                // The anchor was deleted or never existed in the first place
                // Drop it, or show UI to ask user to anchor the content anew
                break;
            case LocateAnchorStatus::NotLocated:
                // The anchor hasn't been found given the location data
                // The user might in the wrong location, or maybe more data will help
                // Show UI to tell user to keep looking around
                break;
        }
    });
```

[!INCLUDE [Deleting](../../../includes/spatial-anchors-create-locate-anchors-deleting.md)]

[DeleteAnchorAsync](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#deleteanchorasync) yöntemi hakkında daha fazla bilgi edinin.

```cpp
    co_await m_cloudSession.DeleteAnchorAsync(cloudAnchor);
    // Perform any processing you may want when delete finishes
```

[!INCLUDE [Stopping](../../../includes/spatial-anchors-create-locate-anchors-stopping.md)]

[Durdur](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#stop) yöntemi hakkında daha fazla bilgi edinin.

```cpp
    m_cloudSession.Stop();
```

[!INCLUDE [Resetting](../../../includes/spatial-anchors-create-locate-anchors-resetting.md)]

[Sıfırla](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#reset) yöntemi hakkında daha fazla bilgi edinin.

```cpp
    m_cloudSession.Reset();
```

[!INCLUDE [Cleanup](../../../includes/spatial-anchors-create-locate-anchors-cleanup-others.md)]

```cpp
    m_cloudSession = nullptr;
```

[!INCLUDE [Next Steps](../../../includes/spatial-anchors-create-locate-anchors-next-steps.md)]
