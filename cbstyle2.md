# ServerCallback (negbook method)  

Server:  
```
local resname,RegisterServerCallback = function(actionname,fn)
	----https://github.com/negbook/ServerCallback
	local resname = function() return GetCurrentResourceName() end 
	local actionhashname = GetHashKey(actionname)
	local eventName,a = resname()..":RequestCallback"..actionhashname
	a = RegisterNetEvent(eventName, function (ticketClient,...) --client send datas into ...
		local source_ = source 
		local ticketServer =  math.abs(GetGameTimer() - GetHashKey(tostring(os.time()))) 
		local eventWithTicket,b = math.abs(GetHashKey(eventName)*ticketClient/(ticketServer+1))
		local sender = function(...) 
			TriggerEvent(eventWithTicket,ticketClient,...) 
		end 
		if source_ then eventWithTicket = tostring(source_)..tostring(eventWithTicket*GetHashKey(GetPlayerName(source_))) 
			
			b = RegisterNetEvent(eventWithTicket, function (ticketCl,...)
				local c = function(...)
					TriggerClientEvent(resname()..":ResultCallback"..tostring(math.abs(actionhashname/ticketCl+1)),source_,...)
				end 
				if fn then fn(source_,c,...) end 
				if b then 
					RemoveEventHandler(b)
				end 
				if NB.RegisterServerCallback  then NB.RegisterServerCallback (actionname,fn) end 
			end) 
			sender(...)
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
		local ticketClient = math.abs(GetGameTimer()*GetHashKey(tostring(GetCloudTimeAsInt()))*GetIdOfThisThread())
		a = RegisterNetEvent(resname..":ResultCallback"..tostring(math.abs(actionhashname/ticketClient+1)), function (...)
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
