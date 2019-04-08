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
- [AffiliationAlmondRequest (Command 1020)](#1020a)
- [AffiliationCompleteResponse (Command 1020)](#1020b)
- [AlmondValidationRequest (Command 106)](#106)
- [HashList (Command 1702)](#1702)
- [ReadyToAddAsSlave,AddWiredSlaveMobile (Command 1600)](#1600)

------------------------------------------------------------------------------------------------
- [Consumer commands:](#ConsumerCommands)
- [AffiliationAlmondComplete (Command 24)](#24)
- [UpdateDeviceIndex,UpdateDeviceName,AddScene,ActivateScene,UpdateScene,RemoveScene,
RemoveAllScenes,AddRule,ValidateRule,UpdateRule,RemoveRule,RemoveAllRules,UpdateClient,
RemoveClient,ChangeAlmondProperties (Command 1062)](#1062)
- [RouterSummary,GetWirelessSettings,SetWirelessSettings,RebootRouter,SendLogs,FirmwareUpdate,
(Command 1100)](#1100c)
- [AlmondNameChange,AlmondModeChange (Command 62)](#62)
- [Command 1020](#1020)
- [CommandType alexaLinking](#alexaLinking)
- [CommandType alexaUnLinking](#alexaUnLinking)
- [ChangeCMSCode (Command 9999)](#9999)
- [UpdateClient](#UpdateClient)
- [Id 269](#269)
- [Type removeAll](#removeAll)
- [Reset (Command 1030)](#1030)
- [Command 2020](#2020)
- [Command 1110](#1110)
- [Command 1060](#1060)
- [Command 1700](#1700)
- [Command 1525](#1525)
- [Command 300](#300)
- [Command 4](#4)
- [Command 1026](#1026)

------------------------------------------------------------------------------------------------

<a name="1100"></a>
## 1) Command 1100
    Command no 
    1100- JSON format
 
    REQUIRED - 
    Command,CommandType,ICID,Payload

    REDIS -
    2.Get ICID_<packet.ICID>              //  value = null

    QUEUE -
    3.send Response to QUEUE - from step 2

    FUNCTIONAL - 
    1.Command 1100

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)
    
<a name="1063"></a>
## 2) Command 1063
    Command no 
    1063- JSON format
 
    REQUIRED - 
    Command,CommandType,ICID,Payload

    REDIS -
    2.Get ICID_<packet.ICID>             // value = null

    QUEUE -
    3.send Response to QUEUE - from step 2

    FUNCTIONAL - 
    1.Command 1063

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)
    
<a name="25"></a>
## 3) AffiliationAlmondComplete (Command 25)
    Command no
    25- XML format

    REQUIRED -
    Command,ICID,Payload,AlmondMAC,CommandType

    SQL -
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
      Params: AlmondMAC,UserID

    //check alexa compatible
    8.Select on UserTempPasswords
      Params: UserID

    REDIS -
    2.Get CODE:<code>                 // value = null
    9.Perform multi:
     i. hmset on UID_<UserID>       // value = (PMAC_<AlmondMAC>,1)
     ii. hmset on AL_<pMAC>         // value = (userID,key)
     iii. hdel on AL_<pMAC>         // value = [subscriptionToken]

    12.Get ICID_<packet.ICID>        // value = null
    14.hgetall on UID_<user_list>    // Returns all the queues for users in user_list

    QUEUE -
    13.Send Response to Queue from step 12 
    15.Send Response to All Queues returned in Step 14

    FUNCTIONAL -
    1.Command 25
    10.Delete affiliationStore[data.AlmondMAC]
    11.Send AffiliationAlmondCompleteResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(affiliation_almond_complete),almondUsers(verify_affiliation_complete)-> processor(dispatchResponses),processor(unicast)->broadcaster(unicast)->processor(broadcaster)->broadcaster(send)
      
<a name="63a"></a>
## 4) AlmondModeChange (Command 63)
    Command no
    63- XML format
   
    REQUIRED -
    Command,ICID,UID,Payload

    REDIS -
    2.Get ICID_<packet.ICID>              // value = null
    4.hgetall on UID_<user_list>          // Returns all the queues for users in user_list

    QUEUE -
    3.Send Respose to QUEUE - from step 2
    5.Send Response to All Queues returned in Step 4

    FUNCTIONAL -
    1.Command 63

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)  
 
<a name="63b"></a>
## 5) AlmondNameChange (Command 63)
    Command no
    63- XML format
   
    REQUIRED -
    Command,ICID,UID,Payload

    REDIS -
    2.Get ICID_<packet.ICID>             //key = (M.ICID + packet.ICID), value = null
    4.hgetall on UID_<user_list>          // Returns all the queues for users in user_list

    QUEUE -
    3.Send Response to QUEUE - from step 2
    5.Send Response to All Queues returned in Step 4

    FUNCTIONAL -
    1.Command 63

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="1050"></a>
## 6) AlmondProperties (Command 1050)
    Command no
    1050- JSON format
   
    REQUIRED -
    Command,AlmondMAC,Payload,CommandType

    REDIS - 
    2.hmset AL_<AlmondMAC>              // value = [mapper.hashColumn, payload.HashNow]        
    5.hgetall on UID_<user_list>         // Returns all the queues for users in user_list
    
    QUEUE -
    4.Send AlmondProperties to BACKGROUND_QUEUE 
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1050
    3.Send AlmondPropertiesResponse to Almond

    FLOW -
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(properties)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)

<a name="1040"></a>
## 7) AlmondHello (Command 1040)
     Command no
     1040- JSON format

     REQUIRED -
     Command,UID,AlmondMAC,Payload,CommandType

     REDIS -
     2.hgetall on AL_<AlmondMAC>        // value = [mapper.hashColumn, payload.HashNow]
     5.Perform multi:
      i. hmset on UID_<UserID>      //key = (M.USER + UserID), value = (PMAC_<data.AlmondMAC>,1)
      ii. hmset on AL_<pMAC>        //key = (M.ALMOND + pMAC), value = (userID,key)
      iii. hdel on AL_<pMAC>        //key = (M.ALMOND + pMAC), value = [subscriptionToken]  

     SQL -     
     3.Select on AlmondUsers
       Parameters: AlmondMAC
     4.Select on AlmondSecondaryUsers
       Parameters: AlmondMAC

     FUNCTIONAL -
     1.Command 1040
     6.Send AlmondHelloResponse to Almond

     FLOW -
     almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(almond_hello)->processor(dispatchResponses)
     
<a name="31"></a>
## 8) AlmondHello (Command 31)
     Command no
     31- XML format

     REQUIRED -
     Command,UID,AlmondMAC,PayloadCommandType

     REDIS -
     // value = [mapper.hashColumn, payload.HashNow]
     2.hgetall on AL_<AlmondMAC>          

     5.Perform multi:
     i. hmset on UID_<UserID>      //key = (M.USER + UserID), value = (PMAC_<data.AlmondMAC>,1)
     ii. hmset on AL_<pMAC>        //key = (M.ALMOND + pMAC), value = (userID,key)
     iii. hdel on AL_<pMAC>        //key = (M.ALMOND + pMAC), value = [subscriptionToken]

     SQL -
     3.Select on AlmondUsers
     Parameters: AlmondMAC
     4.Select on AlmondSecondaryUsers
     Parameters: AlmondMAC

     FUNCTIONAL -
     1.Command 31
     6.Send AlmondHelloResponse to Almond

     FLOW -
     almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(almond_hello)->processor(dispatchResponses)

<a name="DynamicCommands"></a>
## 9) DynamicCommands
<a name="1050"></a>
## a) DynamicAlmondProperties (Command 1050)
    Command no
    1050- JSON format
   
    REQUIRED -
    Command,CommandType,AlmondMAC,Payload

    REDIS -  
    2.hmset AL_<AlmondMAC>              // value = [mapper.hashColumn, payload.HashNow]           
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicAlmondProperties to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1050
    3.Send DynamicAlmondPropertiesResponse to Almond

    FLOW -
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(properties)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)

<a name="1200a"></a>
## b) DynamicIndexUpdated (Command 1200)
    Command no
    1200- JSON format
   
    REQUIRED -
    Command,UID,AlmondMAC,CommandType,Payload

    REDIS -
    2.hmset on AL_<AlmondMAC>             // value = [mapper.hashColumn, payload.HashNow]
    7.hgetall on UID_<user_list>         // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicIndexUpdated to config.ALEXA_QUEUE
    6.Send DynamicIndexUpdated to BACKGROUND_QUEUE
    8.Send Response to All Queues returned in Step 7

    FUNCTIONAL -
    1.Command 1200
    3.Send DynamicIndexUpdatedResponse to Almond
    5.delete packet.alexa

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1200b"></a>
## c) DynamicDeviceUpdated (Command 1200)
    Command no
    1200- JSON format
   
    REQUIRED -
    Command,UID,CommandType,AlmondMAC,Payload

    REDIS -
    2.hmset on AL_<AlmondMAC>              // value = [mapper.hashColumn, payload.HashNow]  
    5.hgetall on UID_<user_list>           // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicDeviceUpdated to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1200
    3.Send DynamicDeviceUpdatedResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300a"></a>
## d) DynamicSceneAdded (Command 1300)
    Command no
    1300- JSON format
   
    REQUIRED -
    Command,UID,CommandType,Payload,AlmondMAC

    REDIS -
    2.hmset on AL_<AlmondMAC>          // value = [mapper.hashColumn, payload.HashNow]     
    5.hgetall on UID_<user_list>       // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicSceneAdded to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1300
    3.Send DynamicSceneAddedResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300b"></a>
## e) DynamicSceneActivated (Command 1300)
    Command no
    1300- JSON format
   
    REQUIRED -
    Command,UID,CommandType,Payload,AlmondMAC

    REDIS - 
    2.hmset on AL_<AlmondMAC>      // value = [mapper.hashColumn, payload.HashNow]        
    5.hgetall on UID_<user_list>       // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicSceneActivated to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1300
    3.Send DynamicSceneActivatedResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300c"></a>
## f) DynamicSceneUpdated (Command 1300)
    Command no
    1300- JSON format
   
    REQUIRED -
    Command,UID,CommandType,Payload,AlmondMAC

    REDIS -   
    2.hmset on AL_<AlmondMAC>          // value = [mapper.hashColumn, payload.HashNow]      
    5.hgetall on UID_<user_list>       // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicSceneUpdated to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1300
    3.Send DynamicSceneUpdatedResponse to Almond

    FLOW -
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300d"></a>
## g) DynamicSceneRemoved (Command 1300)
    Command no
    1300- JSON format
   
    REQUIRED -
    Command,UID,CommandType,Payload,AlmondMAC

    REDIS -
    2.hmset on AL_<AlmondMAC>          // value = [mapper.hashColumn, payload.HashNow]    
    5.hgetall on UID_<user_list>       // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicSceneRemoved to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1300
    3.Send DynamicSceneRemovedResponse to Almond

    FLOW -
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1300e"></a>
## h) DynamicAllSceneRemoved (Command 1300)
    Command no
    1300- JSON format
   
    REQUIRED -
    Command,UID,CommandType,Payload,AlmondMAC

    REDIS -  
    2.hmset on AL_<AlmondMAC>             // value = [mapper.hashColumn, payload.HashNow]       
    5.hgetall on UID_<user_list>         // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicAllSceneRemoved to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1300
    3.Send DynamicAllSceneRemovedResponse to Almond

    FLOW -
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1400a"></a>
## i) DynamicRuleAdded (Command 1400)
    Command no
    1400- JSON format

    REQUIRED -
    Command,UID,CommandType,Payload,AlmondMAC

    REDIS -
    2.hmset on AL_<AlmondMAC>               // value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>            // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicRuleAdded to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1400
    3.Send DynamicRuleAddedResponse to Almond

    FLOW -
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1400b"></a>
## j) DynamicRuleUpdated (Command 1400)
    Command no
    1400- JSON format

    REQUIRED -
    Command,UID,CommandType,Payload,AlmondMAC

    REDIS -  
    2.hmset on AL_<AlmondMAC>              // value = [mapper.hashColumn, payload.HashNow]    
    5.hgetall on UID_<user_list>           // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicRuleUpdated to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1400
    3.Send DynamicRuleUpdatedResponse to Almond

    FLOW -
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1400c"></a>
## k) DynamicRuleRemoved (Command 1400)
    Command no
    1400- JSON format

    REQUIRED -
    Command,UID,CommandType,Payload,AlmondMAC

    REDIS -
    2.hmset on AL_<AlmondMAC>           // value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicRuleRemoved to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1400
    3.Send DynamicRuleRemovedResponse to Almond

    FLOW -
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1400d"></a>
## l) DynamicAllRulesRemoved (Command 1400)
    Command no
    1400- JSON format

    REQUIRED -
    Command,UID,CommandType,Payload,AlmondMAC

    REDIS -
    2.hmset on AL_<AlmondMAC>           // value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicAllRulesRemoved to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1400
    3.Send DynamicAllRulesRemovedResponse to Almond

    FLOW -
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500a"></a>
## m) DynamicClientAdded (Command 1500)
    Command no
    1500- JSON format

    REQUIRED -
    Command,UID,CommandType,Payload,AlmondMAC

    REDIS -    
    2.hmset on AL_<AlmondMAC>           // value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicClientAdded to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1500
    3.Send DynamicClientAddedResponse to Almond

    FLOW -
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500b"></a>
## n) DynamicClientUpdated (Command 1500)
    Command no
    1500- JSON format

    REQUIRED -
    Command,UID,CommandType,Payload,AlmondMAC

    REDIS -  
    2.hmset on AL_<AlmondMAC>              // value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>           // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicClientUpdated to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1500
    3.Send DynamicClientUpdatedResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500c"></a>
## o) DynamicClientRemoved (Command 1500)
    Command no
    1500- JSON format

    REQUIRED -
    Command,UID,CommandType,Payload,AlmondMAC

    REDIS -   
    2.hmset on AL_<AlmondMAC>          //value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>       // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicClientRemoved to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1500
    3.Send DynamicClientRemovedResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="49"></a>
## p) DynamicAlmondNameChange (Command 49)
    Command no
    49- XML format

    REQUIRED -
    Command,UID,CommandType,Payload

    REDIS -
    4.hgetall on UID_<user_list>       // Returns all the queues for users in user_list

    QUEUE -
    3.Send DynamicAlmondNameChange to BACKGROUND_QUEUE
    5.Send Response to All Queues returned in Step 4

    FUNCTIONAL -
    1.Command 49
    2.Send DynamicAlmondNameChangeResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="153"></a>
## q) DynamicAlmondModeChange (Command 153)
    Command no
    153- XML format

    REQUIRED -
    Command,UID,CommandType,Payload

    REDIS -
    4.hgetall on UID_<user_list>       // Returns all the queues for users in user_list

    QUEUE -
    3.Send DynamicAlmondModeChange to BACKGROUND_QUEUE
    5.Send Response to All Queues returned in Step 4

    FUNCTIONAL -
    1.Command 153
    2.Send DynamicAlmondModeChangeResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(almondmode_change)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1200c"></a>
## r) DynamicDeviceList (Command 1200)
    Command no
    1200- JSON format

    REQUIRED -
    Command,UID,CommandType,Payload

    REDIS -   
    2.hmset on AL_<AlmondMAC>          //value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicDeviceList to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1200
    3.Send DynamicDeviceListResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1200d"></a>
## s) DynamicDeviceAdded (Command 1200)
    Command no
    1200- JSON format

    REQUIRED -
    Command,UID,CommandType,Payload

    REDIS -   
    2.hmset on AL_<AlmondMAC>          //value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicDevieAdded to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1200
    3.Send DynamicDeviceAddedResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1200e"></a>
## t) DynamicAllDevicesRemoved (Command 1200)
    Command no
    1200- JSON format

    REQUIRED -
    Command,UID,CommandType,Payload

    REDIS -   
    2.hmset on AL_<AlmondMAC>          //value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicAllDevicesRemoved to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1200
    3.Send DynamicAllDevicesRemovedResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1200f"></a>
## u) DynamicDeviceRemoved (Command 1200)
    Command no
    1200- JSON format

    REQUIRED -
    Command,UID,CommandType,Payload

    REDIS -   
    2.hmset on AL_<AlmondMAC>          //value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicDeviceRemoved to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1200
    3.Send DynamicDeviceRemovedResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500d"></a>
## v) DynamicClientList (Command 1500)
    Command no
    1500- JSON format

    REQUIRED -
    Command,UID,CommandType,Payload

    REDIS -    
    2.hmset on AL_<AlmondMAC>           // value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicClientList to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1500
    3.Send DynamicClientListResponse to Almond

    FLOW -
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500e"></a>
## w) DynamicAllClientsRemoved (Command 1500)
    Command no
    1500- JSON format

    REQUIRED -
    Command,UID,CommandType,Payload

    REDIS -    
    2.hmset on AL_<AlmondMAC>           // value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicAllClientsRemoved to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1500
    3.Send DynamicAllClientsRemovedResponse to Almond

    FLOW -
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500f"></a>
## x) DynamicClientJoined (Command 1500)
    Command no
    1500- JSON format

    REQUIRED -
    Command,UID,CommandType,Payload

    REDIS -    
    2.hmset on AL_<AlmondMAC>           // value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicClientJoined to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1500
    3.Send DynamicClientJoinedResponse to Almond

    FLOW -
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="1500g"></a>
## y) DynamicClientLeft (Command 1500)
    Command no
    1500- JSON format

    REQUIRED -
    Command,UID,CommandType,Payload

    REDIS -    
    2.hmset on AL_<AlmondMAC>           // value = [mapper.hashColumn, payload.HashNow]
    5.hgetall on UID_<user_list>        // Returns all the queues for users in user_list

    QUEUE -
    4.Send DynamicClientLeft to BACKGROUND_QUEUE
    6.Send Response to All Queues returned in Step 5

    FUNCTIONAL -
    1.Command 1500
    3.Send DynamicClientLeftResponse to Almond

    FLOW -
    almondProtocol(packet)->processor(do)->processor(validate)->almondUsers(execute)->processor(dispatchResponses),processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="8"></a>
## 10) CloudReset (Command 8)
    Command no
    8- XML format

    REQUIRED -
    Command,CommandType,Payload

    REDIS -
    4.hgetall on UID_<user_list>       // Returns all the queues for users in user_list

    QUEUE -
    3.Send CloudReset to BACKGROUND_QUEUE
    5.Send Response to All Queues returned in Step 4

    FUNCTIONAL -
    1.Command 8
    2.Send CloudResetResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(almondReset)->processor(dispatchResponses)->processor(sendToBackground)->broadcaster(sendToBackground)->processor(broadcast)->broadcaster(send)

<a name="21"></a>
## 11) AffiliationAlmondRequest (Command 21)
    Command no
    21- XML format

    REQUIRED -
    Command,CommandType,Payload,ALmondMAC

    SQL -
    2.Select on AlmondUsers
      params: AlmondMAC, ownership
    3.Select on AllAlmondPlus
      params:AlmondMAC
    4.Insert on AllAlmondPlus
      params: AlmondMAC, AlmondID, FactoryDate, FactoryAdmin

    REDIS -
    5.hgetall on CODE:code                  // where code = random string 
    6.setex on CODE:code  
    // where code = random string,values =AlmondMAC + config.Connections.RabbitMQ.Queue

    FUNCTIONAL -
    1.Command 21
    7.Send AffiliationAlmondRequestResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(affiliation_almond)->affiliate-almond(affiliate_almond)->processor(dispatchResponses)

<a name="104"></a>
## 12) KeepAlive (Command 104)
    Command no
    104- XML format

    REQUIRED -
    Command,CommandType,Payload,AlmondMAC

    REDIS -
    3.hmset on AL_<AlmondMAC>        // values = status,1,server,config.Connections.RabbitMQ.QUEUE -

    // if (socket.zenAlmond)
    CASSANDRA -
    4.Update on cloudlogs.connection_log
      params: dateyear, mac
     
    FUNCTIONAL -
    1.Command 104
    2.delete socket.almondOnline

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondStore(keepAlive)

<a name="1030"></a>
## 13) AlmondReset (Command 1030)
    Command no
    1030- JSON format

    REQUIRED -
    Command,CommandType,Payload,AlmondMAC

    FUNCTIONAL -
    1.Command 1030
    2.Send AlmondResetResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(almondReset)->processor(dispatchResponses)

<a name="1020a"></a>
## 14) AffiliationAlmondRequest (Command 1020)
    Command no
    1020- JSON format

    REQUIRED -
    Command,CommandType,Payload,AlmondMAC

    SQL -
    2.Select on AlmondUsers
      params: AlmondMAC, ownership
    3.Select on AllAlmondPlus
      params:AlmondMAC
    4.Insert on AllAlmondPlus
      params: AlmondMAC, AlmondID, FactoryDate, FactoryAdmin

    REDIS -
    5.hgetall on CODE:code                  // where code = random string 
    6.setex on CODE:code  
    // where code = random string,values =AlmondMAC + config.Connections.RabbitMQ.Queue

    FUNCTIONAL -
    1.Command 1020
    7.Send AffiliationAlmondRequestResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(affiliation_almond)->affiliate-almond(affiliate_almond)->processor(dispatchResponses)

<a name="1020b"></a>
## 15) AffiliationCompleteResponse (Command 1020)
    Command no
    1020- JSON format

    REQUIRED -
    Command,CommandType,Payload,AlmondMAC

    SQL -
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
      Params: AlmondMAC,UserID

    //check alexa compatible
    8.Select on UserTempPasswords
      Params: UserID

    REDIS -
    2.Get CODE:<code>                 // value = null
    9.Perform multi:
     i. hmset on UID_<UserID>       // value = (PMAC_<AlmondMAC>,1)
     ii. hmset on AL_<pMAC>         // value = (userID,key)
     iii. hdel on AL_<pMAC>         // value = [subscriptionToken]

    12.Get ICID_<packet.ICID>        // value = null
    14.hgetall on UID_<user_list>    // Returns all the queues for users in user_list

    QUEUE -
    13.Send Response to Queue from step 12 
    15.Send Response to All Queues returned in Step 14

    FUNCTIONAL -
    1.Command 1020
    10.Delete affiliationStore[data.AlmondMAC]
    11.Send AffiliationAlmondCompleteResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(affiliation_almond_complete),almondUsers(verify_affiliation_complete)-> processor(dispatchResponses),processor(unicast)->broadcaster(unicast)->processor(broadcaster)->broadcaster(send)

<a name="106"></a>
## 16) AlmondValidationRequest (Command 106)
    Command no
    106- XML format

    REQUIRED -
    Command,CommandType,Payload,AlmondMAC
    
    SQL -
    2. Select on AllAlmondPlus
       params:  AlmondMAC

    FUNCTIONAL -
    1.Command 106
    3.Send AlmondValidationRequestResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(Almond_Validate)-> processor(dispatchResponses)

<a name="1702"></a>
## 17) HashList (Command 1702)
    Command no
    1702- JSON format

    REQUIRED -
    Command,CommandType,Payload,AlmondMAC
    
    REDIS -
    3.hmset on AL_<AlmondMAC>        // values = [router, payload.RouterMode = '%s']

    FUNCIONAL -
    1.Command 1702
    2.delete socket[i]     // i = number of sockets
    4.Send HashListResponse to Almond

    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(checkAllHash)-> processor(dispatchResponses)

<a name="1600"></a>
## 18) ReadyToAddAsSlave,AddWiredSlaveMobile (Command 1600)
    Command no
    1600- JSON format

    REQUIRED -
    Command,CommandType,Payload,AlmondMAC
    
    REDIS
    2.hgetall on UID_<user_list>    // Returns all the queues for users in user_list

    QUEUE -
    3.Send Response to All Queues returned in Step 2

    FUNCTIONAL -
    1.Command 1600
    
    FLOW -
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(broadcaster)->broadcaster(send)

------------------------------------------------------------------------------------------------

<a name="ConsumerCommands"></a>
## ConsumerCommands
<a name="24"></a>
## 1) AffiliationAlmondComplete (Command 24)
    Command no
    26- XML format

    REQUIRED -
    Command,Payload,ICID,unicastID,AlmondMAC

    FUNCTIONAL -
    1.Command 26
    2.Send Res,CommandLengthType to Almond

<a name="1062"></a>
## 2) Command 1062
    Command no 
    1062- JSON format

    REQUIRED -
    Command,Payload,ICID,unicastID,AlmondMAC

    FUNCTIONAL -
    1.Command 1062
    2.Send Res,CommandLengthType to Almond

<a name="1100c"></a>
## 3) Command 1100
    Command no 
    1110- JSON format

    REQUIRED -
    Command,Payload,ICID,unicastID,AlmondMAC

    REDIS -
    2.get on ICID_<unicastID>        

    QUEUE -
    3.Send Response to queue

    FUNCTIONAL -
    1.Command 1100
    4.Append result.almondMAC to offlineMACS.txt

<a name="62"></a>
## 4) AlmondNameChange,AlmondModeChange (Command 62)
    Command no 
    62- XML format
 
    REQUIRED - 
    Command,CommandType,ICID,Payload

    REDIS -
    2.get on ICID_<unicastID>          

    QUEUE -
    3.Send Response to queue
 
    FUNCTIONAL -
    1.Command 62
    4.Append result.almondMAC to offlineMACS.txt

<a name="1020"></a>
## 5) AffiliationAlmondComplete (Command 1020)
    Command no
    1020- JSON format

    REQUIRED -
    Command,Payload,ICID,unicastID,AlmondMAC          

    FUNCTIONAL -
    1.Command 1020
    2.Send Res,CommandLengthType to Almond

<a name="alexaLinking"></a>
## 6) CommandType alexaLinking (Command 1200)
    Command no & CommandType
    1200- JSON format,alexaLinking

    REQUIRED -
    CommandType,Payload,ICID,unicastID,AlmondMAC

    REDIS -
    2.hmset on AL_<almondMAC>                // values = [alexa, true]           

    QUEUE -
    3.Send alexaLinking to BACKGROUND_QUEUE

    FUNCTIONAL -
    1.CommandType alexaLinking
    4.Send Res,CommandLengthType to Almond

<a name="alexaUnLinking"></a>
## 7) CommandType alexaUnLinking (Command 1200)
    CommandType
    alexaUnLinking - XML 

    REQUIRED -
    CommandType,Payload,ICID,unicastID,AlmondMAC
    
    REDIS -
    2.hdel on AL_<payload.almondMAC>        // values = [alexa]

    FUNCTIONAL -
    1.CommandType alexaUnLinking
    3.delete socket.alexa
    4.Send Res,CommandLengthType to Almond

<a name="9999"></a>
## 8) Command 9999 (ChangeCMSCode)
    Command no
    9999- JSON format

    REQUIRED -
    Command,Payload,ICID,unicastID,AlmondMAC             

    FUNCTIONAL -
    1.Command 9999
    2.Send Res,CommandLengthType to Almond
    
<a name="UpdateClient"></a>
## 9) CommandType UpdateClient
    CommandType 
    UpdateClient - XML
   
    REQUIRED -
    Command,Payload,ICID,unicastID,AlmondMAC

    FUNCTIONAL -
    1.CommandType UpdateClient 
    2.Send Res,CommandLengthType to Almond

<a name="269"></a>
## 10) Id  269
    Command no 
    269- XML format
 
    REQUIRED - 
    Command,CommandType,Payload

    REDIS -
    // if(nodelay)
    3.hgetall on AL_<AlmondMAC>      
   
    4.hmset on AL_<AlmondMAC>       
    /* values = [status,0,offline,Date,server,config.Connections.RabbitMQ.QUEUE -] */

    QUEUE -
    5.Send AlmondStatus to config.BACKGROUND_QUEUE

    FUNCTIONAL -
    1.Id 269
    2.delete socketStore[socket.almondMAC]

<a name="removeAll"></a>
## 11) Type removeAll
    Type 
    removeAll - XML

    REQUIRED - 
    Command,CommandType,Payload

    REDIS -
    // if(nodelay)

    3.hgetall on AL_<AlmondMAC>    
    4.hmset on AL_<AlmondMAC>  
    //values = [status,0,offline,Date,server,config.Connections.RabbitMQ.QUEUE -]

    QUEUE -
    5.Send AlmondStatus to config.BACKGROUND_QUEUE

    FUNCTIONAL -
    1.Type removeAll
    2.delete socketStore[socket.almondMAC]

<a name="1030"></a>
## 12) Command 1030 (reset)
    Command 
    1030- JSON format 
    
    REQUIRED - 
    Command,CommandType,almondMAC

    FUNCTIONAL -
    1.Command 1030

    //if (ZEN.MACS.indexOf(parseInt(mac)) >= 0) 
    2. return almondMAC

<a name="2020"></a>
## 13) ID 2020
    Command 
    2020- JSON format 
    
    REQUIRED - 
    Command,CommandType,almondMAC
 
    QUEUE -
    2.Send Response to config.Connections.RabbitMQ.Queue
    
    FUNCTIONAL -
    1.Command 2020

<a name="1110"></a>
## 14) Command 1110
    Command 
    1110- JSON format 
    
    REQUIRED - 
    Command,CommandType,almondMAC

    FUNCTIONAL -
    1.Command 1110
    2.Send Res,CommandLengthType to Almond

<a name="1060"></a>
## 15) Command 1060
    Command 
    1060- JSON format 
    
    REQUIRED - 
    Command,CommandType,almondMAC

    FUNCTIONAL -
    1.Command 1060
    2.Send Res,CommandLengthType to Almond

<a name="1700"></a>
## 16) Command 1700
    Command 
    1700- JSON format 
    
    REQUIRED - 
    Command,CommandType,almondMAC

    FUNCTIONAL -
    1.Command 1700
    2.Send Res,CommandLengthType to Almond

<a name="1525"></a>
## 17) Command 1525
    Command 
    1525- JSON format 
    
    REQUIRED - 
    Command,CommandType,almondMAC

    FUNCTIONAL -
    1.Command 1525
    2.Send Res,CommandLengthType to Almond

<a name="300"></a>
## 18) Command 300
    Command 
    300- XML format 
    
    REQUIRED - 
    Command,CommandType,almondMAC

    FUNCTIONAL -
    1.Command 300
    2.Send Res,CommandLengthType to Almond

<a name="4"></a>
## 19) Command 4
    Command 
    4- XML format 
    
    REQUIRED - 
    Command,CommandType,almondMAC

    FUNCTIONAL -
    1.Command 4
    2.Send Res,CommandLengthType to Almond

<a name="1026"></a>
## 20) Command 1026
    Command 
    1026- JSON format 
    
    REQUIRED - 
    Command,CommandType,almondMAC

    FUNCTIONAL -
    1.Command 300
    2.Send Res,CommandLengthType to Almond
