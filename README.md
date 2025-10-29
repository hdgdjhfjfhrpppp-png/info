-- ===== Orion Full GUI (with your pastebin) =====
local OrionLib = loadstring(game:HttpGet('https://raw.githubusercontent.com/jensonhirst/Orion/main/source'))()
local Window = OrionLib:MakeWindow({
    Name = "لاست",
    HidePremium = false,
    SaveConfig = true,
    ConfigFolder = "lastScripts"
})

-- ===== تبويب سكربتاتي الصملة 🔥 =====
local Tab = Window:MakeTab({
    Name = "سكربتاتي الصملة 🔥",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

local scriptsList = {
    {"ALSHABA7 VOIID", 'https://raw.githubusercontent.com/XxAbood/ALSHABA7-VOIID/refs/heads/main/ALSHABA7%20VOIID'},
    {"Auto Click", 'https://raw.githubusercontent.com/MADARA9223/AUTO-CLICK/refs/heads/main/MADARA%20AUTO%20CLICK'},
    {"VR7", 'https://raw.githubusercontent.com/VR7ss/OMK/refs/heads/main/VR7-ON-TOP'},
    {"HAMODAH", 'https://raw.githubusercontent.com/SALAH142876/HAMODAH_ON_TOP/refs/heads/main/Protected_7545697692462583.txt'},
    {"AntiAFK", 'https://rawscripts.net/raw/Universal-Script-AntiAFK-v-AntiKick-V3-v-Kick-Attempt-Logger-27977'},
    {"رحمه القمر 🌙", 'https://raw.githubusercontent.com/n0kc/AtomicHub/main/Map-Al-Biout.lua'}
}

for _,v in pairs(scriptsList) do
    Tab:AddButton({
        Name = "تشغيل "..v[1],
        Callback = function()
            pcall(function() loadstring(game:HttpGet(v[2]))() end)
            OrionLib:MakeNotification({Name="تم التشغيل", Content=v[1].." شغال الآن", Image="rbxassetid://4483345998", Time=3})
        end
    })
end

-- === ADDED: your pastebin script button ===
Tab:AddButton({
    Name = "تشغيل (pastebin zk61AmRh)",
    Callback = function()
        pcall(function()
            loadstring(game:HttpGet("https://pastebin.com/raw/zk61AmRh"))()
        end)
        OrionLib:MakeNotification({Name="تم التشغيل", Content="pastebin zk61AmRh شغال", Image="rbxassetid://4483345998", Time=3})
    end
})

-- ===== تبويب لاست — أدوات =====
local LastTab = Window:MakeTab({
    Name = "لاست — أدوات",
    Icon = "rbxassetid://6035027362",
    PremiumOnly = false
})

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer
local Workspace = game:GetService("Workspace")

local state = { antiafk=false, reduceLag=false, antiKickLocal=false, autoMove=false, autokeyActive=false, repLagEnabled=false, reachMultiplier=1, vanished=false }
local savedParts = {}

-- حفظ واسترجاع الشخصية
local function saveCharacterState(char)
    savedParts = {}
    for _,v in pairs(char:GetDescendants()) do
        if v:IsA("BasePart") then savedParts[v]={Transparency=v.Transparency, CanCollide=v.CanCollide}
        elseif v:IsA("Decal") then savedParts[v]={Transparency=v.Transparency} end
    end
end
local function restoreCharacter(char)
    for obj,props in pairs(savedParts) do
        pcall(function()
            if obj and obj.Parent then
                if obj:IsA("BasePart") then obj.Transparency=props.Transparency or 0; obj.CanCollide=(props.CanCollide==nil) and true or props.CanCollide; if rawget(obj,"LocalTransparencyModifier")~=nil then obj.LocalTransparencyModifier=0 end
                elseif obj:IsA("Decal") then obj.Transparency=props.Transparency or 0 end
            end
        end)
    end
    savedParts={}
    state.vanished=false
end

-- وظائف محلية
local antiafkConn
local function enableAntiafk()
    if state.antiafk then return end
    state.antiafk=true
    antiafkConn=RunService.Heartbeat:Connect(function()
        local char=LocalPlayer.Character
        if char and char.PrimaryPart then
            pcall(function() char.PrimaryPart.CFrame=char.PrimaryPart.CFrame+Vector3.new(0,0.03*math.sin(tick()),0) end)
        end
    end)
    OrionLib:MakeNotification({Name="AntiAFK", Content="تم تفعيل AntiAFK", Image="rbxassetid://4483345998", Time=3})
end
local function disableAntiafk() state.antiafk=false; if antiafkConn then antiafkConn:Disconnect(); antiafkConn=nil end; OrionLib:MakeNotification({Name="AntiAFK", Content="تم إيقاف AntiAFK", Image="rbxassetid://4483345998", Time=3}) end

local function enableReduceLag()
    if state.reduceLag then return end
    state.reduceLag=true
    for _,v in pairs(Workspace:GetDescendants()) do
        if v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Smoke") then pcall(function() v.Enabled=false end) end
    end
    pcall(function() Lighting.GlobalShadows=false end)
    OrionLib:MakeNotification({Name="ReduceLag", Content="تم تقليل العناصر المزعجة", Image="rbxassetid://4483345998", Time=3})
end
local function disableReduceLag() state.reduceLag=false; OrionLib:MakeNotification({Name="ReduceLag", Content="تم إيقاف ReduceLag", Image="rbxassetid://4483345998", Time=3}) end

local function enableAntiKickLocal()
    state.antiKickLocal=true
    Players.LocalPlayer.Idled:Connect(function()
        if state.antiKickLocal and LocalPlayer.Character then
            saveCharacterState(LocalPlayer.Character)
            pcall(function()
                for _,p in pairs(LocalPlayer.Character:GetDescendants()) do
                    if p:IsA("BasePart") then p.Transparency=1; p.CanCollide=false end
                    if p:IsA("Decal") then p.Transparency=1 end
                    if p:IsA("ParticleEmitter") or p:IsA("Trail") then p.Enabled=false end
                end
            end)
            wait(2)
            if LocalPlayer.Character then restoreCharacter(LocalPlayer.Character) end
        end
    end)
    OrionLib:MakeNotification({Name="AntiKickLocal", Content="حماية الطرد مفعلة", Image="rbxassetid://4483345998", Time=3})
end
local function disableAntiKickLocal() state.antiKickLocal=false; OrionLib:MakeNotification({Name="AntiKickLocal", Content="حماية الطرد معطلة", Image="rbxassetid://4483345998", Time=3}) end

local function brightnessNormal() pcall(function() Lighting.Brightness=1 end); OrionLib:MakeNotification({Name="Brightness", Content="Brightness = 1", Image="rbxassetid://4483345998", Time=2}) end
local function brightnessNan() pcall(function() Lighting.Brightness=0.01 end); OrionLib:MakeNotification({Name="Brightness", Content="Brightness = 0.01", Image="rbxassetid://4483345998", Time=2}) end
local function brightnessInf() pcall(function() Lighting.Brightness=12 end); OrionLib:MakeNotification({Name="Brightness", Content="Brightness = 12", Image="rbxassetid://4483345998", Time=2}) end

local guiWhitelist={"Map","HUD","hud","Minimap","MapUI","Brainrot","Menu"}
local function isWhitelisted(g)
    if not g or not g.Name then return false end
    for _,kw in ipairs(guiWhitelist) do if string.find(g.Name,kw) then return true end end
    return false
end
local function nogui()
    for _,g in pairs(LocalPlayer.PlayerGui:GetChildren()) do
        if g:IsA("ScreenGui") and not isWhitelisted(g) then pcall(function() g.Enabled=false end) end
    end
    OrionLib:MakeNotification({Name="NoGUI", Content="تم إخفاء الواجهات", Image="rbxassetid://4483345998", Time=2})
end

local function setfpscap(val) pcall(function() if typeof(settings)=="function" then settings().Physics.ForceFPS=tonumber(val) or 30 end end); OrionLib:MakeNotification({Name="FPS Cap", Content="تم ضبط FPS Cap", Image="rbxassetid://4483345998", Time=2}) end

local repLagThread
local function enableReplicationLag(ms)
    if state.repLagEnabled then return end
    state.repLagEnabled=true
    repLagThread=spawn(function() while state.repLagEnabled do wait((tonumber(ms) or 80)/1000) end end)
    OrionLib:MakeNotification({Name="RepLag", Content="Replication lag مفعل", Image="rbxassetid://4483345998", Time=2})
end
local function disableReplicationLag() state.repLagEnabled=false; repLagThread=nil; OrionLib:MakeNotification({Name="RepLag", Content="Replication lag معطل", Image="rbxassetid://4483345998", Time=2}) end

local function cpuReduce()
    for _,v in pairs(Workspace:GetDescendants()) do
        pcall(function()
            if v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Smoke") then v.Enabled=false end
            if v:IsA("Decal") then v.Transparency=math.max(v.Transparency or 0,0.5) end
        end)
    end
    pcall(function() Lighting.GlobalShadows=false end)
    OrionLib:MakeNotification({Name="CPU Reduce", Content="تم تقليل حمل الـ CPU", Image="rbxassetid://4483345998", Time=2})
end

local function setDay() pcall(function() Lighting.TimeOfDay="14:00:00" end); OrionLib:MakeNotification({Name="Day", Content="تم ضبط الوقت", Image="rbxassetid://4483345998", Time=2}) end
local function nofog(val) pcall(function() Lighting.FogEnd=tonumber(val) or 0.1 end); OrionLib:MakeNotification({Name="NoFog", Content="تم ضبط NoFog", Image="rbxassetid://4483345998", Time=2}) end
local function antilag()
    pcall(function()
        Lighting.GlobalShadows=false
        Lighting.FogEnd=10000
        for _,v in pairs(Workspace:GetDescendants()) do
            if v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Smoke") then v.Enabled=false end
            if v:IsA("Decal") then v.Transparency=math.max(v.Transparency or 0,0.5) end
        end
    end)
    OrionLib:MakeNotification({Name="AntiLag", Content="تم تفعيل AntiLag", Image="rbxassetid://4483345998", Time=2})
end

local function setReach(mult)
    local char=LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        pcall(function() char.HumanoidRootPart.Size=Vector3.new(2*(mult or 1),2*(mult or 1),1) end)
        state.reachMultiplier=mult or 1
        OrionLib:MakeNotification({Name="Reach", Content="Reach x"..tostring(mult), Image="rbxassetid://4483345998", Time=2})
    end
end

local function autokey_w(times)
    if state.autokeyActive then return end
    state.autokeyActive=true
    spawn(function()
        local t=tonumber(times) or 26
        for i=1,t do
            local char=LocalPlayer.Character
            if char then
                local humanoid=char:FindFirstChildOfClass("Humanoid")
                local hrp=char:FindFirstChild("HumanoidRootPart")
                local cam=workspace.CurrentCamera
                if humanoid and hrp and cam then
                    local dir=cam.CFrame.LookVector
                    local horiz=Vector3.new(dir.X,0,dir.Z)
                    if horiz.Magnitude>0 then
                        local target=hrp.Position+horiz.Unit*4
                        pcall(function() humanoid:MoveTo(Vector3.new(target.X,hrp.Position.Y,target.Z)) end)
                    end
                end
            end
            wait(0.12)
        end
        state.autokeyActive=false
        OrionLib:MakeNotification({Name="Autokey", Content="انتهى Autokey", Image="rbxassetid://4483345998", Time=2})
    end)
end

local autoMoveThread
local function startAutoMove()
    if state.autoMove then return end
    state.autoMove=true
    autoMoveThread=spawn(function()
        while state.autoMove do
            local char=LocalPlayer.Character
            if char and char.PrimaryPart then
                local humanoid=char:FindFirstChildOfClass("Humanoid")
                local hrp=char.PrimaryPart
                local cam=workspace.CurrentCamera
                if humanoid and hrp and cam then
                    local dir=cam.CFrame.LookVector
                    local horiz=Vector3.new(dir.X,0,dir.Z)
                    if horiz.Magnitude>0 then
                        local target=hrp.Position+horiz.Unit*6
                        pcall(function() humanoid:MoveTo(Vector3.new(target.X,hrp.Position.Y,target.Z)) end)
                    end
                end
            end
            wait(0.2)
        end
    end)
    OrionLib:MakeNotification({Name="AutoMove", Content="تحرك تلقائي مفعل", Image="rbxassetid://4483345998", Time=2})
end
local function stopAutoMove() state.autoMove=false; autoMoveThread=nil; OrionLib:MakeNotification({Name="AutoMove", Content="تحرك تلقائي متوقف", Image="rbxassetid://4483345998", Time=2}) end

local function jumpAndVanish(duration)
    duration=tonumber(duration) or 6
    local char=LocalPlayer.Character
    if not char then return end
    local humanoid=char:FindFirstChildOfClass("Humanoid")
    if humanoid then pcall(function() humanoid.Jump=true end) end
    wait(0.12)
    saveCharacterState(char)
    for _,v in pairs(char:GetDescendants()) do
        pcall(function()
            if v:IsA("BasePart") then v.Transparency=1; v.CanCollide=false end
            if v:IsA("Decal") then v.Transparency=1 end
            if v:IsA("ParticleEmitter") or v:IsA("Trail") then v.Enabled=false end
        end)
    end
    state.vanished=true
    OrionLib:MakeNotification({Name="Vanish", Content="تم الاختفاء مؤقتاً", Image="rbxassetid://4483345998", Time=2})
    spawn(function() wait(duration) if LocalPlayer.Character then restoreCharacter(LocalPlayer.Character) end end)
end

-- إضافة أزرار تبويب لاست
local function addButton(name,fn)
    LastTab:AddButton({ Name=name, Callback=function() pcall(fn) end })
end

addButton("AntiAFK (Toggle)", function() if state.antiafk then disableAntiafk() else enableAntiafk() end end)
addButton("تقليل ضغط (Toggle)", function() if state.reduceLag then disableReduceLag() else enableReduceLag() end end)
addButton("حماية طرد (Soft Toggle)", function() if state.antiKickLocal then disableAntiKickLocal() else enableAntiKickLocal() end end)
addButton("brightness عادي", brightnessNormal)
addButton("brightness nan", brightnessNan)
addButton("brightness inf", brightnessInf)
addButton("nogui", nogui)
addButton("FPS 0.1", function() setfpscap(0.1) end)
addButton("replicationlag 80ms", function() if state.repLagEnabled then disableReplicationLag() else enableReplicationLag(80) end end)
addButton("CPU reduce", cpuReduce)
addButton("day", setDay)
addButton("nofog 0.1", function() nofog(0.1) end)
addButton("antilag", antilag)
addButton("reach x2", function() setReach(2) end)
addButton("autokeypress W x26", function() autokey_w(26) end)
addButton("تحرك تلقائي", function() if state.autoMove then stopAutoMove() else startAutoMove() end end)
addButton("قفز + اختفاء", function() jumpAndVanish(6) end)

-- ===== تبويب ضغط / تحسين الأداء =====
local PressureTab = Window:MakeTab({
    Name = "ضغط / تحسين الأداء",
    Icon = "rbxassetid://6035027362",
    PremiumOnly = false
})

PressureTab:AddButton({ Name = "تشغيل سكربت AL7FRAA-MADARAxCATAY", Callback = function()
    pcall(function() loadstring(game:HttpGet("https://raw.githubusercontent.com/MADARA9223/AL7FRAA-MADARAxCATAY/refs/heads/main/AL7FRAA%20%7C%20MADARAxCATAYxROBERTO"))() end)
    OrionLib:MakeNotification({Name="تم التشغيل", Content="AL7FRAA-MADARAxCATAY شغال", Image="rbxassetid://4483345998", Time=3})
end })

-- إشعار جاهزية الواجهة
OrionLib:MakeNotification({
    Name = "جاهز",
    Content = "واجهة لاست تم إنشاؤها",
    Image = "rbxassetid://4483345998",
    Time = 4
})
