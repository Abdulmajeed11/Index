# Index
### Table of contents 
- [RouterSummary](#RouterSummary)
- [DynamicIndexUpdated](#DynamicIndexUpdated)

###RouterSummary
 Command no 
  1100- JSON  format

Required 
Command, CommandType,ICID,Payload

Redis

2.Get ICID_<packet.ICID>  (will give the queue)

Queue
3. send the packet to queue which came from 2

Functional 
1. command 1100
4. Gets the details of the Of the key(ICID),packet

FLow diagram:

 almondProtocol(on)->tracker(sendHttp)->almondProtocol(dynamicUpdate)->tracker(almond_tracking)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses)->processor(unicast)->broadcaster(unicast)


###DynamicIndexUpdated
 Command no
 1063- JSON format


Required
Command, ICID,Payload

SQL
-

Redis
2.Get ICID_<packet.ICID>  (will give the queue)

Queue
3. send the packet to queue which came from 2

Functional
1. Command 1063
4. Gets the details of the Of the key(ICID),packet  



Flow diagram

 almondProtocol(on)->tracker(sendHttp)->almondProtocol(dynamicUpdate)->tracker(almond_tracking)-> processor(do)->processor(validate)->almondUsers(dummyModel)->processor(dispatchResponses)->processor(unicast)->broadcaster(unicast)


//yet to work on
###CommanToAll

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
