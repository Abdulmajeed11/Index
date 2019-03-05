# Index
### Table of contents 
- [RouterSummary,GetWirelessSettings,SetWirelessSettings,RebootRouter,SendLogs,FirmwareUpdate (Command 1100)](#1100)
- [UpdateDeviceIndex,UpdateDeviceName,AddScene,ActiveScene,UpdateScene,RemoveScene,RemoveAlllScenes,
AddRule,ValidateRule,UpdateRule,RemoveRule,RemoveAllRules,UpdateClient,RemoveClient (Command 1063)](#1063)
- [AffiliationAlmondComplete (Command 25)](#25)
- [AlmondModeChange (Command 63)](#63)
- [AlmondNameChange (Command 63)](#63)
- [AlmondProperties (Command 1050)](#1050)
- [AlmondHello (Command 1040)](#1040)
- [AlmondHello (Command 31)](#31)
- [Dynamic commands:](#DynamicCommands)
  - [DynamicAlmondProperties (Command 1050)](#1050),[DynamicIndexUpdated (Command 1200)](#1200),[DynamicDeviceUpdated (Command 1200)](#1200),[DynamicSceneAdded (Command 1300)](#1300),
  [DynamicSceneActivated (Command 1300)](#1300),[DynamicSceneUpdated (Command 1300)](#1300),[DynamicSceneRemoved (Command 1300)](#1300),[DynamicAllSceneRemoved (Commnad 1300)](#1300),
  [DynamicRuleAdded (Command 1400)](#1400),[DynamicRuleUpdated (Command 1400)](#1400),[DynamicRuleRemoved (Command 1400)](#1400),[DynamicAllRulesRemoved (Command 1400)](#1400),
  [DyanamicClientAdded (Command 1500)](#1500),[DynamicClientUpdated (Command 1500)](#1500),[DynamicClientRemoved (Command 1500)](#1500)  
- [CommonToAll](#CommonToAll)

<a name="1100"></a>
## 1.Command 1100
    Command no 
    1100- JSON format
 
    Required 
    Command,CommandType,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>              (M.ICID + packet.ICID)

    Queue
    3.send ICID to queue

    Functional 
    1.Command 1100

    FLow:
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)
    
<a name="1063"></a>
## 2.Command 1063
    Command no 
    1100- JSON format
 
    Required 
    Command,CommandType,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>             (M.ICID + packet.ICID)

    Queue
    3.send ICID to queue

    Functional 
    1.Command 1100

    FLow:
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)
    
<a name="25"></a>
## 3.Command 25
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
    2.Get CODE:<code>                 (M.CODE + code)
    8.Perform multi:
       i. hmset on UID_<UserID>          (M.USER + UserID)
       ii. hmset on AL_<pMAC>            (M.ALMOND + pMAC)
       iii. hdel on AL_<pMAC>            (M.ALMOND + pMAC)

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
      
<a name="63"></a>
## 4.Command 63 (AlmondModeChange)
    Command no
    63- JSON format
   
    Required
    Command,ICID,UID,Payload

    Redis
    2.Get ICID_<packet.ICID>              (M.ICID + packet.ICID)
    4.hgetall on UID_<user_list>          (M.USER + user_list)

    Queue
    3.Send ICID to queue
    5.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 63

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)
    
 
<a name="63"></a>
## 5.Command 63 (AlmondNameChange)
    Command no
    63- JSON format
   
    Required
    Command,ICID,UID,Payload

    Redis
    2.Get ICID_<packet.ICID>             (M.ICID + packet.ICID)
    4.hgetall on UID_<user_list>         (M.USER + user_list)

    Queue
    3.Send ICID to queue
    5.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1. Command 63

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="1050"></a>
## 6.Command 1050 (AlmondProperties)
    Command no
    1050- JSON format
   
    Required
    Command,AlmondMAC,Payload

    Redis
    2.hmset AL_<AlmondMAC>               (M.ALMOND + AlmondMAC)
    5.hgetall on UID_<user_list>         (M.USER + user_list)
    
    Queue
    4.Send AlmondProperties to BACKGROUND_QUEUE 
    6.Send response to (<user_list>.Substring(Q_length,user_list))

    Functional
    1.Command 1050
    3.Send AlmondPropertiesResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(properties)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)

<a name="1040"></a>
## 7.Command 1040 (AlmondHello)
     Command no
     1040- JSON format

     Required
     Command,UID,AlmondMAC,Payload

     Redis
     2.hgetall on AL_<AlmondMAC>          (M.Almond+AlmondMAC)
     5.Perform multi:
       i. hmset on UID_<UserID>          (M.USER + UserID)
       ii. hmset on AL_<pMAC>            (M.ALMOND + pMAC)
       iii. hdel on AL_<pMAC>            (M.ALMOND + pMAC)

     SQl
     3.Select on AlmondUsers
       Parameters: AlmondMAC
     4.Select on AlmondSecondaryUsers
       Parameters: AlmondMAC

     Functional
     1.Command 1050

     Flow
     almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(almond_hello)
     
<a name="31"></a>
## 8.Command 31 (AlmondHello)
     Command no
     1040- JSON format

     Required
     Command,UID,AlmondMAC,Payload

     Redis
     2.hgetall on AL_<AlmondMAC>          (M.Almond+AlmondMAC)
     5.Perform multi:
       i. hmset on UID_<UserID>          (M.USER + UserID)
       ii. hmset on AL_<pMAC>            (M.ALMOND + pMAC)
       iii. hdel on AL_<pMAC>            (M.ALMOND + pMAC)

     SQl
     3.Select on AlmondUsers
     Parameters: AlmondMAC
     4.Select on AlmondSecondaryUsers
     Parameters: AlmondMAC

     Functional
     1.Command 1050

     Flow
     almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(almond_hello)

<a name="DynamicCommands"></a>
## 9.DynamicCommands
<a name="1050"></a>
## a)Command 1050 (DynamicAlmondProperties)
    Command no
    1050- JSON format
   
    Required
    Command,AlmondMAC,Payload

    Redis
    2.hmset AL_<AlmondMAC>               (M.ALMOND + AlmondMAC)
    5.hgetall on UID_<user_list>         (M.USER + user_list)

    Queue
    4.Send DynamicAlmondProperties to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1050
    3.Send DynamicAlmondPropertiesResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(properties)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)


<a name="1200"></a>
## b)Command 1200 (DynamicIndexUpdated)
    Command no
    1200- JSON format
   
    Required
    Command,UID,AlmondMAC,Payload

    Redis
    2.hmset on AL_<AlmondMAC>            (M.ALMOND + AlmondMAC)
    7.hgetall on UID_<user_list>         (M.USER + user_list)

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

<a name="1200"></a>
## c)Command 1200 (DynamicDeviceUpdated)
    Command no
    1200- JSON format
   
    Required
    Command,UID,AlmondMAC,Payload

    Redis
    2.hmset on AL_<AlmondMAC>                (M.ALMOND + AlmondMAC)
    5.hgetall on UID_<user_list>             (M.USER + user_list)

    Queue
    4.Send DynamicDeviceUpdated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length ,user_list.length))

    Functional
    1.Command 1200
    3.Send DynamicDeviceUpdatedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300"></a>
## d)Command 1300 (DynamicSceneAdded)
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMAC,Payload

    Redis
    2.hmset on AL_<AlmondMAC>              (M.ALMOND + AlmondMAC)
    5.hgetall on UID_<user_list>           (M.USER + user_list)

    Queue
    4.Send DynamicSceneAdded to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))
    Functional
    1.Command 1300
    3.Send DynamicSceneAddedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300"></a>
## e)Command 1300 (DynamicSceneActivated)
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMAC,Payload

    Redis
    2.hmset on AL_<AlmondMAC>              (M.ALMOND + AlmondMAC)
    5.hgetall on UID_<user_list>           (M.USER + user_list)

    Queue
    4.Send DynamicSceneActivated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list))

    Functional
    1.Command 1300
    3.Send DynamicSceneActivatedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300"></a>
## f)Command 1300 (DynamicSceneUpdated)
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMAC,Payload

    Redis
    2.hmset on AL_<AlmondMAC>              (M.ALMOND + AlmondMAC)
    5.hgetall on UID_<user_list>           (M.USER + user_list)

    Queue
    4.Send DynamicSceneUpdated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1300
    3.Send DynamicSceneUpdatedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300"></a>
## g)Command 1300 (DynamicSceneRemoved)
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMAC,Payload

    Redis
    2.hmset on AL_<AlmondMAC>             (M.ALMOND + AlmondMAC)
    5.hgetall on UID_<user_list>          (M.USER + user_list)

    Queue
    4.Send DynamicSceneRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1300
    3.Send DynamicSceneRemovedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300"></a>
## h)Command 1300 (DynamicAllSceneRemoved)
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMAC,Payload

    Redis
    2.hmset on AL_<AlmondMAC>               (M.ALMOND + AlmondMAC)
    5.hgetall on UID_<user_list>            (M.USER + user_list)

    Queue
    4.Send DynamicAllSceneRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1300
    3.Send DynamicAllSceneRemovedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1400"></a>
## i)Command 1400 (DynamicRuleAdded)
    Command no
    1400- JSON format

    Required
    Command,UID,AlmondMAC,Payload

    Redis
    2.hmset on AL_<AlmondMAC>              (M.ALMOND + AlmondMAC)
    5.hgetall on UID_<user_list>           (M.USER + user_list)

    Queue
    4.Send DynamicRuleAdded to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1400
    3.Send DynamicRuleAddedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1400"></a>
## j)Command 1400 (DynamicRuleUpdated)
    Command no
    1400- JSON format

    Required
    Command,UID,AlmondMAC,Payload

    Redis
    2.hmset on AL_<AlmondMAC>             (M.ALMOND + AlmondMAC)
    5.hgetall on UID_<user_list>          (M.USER + user_list)

    Queue
    4.Send DynamicRuleUpdated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1400
    3.Send DynamicRuleUpdatedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1400"></a>
## k)Command 1400 (DynamicRuleRemoved)
    Command no
    1400- JSON format

    Required
    Command,UID,AlmondMAC,Payload

    Redis
    2.hmset on AL_<AlmondMAC>           (M.ALMOND + AlmondMAC)
    5.hgetall on UID_<user_list>        (M.USER + user_list)

    Queue
    4.Send DynamicRuleRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1400
    3.Send DynamicRuleRemovedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1400"></a>
## l)Command 1400 (DynamicAllRulesRemoved)
    Command no
    1400- JSON format

    Required
    Command,UID,AlmondMAC,Payload

    Redis
    2.hmset on AL_<AlmondMAC>           (M.ALMOND + AlmondMAC)
    5.hgetall on UID_<user_list>        (M.USER + user_list)

    Queue
    4.Send DynamicAllRulesRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1400
    3.Send DynamicAllRulesRemovedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500"></a>
## m)Command 1500 (DynamicClientAdded)
    Command no
    1500- JSON format

    Required
    Command,UID,AlmondMAC,Payload

    Redis
    2.hmset on AL_<AlmondMAC>           (M.ALMOND + AlmondMAC)
    5.hgetall on UID_<user_list>        (M.USER + user_list)

    Queue
    4.Send DynamicClientAdded to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1500
    3.Send DynamicClientAddedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500"></a>
## n)Command 1500 (DynamicClientUpdated)
    Command no
    1500- JSON format

    Required
    Command,UID,AlmondMAC,Payload

    Redis
    2.hmset on AL_<AlmondMAC>             (M.ALMOND + AlmondMAC)
    5.hgetall on UID_<user_list>          (M.USER + user_list)

    Queue
    4.Send DynamicClientUpdated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1500
    3.Send DynamicClientUpdatedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500"></a>
## o)Command 1500 (DynamicClientRemoved)
    Command no
    1500- JSON format

    Required
    Command,UID,AlmondMAC,Payload

    Redis
    2.hmset on AL_<AlmondMAC>         (M.ALMOND + AlmondMAC) 
    5.hgetall on UID_<user_list>       (M.USER + user_list)

    Queue
    4.Send DynamicClientRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1500
    3.Send DynamicClientRemovedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="CommonToAll"></a>
## 10.CommonToAll

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

