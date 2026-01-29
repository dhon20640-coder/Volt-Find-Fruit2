-- Volt Find Fruit Script - Versão Otimizada
local Players,TweenService,RunService,ReplicatedStorage,Workspace,TeleportService=game:GetService("Players"),game:GetService("TweenService"),game:GetService("RunService"),game:GetService("ReplicatedStorage"),game:GetService("Workspace"),game:GetService("TeleportService")
local Player,PlayerGui=Players.LocalPlayer,Players.LocalPlayer:WaitForChild("PlayerGui")

-- ==================== CONFIGURAÇÕES GLOBAIS ====================
getgenv().VoltFindFruit=getgenv().VoltFindFruit or{StartTime=0,TotalTime=0,ScriptActive=true}
if getgenv().VoltFindFruit.StartTime==0 then getgenv().VoltFindFruit.StartTime=tick()end
getgenv().ACF,AutoRF,AutoSF=true,true,true

-- ==================== FUNÇÕES AUXILIARES ====================
local function FormatTime(s)return string.format("%02d:%02d:%02d",math.floor(s/3600),math.floor((s%3600)/60),math.floor(s%60))end
local function GetTotalActiveTime()return getgenv().VoltFindFruit.TotalTime+(tick()-getgenv().VoltFindFruit.StartTime)end

-- ==================== NOTIFICAÇÃO ====================
local function ShowNotification(title,message,duration)
    local gui=Instance.new("ScreenGui")gui.Name="VoltNotification"gui.ResetOnSpawn=false gui.ZIndexBehavior=Enum.ZIndexBehavior.Sibling gui.DisplayOrder=999
    local frame=Instance.new("Frame")frame.Size=UDim2.new(0,350,0,70)frame.Position=UDim2.new(0.5,-175,0,-80)frame.BackgroundColor3=Color3.fromRGB(30,30,30)frame.BorderSizePixel=0 frame.ZIndex=1000 frame.Parent=gui
    local corner=Instance.new("UICorner")corner.CornerRadius=UDim.new(0,10)corner.Parent=frame
    local t=Instance.new("TextLabel")t.Size=UDim2.new(1,-20,0,25)t.Position=UDim2.new(0,10,0,8)t.BackgroundTransparency=1 t.Font=Enum.Font.GothamBold t.Text=title t.TextColor3=Color3.fromRGB(255,100,100)t.TextSize=16 t.TextXAlignment=Enum.TextXAlignment.Left t.ZIndex=1001 t.Parent=frame
    local m=Instance.new("TextLabel")m.Size=UDim2.new(1,-20,0,22)m.Position=UDim2.new(0,10,0,38)m.BackgroundTransparency=1 m.Font=Enum.Font.Gotham m.Text=message m.TextColor3=Color3.fromRGB(255,255,255)m.TextSize=15 m.TextXAlignment=Enum.TextXAlignment.Left m.ZIndex=1001 m.Parent=frame
    gui.Parent=PlayerGui
    frame:TweenPosition(UDim2.new(0.5,-175,0,5),Enum.EasingDirection.Out,Enum.EasingStyle.Sine,0.3,true)
    task.delay(duration or 3,function()frame:TweenPosition(UDim2.new(0.5,-175,0,-80),Enum.EasingDirection.In,Enum.EasingStyle.Sine,0.3,true,function()task.wait(0.1)gui:Destroy()end)end)
end

