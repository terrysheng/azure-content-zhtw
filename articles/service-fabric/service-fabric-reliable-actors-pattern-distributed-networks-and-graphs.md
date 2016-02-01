<properties
   pageTitle="分散式網路和圖形模式 | Microsoft Azure"
   description="如何使用 Service Fabric Reliable Actors 將應用程式模型製作成分散式網路與圖形的設計模式。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/29/2015"
   ms.author="claudioc"/>

# Reliable Actors 設計模式：分散式網路和圖形
Azure Service Fabric Reliable Actors 程式設計模型適用於為涉及關係的複雜解決方案製作模型，以及將這些關係的模型製作成物件。

![Azure Service Fabric Reliable Actors 模型][1]

如上圖所示，將使用者的模型製作成動作項目執行個體 (網路中的節點) 相當簡單。例如，「朋友摘要」(有時稱做「追蹤者」問題) 可讓使用者檢視其聯繫者的狀態更新，其運作方式類似 Facebook 與 Twitter。

Reliable Actors 模型能夠彈性地切入具體化問題。我們可以在事件時間填入「朋友摘要」、在張貼更新時更新所有朋友的「朋友摘要」，如下圖所示：

![Reliable Actors 模型和「朋友摘要」母體][2]


## 智慧型快取程式碼範例 -- 社交網路朋友摘要 (事件時間)

填入「朋友摘要」的範例程式碼：

```csharp
public interface ISocialPerson : IActor
{
    Task AddFriend(long person);
    Task RemoveFriend(long person);
    Task<List<SocialStatus>> GetFriendsFeed();
    Task<SocialStatus> GetStatus();
    Task<List<SocialStatus>> GetMyFeed();
    Task UpdateStatus(string status);
    Task UpdateFriendFeedAsync(SocialStatus status);
}

[DataContract]
Public class SocialPersonState
{
    [DataMember]
    public string _name; // my name
    [DataMember]
    public List<long> _friends; // list of my friends' IDs
    [DataMember]
    public List<SocialStatus> _friendsFeed; // my friends feeds
    [DataMember]
    public List<SocialStatus> _myFeed; // this is my feed, all my status updates
    [DataMember]
    public SocialStatus _lastStatus; // this is my last update
}

public class SocialPerson : StatefulActor<SocialPersonState>, ISocialPerson
{
    public override Task ActivateAsync()
    {
        CreateOrRestoreState();
        return base.ActivateAsync();
    }

    public Task AddFriend(long person)
    {
        State._friends.Add(person);
        return TaskDone.Done;
    }

    public Task RemoveFriend(long person)
    {
        State._friends.Remove(person);
        return TaskDone.Done;
    }

    public Task<List<SocialStatus>> GetFriendsFeed()
    {
        return Task.FromResult(State._friendsFeed);
    }

    public Task UpdateStatus(string status)
    {
        State._lastStatus = new SocialStatus()
        {
            Name = _name,
            Status = status,
            Timestamp = DateTime.UtcNow
        };
        State._myFeed.Add(_lastStatus);

        var taskList = new List<Task>();

        foreach(var friendId in _friends)
        {
            var friend = ActorProxy.Create<ISocialPerson>(friendId);
            taskList.Add(friend.UpdateFriendFeedAsync(_lastStatus));
        }

        return Task.WhenAll(taskList);
    }

    public Task UpdateFriendFeed(SocialStatus status)
    {
        State._friendsFeed.Add(status);

        return TaskDone.Done;
    }

    public Task<SocialStatus> GetStatus()
    {
        return Task.FromResult(State._lastStatus);
    }

    public Task<List<SocialStatus>> GetMyFeed()
    {
        return Task.FromResult(State._myFeed);
    }
}
```

或者，您可以製作動作項目的模型，在使用者要求「朋友摘要」時，依查詢計時器展開傳送與編譯「朋友摘要」。您也可以依計時器具體化「朋友摘要」(例如每隔 5 分鐘)。或者，您可以最佳化模型，並視使用者的習慣，例如他們登入或張貼更新的頻率，將事件時間和查詢時間處理與計時器式模型結合。

在社交網路中製作動作項目的模型時，您應該考量到有數百萬名追蹤者的「超級使用者」。開發人員應該將這類使用者的狀態與行為製作不同的模式，以滿足更高的需求。

同樣的，這也適用於當有一個活動會將許多使用者動作項目連線到一個活動動作項目 (中樞與輪輻)，而您想要製作該活動的模型時。例如，群組聊天和遊戲裝載這兩個範例。讓我們以群組聊天為例。一組參與者建立一個群組聊天動作項目，此動作項目會將一位參與者的訊息散佈給該群組。如下列範例所示：

## 智慧型快取程式碼範例 -- 群組聊天

```csharp
public interface IGroupChat : IActor
{
    Task PublishMessageAsync(long participantId, string message);
    Task<List<GroupChatMessage>> GetMessagesAsync();
    Task AddParticipantAsync(long participantId);
    Task RemoveParticipantAsync(long partitipantId);
}

[DataContract]
public class GroupChatParticipantState
{
    [DataMember]
    Public long _groupChatId;
    [DataMember]
    public List<GroupChatMessage> _messages;
}

public class GroupChatParticipant : StatefulActor<GroupChatParticipantState>, IGroupParticipant
{
    public Task SendMessageAsync(string message)
    {
        if (State._groupChatId != -1)
        {
            var groupChat = ActorProxy.Create<IGroupChat>(State._groupChatId);
            return groupChat.PublishMessageAsync(this.Id, message);
        }

        return TaskDone.Done;
    }

    ...
}

[DataContract]
public class GroupChatState
{
    [DataMember]
    Public List<long> _participants;
    [DataMember]
    Public List<GroupChatMessage> _messages;
}


public class GroupChat : StatefulActor<GroupChatState>, IGroupChat
{

public Task PublishMessageAsync(long participantId, string message)
{
    var chatMessage = new GroupChatMessage()
    {
        ParticipantId = participantId,
        Message = message,
        Timestamp = DateTime.Now
    };

    State._messages.Add(chatMessage);

    var taskList = new List<Task>();

    foreach(var id in State._participants)
    {
        if (id != participantId)
        {
            var participant = ActorProxy.Create<IGroupParticipant>.Create(id);
            taskList.Add(participant.ReceiveMessageAsync(chatMessage));
        }
    }
    return Task.WhenAll(taskList);
}

...

}
```

此方法運用 Reliable Actors 模型的功能，讓任何動作項目能夠依識別碼定址叢集中的任何其他動作項目。它們可以進行通訊，而無須擔心位置、定址、快取、傳訊、序列化或路由。

## 後續步驟
[模式：智慧型快取](service-fabric-reliable-actors-pattern-smart-cache.md)

[模式：資源管理](service-fabric-reliable-actors-pattern-resource-governance.md)

[模式：可設定狀態的服務組合](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[模式：物聯網](service-fabric-reliable-actors-pattern-internet-of-things.md)

[模式：分散式計算](service-fabric-reliable-actors-pattern-distributed-computation.md)

[某些反向模式](service-fabric-reliable-actors-anti-patterns.md)

[Service Fabric Actor 簡介](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch2.png

<!---HONumber=AcomDC_0121_2016-->