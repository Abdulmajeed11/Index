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
- [CommonToAll](#CommonToAll)
<a name="RouterSummary"></a>
## 1.RouterSummary
    Command no 
    1100- JSON  format
 
    Required 
    Command,CommandType,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>  (will give the queue)

    Queue
    3.send the ICID packet to queue

    Functional 
    1. command 1100

    FLow:
     almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses),processor(unicast)->broadcaster(unicast)

<a name="DynamicIndexUpdated"></a>
## 2.DynamicIndexUpdated
    Command no
    1063- JSON format

    **Required**
    Command,ICID,Payload

    Redis
    2.Get ICID_<packet.ICID>  (will give the queue)

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1063  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses) , processor(unicast)->broadcaster(unicast)
    
<a name="AffiliationAlmondComplete"></a>
## 3.AffiliationAlmondComplete
    Command no
    25- JSON format

    Required
    Command,ICID,Payload

    Sql
    3. Perform INSERT operation on AlmondUsers table having parameters userID, AlmondMAC, AlmondName, LongSecret, ownership ,FirmwareVersion, AffiliationTS
    4. Perfrom INSERT operation on AlmondProperties2 table having parameters AlmondMAC,Properties,MobileProperties
    5. Perform Select operation on Subscriptions table
    6. Perform Update operation on Subscription table
    7. Perform Select operation on UserTempPasswords table

    Redis
    2. Get CODE:code  

    8. Perform hmset on UID_UserId
    9. Perform hmset on AL_pMAC
    10.Perfrom hdel on AL_pMAC

    14. Perform Get on ICID_<packet.ICID>
    16. Perfrom hgetall on UID_<user_list>

    Queue
    15. Send the ICID_<packet.ICID> to queue 
    17. Send <user_list>.Substring(Q_length),user_list),Payload to queue

    Functional
    1.Command 25
    11.Perform multi on addPrimaryAlmond query
    12.Delete affiliationStore[id]
    13.Destroy Socket


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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

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
    2.Get ICID_<packet.ICID>  (will give the queue)

    Queue
    3.Send the packet ICID to queue

    Functional
    1.Command 1100  

    Flow
    almondProtocol(packet)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses) , processor(unicast)->broadcaster(unicast)
    
<a name="AlmondModeChange"></a>
## 25.AlmondModeChange
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

<a name="CommonToAll"></a>
## 26.CommonToAll

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
 
