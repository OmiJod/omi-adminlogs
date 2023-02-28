-------------------------------------------------------------------------------------------------------
1) Repair Weapon Logs 
qb-weapons/client/ line 71
Replace this Event 
```lua
RegisterNetEvent('weapons:client:SetWeaponQuality', function(amount)
    if CurrentWeaponData and next(CurrentWeaponData) then
        TriggerServerEvent("weapons:server:SetWeaponQuality", CurrentWeaponData, amount)
    end
end)
```
with 
```lua
RegisterNetEvent('weapons:client:SetWeaponQuality', function(amount)
    local player = QBCore.Functions.GetPlayerData()
    if CurrentWeaponData and next(CurrentWeaponData) then
        local weaponname = CurrentWeaponData.label
        local info = CurrentWeaponData.info
        local quality = info.quality -- Assuming the "serie" key is an array
        TriggerServerEvent("weapons:server:SetWeaponQuality", CurrentWeaponData, amount)
        TriggerServerEvent('moon-menu:server:CreateLog', 'adminlogs', 'Repair Weapon command', 'red', 
        " \n" ..'**Player License**: '..QBCore.PlayerData.license..
        " \n" ..'**Player CitizenID**: '..QBCore.PlayerData.citizenid..
        " \n" ..'**First Name**: '..QBCore.PlayerData.charinfo.firstname.. 
        " \n" ..'**Last Name**: '..QBCore.PlayerData.charinfo.lastname..
        " \n" .. "**Repaired Weapon**: ".. weaponname ..
        " \n".. '**Original Quality**: '.. quality..
        " \n"..'**Changed Quality**: '.. amount, false)
    end
end)
```
-------------------------------------------------------------------------------------------------------
2) Set Ammo Logs 
qb-adminmenu/client/event.lus Line 129
Replace this Event
```lua
RegisterNetEvent('qb-weapons:client:SetWeaponAmmoManual', function(weapon, ammo)
    local ped = PlayerPedId()
    if weapon ~= "current" then
        weapon = weapon:upper()
        SetPedAmmo(ped, GetHashKey(weapon), ammo)
        QBCore.Functions.Notify(Lang:t("info.ammoforthe", {value = ammo, weapon = QBCore.Shared.Weapons[weapon]["label"]}), 'success')
    else
        weapon = GetSelectedPedWeapon(ped)
        if weapon ~= nil then
            SetPedAmmo(ped, weapon, ammo)
            QBCore.Functions.Notify(Lang:t("info.ammoforthe", {value = ammo, weapon = QBCore.Shared.Weapons[weapon]["label"]}), 'success')
        else
            QBCore.Functions.Notify(Lang:t("error.no_weapon"), 'error')
        end
    end
end)
```
With
```lua
RegisterNetEvent('qb-weapons:client:SetWeaponAmmoManual', function(weapon, ammo)
    local player = QBCore.Functions.GetPlayerData()
    local ped = PlayerPedId()
    if weapon ~= "current" then
        -- local weaponname = CurrentWeaponData.label
        weapon = weapon:upper()
        SetPedAmmo(ped, GetHashKey(weapon), ammo)
        QBCore.Functions.Notify(Lang:t("info.ammoforthe", {value = ammo, weapon = QBCore.Shared.Weapons[weapon]["label"]}), 'success')
        TriggerServerEvent('moon-menu:server:CreateLog', 'adminlogs', 'Admin Command - Repair Weapon', 'red', 
        " \n" ..'**Player License**: '..QBCore.PlayerData.license..
        " \n" ..'**Player CitizenID**: '..QBCore.PlayerData.citizenid..
        " \n" ..'**First Name**: '..QBCore.PlayerData.charinfo.firstname.. 
        " \n" ..'**Last Name**: '..QBCore.PlayerData.charinfo.lastname..
        " \n" .. "**Set Ammo For Weapon**: ".. QBCore.Shared.Weapons[weapon]["label"] ..
        " \n"..'**Set ammo Amount**: '.. ammo, false)
    else
        weapon = GetSelectedPedWeapon(ped)
        if weapon ~= nil then
            SetPedAmmo(ped, weapon, ammo)
            QBCore.Functions.Notify(Lang:t("info.ammoforthe", {value = ammo, weapon = QBCore.Shared.Weapons[weapon]["label"]}), 'success')
        else
            QBCore.Functions.Notify(Lang:t("error.no_weapon"), 'error')
        end
        TriggerServerEvent('moon-menu:server:CreateLog', 'adminlogs', 'Admin Command - Set Ammo', 'red', 
        " \n" ..'**Player License**: '..QBCore.PlayerData.license..
        " \n" ..'**Player CitizenID**: '..QBCore.PlayerData.citizenid..
        " \n" ..'**First Name**: '..QBCore.PlayerData.charinfo.firstname.. 
        " \n" ..'**Last Name**: '..QBCore.PlayerData.charinfo.lastname..
        " \n" .. "**Set Ammo For Weapon**: ".. QBCore.Shared.Weapons[weapon]["label"] ..
        " \n"..'**Set ammo Amount**: '.. ammo, false)   
    end
end)
```
-------------------------------------------------------------------------------------------------------
3) Max Mods 
qb-adminmenu/client/events.lua Line 181
Replace this event
```lua
RegisterNetEvent('qb-admin:client:maxmodVehicle', function()
    local vehicle = GetVehiclePedIsIn(PlayerPedId())
    PerformanceUpgradeVehicle(vehicle)
end)
```
With
```lua
RegisterNetEvent('qb-admin:client:maxmodVehicle', function()
    local vehicle = GetVehiclePedIsIn(PlayerPedId())
    PerformanceUpgradeVehicle(vehicle)
    local vehicleName = GetDisplayNameFromVehicleModel(GetEntityModel(vehicle))
    TriggerServerEvent('moon-menu:server:CreateLog', 'adminlogs', 'Admin Command - Max Mods', 'red', 
    " \n" ..'**Player License**: '..QBCore.PlayerData.license..
    " \n" ..'**Player CitizenID**: '..QBCore.PlayerData.citizenid..
    " \n" ..'**First Name**: '..QBCore.PlayerData.charinfo.firstname.. 
    " \n" ..'**Last Name**: '..QBCore.PlayerData.charinfo.lastname..
    " \n" .. "**Used Max Mods Command On Vehicle**: ".. vehicleName, false)   
end)
```
-------------------------------------------------------------------------------------------------------
4) Admin Car
qb-adminmenu/client/events.lua Line 64
Replace This Event
```lua
RegisterNetEvent('qb-admin:client:SaveCar', function()
    local ped = PlayerPedId()
    local veh = GetVehiclePedIsIn(ped)

    if veh ~= nil and veh ~= 0 then
        local plate = QBCore.Functions.GetPlate(veh)
        local props = QBCore.Functions.GetVehicleProperties(veh)
        local hash = props.model
        local vehname = getVehicleFromVehList(hash)
        if QBCore.Shared.Vehicles[vehname] ~= nil and next(QBCore.Shared.Vehicles[vehname]) ~= nil then
            TriggerServerEvent('qb-admin:server:SaveCar', props, QBCore.Shared.Vehicles[vehname], GetHashKey(veh), plate)
        else
            QBCore.Functions.Notify(Lang:t("error.no_store_vehicle_garage"), 'error')
        end
    else
        QBCore.Functions.Notify(Lang:t("error.no_vehicle"), 'error')
    end
end)
```
with
```lua
RegisterNetEvent('qb-admin:client:SaveCar', function()
    local player = QBCore.Functions.GetPlayerData()
    local ped = PlayerPedId()
    local veh = GetVehiclePedIsIn(ped)
    local vehicleName = GetDisplayNameFromVehicleModel(GetEntityModel(veh))

    if veh ~= nil and veh ~= 0 then
        local plate = QBCore.Functions.GetPlate(veh)
        local props = QBCore.Functions.GetVehicleProperties(veh)
        local hash = props.model
        local vehname = getVehicleFromVehList(hash)
        if QBCore.Shared.Vehicles[vehname] ~= nil and next(QBCore.Shared.Vehicles[vehname]) ~= nil then
            TriggerServerEvent('qb-admin:server:SaveCar', props, QBCore.Shared.Vehicles[vehname], GetHashKey(veh), plate)
                    TriggerServerEvent('moon-menu:server:CreateLog', 'adminlogs', 'Admin Command - Admin Car', 'red', 
        " \n" ..'**Player License**: '..QBCore.PlayerData.license..
        " \n" ..'**Player CitizenID**: '..QBCore.PlayerData.citizenid..
        " \n" ..'**First Name**: '..QBCore.PlayerData.charinfo.firstname.. 
        " \n" ..'**Last Name**: '..QBCore.PlayerData.charinfo.lastname..
        " \n" .. "**Saved Car**: "..vehicleName, false)
        else
            QBCore.Functions.Notify(Lang:t("error.no_store_vehicle_garage"), 'error')
        end
    else
        QBCore.Functions.Notify(Lang:t("error.no_vehicle"), 'error')
    end
end)
```
-------------------------------------------------------------------------------------------------------
5) Revive 
qb-ambulancejob / client / main.lua Line 566
Replace this Event
```lua
RegisterNetEvent('hospital:client:Revive', function()
    local player = PlayerPedId()

    if isDead or InLaststand then
        local pos = GetEntityCoords(player, true)
        NetworkResurrectLocalPlayer(pos.x, pos.y, pos.z, GetEntityHeading(player), true, false)
        isDead = false
        SetEntityInvincible(player, false)
        SetLaststand(false)
    end

    if isInHospitalBed then
        loadAnimDict(inBedDict)
        TaskPlayAnim(player, inBedDict , inBedAnim, 8.0, 1.0, -1, 1, 0, 0, 0, 0 )
        SetEntityInvincible(player, true)
        canLeaveBed = true
    end

    TriggerServerEvent("hospital:server:RestoreWeaponDamage")
    SetEntityMaxHealth(player, 200)
    SetEntityHealth(player, 200)
    ClearPedBloodDamage(player)
    SetPlayerSprint(PlayerId(), true)
    ResetAll()
    ResetPedMovementClipset(player, 0.0)
    TriggerServerEvent('hud:server:RelieveStress', 100)
    TriggerServerEvent("hospital:server:SetDeathStatus", false)
    TriggerServerEvent("hospital:server:SetLaststandStatus", false)
    emsNotified = false
    QBCore.Functions.Notify(Lang:t('info.healthy'))
end)
```
With 
```lua
RegisterNetEvent('hospital:client:Revive', function()
    local player = PlayerPedId()

    if isDead or InLaststand then
        local pos = GetEntityCoords(player, true)
        NetworkResurrectLocalPlayer(pos.x, pos.y, pos.z, GetEntityHeading(player), true, false)
        isDead = false
        SetEntityInvincible(player, false)
        SetLaststand(false)
    end

    if isInHospitalBed then
        loadAnimDict(inBedDict)
        TaskPlayAnim(player, inBedDict , inBedAnim, 8.0, 1.0, -1, 1, 0, 0, 0, 0 )
        SetEntityInvincible(player, true)
        canLeaveBed = true
    end

    TriggerServerEvent("hospital:server:RestoreWeaponDamage")
    SetEntityMaxHealth(player, 200)
    SetEntityHealth(player, 200)
    ClearPedBloodDamage(player)
    SetPlayerSprint(PlayerId(), true)
    ResetAll()
    ResetPedMovementClipset(player, 0.0)
    TriggerServerEvent('hud:server:RelieveStress', 100)
    TriggerServerEvent("hospital:server:SetDeathStatus", false)
    TriggerServerEvent("hospital:server:SetLaststandStatus", false)
    emsNotified = false
    QBCore.Functions.Notify(Lang:t('info.healthy'))
    TriggerServerEvent('moon-menu:server:CreateLog', 'adminlogs', 'Admin Command - Revive', 'red', 
    " \n" ..'**Player License**: '..QBCore.PlayerData.license..
    " \n" ..'**Player CitizenID**: '..QBCore.PlayerData.citizenid..
    " \n" ..'**First Name**: '..QBCore.PlayerData.charinfo.firstname.. 
    " \n" ..'**Last Name**: '..QBCore.PlayerData.charinfo.lastname..
    " \n" .. "**Revived Himself or Some Another Player**", false)  
end)
```
-------------------------------------------------------------------------------------------------------
6) setpain 
qb-ambulancejob / client / main.lua Line 604
Replace this Event
```lua
RegisterNetEvent('hospital:client:SetPain', function()
    ApplyBleed(math.random(1,4))
    if not BodyParts[Config.Bones[24816]].isDamaged then
        BodyParts[Config.Bones[24816]].isDamaged = true
        BodyParts[Config.Bones[24816]].severity = math.random(1, 4)
        injured[#injured+1] = {
            part = Config.Bones[24816],
            label = BodyParts[Config.Bones[24816]].label,
            severity = BodyParts[Config.Bones[24816]].severity
        }
    end

    if not BodyParts[Config.Bones[40269]].isDamaged then
        BodyParts[Config.Bones[40269]].isDamaged = true
        BodyParts[Config.Bones[40269]].severity = math.random(1, 4)
        injured[#injured+1] = {
            part = Config.Bones[40269],
            label = BodyParts[Config.Bones[40269]].label,
            severity = BodyParts[Config.Bones[40269]].severity
        }
    end

    TriggerServerEvent('hospital:server:SyncInjuries', {
        limbs = BodyParts,
        isBleeding = tonumber(isBleeding)
    })
end)
```
with
```lua
RegisterNetEvent('hospital:client:SetPain', function()
    ApplyBleed(math.random(1,4))
    if not BodyParts[Config.Bones[24816]].isDamaged then
        BodyParts[Config.Bones[24816]].isDamaged = true
        BodyParts[Config.Bones[24816]].severity = math.random(1, 4)
        injured[#injured+1] = {
            part = Config.Bones[24816],
            label = BodyParts[Config.Bones[24816]].label,
            severity = BodyParts[Config.Bones[24816]].severity
        }
    end

    if not BodyParts[Config.Bones[40269]].isDamaged then
        BodyParts[Config.Bones[40269]].isDamaged = true
        BodyParts[Config.Bones[40269]].severity = math.random(1, 4)
        injured[#injured+1] = {
            part = Config.Bones[40269],
            label = BodyParts[Config.Bones[40269]].label,
            severity = BodyParts[Config.Bones[40269]].severity
        }
    end

    TriggerServerEvent('hospital:server:SyncInjuries', {
        limbs = BodyParts,
        isBleeding = tonumber(isBleeding)
    })
    TriggerServerEvent('moon-menu:server:CreateLog', 'adminlogs', 'Admin Command - Setpain', 'red', 
    " \n" ..'**Player License**: '..QBCore.PlayerData.license..
    " \n" ..'**Player CitizenID**: '..QBCore.PlayerData.citizenid..
    " \n" ..'**First Name**: '..QBCore.PlayerData.charinfo.firstname.. 
    " \n" ..'**Last Name**: '..QBCore.PlayerData.charinfo.lastname..
    " \n" .. "**SetPain for Some Another Player**", false)  
end)
```
-------------------------------------------------------------------------------------------------------

