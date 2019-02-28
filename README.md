# Index
### Table of contents 
- [RouterSummary,GetWirelessSettings,SetWirelessSettings,RebootRouter,SendLogs,FirmwareUpdate (Command 1100)](#1100)
- [UpdateDeviceIndex,UpdateDeviceName,AddScene,ActiveScene,UpdateScene,RemoveScene,RemoveAlllScenes,
AddRule,ValidateRule,UpdateRule,RemoveRule,RemoveAllRules,UpdateClient,RemoveClient (Command 1063)](#1063)
- [AffiliationAlmondComplete](#AffiliationAlmondComplete)
- [AlmondModeChange](#AlmondModeChange)
- [AlmondNameChange](#AlmondNameChange)
- [AlmondProperties](#AlmondProperties)
- [Dynamic commands](#DynamicCommands)
  - [DynamicAlmondProperties](#DynamicAlmondProperties)
  - [DynamicIndexUpdated](#DynamicIndexUpdated)
  - [DynamicDeviceUpdated](#DynamicDeviceUpdated)
  - [DynamicSceneAdded](#DynamicSceneAdded)
  - [DynamicSceneActivated](#DynamicSceneActivated)
  - [DynamicSceneUpdated](#DynamicSceneUpdated)
  - [DynamicSceneRemoved](#DynamicSceneRemoved)
  - [DynamicAllSceneRemoved](#DynamicAllSceneRemoved)
  - [DynamicRuleAdded](#DynamicRuleAdded)
  - [DynamicRuleUpdated](#DynamicRuleUpdated)
  - [DynamicRuleRemoved](#DynamicRuleRemoved)
  - [DynamicAllRulesRemoved](#DynamicAllRulesRemoved) 
  - [DyanamicClientAdded](#DynamicClientAdded)
  - [DynamicClientUpdated](#DynamicClientUpdated)
  - [DynamicClientRemoved](#DynamicClientRemoved)
- [CommonToAll](#CommonToAll)

<a name="1100"></a>
## 1.Command 1100
    Command no 
    1100- JSON format
 
    Required 
    Command,CommandType,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>             (done on RM.redisExecute)

    Queue
    3.send ICID to queue

    Functional 
    1. command 1100

    FLow:
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)
    
<a name="1063"></a>
## 2.Command 1063
    Command no 
    1100- JSON format
 
    Required 
    Command,CommandType,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>             (done on RM.redisExecute)

    Queue
    3.send ICID to queue

    Functional 
    1. command 1100

    FLow:
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)
    
<a name="AffiliationAlmondComplete"></a>
## 3.AffiliationAlmondComplete
    Command no
    25- JSON format

    Required
    Command,ICID,Payload

    Sql
    3.INSERT on AlmondUsers
      Parameters: userID,AlmondMAC,AlmondName,LongSecret,ownership,FirmwareVersion,AffiliationTS
    
    If(rows are affected)
    4.INSERT on AlmondProperties2
      Parameters: AlmondMAC,Properties,MobileProperties
    5.Select on Subscriptions

    else
    6.Update on Subscription

    check alexa compatible
    7.Select on UserTempPasswords

    Redis
    2.Get CODE:<code>               (done on RM.getCode)
    8.Perform multi:
       i. hmset on UID_<UserId>
       ii. hmset on AL_<pMAC>
       iii. hdel on AL_<pMAC>

    12.Get ICID_<packet.ICID>
    14.hgetall on UID_<user_list>

    Queue
    13.Send ICID to queue 
    15.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 25
    9.Delete affiliationStore[id]
    10.Destroy Socket
    11.Send AffiliationAlmondCompleteResponse to Almond


    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(affiliation_almond_complete),almondUsers(verify_affiliation_complete)-> processor(dispatchResponses),processor(unicast)->broadcaster(unicast)->processor(broadcaster)->broadcaster(send)
      
<a name="AlmondModeChange"></a>
## 4.AlmondModeChange
    Command no
    63- JSON format
   
    Required
    Command,ICID,UID,Payload

    Redis
    2.Get ICID_<packet.ICID>     (done on RM.redisExecute)
    4.hgetall on UID_<user_list>

    Queue
    3.Send ICID to queue
    5.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 63

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)
    
 
<a name="AlmondNameChange"></a>
## 5.AlmondNameChange
    Command no
    63- JSON format
   
    Required
    Command,ICID,UID,Payload

    Redis
    2.Get ICID_<packet.ICID>            (done on RM.redisExecute)
    4.hgetall on UID_<user_list>

    Queue
    3.Send ICID to queue
    5.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1. Command 63

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="AlmondProperties"></a>
## 6.AlmondProperties
    Command no
    1050- JSON format
   
    Required
    Command,AlmondMac,Payload

    Redis
    2.hmset AL_<AlmondMac>          (done on RM.UpdateAlmond)
    5.hgetall on UID_<user_list>
    
    Queue
    4.Send AlmondProperties to BACKGROUND_QUEUE 
    6.Send response to (<user_list>.Substring(Q_length,user_list))

    Functional
    1.Command 1050
    3.Send AlmondPropertiesResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(properties)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)

<a name="DynamicCommands"></a>
## 7.DynamicCommands
<a name="DynamicAlmondProperties"></a>
## a)DynamicAlmondProperties
    Command no
    1050- JSON format
   
    Required
    Command,AlmondMac,Payload

    Redis
    2.hmset AL_<AlmondMac>         (done on RM.UpdateAlmond)
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicAlmondProperties to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1050
    3.Send DynamicAlmondPropertiesResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(properties)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)


<a name="DynamicIndexUpdated"></a>
## b)DynamicIndexUpdated
    Command no
    1200- JSON format
   
    Required
    Command,UID,AlmondMac,Payload

    Redis
    2.hmset on AL_<AlmondMac>   (done on RM.UpdateAlmond)
    7.hgetall on UID_<user_list> 

    Queue
    4.Send DynamicIndexUpdated to config.ALEXA_QUEUE
    6.Send DynamicIndexUpdated to BACKGROUND_QUEUE
    8.Send response to (<user_list>.Substring(Q_.length ,user_list.length))

    Functional
    1.Command 1200
    3.Send DynamicIndexUpdatedResponse to Almond
    5.delete packet.alexa

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicDeviceUpdated"></a>
## c)DynamicDeviceUpdated
    Command no
    1200- JSON format
   
    Required
    Command,UID,AlmondMac,Payload

    Redis
    2.hmset on AL_<AlmondMac>       (done on RM.UpdateAlmond)
    5.hgetall on UID_<user_list> 

    Queue
    4.Send DynamicDeviceUpdated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length ,user_list.length))

    Functional
    1.Command 1200
    3.Send DynamicDeviceUpdatedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicSceneAdded"></a>
## d)DynamicSceneAdded
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMac,Payload

    Redis
    2.hmset on AL_<AlmondMac>    (done on RM.updateAlmond)
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicSceneAdded to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))
    Functional
    1.Command 1300
    3.Send DynamicSceneAddedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicSceneActivated"></a>
## e)DynamicSceneActivated
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMac,Payload

    Redis
    2.hmset on AL_<AlmondMac>     (done on RM.updateAlmond)
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicSceneActivated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list))

    Functional
    1.Command 1300
    3.Send DynamicSceneActivatedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicSceneUpdated"></a>
## f)DynamicSceneUpdated
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMac,Payload

    Redis
    2.hmset on AL_<AlmondMac>   (done on RM.updateAlmond)
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicSceneUpdated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1300
    3.Send DynamicSceneUpdatedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicSceneRemoved"></a>
## g)DynamicSceneRemoved
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMac,Payload

    Redis
    2.hmset on AL_<AlmondMac>     (done on RM.updateAlmond)
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicSceneRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1300
    3.Send DynamicSceneRemovedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicAllSceneRemoved"></a>
## h)DynamicAllSceneRemoved
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMac,Payload

    Redis
    2.hmset on AL_<AlmondMac>       (done on RM.updateAlmond)
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicAllSceneRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1300
    3.Send DynamicAllSceneRemovedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicRuleAdded"></a>
## i)DynamicRuleAdded
    Command no
    1400- JSON format

    Required
    Command,UID,AlmondMac,Payload

    Redis
    2.hmset on AL_<AlmondMac>    (done on RM.updateAlmond)
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicRuleAdded to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1400
    3.Send DynamicRuleAddedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicRuleUpdated"></a>
## j)DynamicRuleUpdated
    Command no
    1400- JSON format

    Required
    Command,UID,AlmondMac,Payload

    Redis
    2.hmset on AL_<AlmondMac>    (done on RM.updateAlmond)
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicRuleUpdated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1400
    3.Send DynamicRuleUpdatedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicRuleRemoved"></a>
## k)DynamicRuleRemoved
    Command no
    1400- JSON format

    Required
    Command,UID,AlmondMac,Payload

    Redis
    2.hmset on AL_<AlmondMac>     (done on RM.updateAlmond)
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicRuleRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1400
    3.Send DynamicRuleRemovedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicAllRulesRemoved"></a>
## l)DynamicAllRulesRemoved
    Command no
    1400- JSON format

    Required
    Command,UID,AlmondMac,Payload

    Redis
    2.hmset on AL_<AlmondMac>       (done on RM.updateAlmond)
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicAllRulesRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1400
    3.Send DynamicAllRulesRemovedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicClientAdded"></a>
## m)DynamicClientAdded
    Command no
    1500- JSON format

    Required
    Command,UID,AlmondMac,Payload

    Redis
    2.hmset on AL_<AlmondMac>       (done on RM.updateAlmond)
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicClientAdded to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1500
    3.Send DynamicClientAddedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicClientUpdated"></a>
## n)DynamicClientUpdated
    Command no
    1500- JSON format

    Required
    Command,UID,AlmondMac,Payload

    Redis
    2.hmset on AL_<AlmondMac>      (done on RM.updateAlmond)
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicClientUpdated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1500
    3.Send DynamicClientUpdatedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicClientRemoved"></a>
## o)DynamicClientRemoved
    Command no
    1500- JSON format

    Required
    Command,UID,AlmondMac,Payload

    Redis
    2.hmset on AL_<AlmondMac>    (done on RM.updateAlmond)
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicClientRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1500
    3.Send DynamicClientRemovedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="CommonToAll"></a>
## 8.CommonToAll

    2. Checks if the packet.command is present, if not then return.
    3. Compares the indexOf packet.command with -1 and evaluates it with socket.almondMAC ,if not then return.
    6. If its not dynamicUpdate then return.
    7. Check if socket.zenAlmond and inxexOf zen.Restrict(packet) is not empty, if so then return.
    8. Check if not tracker.commandList(command) then assign the value 0 and then increment it.
    9. Convert the socket.almondMac to object and assign it to MAC.
    10. Check packet.payload and and packet.payload.toString, if true then return the length of the payload else return 0.
    11. If packet.Command is not equal to 31 then goto tracker.almond_tracking.
    13. Goes to Processor.do
    15. checks and calls the dispatchResponses function

