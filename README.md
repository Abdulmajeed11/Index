# AlmondServer
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
  [DyanamicClientAdded (Command 1500)](#1500a),[DynamicClientUpdated (Command 1500)](#1500b),[DynamicClientRemoved (Command 1500)](#1500c),
  [DynamicAlmondNameChange (Command 49)](#49),[DynamicAlmondModeChange (Command 153)](#153),
  [DynamicDeviceList (Command 1200)](#1200c),[DynamicDeviceAdded (Command 1200)](#1200d),
  [DynamicAllDevicesRemoved (Command 1200)](#1200e),[DynamicDeviceRemoved (Command 1200)](#1200f)
  [DynamicClientList (Command 1500)](#1500d),[DynamicAllClientsRemoved (Command 1500)](#1500e),
  [DynamicClientJoined (Command 1500)](#1500f),[DynamicClientLeft (Command 1500)](#1500g) 
- [CloudReset (Command 8)](#8)
- [AffiliationAlmondRequest (Command 21)](#21)
- [KeepAlive (Command 104)](#104)
- [AlmondReset (Command 1030)](#1030)

------------------------------------------------------------------------------------------------
- [Consumer commands:](#ConsumerCommands)
- [AffiliationAlmondComplete (Command 24)](#24)
- [UpdateDeviceIndex,UpdateDeviceName,AddScene,ActivateScene,UpdateScene,RemoveScene,
RemoveAllScenes,AddRule,ValidateRule,UpdateRule,RemoveRule,RemoveAllRules,UpdateClient,
RemoveClient,ChangeAlmondProperties (Command 1062)](#1062)
- [RouterSummary,GetWirelessSettings,SetWirelessSettings,RebootRouter,SendLogs,FirmwareUpdate,
(Command 1100)](#1100c)
- [AlmondNameChange (Command 62)](#62a)
- [AlmondModeChange (Command 62)](#62b)
- [Command 1020](#1020)
- [CommandType alexaLinking](#alexaLinking)
- [CommandType alexaUnLinking](#alexaUnLinking)
- [ChangeCMSCode (Command 9999)](#9999)
- [UpdateClient](#UpdateClient)
- [Id 269](#269)
- [Type removeAll](#removeAll)
- [Reset (Command 1030)](#1030)
- [Command 2020](#2020)
------------------------------------------------------------------------------------------------

<a name="1100"></a>
## 1) Command 1100
    Command no 
    1100- JSON format
 
    Required 
    Command,CommandType,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>              //  value = null

    Queue
    3.send Response to queue from step 2

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
    2.Get ICID_<packet.ICID>             // value = null

    Queue
    3.send Response to queue from step 2

    Functional 
    1.Command 1063

    FLow:
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)
    
<a name="25"></a>
## 3) AffiliationAlmondComplete (Command 25)
    Command no
    25- JSON format

    Required
    Command,ICID,Payload,AlmondMAC,CommandType

    Sql
    3.INSERT on AlmondUsers
      Parameters: userID,AlmondMAC,AlmondName,LongSecret,ownership,FirmwareVersion,AffiliationTS
    
    // If(rows are affected)
    4.INSERT on AlmondProperties2
      Parameters: AlmondMAC,Properties,MobileProperties
    5.Select on Subscriptions
      params: AlmondMAC


    // if ((rsData.CMSCode && sRows[i].CMSCode == rsData.CMSCode) || (!rsData.CMSCode &&
    !sRows[i].CMSCode))
    6.Update on Subscriptions
      params: AlmondMAC, UserID

    //else
    7.Update on Subscriptions
      Params: AlmondMAC,USerID

    //check alexa compatible
    8.Select on UserTempPasswords
      Params: UserID

    Redis
    2.Get CODE:<code>                 // value = null
    9.Perform multi:
     i. hmset on UID_<UserID>       // value = (PMAC_<AlmondMAC>,1)
     ii. hmset on AL_<pMAC>         // value = (userID,key)
     iii. hdel on AL_<pMAC>         // value = [subscriptionToken]

    12.Get ICID_<packet.ICID>        // value = null
    14.hgetall on UID_<user_list>    // Returns all the queues for users in user_list

    Queue
    13.Send Response to queue from step 12 
    15.Send Response to All Queues returned in Step 14

    Functional
    1.Command 25
    10.Delete affiliationStore[data.AlmondMAC]
    11.Send AffiliationAlmondCompleteResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(affiliation_almond_complete),almondUsers(verify_affiliation_complete)-> processor(dispatchResponses),processor(unicast)->broadcaster(unicast)->processor(broadcaster)->broadcaster(send)
      
<a name="63a"></a>
## 4) AlmondModeChange (Command 63)
    Command no
    63- JSON format
   
    Required
    Command,ICID,UID,Payload

    Redis
    2.Get ICID_<packet.ICID>              // value = null
    4.hgetall on UID_<user_list>          // Returns all the queues for users in user_list

    Queue
    3.Send Respose to queue from step 2
    5.Send Response to All Queues returned in Step 4

    Functional
    1.Command 63

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)  
 
<a name="63b"></a>
## 5) AlmondNameChange (Command 63)
    Command no
    63- JSON format
   
    Required
    Command,ICID,UID,Payload

    Redis
    2.Get ICID_<packet.ICID>             //key = (M.ICID + packet.ICID), value = null
    4.hgetall on UID_<user_list>          // Returns all the queues for users in user_list

    Queue
    3.Send Response to queue from step 2
    5.Send Response to All Queues returned in Step 4

    Functional
    1.Command 63

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="1050"></a>
## 6) AlmondProperties (Command 1050)
    Command no
    1050- JSON format
   
    Required
    Command,AlmondMAC,Payload,CommandType

    Redis 
    2.hmset AL_<AlmondMAC>              // value = [mapper.hashColumn, payload.HashNow]        
    5.hgetall on UID_<user_list>         // Returns all the queues for users in user_list
    
    Queue
    4.Send AlmondProperties to BACKGROUND_QUEUE 
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1050
    3.Send AlmondPropertiesResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(properties)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)

<a name="1040"></a>
## 7) AlmondHello (Command 1040)
     Command no
     1040- JSON format

     Required
     Command,UID,AlmondMAC,Payload,CommandType

     Redis
     2.hgetall on AL_<AlmondMAC>        // value = [mapper.hashColumn, payload.HashNow]
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
     6.Send AlmondHelloResponse to Almond

     Flow
     almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(almond_hello)->processor(dispatchResponses)
     
<a name="31"></a>
## 8) AlmondHello (Command 31)
     Command no
     31- JSON format

     Required
     Command,UID,AlmondMAC,PayloadCommandType

     Redis
     // value = [mapper.hashColumn, payload.HashNow]
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
     6.Send AlmondHelloResponse to Almond

     Flow
     almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(almond_hello)->processor(dispatchResponses)

<a name="DynamicCommands"></a>
## 9) DynamicCommands
<a name="1050"></a>
## a) DynamicAlmondProperties (Command 1050)
    Command no
    1050- JSON format
   
    Required
    Command,CommandType,AlmondMAC,Payload

    Redis  
    2.hmset AL_<AlmondMAC>              // value = [mapper.hashColumn, payload.HashNow]           
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    Queue
    4.Send DynamicAlmondProperties to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1050
    3.Send DynamicAlmondPropertiesResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(properties)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)

<a name="1200a"></a>
## b) DynamicIndexUpdated (Command 1200)
    Command no
    1200- JSON format
   
    Required
    Command,UID,AlmondMAC,CommandType,Payload

    Redis
    2.hmset on AL_<AlmondMAC>             // value = [mapper.hashColumn, payload.HashNow]
    7.hgetall on UID_<user_list>         // Returns all the queues for users in user_list

    Queue
    4.Send DynamicIndexUpdated to config.ALEXA_QUEUE
    6.Send DynamicIndexUpdated to BACKGROUND_QUEUE
    8.Send Response to All Queues returned in Step 7

    Functional
    1.Command 1200
    3.Send DynamicIndexUpdatedResponse to Almond
    5.delete packet.alexa

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1200b"></a>
## c) DynamicDeviceUpdated (Command 1200)
    Command no
    1200- JSON format
   
    Required
    Command,UID,CommandType,AlmondMAC,Payload

    Redis
    2.hmset on AL_<AlmondMAC>              // value = [mapper.hashColumn, payload.HashNow]  
    5.hgetall on UID_<user_list>           // Returns all the queues for users in user_list

    Queue
    4.Send DynamicDeviceUpdated to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1200
    3.Send DynamicDeviceUpdatedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300a"></a>
## d) DynamicSceneAdded (Command 1300)
    Command no
    1300- JSON format
   
    Required
    Command,UID,CommandType,Payload,AlmondMAC

    Redis
    2.hmset on AL_<AlmondMAC>          // value = [mapper.hashColumn, payload.HashNow]     
    5.hgetall on UID_<user_list>       // Returns all the queues for users in user_list

    Queue
    4.Send DynamicSceneAdded to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1300
    3.Send DynamicSceneAddedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300b"></a>
## e) DynamicSceneActivated (Command 1300)
    Command no
    1300- JSON format
   
    Required
    Command,UID,CommandType,Payload,AlmondMAC

    Redis 
    2.hmset on AL_<AlmondMAC>      // value = [mapper.hashColumn, payload.HashNow]        
    5.hgetall on UID_<user_list>       // Returns all the queues for users in user_list

    Queue
    4.Send DynamicSceneActivated to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1300
    3.Send DynamicSceneActivatedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300c"></a>
## f) DynamicSceneUpdated (Command 1300)
    Command no
    1300- JSON format
   
    Required
    Command,UID,CommandType,Payload,AlmondMAC

    Redis   
    2.hmset on AL_<AlmondMAC>          // value = [mapper.hashColumn, payload.HashNow]      
    5.hgetall on UID_<user_list>       // Returns all the queues for users in user_list

    Queue
    4.Send DynamicSceneUpdated to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1300
    3.Send DynamicSceneUpdatedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300d"></a>
## g) DynamicSceneRemoved (Command 1300)
    Command no
    1300- JSON format
   
    Required
    Command,UID,CommandType,Payload,AlmondMAC

    Redis
    2.hmset on AL_<AlmondMAC>          // value = [mapper.hashColumn, payload.HashNow]    
    5.hgetall on UID_<user_list>       // Returns all the queues for users in user_list

    Queue
    4.Send DynamicSceneRemoved to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1300
    3.Send DynamicSceneRemovedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300e"></a>
## h) DynamicAllSceneRemoved (Command 1300)
    Command no
    1300- JSON format
   
    Required
    Command,UID,CommandType,Payload,AlmondMAC

    Redis
   
    2.hmset on AL_<AlmondMAC>             // value = [mapper.hashColumn, payload.HashNow]       
    5.hgetall on UID_<user_list>         // Returns all the queues for users in user_list

    Queue
    4.Send DynamicAllSceneRemoved to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1300
    3.Send DynamicAllSceneRemovedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1400a"></a>
## i) DynamicRuleAdded (Command 1400)
    Command no
    1400- JSON format

    Required
    Command,UID,CommandType,Payload,AlmondMAC

    Redis
    2.hmset on AL_<AlmondMAC>               // value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>            // Returns all the queues for users in user_list

    Queue
    4.Send DynamicRuleAdded to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1400
    3.Send DynamicRuleAddedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1400b"></a>
## j) DynamicRuleUpdated (Command 1400)
    Command no
    1400- JSON format

    Required
    Command,UID,CommandType,Payload,AlmondMAC

    Redis  
    2.hmset on AL_<AlmondMAC>              // value = [mapper.hashColumn, payload.HashNow]    
    5.hgetall on UID_<user_list>           // Returns all the queues for users in user_list

    Queue
    4.Send DynamicRuleUpdated to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1400
    3.Send DynamicRuleUpdatedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1400c"></a>
## k) DynamicRuleRemoved (Command 1400)
    Command no
    1400- JSON format

    Required
    Command,UID,CommandType,Payload,AlmondMAC

    Redis
    2.hmset on AL_<AlmondMAC>           // value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    Queue
    4.Send DynamicRuleRemoved to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1400
    3.Send DynamicRuleRemovedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1400d"></a>
## l) DynamicAllRulesRemoved (Command 1400)
    Command no
    1400- JSON format

    Required
    Command,UID,CommandType,Payload,AlmondMAC

    Redis
    2.hmset on AL_<AlmondMAC>           // value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    Queue
    4.Send DynamicAllRulesRemoved to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1400
    3.Send DynamicAllRulesRemovedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500a"></a>
## m) DynamicClientAdded (Command 1500)
    Command no
    1500- JSON format

    Required
    Command,UID,CommandType,Payload,AlmondMAC

    Redis    
    2.hmset on AL_<AlmondMAC>           // value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    Queue
    4.Send DynamicClientAdded to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1500
    3.Send DynamicClientAddedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500b"></a>
## n) DynamicClientUpdated (Command 1500)
    Command no
    1500- JSON format

    Required
    Command,UID,CommandType,Payload,AlmondMAC

    Redis  
    2.hmset on AL_<AlmondMAC>              // value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>           // Returns all the queues for users in user_list

    Queue
    4.Send DynamicClientUpdated to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1500
    3.Send DynamicClientUpdatedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500c"></a>
## o) DynamicClientRemoved (Command 1500)
    Command no
    1500- JSON format

    Required
    Command,UID,CommandType,Payload,AlmondMAC

    Redis   
    2.hmset on AL_<AlmondMAC>          //value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>       // Returns all the queues for users in user_list

    Queue
    4.Send DynamicClientRemoved to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1500
    3.Send DynamicClientRemovedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="49"></a>
## p) DynamicAlmondNameChange (Command 49)
    Command no
    49- JSON format

    Required
    Command,UID,CommandType,Payload

    Redis
    4.hgetall on UID_<user_list>       // Returns all the queues for users in user_list

    Queue
    3.Send DynamicAlmondNameChange to BACKGROUND_QUEUE
    5.Send Response to All Queues returned in Step 4

    Functional
    1.Command 49
    2.Send DynamicAlmondNameChangeResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="153"></a>
## q) DynamicAlmondModeChange (Command 153)
    Command no
    153- JSON format

    Required
    Command,UID,CommandType,Payload

    Redis
    4.hgetall on UID_<user_list>       // Returns all the queues for users in user_list

    Queue
    3.Send DynamicAlmondModeChange to BACKGROUND_QUEUE
    5.Send Response to All Queues returned in Step 4

    Functional
    1.Command 153
    2.Send DynamicAlmondModeChangeResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(almondmode_change)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1200c"></a>
## r) DynamicDeviceList (Command 1200)
    Command no
    1200- JSON format

    Required
    Command,UID,CommandType,Payload

    Redis   
    2.hmset on AL_<AlmondMAC>          //value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    Queue
    4.Send DynamicDeviceList to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1200
    3.Send DynamicDeviceListResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1200d"></a>
## s) DynamicDeviceAdded (Command 1200)
    Command no
    1200- JSON format

    Required
    Command,UID,CommandType,Payload

    Redis   
    2.hmset on AL_<AlmondMAC>          //value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    Queue
    4.Send DynamicDevieAdded to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1200
    3.Send DynamicDeviceAddedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1200e"></a>
## t) DynamicAllDevicesRemoved (Command 1200)
    Command no
    1200- JSON format

    Required
    Command,UID,CommandType,Payload

    Redis   
    2.hmset on AL_<AlmondMAC>          //value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    Queue
    4.Send DynamicAllDevicesRemoved to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1200
    3.Send DynamicAllDevicesRemovedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1200f"></a>
## u) DynamicDeviceRemoved (Command 1200)
    Command no
    1200- JSON format

    Required
    Command,UID,CommandType,Payload

    Redis   
    2.hmset on AL_<AlmondMAC>          //value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    Queue
    4.Send DynamicDeviceRemoved to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1200
    3.Send DynamicDeviceRemovedResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500d"></a>
## v) DynamicClientList (Command 1500)
    Command no
    1500- JSON format

    Required
    Command,UID,CommandType,Payload

    Redis    
    2.hmset on AL_<AlmondMAC>           // value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    Queue
    4.Send DynamicClientList to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1500
    3.Send DynamicClientListResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500e"></a>
## w) DynamicAllClientsRemoved (Command 1500)
    Command no
    1500- JSON format

    Required
    Command,UID,CommandType,Payload

    Redis    
    2.hmset on AL_<AlmondMAC>           // value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    Queue
    4.Send DynamicAllClientsRemoved to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1500
    3.Send DynamicAllClientsRemovedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500f"></a>
## x) DynamicClientJoined (Command 1500)
    Command no
    1500- JSON format

    Required
    Command,UID,CommandType,Payload

    Redis    
    2.hmset on AL_<AlmondMAC>           // value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    Queue
    4.Send DynamicClientJoined to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1500
    3.Send DynamicClientJoinedResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500g"></a>
## y) DynamicClientLeft (Command 1500)
    Command no
    1500- JSON format

    Required
    Command,UID,CommandType,Payload

    Redis    
    2.hmset on AL_<AlmondMAC>           // value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    Queue
    4.Send DynamicClientLeft to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    Functional
    1.Command 1500
    3.Send DynamicClientLeftResponse to Almond

    Flow
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="8"></a>
## 10) CloudReset (Command 8)
    Command no
    8- JSON format

    Required
    Command,CommandType,Payload

    Redis
    4.hgetall on UID_<user_list>       // Returns all the queues for users in user_list

    Queue
    3.Send CloudReset to BACKGROUND_QUEUE
    5.Send Response to All Queues returned in Step 4

    Functional
    1.Command 8
    2.Send CloudResetResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(almondReset)->processor(dispatchResponses)->processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="21"></a>
## 11) AffiliationAlmondRequest (Command 21)
    Command no
    21- JSON format

    Required
    Command,CommandType,Payload,ALmondMAC

    SQl
    2.Select on AlmondUsers
      params: AlmondMAC, ownership
    3.Select on AllAlmondPlus
      params:AlmondMAC
    4.Insert on AllAlmondPlus
      params: AlmondMAC, AlmondID, FactoryDate, FactoryAdmin

    Redis
    5.hgetall on CODE:code                  // where code = random string 
    6.setex on CODE:code  
    // where code = random string,values =AlmondMAC + config.Connections.RabbitMQ.Queue

    Functional
    1.Command 21
    7.Send AffiliationAlmondRequestResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(affiliation_almond)->affiliate-almond(affiliate_almond)->processor(dispatchResponses)

<a name="104"></a>
## 12) KeepAlive (Command 104)
    Command no
    104- JSON format

    Required
    Command,CommandType,Payload,AlmondMAC

    Redis
    3.hmset on AL_<AlmondMAC>        // values = status,1,server,config.Connections.RabbitMQ.Queue

    // if (socket.zenAlmond)
    Cassandra
    4.Update on cloudlogs.connection_log
      params: dateyear, mac
     
    Functional
    1.Command 104
    2.delete socket.almondOnline

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondStore(keepAlive)

<a name="1030"></a>
## 13) AlmondReset (Command 1030)
    Command no
    1030- JSON format

    Required
    Command,CommandType,Payload,AlmondMAC

    Functional
    1.Command 1030
    2.Send AlmondResetResponse to Almond

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(almondReset)->processor(dispatchResponses)

------------------------------------------------------------------------------------------------

<a name="ConsumerCommands"></a>
## ConsumerCommands
<a name="24"></a>
## 1) AffiliationAlmondComplete (Command 24)
    Command no
    24- JSON format

    Required
    Command,Payload,ICID,unicastID,AlmondMAC

    Redis
    3.get on ICID_<result.unicastID>    

    Queue
    4.Send Response to queue

    Functional
    1.Command 24
    2.Send Res,CommandLengthType to Almond
    5.Append result.almondMAC to offlineMACS.txt

<a name="1062"></a>
## 2) Command 1062
    Command no 
    1062- JSON format

    Required
    Command,Payload,ICID,unicastID,AlmondMAC

    Redis
    3.get on ICID_<result.unicastID>        

    Queue
    4.Send Response to queue

    Functional
    1.Command 1062
    2.Send Res,CommandLengthType to Almond
    5.Append result.almondMAC to offlineMACS.txt

<a name="1100c"></a>
## 3) Command 1100
    Command no 
    1110- JSON format

    Required
    Command,Payload,ICID,unicastID,AlmondMAC

    Redis
    3.get on ICID_<result.unicastID>        

    Queue
    4.Send Response to queue

    Functional
    1.Command 1100
    2.Send Res,CommandLengthType to Almond
    5.Append result.almondMAC to offlineMACS.txt

<a name="62a"></a>
## 4) AlmondNameChange (Command 62)
    Command no 
    62- JSON format
 
    Required 
    Command,CommandType,ICID,Payload

    Redis
    2.get on ICID_<result.unicastID>          

    Queue
    3.Send Response to queue
 
    Functional
    1.Command 62
    4.Append result.almondMAC to offlineMACS.txt

<a name="62b"></a>
## 5) AlmondModeChange (Command 62)
    Command no 
    62- JSON format
 
    Required 
    Command,CommandType,ICID,Payload

    Redis
    2.get on ICID_<result.unicastID>           

    Queue
    3.Send Response to queue
 
    Functional
    1.Command 62
    4.Append result.almondMAC to offlineMACS.txt

<a name="1020"></a>
## 6) AffiliationAlmondComplete(Command 1020)
    Command no
    1020- JSON format

    Required
    Command,Payload,ICID,unicastID,AlmondMAC

    Redis
    2.get on ICID_<result.unicastID>          

    Queue
    3.Send Response to queue

    Functional
    1.Command 1020
    4.Append result.almondMAC to offlineMACS.txt

<a name="alexaLinking"></a>
## 7) CommandType alexaLinking (Command 1200)
    Command no & CommandType
    1200- JSON format,alexaLinking

    Required
    CommandType,Payload,ICID,unicastID,AlmondMAC

    Redis
    2.hmset on AL_<payload.almondMAC>
    5.get on ICID_<result.unicastID>            

    Queue
    3.Send alexaLinking to BACKGROUND_QUEUE
    6.Send Response to queue

    Functional
    1.CommandType alexaLinking
    4.Send Res,CommandLengthType to Almond
    7.Append result.almondMAC to offlineMACS.txt

<a name="alexaUnLinking"></a>
## 8) CommandType alexaUnLinking (Command 1200)
    CommandType
    alexaUnLinking - xml 

    Required
    CommandType,Payload,ICID,unicastID,AlmondMAC
    
    Redis
    2.hdel on AL_<payload.almondMAC>
    5.get on ICID_<result.unicastID>           

    Queue
    6.Send Response to queue

    Functional
    1.CommandType alexaUnLinking
    3.delete socket.alexa
    4.Send Res,CommandLengthType to Almond
    7.Append result.almondMAC to offlineMACS.txt

<a name="9999"></a>
## 9) Command 9999 (ChangeCMSCode)
    Command no
    9999- JSON format

    Required
    Command,Payload,ICID,unicastID,AlmondMAC

    Redis
    2.get on ICID_<result.unicastID>              

    Queue
    3.Send Response to queue

    Functional
    1.Command 9999
    4.Append result.almondMAC to offlineMACS.txt

<a name="UpdateClient"></a>
## 10) CommandType UpdateClient
    CommandType 
    UpdateClient - xml
   
    Required
    Command,Payload,ICID,unicastID,AlmondMAC

    Redis
    3.get on ICID_<result.unicastID>             

    Queue
    4.Send Response to queue

    Functional
    1.CommandType UpdateClient 
    2.Send Res,CommandLengthType to Almond
    5.Append result.almondMAC to offlineMACS.txt

<a name="269"></a>
## 11) Id  269
    Command no 
    269- JSON format
 
    Required 
    Command,CommandType,Payload

    Redis
    // if(nodelay)
    3.hgetall on AL_<AlmondMAC>      
   
    4.hmset on AL_<AlmondMAC>       
    /* values = [status,0,offline,Date,server,config.Connections.RabbitMQ.Queue] */

    Queue
    5.Send AlmondStatus to config.BACKGROUND_QUEUE

    Functional
    1.Id 269
    2.delete socketStore[socket.almondMAC]

<a name="removeAll"></a>
## 12) Type removeAll
    Type 
    removeAll - xml

    Required 
    Command,CommandType,Payload

    Redis
    // if(nodelay)

    5.hgetall on AL_<AlmondMAC>    
    6.hmset on AL_<AlmondMAC>  
    //values = [status,0,offline,Date,server,config.Connections.RabbitMQ.Queue]

    Queue
    7.Send AlmondStatus to config.BACKGROUND_QUEUE

    Functional
    1.Type removeAll
    4.delete socketStore[socket.almondMAC]

<a name="1030"></a>
## 13) Command 1030 (reset)
    Command 
    1030- JSON format 
    
    Required 
    Command,CommandType,almondMAC

    Functional
    1.Command 1030

    //if (ZEN.MACS.indexOf(parseInt(mac)) >= 0) 
    2. return almondMAC

<a name="2020"></a>
## 14) ID 2020
    Command 
    2020- JSON format 
    
    Required 
    Command,CommandType,almondMAC
 
    Queue
    2.Send Response to result.queue
    
    Functional
    1.Command 2020




