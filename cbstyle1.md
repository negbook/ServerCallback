# ServerCallback (negbook method)  

Server:  
```
local resname,RegisterServerCallback = GetCurrentResourceName()
RegisterServerCallback = function(actionname,fn)
		local resname = GetCurrentResourceName()
		local actionhashname = GetHashKey(actionname)
		local eventName,a = resname..":RequestCallback"..actionhashname
		a = RegisterNetEvent(eventName, function (ticketClient,...)
			local source_ = source 
			local ticketServer =  tostring(GetGameTimer())..tostring(os.time())
			local eventWithTicket,b = eventName .. ticketClient .. ticketServer
			if source_ then eventWithTicket = eventWithTicket .. tostring(source_)..tostring(GetHashKey(GetPlayerName(source_))) 
				b = RegisterNetEvent(eventWithTicket, function (ticketCl,...)
					TriggerClientEvent(resname..":ResultCallback"..actionhashname..ticketCl,source_,fn(...),...)
					if b then 
						RemoveEventHandler(b)
					end 
					CreateThread(function()
					if NB.RegisterServerCallback  then NB.RegisterServerCallback (actionname,fn) end 
					end)
				end) 
				TriggerEvent(eventWithTicket,ticketClient,...)
			end 
			if a then 
				RemoveEventHandler(a)
			end 
		end)
	end 

RegisterServerCallback("servertime",function(...)
    return os.date("%Y %m %d %H %M %S")
end )
```

Client:  
```
local resname,TriggerServerCallback = GetCurrentResourceName()
TriggerServerCallback = function(actionname,fn,...)
		local resname = GetCurrentResourceName()
		local a 
		local actionhashname = GetHashKey(actionname)
		
		local ticketClient = tostring(GetGameTimer())..tostring(GetCloudTimeAsInt())
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