-- ==================== CRIAÇÃO DO PAINEL GUI ====================
local function CreateMainGUI()
    if PlayerGui:FindFirstChild("VoltFindFruitGUI")then PlayerGui:FindFirstChild("VoltFindFruitGUI"):Destroy()end
    local gui=Instance.new("ScreenGui")gui.Name="VoltFindFruitGUI"gui.ResetOnSpawn=false gui.ZIndexBehavior=Enum.ZIndexBehavior.Sibling
    local main=Instance.new("Frame")main.Name="MainFrame"main.Size=UDim2.new(0,450,0,250)main.Position=UDim2.new(0.5,-225,0.5,-125)main.BackgroundColor3=Color3.fromRGB(200,30,30)main.BorderSizePixel=0 main.Parent=gui
    local shadow=Instance.new("Frame")shadow.Size=UDim2.new(1,10,1,10)shadow.Position=UDim2.new(0,-5,0,-5)shadow.BackgroundColor3=Color3.fromRGB(0,0,0)shadow.BackgroundTransparency=0.7 shadow.BorderSizePixel=0 shadow.ZIndex=0 shadow.Parent=main
    local c1,c2=Instance.new("UICorner"),Instance.new("UICorner")c1.CornerRadius=UDim.new(0,12)c1.Parent=main c2.CornerRadius=UDim.new(0,12)c2.Parent=shadow
    local title=Instance.new("TextLabel")title.Size=UDim2.new(1,0,0,50)title.BackgroundColor3=Color3.fromRGB(150,20,20)title.BorderSizePixel=0 title.Font=Enum.Font.GothamBold title.Text="Volt Find Fruit"title.TextColor3=Color3.fromRGB(255,255,255)title.TextSize=28 title.Parent=main
    local c3=Instance.new("UICorner")c3.CornerRadius=UDim.new(0,12)c3.Parent=title
    local div=Instance.new("Frame")div.Size=UDim2.new(1,-40,0,2)div.Position=UDim2.new(0,20,0,60)div.BackgroundColor3=Color3.fromRGB(255,255,255)div.BackgroundTransparency=0.5 div.BorderSizePixel=0 div.Parent=main
    local info=Instance.new("Frame")info.Size=UDim2.new(1,-40,0,140)info.Position=UDim2.new(0,20,0,80)info.BackgroundTransparency=1 info.Parent=main
    local farm=Instance.new("TextLabel")farm.Size=UDim2.new(0.5,-10,0,40)farm.Position=UDim2.new(0,0,0,20)farm.BackgroundTransparency=1 farm.Font=Enum.Font.GothamBold farm.Text="Time Farming Fruit:"farm.TextColor3=Color3.fromRGB(255,255,255)farm.TextSize=20 farm.TextXAlignment=Enum.TextXAlignment.Left farm.Parent=info
    local time=Instance.new("TextLabel")time.Size=UDim2.new(0.5,-10,0,40)time.Position=UDim2.new(0.5,10,0,20)time.BackgroundColor3=Color3.fromRGB(40,40,40)time.BorderSizePixel=0 time.Font=Enum.Font.GothamBold time.Text="00:00:00"time.TextColor3=Color3.fromRGB(0,255,100)time.TextSize=24 time.Parent=info
    local c4=Instance.new("UICorner")c4.CornerRadius=UDim.new(0,8)c4.Parent=time
    local status=Instance.new("TextLabel")status.Size=UDim2.new(1,0,0,35)status.Position=UDim2.new(0,0,0,80)status.BackgroundColor3=Color3.fromRGB(30,30,30)status.BorderSizePixel=0 status.Font=Enum.Font.Gotham status.Text="🟢 Script Ativo | Auto Farm Habilitado"status.TextColor3=Color3.fromRGB(0,255,100)status.TextSize=16 status.Parent=info
    local c5=Instance.new("UICorner")c5.CornerRadius=UDim.new(0,8)c5.Parent=status
    local dragging,dragInput,dragStart,startPos
    local function update(input)local delta=input.Position-dragStart main.Position=UDim2.new(startPos.X.Scale,startPos.X.Offset+delta.X,startPos.Y.Scale,startPos.Y.Offset+delta.Y)end
    title.InputBegan:Connect(function(input)if input.UserInputType==Enum.UserInputType.MouseButton1 or input.UserInputType==Enum.UserInputType.Touch then dragging=true dragStart=input.Position startPos=main.Position input.Changed:Connect(function()if input.UserInputState==Enum.UserInputState.End then dragging=false end end)end end)
    title.InputChanged:Connect(function(input)if input.UserInputType==Enum.UserInputType.MouseMovement or input.UserInputType==Enum.UserInputType.Touch then dragInput=input end end)
    RunService.Heartbeat:Connect(function()if dragging and dragInput then update(dragInput)end end)
    gui.Parent=PlayerGui
    return gui,time,status
end

