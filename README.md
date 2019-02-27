# Index
### Table of contents 
- [RouterSummary](#RouterSummary)
- [UpdateDeviceIndex](#UpdateDeviceIndex)
- [AffiliationAlmondComplete](#AffiliationAlmondComplete)
- [AlmondModeChange](#AlmondModeChange)
- [UpdateIndex](#UpdateIndex)
- [UpdateDeviceName](#UpdateDeviceName)
- [AddScene](#AddScene)
- [ActiveScene](#ActiveScene)
- [UpdateScene](#UpdateScene)
- [RemoveScene](#RemoveScene)
- [RemoveAllScenes](#RemoveAllScenes)
- [AddRule](#AddRule)
- [ValidateRule](#ValidateRule)
- [UpdateRule](#UpdateRule)
- [RemoveRule](#RemoveRule)
- [RemoveAllRules](#RemoveAllRules)
- [UpdateClient](#UpdateClient)
- [RemoveClient](#RemoveClient)
- [ChangeAlmondProperties](#ChangeAlmondProperties)
- [GetWirelessSettings](#GetWirelessSettings)
- [SetWirelessSettings](#SetWirelessSettings)
- [RebootRouter](#RebootRouter)
- [SendLogs](#SendLogs)
- [FirmwareUpdate](#FirmwareUpdate)
- [AlmondNameChange](#AlmondNameChange)
- [DynamicAlmondProperties](#DynamicAlmondProperties)
- [AlmondProperties](#AlmondProperties)
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
<a name="RouterSummary"></a>
## 1.RouterSummary
    Command no 
    1100- JSON format
 
    Required 
    Command,CommandType,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>

    Queue
    3.send the ICID packet to queue

    Functional 
    1. command 1100

    FLow:
     almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="UpdateDeviceIndex"></a>
## 2.UpdateDeviceIndex
    Command no
    1063- JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1063  

    Flow
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
    2.Get CODE:<code>  
    8.Perform multi:
       i. hmset on UID_<UserId>
       ii. hmset on AL_<pMAC>
       iii. hdel on AL_<pMAC>

    11.Get on ICID_<packet.ICID>
    13.hgetall on UID_<user_list>

    Queue
    12.Send the ICID_<packet.ICID> to queue 
    14.Send response to (<user_list>.Substring(Q_length),user_list))

    Functional
    1.Command 25
    9.Delete affiliationStore[id]
    10.Destroy Socket

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(affiliation_almond_complete),almondUsers(verify_affiliation_complete)-> processor(dispatchResponses),processor(unicast)->broadcaster(unicast)->processor(broadcaster)->broadcaster(send)

<a name="AlmondModeChange"></a>
## 4.AlmondModeChange
    Command no
    63- JSON format
   
    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID> 

    Queue
    3.Send the packet ICID to queue

    FUnctional
    1. Command 63

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="UpdateIndex"></a>
## 5.UpdateIndex
    Command no
    1063 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1063  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="UpdateDeviceName"></a>
## 6.UpdateDeviceName
    Command no
    1063 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID> 

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1063  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="AddScene"></a>
## 7.AddScene
    Command no
    1063 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID> 

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1063  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="ActiveScene"></a>
## 8.ActiveScene
    Command no
    1063 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1063  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="UpdateScene"></a>
## 9.UpdateScene
    Command no
    1063 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID> 

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1063  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="RemoveScene"></a>
## 10.RemoveScene
    Command no
    1063 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID> 

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1063  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="RemoveAllScenes"></a>
## 11.RemoveAllScenes
    Command no
    1063 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID> 

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1063  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="AddRule"></a>
## 12.AddRule
    Command no
    1063 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1063  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="ValidateRule"></a>
## 13.ValidateRule
    Command no
    1063 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID> 

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1063  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="UpdateRule"></a>
## 14.UpdateRule
    Command no
    1063 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1063  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="RemoveRule"></a>
## 15.RemoveRule
    Command no
    1063 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1063  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="RemoveAllRules"></a>
## 16.RemoveAllRules
    Command no
    1063 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1063  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="UpdateClient"></a>
## 17.UpdateClient
    Command no
    1063 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1063  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="RemoveClient"></a>
## 18.RemoveClient
    Command no
    1063 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1063  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="ChangeAlmondProperties"></a>
## 19.ChangeAlmondProperties
    Command no
    1063 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID> 

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1063  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)
  
<a name="GetWirelessSettings"></a>
## 20.GetWirelessSettings
    Command no
    1100 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1100  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses) , processor(unicast)->broadcaster(unicast)

<a name="SetWirelessSettings"></a>
## 21.SetWirelessSettings
    Command no
    1100 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID> 

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1100  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses) , processor(unicast)->broadcaster(unicast) 

<a name="RebootRouter"></a>
## 22.RebootRouter
    Command no
    1100 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID> 

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1100  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses) , processor(unicast)->broadcaster(unicast)

<a name="SendLogs"></a>
## 23.SendLogs
    Command no
    1100 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1100  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses) , processor(unicast)->broadcaster(unicast)

<a name="FirmwareUpdate"></a>
## 24.FirmwareUpdate
    Command no
    1100 - JSON format

    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1100  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses) , processor(unicast)->broadcaster(unicast)
    
<a name="AlmondNameChange"></a>
## 25.AlmondNameChange
    Command no
    63- JSON format
   
    Required
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID> 

    Queue
    3.Send the packet ICID to queue

    FUnctional
    1. Command 63

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)
    
<a name="DynamicAlmondProperties"></a>
## 26.DynamicAlmondProperties
    Command no
    1050- JSON format
   
    Required
    Command,AlmondMac,Payload

    Redis
    2.hmset AL_<AlmondMac> 
    5.hgetall on UID_<user_list>

    Queue
    3.Send the packet AL_<AlmondMac> to queue
    4.Send DynamicAlmondProperties to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_length),user_list))
    
    Functional
    1. Command 1050

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(properties)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)

<a name="AlmondProperties"></a>
## 27.AlmondProperties
    Command no
    1050- JSON format
   
    Required
    Command,AlmondMac,Payload

    Redis
    2.hmset AL_<AlmondMac> 
    5.hgetall on UID_<user_list>

    Queue
    3.Send the packet AL_<AlmondMac> to queue
    4.Send AlmondProperties to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_length),user_list))

    Functional
    1.Command 1050

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(properties)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)

<a name="DynamicIndexUpdated"></a>
## 28.DynamicIndexUpdated
    Command no
    1200- JSON format
   
    Required
    Command,UID,AlmondMac,Payload

    Redis
    3.hmset on AL_<AlmondMac>
    7.hgetall on UID_<user_list> 

    Queue
    4.Send DynamicIndexUpdated to config.ALEXA_QUEUE
    6.Send DynamicIndexUpdated to BACKGROUND_QUEUE
    8.Send response to UID_<user_list>

    Functional
    1.Command 1200
    2.delete socket[mapper.hashColumn]
    5.delete packet.alexa

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicDeviceUpdated"></a>
## 29.DynamicDeviceUpdated
    Command no
    1200- JSON format
   
    Required
    Command,UID,AlmondMac,Payload

    Redis
    3.hmset on AL_<AlmondMac>
    5.hgetall on UID_<user_list> 

    Queue
    4.Send DynamicDeviceUpdated to BACKGROUND_QUEUE
    6.Send response to UID_<user_list> 

    Functional
    1.Command 1200
    2.delete socket[mapper.hashColumn]

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)
    
<a name="DynamicSceneAdded"></a>
## 30.DynamicSceneAdded
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMac,Payload

    Redis
    3.hmset on AL_<AlmondMac>
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicSceneAdded to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_length),user_list))

    Functional
    1.Command 1300
    2.delete socket[mapper.hashColumn]

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicSceneActivated"></a>
## 31.DynamicSceneActivated
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMac,Payload

    Redis
    3.hmset on AL_<AlmondMac>
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicSceneActivated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_length),user_list))

    Functional
    1.Command 1300
    2.delete socket[mapper.hashColumn]

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicSceneUpdated"></a>
## 32.DynamicSceneUpdated
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMac,Payload

    Redis
    3.hmset on AL_<AlmondMac>
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicSceneUpdated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_length),user_list))

    Functional
    1.Command 1300
    2.delete socket[mapper.hashColumn]

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicSceneRemoved"></a>
## 33.DynamicSceneRemoved
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMac,Payload

    Redis
    3.hmset on AL_<AlmondMac>
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicSceneRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_length),user_list))

    Functional
    1.Command 1300
    2.delete socket[mapper.hashColumn]

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicAllSceneRemoved"></a>
## 34.DynamicAllSceneRemoved
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMac,Payload

    Redis
    3.hmset on AL_<AlmondMac>
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicAllSceneRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_length),user_list))

    Functional
    1.Command 1300
    2.delete socket[mapper.hashColumn]

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicRuleAdded"></a>
## 35.DynamicRuleAdded
    Command no
    1400- JSON format

    Required
    Command,UID,AlmondMac,Payload

    Redis
    3.hmset on AL_<AlmondMac>
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicRuleAdded to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_length),user_list))

    Functional
    1.Command 1400
    2.delete socket[mapper.hashColumn]

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicRuleUpdated"></a>
## 36.DynamicRuleUpdated
    Command no
    1400- JSON format

    Required
    Command,UID,AlmondMac,Payload

    Redis
    3.hmset on AL_<AlmondMac>
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicRuleUpdated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_length),user_list))

    Functional
    1.Command 1400
    2.delete socket[mapper.hashColumn]

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicRuleRemoved"></a>
## 37.DynamicRuleRemoved
    Command no
    1400- JSON format

    Required
    Command,UID,AlmondMac,Payload

    Redis
    3.hmset on AL_<AlmondMac>
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicRuleRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_length),user_list))

    Functional
    1.Command 1400
    2.delete socket[mapper.hashColumn]

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicAllRulesRemoved"></a>
## 38.DynamicAllRulesRemoved
    Command no
    1400- JSON format

    Required
    Command,UID,AlmondMac,Payload

    Redis
    3.hmset on AL_<AlmondMac>
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicAllRulesRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_length),user_list))

    Functional
    1.Command 1400
    2.delete socket[mapper.hashColumn]

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicClientAdded"></a>
## 39.DynamicClientAdded
    Command no
    1500- JSON format

    Required
    Command,UID,AlmondMac,Payload

    Redis
    3.hmset on AL_<AlmondMac>
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicClientAdded to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_length),user_list))

    Functional
    1.Command 1500
    2.delete socket[mapper.hashColumn]

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicClientUpdated"></a>
## 40.DynamicClientUpdated
    Command no
    1500- JSON format

    Required
    Command,UID,AlmondMac,Payload

    Redis
    3.hmset on AL_<AlmondMac>
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicClientUpdated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_length),user_list))

    Functional
    1.Command 1500
    2.delete socket[mapper.hashColumn]

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="DynamicClientRemoved"></a>
## 41.DynamicClientRemoved
    Command no
    1500- JSON format

    Required
    Command,UID,AlmondMac,Payload

    Redis
    3.hmset on AL_<AlmondMac>
    5.hgetall on UID_<user_list>

    Queue
    4.Send DynamicClientRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_length),user_list))

    Functional
    1.Command 1500
    2.delete socket[mapper.hashColumn]

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="CommonToAll"></a>
## 42.CommonToAll

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
 
