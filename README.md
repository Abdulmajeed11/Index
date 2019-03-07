# Index
### Table of contents 
- [RouterSummary,GetWirelessSettings,SetWirelessSettings,RebootRouter,SendLogs,FirmwareUpdate (Command 1100)](#1100)
- [UpdateDeviceIndex,UpdateDeviceName,AddScene,ActiveScene,UpdateScene,RemoveScene,RemoveAlllScenes,
AddRule,ValidateRule,UpdateRule,RemoveRule,RemoveAllRules,UpdateClient,RemoveClient (Command 1063)](#1063)
- [AffiliationAlmondComplete (Command 25)](#25)
- [AlmondModeChange (Command 63)](#63a)
- [AlmondNameChange Command 63)](#63b)
- [AlmondProperties (Command 1050)](#1050)
- [AlmondHello (Command 1040)](#1040)
- [AlmondHello (Command 31)](#31)
- [Dynamic commands:](#DynamicCommands)
  - [DynamicAlmondProperties (Command 1050)](#1050),[DynamicIndexUpdated (Command 1200)](#1200a),[DynamicDeviceUpdated (Command 1200)](#1200b),[DynamicSceneAdded (Command 1300)](#1300a),
  [DynamicSceneActivated (Command 1300)](#1300b),[DynamicSceneUpdated (Command 1300)](#1300c),[DynamicSceneRemoved (Command 1300)](#1300d),[DynamicAllSceneRemoved (Commnad 1300)](#1300e),
  [DynamicRuleAdded (Command 1400)](#1400a),[DynamicRuleUpdated (Command 1400)](#1400b),[DynamicRuleRemoved (Command 1400)](#1400c),[DynamicAllRulesRemoved (Command 1400)](#1400d),
  [DyanamicClientAdded (Command 1500)](#1500a),[DynamicClientUpdated (Command 1500)](#1500b),[DynamicClientRemoved (Command 1500)](#1500c)  
- [CommonToAll](#CommonToAll)
------------------------------------------------------------------------------------------------
- [Consumer commands:](#ConsumerCommands)
- [AffiliationAlmondComplete (Command 24)](#24)
- [UpdateDeviceIndex,UpdateDeviceName,AddScene,ActivateScene,UpdateScene,RemoveScene,
RemoveAllScenes,AddRule,ValidateRule,UpdateRule,RemoveRule,RemoveAllRules,UpdateClient,
RemoveClient,ChangeAlmondProperties (Command 1062)](#1062)
- [RouterSummary,GetWirelessSettings,SetWirelessSettings,RebootRouter,SendLogs,FirmwareUpdate,
(Command 1110)](#1110c)
- [AlmondNameChange (Command 62)](#62a)
- [AlmondModeChange (Command 62)](#62b)
- [Command 1020](#1020)
- [CommandType alexaLinking](#alexaLinking)
- [CommandType alexaUnLinking](#alexaUnLinking)
- [ChangeCMSCode (Command 9999)](#9999)
- [Updateclient](#UpdateClient)
- [Id 269](#269)
- [Type removeAll](#removeAll)
------------------------------------------------------------------------------------------------

<a name="1100"></a>
## 1) Command 1100
    Command no 
    1100- JSON format
 
    Required 
    Command,CommandType,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>              //key = (M.ICID + packet.ICID), value = null

    Queue
    3.send packet.ICID to queue

    Functional 
    1.Command 1100

    FLow:
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)
    
<a name="1063"></a>
## 2) Command 1063
    Command no 
    1063- JSON format
 
    Required 
    Command,CommandType,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>             //key = (M.ICID + packet.ICID), value = null

    Queue
    3.send packet.ICID to queue

    Functional 
    1.Command 1063

    FLow:
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)
    
<a name="25"></a>
## 3) Command 25
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
    2.Get CODE:<code>                 //key = (M.CODE + code), value = null
    8.Perform multi:
     i. hmset on UID_<UserID>       //key = (M.USER + UserID), value = (PMAC_<data.AlmondMAC>,1)
     ii. hmset on AL_<pMAC>         //key = (M.ALMOND + pMAC), value = (userID,key)
     iii. hdel on AL_<pMAC>         //key = (M.ALMOND + pMAC),value = [subscriptionToken]

    12.Get ICID_<packet.ICID>        //key = (M.ICID + packet.ICID), value = null
    14.hgetall on UID_<user_list>    //key = (M.USER + <user_list>), value = null

    Queue
    13.Send packet.ICID to queue 
    15.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 25
    9.Delete affiliationStore[data.AlmondMAC]
    10.Destroy Socket
    11.Send AffiliationAlmondCompleteResponse to Almond


    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(affiliation_almond_complete),almondUsers(verify_affiliation_complete)-> processor(dispatchResponses),processor(unicast)->broadcaster(unicast)->processor(broadcaster)->broadcaster(send)
      
<a name="63a"></a>
## 4) Command 63 (AlmondModeChange)
    Command no
    63- JSON format
   
    Required
    Command,ICID,UID,Payload

    Redis
    2.Get ICID_<packet.ICID>              //key = (M.ICID + packet.ICID), value = null
    4.hgetall on UID_<user_list>          //key = (M.USER + <user_list>), value = null

    Queue
    3.Send packet.ICID to queue
    5.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 63

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)
    
 
<a name="63b"></a>
## 5) Command 63 (AlmondNameChange)
    Command no
    63- JSON format
   
    Required
    Command,ICID,UID,Payload

    Redis
    2.Get ICID_<packet.ICID>             //key = (M.ICID + packet.ICID), value = null
    4.hgetall on UID_<user_list>         //key = (M.USER + <user_list>), value = null

    Queue
    3.Send packet.ICID to queue
    5.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 63

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="1050"></a>
## 6) Command 1050 (AlmondProperties)
    Command no
    1050- JSON format
   
    Required
    Command,AlmondMAC,Payload

    Redis
    //key = (M.ALMOND + AlmondMAC), value = [mapper.hashColumn, payload.HashNow]
    2.hmset AL_<AlmondMAC>               

    5.hgetall on UID_<user_list>         //key = (M.USER + <user_list>), value = null
    
    Queue
    4.Send AlmondProperties to BACKGROUND_QUEUE 
    6.Send response to (<user_list>.Substring(Q_length,user_list))

    Functional
    1.Command 1050
    3.Send AlmondPropertiesResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(properties)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)

<a name="1040"></a>
## 7) Command 1040 (AlmondHello)
     Command no
     1040- JSON format

     Required
     Command,UID,AlmondMAC,Payload

     Redis
     //key = (M.ALMOND + AlmondMAC), value = [mapper.hashColumn, payload.HashNow]
     2.hgetall on AL_<AlmondMAC> 

     5.Perform multi:
      i. hmset on UID_<UserID>      //key = (M.USER + UserID), value = (PMAC_<data.AlmondMAC>,1)
      ii. hmset on AL_<pMAC>        //key = (M.ALMOND + pMAC), value = (userID,key)
      iii. hdel on AL_<pMAC>        //key = (M.ALMOND + pMAC), value = [subscriptionToken]  

     SQl
     3.Select on AlmondUsers
       Parameters: AlmondMAC
     4.Select on AlmondSecondaryUsers
       Parameters: AlmondMAC

     Functional
     1.Command 1040

     Flow
     almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(almond_hello)
     
<a name="31"></a>
## 8) Command 31 (AlmondHello)
     Command no
     1040- JSON format

     Required
     Command,UID,AlmondMAC,Payload

     Redis
     //key = (M.ALMOND + AlmondMAC), value = [mapper.hashColumn, payload.HashNow]
     2.hgetall on AL_<AlmondMAC>          

     5.Perform multi:
     i. hmset on UID_<UserID>      //key = (M.USER + UserID), value = (PMAC_<data.AlmondMAC>,1)
     ii. hmset on AL_<pMAC>        //key = (M.ALMOND + pMAC), value = (userID,key)
     iii. hdel on AL_<pMAC>        //key = (M.ALMOND + pMAC), value = [subscriptionToken]

     SQl
     3.Select on AlmondUsers
     Parameters: AlmondMAC
     4.Select on AlmondSecondaryUsers
     Parameters: AlmondMAC

     Functional
     1.Command 31

     Flow
     almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(almond_hello)

<a name="DynamicCommands"></a>
## 9) DynamicCommands
<a name="1050"></a>
## a) Command 1050 (DynamicAlmondProperties)
    Command no
    1050- JSON format
   
    Required
    Command,AlmondMAC,Payload

    Redis
    //key = (M.ALMOND + AlmondMAC), value = [mapper.hashColumn, payload.HashNow]
    2.hmset AL_<AlmondMAC>               

    5.hgetall on UID_<user_list>         //key = (M.USER + <user_list>), value = null

    Queue
    4.Send DynamicAlmondProperties to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1050
    3.Send DynamicAlmondPropertiesResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(properties)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)


<a name="1200a"></a>
## b) Command 1200 (DynamicIndexUpdated)
    Command no
    1200- JSON format
   
    Required
    Command,UID,AlmondMAC,Payload

    Redis
    //key = (M.ALMOND + AlmondMAC), value = [mapper.hashColumn, payload.HashNow]
    2.hmset on AL_<AlmondMAC>            

    7.hgetall on UID_<user_list>         //key = (M.USER + <user_list>), value = null

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

<a name="1200b"></a>
## c) Command 1200 (DynamicDeviceUpdated)
    Command no
    1200- JSON format
   
    Required
    Command,UID,AlmondMAC,Payload

    Redis
    //key = (M.ALMOND + AlmondMAC), value = [mapper.hashColumn, payload.HashNow]
    2.hmset on AL_<AlmondMAC>                

    5.hgetall on UID_<user_list>             //key = (M.USER + <user_list>), value = null

    Queue
    4.Send DynamicDeviceUpdated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length ,user_list.length))

    Functional
    1.Command 1200
    3.Send DynamicDeviceUpdatedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300a"></a>
## d) Command 1300 (DynamicSceneAdded)
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMAC,Payload

    Redis
    //key = (M.ALMOND + AlmondMAC), value = [mapper.hashColumn, payload.HashNow]
    2.hmset on AL_<AlmondMAC>              

    5.hgetall on UID_<user_list>           //key = (M.USER + <user_list>), value = null

    Queue
    4.Send DynamicSceneAdded to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))
    Functional
    1.Command 1300
    3.Send DynamicSceneAddedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300b"></a>
## e) Command 1300 (DynamicSceneActivated)
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMAC,Payload

    Redis
    //key = (M.ALMOND + AlmondMAC), value = [mapper.hashColumn, payload.HashNow]
    2.hmset on AL_<AlmondMAC>              

    5.hgetall on UID_<user_list>           //key = (M.USER + <user_list>), value = null

    Queue
    4.Send DynamicSceneActivated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list))

    Functional
    1.Command 1300
    3.Send DynamicSceneActivatedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300c"></a>
## f) Command 1300 (DynamicSceneUpdated)
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMAC,Payload

    Redis
    //key = (M.ALMOND + AlmondMAC), value = [mapper.hashColumn, payload.HashNow]
    2.hmset on AL_<AlmondMAC>              

    5.hgetall on UID_<user_list>           //key = (M.USER + <user_list>), value = null

    Queue
    4.Send DynamicSceneUpdated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1300
    3.Send DynamicSceneUpdatedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300d"></a>
## g) Command 1300 (DynamicSceneRemoved)
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMAC,Payload

    Redis
    //key = (M.ALMOND + AlmondMAC), value = [mapper.hashColumn, payload.HashNow]
    2.hmset on AL_<AlmondMAC>             

    5.hgetall on UID_<user_list>          //key = (M.USER + <user_list>), value = null

    Queue
    4.Send DynamicSceneRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1300
    3.Send DynamicSceneRemovedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300e"></a>
## h) Command 1300 (DynamicAllSceneRemoved)
    Command no
    1300- JSON format
   
    Required
    Command,UID,AlmondMAC,Payload

    Redis
    //key = (M.ALMOND + AlmondMAC), value = [mapper.hashColumn, payload.HashNow]
    2.hmset on AL_<AlmondMAC>               

    5.hgetall on UID_<user_list>            //key = (M.USER + <user_list>), value = null

    Queue
    4.Send DynamicAllSceneRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1300
    3.Send DynamicAllSceneRemovedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1400a"></a>
## i) Command 1400 (DynamicRuleAdded)
    Command no
    1400- JSON format

    Required
    Command,UID,AlmondMAC,Payload

    Redis
    //key = (M.ALMOND + AlmondMAC), value = [mapper.hashColumn, payload.HashNow]
    2.hmset on AL_<AlmondMAC>              

    5.hgetall on UID_<user_list>           //key = (M.USER + <user_list>), value = null

    Queue
    4.Send DynamicRuleAdded to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1400
    3.Send DynamicRuleAddedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1400b"></a>
## j) Command 1400 (DynamicRuleUpdated)
    Command no
    1400- JSON format

    Required
    Command,UID,AlmondMAC,Payload

    Redis
    //key = (M.ALMOND + AlmondMAC), value = [mapper.hashColumn, payload.HashNow]
    2.hmset on AL_<AlmondMAC>             

    5.hgetall on UID_<user_list>          //key = (M.USER + <user_list>), value = null

    Queue
    4.Send DynamicRuleUpdated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1400
    3.Send DynamicRuleUpdatedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1400c"></a>
## k) Command 1400 (DynamicRuleRemoved)
    Command no
    1400- JSON format

    Required
    Command,UID,AlmondMAC,Payload

    Redis
    //key = (M.ALMOND + AlmondMAC), value = [mapper.hashColumn, payload.HashNow]
    2.hmset on AL_<AlmondMAC>           

    5.hgetall on UID_<user_list>        //key = (M.USER + <user_list>), value = null

    Queue
    4.Send DynamicRuleRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1400
    3.Send DynamicRuleRemovedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1400d"></a>
## l) Command 1400 (DynamicAllRulesRemoved)
    Command no
    1400- JSON format

    Required
    Command,UID,AlmondMAC,Payload

    Redis
    //key = (M.ALMOND + AlmondMAC), value = [mapper.hashColumn, payload.HashNow]
    2.hmset on AL_<AlmondMAC>           

    5.hgetall on UID_<user_list>        //key = (M.USER + <user_list>), value = null

    Queue
    4.Send DynamicAllRulesRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1400
    3.Send DynamicAllRulesRemovedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500a"></a>
## m) Command 1500 (DynamicClientAdded)
    Command no
    1500- JSON format

    Required
    Command,UID,AlmondMAC,Payload

    Redis
    //key = (M.ALMOND + AlmondMAC), value = [mapper.hashColumn, payload.HashNow]
    2.hmset on AL_<AlmondMAC>           

    5.hgetall on UID_<user_list>        //key = (M.USER + <user_list>), value = null

    Queue
    4.Send DynamicClientAdded to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1500
    3.Send DynamicClientAddedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500b"></a>
## n) Command 1500 (DynamicClientUpdated)
    Command no
    1500- JSON format

    Required
    Command,UID,AlmondMAC,Payload

    Redis
    //key = (M.ALMOND + AlmondMAC), value = [mapper.hashColumn, payload.HashNow]
    2.hmset on AL_<AlmondMAC>             

    5.hgetall on UID_<user_list>          //key = (M.USER + <user_list>), value = null

    Queue
    4.Send DynamicClientUpdated to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1500
    3.Send DynamicClientUpdatedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500c"></a>
## o) Command 1500 (DynamicClientRemoved)
    Command no
    1500- JSON format

    Required
    Command,UID,AlmondMAC,Payload

    Redis
    //key = (M.ALMOND + AlmondMAC), value = [mapper.hashColumn, payload.HashNow] 
    2.hmset on AL_<AlmondMAC> 

    5.hgetall on UID_<user_list>       //key = (M.USER + <user_list>), value = null

    Queue
    4.Send DynamicClientRemoved to BACKGROUND_QUEUE
    6.Send response to (<user_list>.Substring(Q_.length,user_list.length))

    Functional
    1.Command 1500
    3.Send DynamicClientRemovedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="CommonToAll"></a>
## 10) CommonToAll

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
------------------------------------------------------------------------------------------------

<a name="ConsumerCommands"></a>
## ConsumerCommands
<a name="24"></a>
## 1) Command 24 (AffiliationAlmondComplete)
    Command no
    24- JSON format

    Required
    Command,Payload,ICID,unicastID,AlmondMAC

    Redis
    4.get on ICID_<result.unicastID>

    Queue
    5.Send result.unicastID to queue

    Functional
    1.Command 24
    2.return socketStore[result.almondMAC]
    3.return affiliationStore[result.almondMAC]
    6.Append result.almondMAC to offlineMACS.txt

<a name="1062"></a>
## 2) Command 1062
    Command no 
    1062- JSON format

    Required
    Command,Payload,ICID,unicastID,AlmondMAC

    Redis
    4.get on ICID_<result.unicastID>

    Queue
    5.Send result.unicastID to queue

    Functional
    1.Command 1062
    2.return socketStore[result.almondMAC]
    3.Send result.payload to Almond
    6.Append result.almondMAC to offlineMACS.txt

<a name="1110c"></a>
## 3) Command 1100
    Command no 
    1110- JSON format

    Required
    Command,Payload,ICID,unicastID,AlmondMAC

    Redis
    4.get on ICID_<result.unicastID>

    Queue
    5.Send result.unicastID to queue

    Functional
    1.Command 1110
    2.return socketStore[result.almondMAC]
    3.Send result.payload to Almond
    6.Append result.almondMAC to offlineMACS.txt

<a name="62a"></a>
## 4) Command 62 (AlmondNameChange)
    Command no 
    62- JSON format
 
    Required 
    Command,CommandType,ICID,Payload

    Redis
    3.get on ICID_<result.unicastID>

    Queue
    4.Send output.CommandType to queue
 
    Functional
    1.Command 62
    2.return socketStore[result.almondMAC]
    5.Append result.almondMAC to offlineMACS.txt

<a name="62b"></a>
## 5) Command 62 (AlmondModeChange)
    Command no 
    62- JSON format
 
    Required 
    Command,CommandType,ICID,Payload

    Redis
    3.get on ICID_<result.unicastID>

    Queue
    4.Send output.CommandType to queue
 
    Functional
    1.Command 62
    2.return socketStore[result.almondMAC]
    5.Append result.almondMAC to offlineMACS.txt

<a name="1020"></a>
## 6) Command 1020 (AffiliationAlmondComplete)
    Command no
    1020- JSON format

    Required
    Command,Payload,ICID,unicastID,AlmondMAC

    Redis
    4.get on ICID_<result.unicastID>

    Queue
    5.Send result.unicastID to queue

    Functional
    1.Command 1020
    2.return socketStore[result.almondMAC]
    3.return affiliationStore[result.almondMAC]
    6.Append result.almondMAC to offlineMACS.txt

<a name="alexaLinking"></a>
## 7) CommandType alexaLinking (Command 1200)
    Command no & CommandType
    1200- JSON format,alexaLinking

    Required
    CommandType,Payload,ICID,unicastID,AlmondMAC

    Redis
    3.hmset on AL_<payload.almondMAC>
    7.get on ICID_<result.unicastID>

    Queue
    5.Send alexaLinking to BACKGROUND_QUEUE
    8.Send result.unicastID to queue

    Functional
    1.CommandType alexaLinking
    2.return socketStore[result.almondMAC]
    4.return clients.master[hmset](AL_<payload.almondMAC>,["alexa", true])
    6.Send result.payload to Almond
    9.Append result.almondMAC to offlineMACS.txt

<a name="alexaUnLinking"></a>
## 8) CommandType alexaUnLinking (Command 1200)
    CommandType
    alexaUnLinking - xml 

    Required
    CommandType,Payload,ICID,unicastID,AlmondMAC
    
    Redis
    3.hdel on AL_<payload.almondMAC>
    7.get on ICID_<result.unicastID>

    Queue
    8.Send result.unicastID to queue

    Functional
    1.CommandType alexaUnLinking
    2.return socketStore[result.almondMAC]
    4.return clients.master[hdel](AL_<payload.almondMAC>,["alexa"])
    5.delete socket.alexa
    6.Send result.payload to Almond
    9.Append result.almondMAC to offlineMACS.txt

<a name="9999"></a>
## 9) Command 9999 (ChangeCMSCode)
    Command no
    9999- JSON format

    Required
    Command,Payload,ICID,unicastID,AlmondMAC

    Redis
    3.get on ICID_<result.unicastID>

    Queue
    4.Send result.unicastID to queue

    Functional
    1.Command 9999
    2.return socketStore[result.almondMAC]
    5.Append result.almondMAC to offlineMACS.txt

<a name="UpdateClient"></a>
## 10) CommandType UpdateClient
    CommandType 
    UpdateClient - xml
   
    Required
    Command,Payload,ICID,unicastID,AlmondMAC

    Redis
    4.get on ICID_<result.unicastID>

    Queue
    5.Send result.unicastID to queue

    Functional
    1.CommandType alexaUnLinking
    2.return socketStore[result.almondMAC] 
    3.Send result.payload to Almond
    6.Append result.almondMAC to offlineMACS.txt

<a name="269"></a>
## 11) Id  269
    Command no 
    269- JSON format
 
    Required 
    Command,CommandType,Payload

    Redis
    if(nodelay)
       if (socket.almondMAC && !socketStore[socket.almondMAC]
    5.hgetall on AL_<AlmondMAC>      //key = (M.Almond+AlmondMAC), value = null

    if (!e && o && o.server == SERVER_NAME)
    //key = (M.Almond+AlmondMAC),value = ["status", "0", "offline", Date.now(), "server", SERVER_NAME]
    6.hmset on AL_<AlmondMAC>  

    Queue
    7.Send AlmondStatus to config.BACKGROUND_QUEUE

    Functional
    1.Id 269
    2.return socketStore[result.almondMAC]
    3.destroySocket(socketStore[result.almondMAC])
    4.delete socketStore[socket.almondMAC]

<a name="removeAll"></a>
## 12) Type removeAll
    Type 
    removeAll - xml

    Required 
    Command,CommandType,Payload

    Redis
    if(nodelay)
      if (socket.almondMAC && !socketStore[socket.almondMAC]
    5.hgetall on AL_<AlmondMAC>      //key = (M.Almond+AlmondMAC), value = null

    if (!e && o && o.server == SERVER_NAME)
    // key = (M.Almond+AlmondMAC),value = ["status", "0", "offline", Date.now(), "server", SERVER_NAME]
    6.hmset on AL_<AlmondMAC>  

    Queue
    7.Send AlmondStatus to config.BACKGROUND_QUEUE

    Functional
    1.Type removeAll
    2.return socketStore[result.almondMAC]
    3.destroySocket(socketStore[result.almondMAC])
    4.delete socketStore[socket.almondMAC]
