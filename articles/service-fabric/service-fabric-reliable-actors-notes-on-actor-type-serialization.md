---
title: Güvenilir Aktörler aktör türü serileştirme üzerine notlar
description: Service Fabric Reliable Actors durumlarını ve arabirimlerini tanımlamak için kullanılabilecek serileştirilebilir sınıfları tanımlamak için temel gereksinimleri tartışır
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 876c4f5f45ff6c81a53274cf32e8bebecc1acfce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349298"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Hizmet Kumaş Güvenilir Aktörler türü serileştirme üzerine notlar
Tüm yöntemlerin bağımsız değişkenleri, aktör arabiriminde her yöntem tarafından döndürülen görevlerin sonuç türleri ve bir aktörün durum yöneticisinde depolanan nesneler [veri sözleşmesi serializable](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer)olmalıdır. Bu, [aktör olay arabirimlerinde](service-fabric-reliable-actors-events.md)tanımlanan yöntemlerin bağımsız değişkenleri için de geçerlidir. (Aktör olay arabirimi yöntemleri her zaman geçersiz döner.)

## <a name="custom-data-types"></a>Özel veri türleri
Bu örnekte, aşağıdaki aktör arabirimi, özel `VoicemailBox`bir veri türü döndüren bir yöntem tanımlar:

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

Arabirim, bir nesneyi depolamak için durum `VoicemailBox` yöneticisini kullanan bir aktör tarafından uygulanır:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

Bu örnekte, `VoicemailBox` nesne aşağıdaki durumlarda seri hale getirilir:

* Nesne bir aktör örneği ve arayan arasında aktarılır.
* Nesne, diske kalıcı olduğu ve diğer düğümlere çoğaltıldığı durum yöneticisine kaydedilir.

Güvenilir Aktör çerçevesi DataContract serileştirmekullanır. Bu nedenle, özel veri nesneleri ve üyeleri sırasıyla **DataContract** ve **DataMember** öznitelikleri ile açıklamalı olmalıdır.

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>Sonraki adımlar
* [Aktör yaşam döngüsü ve çöp toplama](service-fabric-reliable-actors-lifecycle.md)
* [Aktör zamanlayıcıları ve anımsatıcılar](service-fabric-reliable-actors-timers-reminders.md)
* [Aktör etkinlikleri](service-fabric-reliable-actors-events.md)
* [Aktör reentrancy](service-fabric-reliable-actors-reentrancy.md)
* [Aktör polimorfizmi ve nesne yönelimli tasarım desenleri](service-fabric-reliable-actors-polymorphism.md)
* [Aktör tanılama ve performans izleme](service-fabric-reliable-actors-diagnostics.md)