-- ==================== AUTO TEAM ====================
local PIRATE,MARINE=true,false
local function AutoSelectTeam()
    task.spawn(function()
        local RS=game:GetService("ReplicatedStorage")
        local CF=RS:WaitForChild("Remotes",5)and RS.Remotes:FindFirstChild("CommF_")
        if not Player.Team and CF then task.wait(2)pcall(function()CF:InvokeServer("SetTeam",PIRATE and"Pirates"or"Marines")end)end
        task.wait(1)repeat task.wait()until Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")task.wait(2)
    end)
end

-- ==================== DETECÇÃO DE FRUTAS ====================
local FruitNames={"Bomb-Bomb","Spike-Spike","Chop-Chop","Spring-Spring","Kilo-Kilo","Smoke-Smoke","Flame-Flame","Ice-Ice","Sand-Sand","Dark-Dark","Ghost-Ghost","Magma-Magma","Quake-Quake","Buddha-Buddha","Love-Love","Spider-Spider","Phoenix-Phoenix","Portal-Portal","Rumble-Rumble","Pain-Pain","Blizzard-Blizzard","Gravity-Gravity","Dough-Dough","Shadow-Shadow","Venom-Venom","Control-Control","Spirit-Spirit","Dragon-Dragon","Leopard-Leopard"}
local FruitSet={}for _,name in ipairs(FruitNames)do FruitSet[name]=true end
local function IsFruitModel(m)if not m or not m:IsA("Model")then return false end return FruitSet[m.Name]or m.Name:find("Fruit")~=nil end
local function AnyFruitInServer()for _,m in ipairs(Workspace:GetChildren())do if IsFruitModel(m)then return true end end return false end
local function GetNearestFruit()
    local c=Player.Character local hrp=c and c:FindFirstChild("HumanoidRootPart")if not hrp then return nil end
    local nearest,minDist=nil,math.huge
    for _,m in ipairs(Workspace:GetChildren())do if IsFruitModel(m)then local p=m:FindFirstChildWhichIsA("BasePart")if p then local d=(p.Position-hrp.Position).Magnitude if d<minDist then minDist=d nearest=p end end end end
    return nearest
end

-- ==================== AUTO HOP SERVER ====================
local lastHopCheck,isHopping=tick(),false
local function ServerHop()
    if isHopping then return end isHopping=true
    pcall(function()
        local servers=game.HttpService:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/"..game.PlaceId.."/servers/Public?sortOrder=Asc&limit=100"))
        if servers and servers.data then for _,s in pairs(servers.data)do if s.id~=game.JobId and s.playing<s.maxPlayers then TeleportService:TeleportToPlaceInstance(game.PlaceId,s.id,Player)return true end end end
        TeleportService:Teleport(game.PlaceId,Player)
    end)
end
local function CheckAndHop()
    if not AnyFruitInServer()then
        warn("⚠️ Nenhuma fruta detectada. Aguardando 5s para confirmar...")
        task.wait(5)
        if not AnyFruitInServer()then
            ShowNotification("🔄 Server Hop","Server Hopping in 5s",5)
            warn("⚠️ Confirmado sem frutas. Server Hopping em 5s...")
            task.wait(5)
            if not AnyFruitInServer()then ServerHop()else warn("✅ Frutas detectadas! Cancelando hop.")end
        else warn("✅ Frutas apareceram! Cancelando verificação.")end
    end
end

-- ==================== AUTO FARM FRUITS ====================
local CF=ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("CommF_")
task.spawn(function()while task.wait(0.25)do pcall(function()if AutoRF and CF then CF:InvokeServer("Cousin","Buy")end end)end end)

local function UpdateStoreFruit()
    pcall(function()
        for _,t in pairs(Player.Backpack:GetChildren())do local e=t:FindFirstChild("EatRemote",true)if e then ReplicatedStorage.Remotes.CommF_:InvokeServer("StoreFruit",e.Parent:GetAttribute("OriginalName"),Player.Backpack:FindFirstChild(t.Name))end end
        local c=Player.Character if c then for _,t in pairs(c:GetChildren())do if t:IsA("Tool")then local e=t:FindFirstChild("EatRemote",true)if e then ReplicatedStorage.Remotes.CommF_:InvokeServer("StoreFruit",e.Parent:GetAttribute("OriginalName"),c:FindFirstChild(t.Name))end end end end
    end)
