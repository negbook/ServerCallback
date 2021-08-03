# ServerCallback (negbook method)  

Server:  
```
local resname,RegisterServerCallback = GetCurrentResourceName()
RegisterServerCallback = function(actionname,fn)
	----https://github.com/negbook/ServerCallback
	local resname = GetCurrentResourceName()
	local actionhashname = GetHashKey(actionname)
	local eventName,a = resname..":RequestCallback"..actionhashname
	a = RegisterNetEvent(eventName, function (ticketClient,...) --client send datas into ...
		local source_ = source 
		local ticketServer =  tostring(GetGameTimer())..tostring(GetHashKey(tostring(os.time()))) 
		local eventWithTicket,b = eventName .. ticketClient .. ticketServer
		if source_ then eventWithTicket = eventWithTicket .. tostring(source_)..tostring(GetHashKey(GetPlayerName(source_))) 
			b = RegisterNetEvent(eventWithTicket, function (ticketCl,...)
				local c = function(...)
					TriggerClientEvent(resname..":ResultCallback"..actionhashname..ticketCl,source_,...)
				end 
				if fn then fn(source_,c,...) end 
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
TriggerServerCallback = function(actionname,...)
	----https://github.com/negbook/ServerCallback
	local resname = GetCurrentResourceName()
	local a 
	local actionhashname = GetHashKey(actionname)
	local args = {...}
	local fn = args[1] 
	if fn and type(fn) == 'function' then 
		table.remove(args,1)
	end 
	local ticketClient = tostring(GetGameTimer())..tostring(GetHashKey(tostring(GetCloudTimeAsInt()))) .. tostring(GetIdOfThisThread())
	a = RegisterNetEvent(resname..":ResultCallback"..actionhashname..ticketClient, function (...)
		if fn then fn(...) end 
		if a then  
			RemoveEventHandler(a)
		end 
	end)
	TriggerServerEvent(resname..":RequestCallback"..actionhashname,ticketClient,table.unpack(args))
end 

CreateThread(function()
    TriggerServerCallback('servertime',function (...)
        print("Server Time",...)
    end)
end)

```