7) Fix Vehicle 
qb-mechanic / client / main.lua Line 866
Replace this Event
```lua
RegisterNetEvent('vehiclemod:client:fixEverything', function()
    if (IsPedInAnyVehicle(PlayerPedId(), false)) then
        local veh = GetVehiclePedIsIn(PlayerPedId(),false)
        if not IsThisModelABicycle(GetEntityModel(veh)) and GetPedInVehicleSeat(veh, -1) == PlayerPedId() then
            local plate = QBCore.Functions.GetPlate(veh)
            TriggerServerEvent("vehiclemod:server:fixEverything", plate)
        else
            QBCore.Functions.Notify(Lang:t('notifications.wrong_seat'), "error")
        end
    else
        QBCore.Functions.Notify(Lang:t('notifications.not_vehicle'), "error")
    end
end)
```
with
```lua
RegisterNetEvent('vehiclemod:client:fixEverything', function()
    if (IsPedInAnyVehicle(PlayerPedId(), false)) then
        local veh = GetVehiclePedIsIn(PlayerPedId(),false)
        local vehicleName = GetDisplayNameFromVehicleModel(GetEntityModel(veh))    
        if not IsThisModelABicycle(GetEntityModel(veh)) and GetPedInVehicleSeat(veh, -1) == PlayerPedId() then
            local plate = QBCore.Functions.GetPlate(veh)
            TriggerServerEvent("vehiclemod:server:fixEverything", plate)
            TriggerServerEvent('moon-menu:server:CreateLog', 'adminlogs', 'Admin Command - Fix', 'red', 
            " \n" ..'**Player License**: '..QBCore.PlayerData.license..
            " \n" ..'**Player CitizenID**: '..QBCore.PlayerData.citizenid..
            " \n" ..'**First Name**: '..QBCore.PlayerData.charinfo.firstname.. 
            " \n" ..'**Last Name**: '..QBCore.PlayerData.charinfo.lastname..
            " \n" .. "**fixed Vehicle**: "..vehicleName..
            " \n" .. "**Plate**: "..plate,  false)  
        else
            QBCore.Functions.Notify(Lang:t('notifications.wrong_seat'), "error")
        end
    else
        QBCore.Functions.Notify(Lang:t('notifications.not_vehicle'), "error")
    end
end)
```