end
task.spawn(function()while task.wait(0.5)do if AutoSF then UpdateStoreFruit()end end end)

local activeTween,heartbeatConn,isCollecting,fruitsCollectedThisSession=nil,nil,false,0
local function StopTween()if activeTween then activeTween:Cancel()activeTween=nil end if heartbeatConn then heartbeatConn:Disconnect()heartbeatConn=nil end end
local function HasFruit()
    for _,t in pairs(Player.Backpack:GetChildren())do if t:FindFirstChild("EatRemote",true)then return true end end
    local c=Player.Character if c then for _,t in pairs(c:GetChildren())do if t:IsA("Tool")and t:FindFirstChild("EatRemote",true)then return true end end end
    return false
end

task.spawn(function()
    while task.wait(0.3)do
        pcall(function()
            if not getgenv().ACF then StopTween()isCollecting=false return end
            if isCollecting then return end
            if not AnyFruitInServer()then StopTween()isCollecting=false if StatusLabel and StatusLabel.Parent then StatusLabel.Text=string.format("🟢 Script Ativo | Frutas Coletadas: %d",fruitsCollectedThisSession)end return end
            isCollecting=true
            local fruitPart=GetNearestFruit()if not fruitPart then isCollecting=false return end
            local c=Player.Character local hrp=c and c:FindFirstChild("HumanoidRootPart")if not hrp then isCollecting=false return end
            local dist=(hrp.Position-fruitPart.Position).Magnitude local dur=dist/250
            activeTween=TweenService:Create(hrp,TweenInfo.new(dur,Enum.EasingStyle.Linear),{CFrame=fruitPart.CFrame})activeTween:Play()
            heartbeatConn=RunService.Heartbeat:Connect(function()if not fruitPart or not fruitPart.Parent or not getgenv().ACF then StopTween()return end hrp.Velocity=Vector3.new(0,0,0)end)
            activeTween.Completed:Wait()task.wait(0.2)StopTween()
            local fruitName=fruitPart.Parent and fruitPart.Parent.Name or"Unknown"local maxWait,waited,collected=3,0,false
            while waited<maxWait and fruitPart and fruitPart.Parent do task.wait(0.1)waited=waited+0.1 if HasFruit()or not fruitPart.Parent then collected=true break end end
            if collected then fruitsCollectedThisSession=fruitsCollectedThisSession+1 warn(string.format("✅ Fruta coletada: %s | Total: %d",fruitName,fruitsCollectedThisSession))if StatusLabel and StatusLabel.Parent then StatusLabel.Text=string.format("🟢 Script Ativo | Frutas: %d | Coletando...",fruitsCollectedThisSession)end end
            isCollecting=false
        end)
    end
end)

-- ==================== INICIALIZAÇÃO ====================
local ScreenGui,TimeDisplay,StatusLabel=CreateMainGUI()
AutoSelectTeam()
task.spawn(function()while task.wait(1)do if TimeDisplay and TimeDisplay.Parent then TimeDisplay.Text=FormatTime(GetTotalActiveTime())end end end)
task.spawn(function()while task.wait(5)do pcall(function()if not isCollecting then CheckAndHop()end end)end end)
task.spawn(function()while task.wait(5)do pcall(function()if not isCollecting and not isHopping then local hasFruits=AnyFruitInServer()if StatusLabel and StatusLabel.Parent then if hasFruits then StatusLabel.Text=string.format("🟢 Script Ativo | Frutas: %d | Procurando...",fruitsCollectedThisSession)StatusLabel.TextColor3=Color3.fromRGB(0,255,100)else StatusLabel.Text=string.format("🔴 Nenhuma fruta | Total coletado: %d",fruitsCollectedThisSession)StatusLabel.TextColor3=Color3.fromRGB(255,100,100)end end end end)end end)
print("✅ Volt Find Fruit carregado!")print("⏱️ Tempo persistente ativo")print("🍎 Auto Collect/Random/Store ativados")print("🔄 Auto Hop: 5s")print("🔔 Notificações habilitadas")
