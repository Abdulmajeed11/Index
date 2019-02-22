# Index
### Table of contents 
- [RouterSummary](#RouterSummary)
- [DynamicIndexUpdated](#DynamicIndexUpdated)
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

<a name="CommonToAll"></a>
## 3.CommonToAll

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
 
