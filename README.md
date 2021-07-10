# ServerCallback (negbook method)  

Server:  
```
local resname,RegisterServerCallback = GetCurrentResourceName()
RegisterServerCallback = function(name,fn)
    local hash = GetHashKey(name)
    local eventName,a = resname..":"..hash..":".."RequestCallback"
    RegisterNetEvent(eventName)
    a = AddEventHandler(eventName, function (ticketClient,...)
        local source_ = source 
        local ticketServer =  tostring(GetGameTimer())..tostring(math.random(0,65535))
        local eventWithTicket,b = eventName .. ticketClient .. ticketServer
        if source_ then eventWithTicket = eventWithTicket .. tostring(source_)..tostring(GetHashKey(GetPlayerName(source_))) 
            RegisterNetEvent(eventWithTicket)
            b = AddEventHandler(eventWithTicket, function (ticketCl,...)
                TriggerClientEvent(resname..":"..hash..":".."ResultCallback"..ticketCl,source_,fn(...),...)
                RemoveEventHandler(b)
                CreateThread(function()
                    if RegisterServerCallback then RegisterServerCallback(name,fn) end 
                end)
            end) 
            TriggerEvent(eventWithTicket,ticketClient,...)
        end 
        RemoveEventHandler(a)
    end)
end 

RegisterServerCallback("servertime",function(...)
    return os.date("%Y %m %d %H %M %S")
end )
```

Client:  
```
local resname,TriggerServerCallback = GetCurrentResourceName()
TriggerServerCallback = function(name,fn,...)
    local resname = GetCurrentResourceName()
    local a 
    local hash = GetHashKey(name)
    local ticketClient = tostring(GetGameTimer())..tostring(NetworkGetRandomIntRanged(0,65535))
    RegisterNetEvent(resname..":"..hash..":".."ResultCallback"..ticketClient)
    a = AddEventHandler(resname..":"..hash..":".."ResultCallback"..ticketClient, function (...)
        fn(...)
        RemoveEventHandler(a)
    end)
    TriggerServerEvent(resname..":"..hash..":".."RequestCallback",ticketClient,...)
end 

CreateThread(function()
    TriggerServerCallback('servertime',function (...)
        print("Server Time",...)
    end)
end)

```
