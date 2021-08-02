# ServerCallback (negbook method)  

Server:  
```
local resname,RegisterServerCallback = GetCurrentResourceName()
RegisterServerCallback = function(actionname,fn) -- NB.RegisterServerCallback('abc',function(cb) cb(1) end)
		
		local resname = GetCurrentResourceName()
		local actionhashname = GetHashKey(actionname)
		local eventName,a = resname..":RequestCallback"..actionhashname
		
		a = RegisterNetEvent(eventName, function (ticketClient,...) --client send datas into ...
			local source_ = source 
			local ticketServer =  tostring(GetGameTimer())..tostring(tostring(GetHashKey(os.time())) 
			local eventWithTicket,b = eventName .. ticketClient .. ticketServer
			if source_ then eventWithTicket = eventWithTicket .. tostring(source_)..tostring(GetHashKey(GetPlayerName(source_))) 
				b = RegisterNetEvent(eventWithTicket, function (ticketCl,...)
					local c = function(...)
						TriggerClientEvent(resname..":ResultCallback"..actionhashname..ticketCl,source_,...)
					end 
					fn(source_,c,...)
					if b then 
						RemoveEventHandler(b)
					end 
					if NB.RegisterServerCallback  then NB.RegisterServerCallback (actionname,fn) end 
				end) 
				TriggerEvent(eventWithTicket,ticketClient,...)
			end 
			if a then 
				RemoveEventHandler(a)
			end 
		end)
	end 

RegisterServerCallback("servertime",function(source,cb)
    cb(os.date("%Y %m %d %H %M %S"))
end )
```

Client:  
```
local resname,TriggerServerCallback = GetCurrentResourceName()
TriggerServerCallback = function(actionname,fn,...)
		local resname = GetCurrentResourceName()
		local a 
		local actionhashname = GetHashKey(actionname)
		
		local ticketClient = tostring(GetGameTimer())..tostring(tostring(GetHashKey(GetCloudTimeAsInt()))) .. tostring(GetIdOfThisThread())
		a = RegisterNetEvent(resname..":ResultCallback"..actionhashname..ticketClient, function (...)
			fn(...)
			if a then  
				RemoveEventHandler(a)
			end 
		end)
		TriggerServerEvent(resname..":RequestCallback"..actionhashname,ticketClient,...)
	end 

CreateThread(function()
    TriggerServerCallback('servertime',function (...)
        print("Server Time",...)
    end)
end)

```
