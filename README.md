local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

local DrawingSupported = pcall(function()
    return Drawing ~= nil
end)

if not DrawingSupported then
    warn("⚠️ Drawing API não suportada! ESP e FOV não funcionarão.")
end

local Config = {
    MainEnabled = false,
    MinimizedPanel = false,
    ESP = {
        Enabled = false,
        ShowBox = true,
        ShowHealthBar = true,
        ShowDistance = true,
        ShowName = true,
        ShowTracers = true,
        BoxColor = Color3.fromRGB(255, 0, 255),
        HealthBarColor = Color3.fromRGB(0, 255, 0),
        TextColor = Color3.fromRGB(255, 255, 255),
        TracerColor = Color3.fromRGB(255, 0, 255),
        MaxDistance = 1000
    },
    Aimbot = {
        Enabled = false,
        FOVCircle = true,
        FOVSize = 150,
        FOVColor = Color3.fromRGB(255, 255, 255),
        Smoothness = 0.3,
        TargetPart = "Head",
        TeamCheck = false,
        VisibleCheck = false,
        MaxDistance = 500
    },
    NoClip = {
        Enabled = false
    },
    Speed = {
        Enabled = false,
        SpeedValue = 50
    },
    InfiniteJump = {
        Enabled = false
    },
    Flight = {
        Enabled = false,
        FlySpeed = 50
    },
    AutoFarm = {
        Enabled = false
    },
    AntiAFK = {
        Enabled = false
    },
    FullBright = {
        Enabled = false
    }
}

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ExploitPanel"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

if LocalPlayer.PlayerGui:FindFirstChild("ExploitPanel") then
    LocalPlayer.PlayerGui.ExploitPanel:Destroy()
end

ScreenGui.Parent = LocalPlayer.PlayerGui

local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 450, 0, 650)
MainFrame.Position = UDim2.new(0.5, -225, 0.5, -325)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
MainFrame.BorderSizePixel = 0
MainFrame.Parent = ScreenGui

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 15)
UICorner.Parent = MainFrame

local Header = Instance.new("Frame")
Header.Name = "Header"
Header.Size = UDim2.new(1, 0, 0, 80)
Header.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
Header.BorderSizePixel = 0
Header.Parent = MainFrame

local HeaderCorner = Instance.new("UICorner")
HeaderCorner.CornerRadius = UDim.new(0, 15)
HeaderCorner.Parent = Header

local Title = Instance.new("TextLabel")
Title.Name = "Title"
Title.Size = UDim2.new(1, -160, 1, 0)
Title.Position = UDim2.new(0, 20, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "🎮 ROBLOX EXPLOIT"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 28
Title.Font = Enum.Font.GothamBold
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = Header

local MinimizeButton = Instance.new("TextButton")
MinimizeButton.Name = "MinimizeButton"
MinimizeButton.Size = UDim2.new(0, 50, 0, 50)
MinimizeButton.Position = UDim2.new(1, -130, 0.5, -25)
MinimizeButton.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
MinimizeButton.Text = "–"
MinimizeButton.TextSize = 30
MinimizeButton.Font = Enum.Font.GothamBold
MinimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
MinimizeButton.BorderSizePixel = 0
MinimizeButton.Parent = Header

local MinimizeCorner = Instance.new("UICorner")
MinimizeCorner.CornerRadius = UDim.new(0, 10)
MinimizeCorner.Parent = MinimizeButton

local PowerButton = Instance.new("TextButton")
PowerButton.Name = "PowerButton"
PowerButton.Size = UDim2.new(0, 60, 0, 60)
PowerButton.Position = UDim2.new(1, -70, 0.5, -30)
PowerButton.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
PowerButton.Text = "⚡"
PowerButton.TextSize = 30
PowerButton.Font = Enum.Font.GothamBold
PowerButton.TextColor3 = Color3.fromRGB(255, 255, 255)
PowerButton.BorderSizePixel = 0
PowerButton.Parent = Header

local PowerCorner = Instance.new("UICorner")
PowerCorner.CornerRadius = UDim.new(0, 12)
PowerCorner.Parent = PowerButton

local FeaturesContainer = Instance.new("ScrollingFrame")
FeaturesContainer.Name = "FeaturesContainer"
FeaturesContainer.Size = UDim2.new(1, -20, 1, -100)
FeaturesContainer.Position = UDim2.new(0, 10, 0, 90)
FeaturesContainer.BackgroundTransparency = 1
FeaturesContainer.BorderSizePixel = 0
FeaturesContainer.ScrollBarThickness = 6
FeaturesContainer.CanvasSize = UDim2.new(0, 0, 0, 1840)
FeaturesContainer.Parent = MainFrame

local function CreateFeatureButton(name, icon, yPosition)
    local Button = Instance.new("TextButton")
    Button.Name = name .. "Button"
    Button.Size = UDim2.new(1, 0, 0, 70)
    Button.Position = UDim2.new(0, 0, 0, yPosition)
    Button.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
    Button.BorderSizePixel = 0
    Button.Text = ""
    Button.Parent = FeaturesContainer
    
    local BtnCorner = Instance.new("UICorner")
    BtnCorner.CornerRadius = UDim.new(0, 10)
    BtnCorner.Parent = Button
    
    local IconLabel = Instance.new("TextLabel")
    IconLabel.Size = UDim2.new(0, 50, 0, 50)
    IconLabel.Position = UDim2.new(0, 10, 0.5, -25)
    IconLabel.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
    IconLabel.Text = icon
    IconLabel.TextSize = 24
    IconLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    IconLabel.Font = Enum.Font.GothamBold
    IconLabel.Parent = Button
    
    local IconCorner = Instance.new("UICorner")
    IconCorner.CornerRadius = UDim.new(0, 8)
    IconCorner.Parent = IconLabel
    
    local NameLabel = Instance.new("TextLabel")
    NameLabel.Size = UDim2.new(1, -180, 1, 0)
    NameLabel.Position = UDim2.new(0, 70, 0, 0)
    NameLabel.BackgroundTransparency = 1
    NameLabel.Text = name
    NameLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    NameLabel.TextSize = 18
    NameLabel.Font = Enum.Font.GothamBold
    NameLabel.TextXAlignment = Enum.TextXAlignment.Left
    NameLabel.Parent = Button
    
    local StatusLabel = Instance.new("TextLabel")
    StatusLabel.Name = "Status"
    StatusLabel.Size = UDim2.new(0, 80, 0, 30)
    StatusLabel.Position = UDim2.new(1, -90, 0.5, -15)
    StatusLabel.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
    StatusLabel.Text = "OFF"
    StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    StatusLabel.TextSize = 14
    StatusLabel.Font = Enum.Font.GothamBold
    StatusLabel.Parent = Button
    
    local StatusCorner = Instance.new("UICorner")
    StatusCorner.CornerRadius = UDim.new(0, 8)
    StatusCorner.Parent = StatusLabel
    
    return Button
end

local ESPButton = CreateFeatureButton("ESP Vision", "👁️", 0)
local ESPRangeButton = CreateFeatureButton("ESP Range: 1000", "📏", 80)
local ColorPickerButton = CreateFeatureButton("ESP Color", "🎨", 160)
local RefreshESPButton = CreateFeatureButton("Refresh ESP", "🔄", 240)
local AimbotButton = CreateFeatureButton("Aimbot", "🎯", 320)
local AimbotRangeButton = CreateFeatureButton("Aim Range: 500", "🎯", 400)
local FOVButton = CreateFeatureButton("FOV Circle", "⭕", 480)
local FOVSizeButton = CreateFeatureButton("FOV Size: 150", "📐", 560)
local FOVColorButton = CreateFeatureButton("FOV Color", "🌈", 640)
local NoClipButton = CreateFeatureButton("No Clip", "👻", 720)
local SpeedButton = CreateFeatureButton("Speed Boost", "⚡", 800)
local FlightButton = CreateFeatureButton("Fly Mode", "✈️", 880)
local JumpButton = CreateFeatureButton("Infinite Jump", "🚀", 960)
local FullBrightButton = CreateFeatureButton("Full Bright", "💡", 1040)
local AntiAFKButton = CreateFeatureButton("Anti AFK", "⏰", 1120)
local AutoFarmButton = CreateFeatureButton("Auto Farm", "🌾", 1200)
local TeleportButton = CreateFeatureButton("Teleport to Player", "📍", 1280)

local ESPObjects = {}

local function CreateESP(player)
    if not DrawingSupported then return end
    if player == LocalPlayer then return end
    
    local ESP = {
        Player = player,
        Box = Drawing.new("Square"),
        HealthBar = Drawing.new("Square"),
        HealthBarBg = Drawing.new("Square"),
        NameText = Drawing.new("Text"),
        DistanceText = Drawing.new("Text"),
        TracerLine = Drawing.new("Line")
    }
    
    ESP.Box.Thickness = 2
    ESP.Box.Filled = false
    ESP.Box.Color = Config.ESP.BoxColor
    ESP.Box.Visible = false
    ESP.Box.Transparency = 1
    
    ESP.HealthBarBg.Thickness = 1
    ESP.HealthBarBg.Filled = true
    ESP.HealthBarBg.Color = Color3.fromRGB(0, 0, 0)
    ESP.HealthBarBg.Visible = false
    ESP.HealthBarBg.Transparency = 1
    
    ESP.HealthBar.Thickness = 1
    ESP.HealthBar.Filled = true
    ESP.HealthBar.Color = Config.ESP.HealthBarColor
    ESP.HealthBar.Visible = false
    ESP.HealthBar.Transparency = 1
    
    ESP.NameText.Center = true
    ESP.NameText.Outline = true
    ESP.NameText.Color = Config.ESP.TextColor
    ESP.NameText.Size = 16
    ESP.NameText.Visible = false
    ESP.NameText.Transparency = 1
    
    ESP.DistanceText.Center = true
    ESP.DistanceText.Outline = true
    ESP.DistanceText.Color = Config.ESP.TextColor
    ESP.DistanceText.Size = 14
    ESP.DistanceText.Visible = false
    ESP.DistanceText.Transparency = 1
    
    ESP.TracerLine.Thickness = 2
    ESP.TracerLine.Color = Config.ESP.TracerColor
    ESP.TracerLine.Transparency = 1
    ESP.TracerLine.Visible = false
    
    ESPObjects[player] = ESP
end

local function RemoveESP(player)
    if ESPObjects[player] then
        for key, obj in pairs(ESPObjects[player]) do
            if key ~= "Player" then
                pcall(function()
                    obj:Remove()
                end)
            end
        end
        ESPObjects[player] = nil
    end
end

local function UpdateESP()
    if not Config.ESP.Enabled or not DrawingSupported then return end
    if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then return end
    
    local camera = workspace.CurrentCamera
    local myPos = LocalPlayer.Character.HumanoidRootPart.Position
    
    for player, esp in pairs(ESPObjects) do
        pcall(function()
            if player.Character and player.Character:FindFirstChild("HumanoidRootPart") and player.Character:FindFirstChild("Humanoid") then
                local hrp = player.Character.HumanoidRootPart
                local humanoid = player.Character.Humanoid
                
                if not hrp or not humanoid then return end
                
                local distance = (myPos - hrp.Position).Magnitude
                
                if distance > Config.ESP.MaxDistance then
                    esp.Box.Visible = false
                    esp.HealthBar.Visible = false
                    esp.HealthBarBg.Visible = false
                    esp.NameText.Visible = false
                    esp.DistanceText.Visible = false
                    esp.TracerLine.Visible = false
                    return
                end
                
                local vector, onScreen = camera:WorldToViewportPoint(hrp.Position)
                
                if onScreen and vector.Z > 0 then
                    local width = math.clamp(2000 / vector.Z, 40, 200)
                    local height = width * 1.6
                    
                    if Config.ESP.ShowBox then
                        esp.Box.Size = Vector2.new(width, height)
                        esp.Box.Position = Vector2.new(vector.X - width / 2, vector.Y - height / 2)
                        esp.Box.Color = Config.ESP.BoxColor
                        esp.Box.Visible = true
                    else
                        esp.Box.Visible = false
                    end
                    
                    if Config.ESP.ShowHealthBar and humanoid.Health > 0 then
                        local healthPercent = math.clamp(humanoid.Health / humanoid.MaxHealth, 0, 1)
                        local barHeight = height
                        local barWidth = 6
                        local barX = vector.X - width / 2 - 12
                        
                        esp.HealthBarBg.Size = Vector2.new(barWidth, barHeight)
                        esp.HealthBarBg.Position = Vector2.new(barX, vector.Y - height / 2)
                        esp.HealthBarBg.Color = Color3.fromRGB(30, 30, 30)
                        esp.HealthBarBg.Filled = true
                        esp.HealthBarBg.Visible = true
                        esp.HealthBarBg.Transparency = 0.8
                        
                        local healthBarHeight = barHeight * healthPercent
                        esp.HealthBar.Size = Vector2.new(barWidth, math.max(healthBarHeight, 2))
                        esp.HealthBar.Position = Vector2.new(barX, vector.Y - height / 2 + (barHeight - healthBarHeight))
                        
                        if healthPercent > 0.5 then
                            esp.HealthBar.Color = Color3.fromRGB(255 * (1 - healthPercent) * 2, 255, 0)
                        else
                            esp.HealthBar.Color = Color3.fromRGB(255, 255 * healthPercent * 2, 0)
                        end
                        
                        esp.HealthBar.Filled = true
                        esp.HealthBar.Visible = true
                        esp.HealthBar.Transparency = 1
                    else
                        esp.HealthBarBg.Visible = false
                        esp.HealthBar.Visible = false
                    end
                    
                    if Config.ESP.ShowName then
                        esp.NameText.Text = player.Name
                        esp.NameText.Position = Vector2.new(vector.X, vector.Y - height / 2 - 18)
                        esp.NameText.Color = Config.ESP.TextColor
                        esp.NameText.Visible = true
                    else
                        esp.NameText.Visible = false
                    end
                    
                    if Config.ESP.ShowDistance then
                        esp.DistanceText.Text = string.format("%.0fm", distance)
                        esp.DistanceText.Position = Vector2.new(vector.X, vector.Y + height / 2 + 5)
                        esp.DistanceText.Color = Config.ESP.TextColor
                        esp.DistanceText.Visible = true
                    else
                        esp.DistanceText.Visible = false
                    end
                    
                    if Config.ESP.ShowTracers then
                        local screenCenter = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y)
                        esp.TracerLine.From = screenCenter
                        esp.TracerLine.To = Vector2.new(vector.X, vector.Y)
                        esp.TracerLine.Color = Config.ESP.TracerColor
                        esp.TracerLine.Visible = true
                    else
                        esp.TracerLine.Visible = false
                    end
                else
                    esp.Box.Visible = false
                    esp.HealthBar.Visible = false
                    esp.HealthBarBg.Visible = false
                    esp.NameText.Visible = false
                    esp.DistanceText.Visible = false
                    esp.TracerLine.Visible = false
                end
            else
                esp.Box.Visible = false
                esp.HealthBar.Visible = false
                esp.HealthBarBg.Visible = false
                esp.NameText.Visible = false
                esp.DistanceText.Visible = false
                esp.TracerLine.Visible = false
            end
        end)
    end
end

local FOVCircle
if DrawingSupported then
    FOVCircle = Drawing.new("Circle")
    FOVCircle.Thickness = 2
    FOVCircle.NumSides = 64
    FOVCircle.Radius = Config.Aimbot.FOVSize
    FOVCircle.Filled = false
    FOVCircle.Visible = false
    FOVCircle.Transparency = 1
    FOVCircle.Color = Config.Aimbot.FOVColor
end

local function GetClosestPlayerInFOV()
    local closestPlayer = nil
    local shortestDistance = math.huge
    local camera = workspace.CurrentCamera
    local screenCenter = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)
    
    if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        return nil
    end
    
    local myPos = LocalPlayer.Character.HumanoidRootPart.Position
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            if Config.Aimbot.TeamCheck and player.Team == LocalPlayer.Team then
                continue
            end
            
            local character = player.Character
            local targetPart = character:FindFirstChild(Config.Aimbot.TargetPart)
            
            if targetPart and character:FindFirstChild("Humanoid") and character.Humanoid.Health > 0 then
                local worldDistance = (myPos - targetPart.Position).Magnitude
                
                if worldDistance > Config.Aimbot.MaxDistance then
                    continue
                end
                
                local screenPos, onScreen = camera:WorldToViewportPoint(targetPart.Position)
                
                if onScreen and screenPos.Z > 0 then
                    if Config.Aimbot.VisibleCheck then
                        local ray = Ray.new(camera.CFrame.Position, (targetPart.Position - camera.CFrame.Position).Unit * 1000)
                        local hit = workspace:FindPartOnRayWithIgnoreList(ray, {LocalPlayer.Character})
                        
                        if hit and not character:IsAncestorOf(hit) then
                            continue
                        end
                    end
                    
                    local screenPoint = Vector2.new(screenPos.X, screenPos.Y)
                    local distance = (screenPoint - screenCenter).Magnitude
                    
                    if distance < Config.Aimbot.FOVSize and distance < shortestDistance then
                        closestPlayer = player
                        shortestDistance = distance
                    end
                end
            end
        end
    end
    
    return closestPlayer
end

local function MoveMouse(targetPos)
    local currentMousePos = UserInputService:GetMouseLocation()
    
    local deltaX = (targetPos.X - currentMousePos.X) * Config.Aimbot.Smoothness
    local deltaY = (targetPos.Y - currentMousePos.Y) * Config.Aimbot.Smoothness
    
    pcall(function()
        if mousemoverel then
            mousemoverel(deltaX, deltaY)
        elseif Input and Input.MouseMove then
            Input.MouseMove(deltaX, deltaY)
        end
    end)
end

local function AimbotLoop()
    if not Config.Aimbot.Enabled then return end
    
    local target = GetClosestPlayerInFOV()
    
    if target and target.Character then
        local targetPart = target.Character:FindFirstChild(Config.Aimbot.TargetPart)
        if targetPart then
            local camera = workspace.CurrentCamera
            local targetPosition = targetPart.Position
            
            local currentCFrame = camera.CFrame
            local targetCFrame = CFrame.new(currentCFrame.Position, targetPosition)
            camera.CFrame = currentCFrame:Lerp(targetCFrame, Config.Aimbot.Smoothness)
            
            local screenPos, onScreen = camera:WorldToViewportPoint(targetPosition)
            if onScreen and screenPos.Z > 0 then
                local targetScreenPos = Vector2.new(screenPos.X, screenPos.Y)
                MoveMouse(targetScreenPos)
            end
        end
    end
end

local function ToggleNoClip()
    if not LocalPlayer.Character then return end
    
    for _, part in pairs(LocalPlayer.Character:GetDescendants()) do
        if part:IsA("BasePart") then
            part.CanCollide = not Config.NoClip.Enabled
        end
    end
end

local function ToggleSpeed()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        if Config.Speed.Enabled then
            LocalPlayer.Character.Humanoid.WalkSpeed = Config.Speed.SpeedValue
        else
            LocalPlayer.Character.Humanoid.WalkSpeed = 16
        end
    end
end

local InfiniteJumpConnection

local function ToggleInfiniteJump()
    if Config.InfiniteJump.Enabled then
        InfiniteJumpConnection = UserInputService.JumpRequest:Connect(function()
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
                LocalPlayer.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            end
        end)
    else
        if InfiniteJumpConnection then
            InfiniteJumpConnection:Disconnect()
        end
    end
end

local Flying = false
local FlySpeed = Config.Flight.FlySpeed
local BodyVelocity, BodyGyro

local function StartFlying()
    local character = LocalPlayer.Character
    if not character then return end
    
    local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
    if not humanoidRootPart then return end
    
    Flying = true
    
    BodyVelocity = Instance.new("BodyVelocity")
    BodyVelocity.Velocity = Vector3.new(0, 0, 0)
    BodyVelocity.MaxForce = Vector3.new(9e9, 9e9, 9e9)
    BodyVelocity.Parent = humanoidRootPart
    
    BodyGyro = Instance.new("BodyGyro")
    BodyGyro.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
    BodyGyro.P = 9e4
    BodyGyro.Parent = humanoidRootPart
    
    repeat
        wait()
        if character and humanoidRootPart then
            local camera = workspace.CurrentCamera
            local moveDirection = Vector3.new(0, 0, 0)
            
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then
                moveDirection = moveDirection + (camera.CFrame.LookVector * FlySpeed)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then
                moveDirection = moveDirection - (camera.CFrame.LookVector * FlySpeed)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then
                moveDirection = moveDirection - (camera.CFrame.RightVector * FlySpeed)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then
                moveDirection = moveDirection + (camera.CFrame.RightVector * FlySpeed)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
                moveDirection = moveDirection + Vector3.new(0, FlySpeed, 0)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then
                moveDirection = moveDirection - Vector3.new(0, FlySpeed, 0)
            end
            
            BodyVelocity.Velocity = moveDirection
            BodyGyro.CFrame = camera.CFrame
        end
    until not Flying or not Config.Flight.Enabled
end

local function StopFlying()
    Flying = false
    if BodyVelocity then BodyVelocity:Destroy() end
    if BodyGyro then BodyGyro:Destroy() end
end

local OriginalLighting = {}

local function ToggleFullBright()
    local Lighting = game:GetService("Lighting")
    
    if Config.FullBright.Enabled then
        OriginalLighting = {
            Ambient = Lighting.Ambient,
            Brightness = Lighting.Brightness,
            ColorShift_Bottom = Lighting.ColorShift_Bottom,
            ColorShift_Top = Lighting.ColorShift_Top,
            OutdoorAmbient = Lighting.OutdoorAmbient,
            FogEnd = Lighting.FogEnd,
            FogStart = Lighting.FogStart
        }
        
        Lighting.Ambient = Color3.new(1, 1, 1)
        Lighting.Brightness = 2
        Lighting.ColorShift_Bottom = Color3.new(1, 1, 1)
        Lighting.ColorShift_Top = Color3.new(1, 1, 1)
        Lighting.OutdoorAmbient = Color3.new(1, 1, 1)
        Lighting.FogEnd = 100000
        Lighting.FogStart = 100000
    else
        if OriginalLighting.Ambient then
            Lighting.Ambient = OriginalLighting.Ambient
            Lighting.Brightness = OriginalLighting.Brightness
            Lighting.ColorShift_Bottom = OriginalLighting.ColorShift_Bottom
            Lighting.ColorShift_Top = OriginalLighting.ColorShift_Top
            Lighting.OutdoorAmbient = OriginalLighting.OutdoorAmbient
            Lighting.FogEnd = OriginalLighting.FogEnd
            Lighting.FogStart = OriginalLighting.FogStart
        end
    end
end

local AntiAFKConnection

local function ToggleAntiAFK()
    if Config.AntiAFK.Enabled then
        local VirtualUser = game:GetService("VirtualUser")
        AntiAFKConnection = LocalPlayer.Idled:Connect(function()
            VirtualUser:CaptureController()
            VirtualUser:ClickButton2(Vector2.new())
        end)
    else
        if AntiAFKConnection then
            AntiAFKConnection:Disconnect()
        end
    end
end

local AutoFarmConnection

local function ToggleAutoFarm()
    if Config.AutoFarm.Enabled then
        AutoFarmConnection = RunService.Heartbeat:Connect(function()
            pcall(function()
                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                    local hrp = LocalPlayer.Character.HumanoidRootPart
                    
                    for _, obj in pairs(workspace:GetDescendants()) do
                        if obj:IsA("BasePart") and (obj.Name == "Coin" or obj.Name:lower():find("coin")) then
                            if (obj.Position - hrp.Position).Magnitude < 50 then
                                hrp.CFrame = CFrame.new(obj.Position)
                                wait(0.1)
                            end
                        end
                    end
                end
            end)
        end)
    else
        if AutoFarmConnection then
            AutoFarmConnection:Disconnect()
        end
    end
end

MinimizeButton.MouseButton1Click:Connect(function()
    Config.MinimizedPanel = not Config.MinimizedPanel
    
    if Config.MinimizedPanel then
        FeaturesContainer.Visible = false
        MainFrame:TweenSize(UDim2.new(0, 450, 0, 80), "Out", "Quad", 0.3, true)
        MinimizeButton.Text = "+"
    else
        MainFrame:TweenSize(UDim2.new(0, 450, 0, 650), "Out", "Quad", 0.3, true, function()
            FeaturesContainer.Visible = true
        end)
        MinimizeButton.Text = "–"
    end
end)

PowerButton.MouseButton1Click:Connect(function()
    Config.MainEnabled = not Config.MainEnabled
    
    if Config.MainEnabled then
        PowerButton.BackgroundColor3 = Color3.fromRGB(40, 167, 69)
        PowerButton.Text = "✓"
        print("🟢 Exploit ATIVADO!")
    else
        PowerButton.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        PowerButton.Text = "⚡"
        print("🔴 Exploit DESATIVADO!")
        
        Config.ESP.Enabled = false
        Config.NoClip.Enabled = false
        Config.Speed.Enabled = false
        Config.InfiniteJump.Enabled = false
        Config.Aimbot.Enabled = false
        Config.Flight.Enabled = false
        Config.FullBright.Enabled = false
        Config.AntiAFK.Enabled = false
        Config.AutoFarm.Enabled = false
        
        ESPButton.Status.Text = "OFF"
        ESPButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        AimbotButton.Status.Text = "OFF"
        AimbotButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        FOVButton.Status.Text = "OFF"
        FOVButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        NoClipButton.Status.Text = "OFF"
        NoClipButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        SpeedButton.Status.Text = "OFF"
        SpeedButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        FlightButton.Status.Text = "OFF"
        FlightButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        JumpButton.Status.Text = "OFF"
        JumpButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        FullBrightButton.Status.Text = "OFF"
        FullBrightButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        AntiAFKButton.Status.Text = "OFF"
        AntiAFKButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        AutoFarmButton.Status.Text = "OFF"
        AutoFarmButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        
        if FOVCircle then FOVCircle.Visible = false end
        ToggleSpeed()
        ToggleNoClip()
        ToggleInfiniteJump()
        StopFlying()
        ToggleFullBright()
        ToggleAntiAFK()
        ToggleAutoFarm()
        
        for player in pairs(ESPObjects) do
            RemoveESP(player)
        end
    end
end)

ESPButton.MouseButton1Click:Connect(function()
    if not Config.MainEnabled then return end
    
    Config.ESP.Enabled = not Config.ESP.Enabled
    
    if Config.ESP.Enabled then
        ESPButton.Status.Text = "ON"
        ESPButton.Status.BackgroundColor3 = Color3.fromRGB(40, 167, 69)
        print("👁️ ESP Ativado!")
        
        for _, player in pairs(Players:GetPlayers()) do
            CreateESP(player)
        end
    else
        ESPButton.Status.Text = "OFF"
        ESPButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        print("👁️ ESP Desativado!")
        
        for player in pairs(ESPObjects) do
            RemoveESP(player)
        end
    end
end)

local ESPRanges = {100, 250, 500, 750, 1000, 1500, 2000, 3000, 5000}
local CurrentESPRangeIndex = 5

ESPRangeButton.MouseButton1Click:Connect(function()
    if not Config.MainEnabled then return end
    
    CurrentESPRangeIndex = CurrentESPRangeIndex + 1
    if CurrentESPRangeIndex > #ESPRanges then
        CurrentESPRangeIndex = 1
    end
    
    Config.ESP.MaxDistance = ESPRanges[CurrentESPRangeIndex]
    
    for _, child in pairs(ESPRangeButton:GetChildren()) do
        if child:IsA("TextLabel") and child.Name ~= "Status" then
            child.Text = "ESP Range: " .. Config.ESP.MaxDistance
        end
    end
    
    ESPRangeButton.Status.Text = tostring(Config.ESP.MaxDistance)
    ESPRangeButton.Status.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
    print("📏 ESP Range: " .. Config.ESP.MaxDistance .. " studs")
end)

local ColorPresets = {
    {name = "Roxo", color = Color3.fromRGB(255, 0, 255)},
    {name = "Vermelho", color = Color3.fromRGB(255, 0, 0)},
    {name = "Verde", color = Color3.fromRGB(0, 255, 0)},
    {name = "Azul", color = Color3.fromRGB(0, 150, 255)},
    {name = "Amarelo", color = Color3.fromRGB(255, 255, 0)},
    {name = "Ciano", color = Color3.fromRGB(0, 255, 255)},
    {name = "Laranja", color = Color3.fromRGB(255, 165, 0)},
    {name = "Branco", color = Color3.fromRGB(255, 255, 255)}
}
local CurrentColorIndex = 1

ColorPickerButton.MouseButton1Click:Connect(function()
    if not Config.MainEnabled then return end
    
    CurrentColorIndex = CurrentColorIndex + 1
    if CurrentColorIndex > #ColorPresets then
        CurrentColorIndex = 1
    end
    
    local selectedColor = ColorPresets[CurrentColorIndex]
    Config.ESP.BoxColor = selectedColor.color
    Config.ESP.TracerColor = selectedColor.color
    
    ColorPickerButton.Status.Text = selectedColor.name
    ColorPickerButton.Status.BackgroundColor3 = selectedColor.color
    
    for _, esp in pairs(ESPObjects) do
        esp.Box.Color = selectedColor.color
        esp.TracerLine.Color = selectedColor.color
    end
end)

RefreshESPButton.MouseButton1Click:Connect(function()
    if not Config.MainEnabled then return end
    
    for player in pairs(ESPObjects) do
        RemoveESP(player)
    end
    
    if Config.ESP.Enabled then
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer then
                CreateESP(player)
            end
        end
        RefreshESPButton.Status.Text = "✓"
        RefreshESPButton.Status.BackgroundColor3 = Color3.fromRGB(40, 167, 69)
        print("🔄 ESP Atualizado!")
        
        wait(1)
        RefreshESPButton.Status.Text = "READY"
        RefreshESPButton.Status.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
    end
end)

AimbotButton.MouseButton1Click:Connect(function()
    if not Config.MainEnabled then return end
    
    Config.Aimbot.Enabled = not Config.Aimbot.Enabled
    
    if Config.Aimbot.Enabled then
        AimbotButton.Status.Text = "ON"
        AimbotButton.Status.BackgroundColor3 = Color3.fromRGB(40, 167, 69)
        print("🎯 Aimbot Ativado!")
    else
        AimbotButton.Status.Text = "OFF"
        AimbotButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        print("🎯 Aimbot Desativado!")
    end
end)

local AimbotRanges = {100, 250, 500, 750, 1000, 1500, 2000}
local CurrentAimbotRangeIndex = 3

AimbotRangeButton.MouseButton1Click:Connect(function()
    if not Config.MainEnabled then return end
    
    CurrentAimbotRangeIndex = CurrentAimbotRangeIndex + 1
    if CurrentAimbotRangeIndex > #AimbotRanges then
        CurrentAimbotRangeIndex = 1
    end
    
    Config.Aimbot.MaxDistance = AimbotRanges[CurrentAimbotRangeIndex]
    
    for _, child in pairs(AimbotRangeButton:GetChildren()) do
        if child:IsA("TextLabel") and child.Name ~= "Status" then
            child.Text = "Aim Range: " .. Config.Aimbot.MaxDistance
        end
    end
    
    AimbotRangeButton.Status.Text = tostring(Config.Aimbot.MaxDistance)
    AimbotRangeButton.Status.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
    print("🎯 Aimbot Range: " .. Config.Aimbot.MaxDistance .. " studs")
end)

FOVButton.MouseButton1Click:Connect(function()
    if not Config.MainEnabled then return end
    
    Config.Aimbot.FOVCircle = not Config.Aimbot.FOVCircle
    
    if Config.Aimbot.FOVCircle then
        FOVButton.Status.Text = "ON"
        FOVButton.Status.BackgroundColor3 = Color3.fromRGB(40, 167, 69)
        print("⭕ FOV Circle Ativado!")
    else
        FOVButton.Status.Text = "OFF"
        FOVButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        if FOVCircle then FOVCircle.Visible = false end
        print("⭕ FOV Circle Desativado!")
    end
end)

local FOVSizes = {50, 100, 150, 200, 250, 300, 400}
local CurrentFOVIndex = 3

FOVSizeButton.MouseButton1Click:Connect(function()
    if not Config.MainEnabled then return end
    
    CurrentFOVIndex = CurrentFOVIndex + 1
    if CurrentFOVIndex > #FOVSizes then
        CurrentFOVIndex = 1
    end
    
    Config.Aimbot.FOVSize = FOVSizes[CurrentFOVIndex]
    if FOVCircle then FOVCircle.Radius = Config.Aimbot.FOVSize end
    
    for _, child in pairs(FOVSizeButton:GetChildren()) do
        if child:IsA("TextLabel") and child.Name ~= "Status" then
            child.Text = "FOV Size: " .. Config.Aimbot.FOVSize
        end
    end
    
    FOVSizeButton.Status.Text = tostring(Config.Aimbot.FOVSize)
    FOVSizeButton.Status.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
    print("📐 FOV Size: " .. Config.Aimbot.FOVSize)
end)

local FOVColorPresets = {
    {name = "Branco", color = Color3.fromRGB(255, 255, 255)},
    {name = "Vermelho", color = Color3.fromRGB(255, 0, 0)},
    {name = "Verde", color = Color3.fromRGB(0, 255, 0)},
    {name = "Azul", color = Color3.fromRGB(0, 150, 255)},
    {name = "Amarelo", color = Color3.fromRGB(255, 255, 0)},
    {name = "Ciano", color = Color3.fromRGB(0, 255, 255)},
    {name = "Roxo", color = Color3.fromRGB(255, 0, 255)},
    {name = "Laranja", color = Color3.fromRGB(255, 165, 0)}
}
local CurrentFOVColorIndex = 1

FOVColorButton.MouseButton1Click:Connect(function()
    if not Config.MainEnabled then return end
    
    CurrentFOVColorIndex = CurrentFOVColorIndex + 1
    if CurrentFOVColorIndex > #FOVColorPresets then
        CurrentFOVColorIndex = 1
    end
    
    local selectedColor = FOVColorPresets[CurrentFOVColorIndex]
    Config.Aimbot.FOVColor = selectedColor.color
    if FOVCircle then FOVCircle.Color = selectedColor.color end
    
    FOVColorButton.Status.Text = selectedColor.name
    FOVColorButton.Status.BackgroundColor3 = selectedColor.color
end)

NoClipButton.MouseButton1Click:Connect(function()
    if not Config.MainEnabled then return end
    
    Config.NoClip.Enabled = not Config.NoClip.Enabled
    
    if Config.NoClip.Enabled then
        NoClipButton.Status.Text = "ON"
        NoClipButton.Status.BackgroundColor3 = Color3.fromRGB(40, 167, 69)
        print("👻 NoClip Ativado!")
    else
        NoClipButton.Status.Text = "OFF"
        NoClipButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        print("👻 NoClip Desativado!")
    end
    
    ToggleNoClip()
end)

SpeedButton.MouseButton1Click:Connect(function()
    if not Config.MainEnabled then return end
    
    Config.Speed.Enabled = not Config.Speed.Enabled
    
    if Config.Speed.Enabled then
        SpeedButton.Status.Text = "ON"
        SpeedButton.Status.BackgroundColor3 = Color3.fromRGB(40, 167, 69)
        print("⚡ Speed Boost Ativado!")
    else
        SpeedButton.Status.Text = "OFF"
        SpeedButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        print("⚡ Speed Boost Desativado!")
    end
    
    ToggleSpeed()
end)

JumpButton.MouseButton1Click:Connect(function()
    if not Config.MainEnabled then return end
    
    Config.InfiniteJump.Enabled = not Config.InfiniteJump.Enabled
    
    if Config.InfiniteJump.Enabled then
        JumpButton.Status.Text = "ON"
        JumpButton.Status.BackgroundColor3 = Color3.fromRGB(40, 167, 69)
        print("🚀 Infinite Jump Ativado!")
    else
        JumpButton.Status.Text = "OFF"
        JumpButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        print("🚀 Infinite Jump Desativado!")
    end
    
    ToggleInfiniteJump()
end)

FlightButton.MouseButton1Click:Connect(function()
    if not Config.MainEnabled then return end
    
    Config.Flight.Enabled = not Config.Flight.Enabled
    
    if Config.Flight.Enabled then
        FlightButton.Status.Text = "ON"
        FlightButton.Status.BackgroundColor3 = Color3.fromRGB(40, 167, 69)
        print("✈️ Fly Mode Ativado!")
        spawn(StartFlying)
    else
        FlightButton.Status.Text = "OFF"
        FlightButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        print("✈️ Fly Mode Desativado!")
        StopFlying()
    end
end)

FullBrightButton.MouseButton1Click:Connect(function()
    if not Config.MainEnabled then return end
    
    Config.FullBright.Enabled = not Config.FullBright.Enabled
    
    if Config.FullBright.Enabled then
        FullBrightButton.Status.Text = "ON"
        FullBrightButton.Status.BackgroundColor3 = Color3.fromRGB(40, 167, 69)
        print("💡 Full Bright Ativado!")
    else
        FullBrightButton.Status.Text = "OFF"
        FullBrightButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        print("💡 Full Bright Desativado!")
    end
    
    ToggleFullBright()
end)

AntiAFKButton.MouseButton1Click:Connect(function()
    if not Config.MainEnabled then return end
    
    Config.AntiAFK.Enabled = not Config.AntiAFK.Enabled
    
    if Config.AntiAFK.Enabled then
        AntiAFKButton.Status.Text = "ON"
        AntiAFKButton.Status.BackgroundColor3 = Color3.fromRGB(40, 167, 69)
        print("⏰ Anti AFK Ativado!")
    else
        AntiAFKButton.Status.Text = "OFF"
        AntiAFKButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        print("⏰ Anti AFK Desativado!")
    end
    
    ToggleAntiAFK()
end)

AutoFarmButton.MouseButton1Click:Connect(function()
    if not Config.MainEnabled then return end
    
    Config.AutoFarm.Enabled = not Config.AutoFarm.Enabled
    
    if Config.AutoFarm.Enabled then
        AutoFarmButton.Status.Text = "ON"
        AutoFarmButton.Status.BackgroundColor3 = Color3.fromRGB(40, 167, 69)
        print("🌾 Auto Farm Ativado!")
    else
        AutoFarmButton.Status.Text = "OFF"
        AutoFarmButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        print("🌾 Auto Farm Desativado!")
    end
    
    ToggleAutoFarm()
end)

local TeleportMenuOpen = false
local TeleportMenu

TeleportButton.MouseButton1Click:Connect(function()
    if not Config.MainEnabled then return end
    
    TeleportMenuOpen = not TeleportMenuOpen
    
    if TeleportMenuOpen then
        TeleportButton.Status.Text = "OPEN"
        TeleportButton.Status.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
        
        if not TeleportMenu then
            TeleportMenu = Instance.new("Frame")
            TeleportMenu.Name = "TeleportMenu"
            TeleportMenu.Size = UDim2.new(0, 300, 0, 400)
            TeleportMenu.Position = UDim2.new(0.5, 160, 0.5, -200)
            TeleportMenu.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
            TeleportMenu.BorderSizePixel = 0
            TeleportMenu.Parent = ScreenGui
            
            local MenuCorner = Instance.new("UICorner")
            MenuCorner.CornerRadius = UDim.new(0, 12)
            MenuCorner.Parent = TeleportMenu
            
            local MenuHeader = Instance.new("Frame")
            MenuHeader.Size = UDim2.new(1, 0, 0, 50)
            MenuHeader.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
            MenuHeader.BorderSizePixel = 0
            MenuHeader.Parent = TeleportMenu
            
            local MenuHeaderCorner = Instance.new("UICorner")
            MenuHeaderCorner.CornerRadius = UDim.new(0, 12)
            MenuHeaderCorner.Parent = MenuHeader
            
            local MenuHeaderFix = Instance.new("Frame")
            MenuHeaderFix.Size = UDim2.new(1, 0, 0, 12)
            MenuHeaderFix.Position = UDim2.new(0, 0, 1, -12)
            MenuHeaderFix.BackgroundColor3 = Color3.fromRGB(138, 43, 226)
            MenuHeaderFix.BorderSizePixel = 0
            MenuHeaderFix.Parent = MenuHeader
            
            local MenuTitle = Instance.new("TextLabel")
            MenuTitle.Size = UDim2.new(1, -60, 1, 0)
            MenuTitle.Position = UDim2.new(0, 15, 0, 0)
            MenuTitle.BackgroundTransparency = 1
            MenuTitle.Text = "📍 TELEPORT TO PLAYER"
            MenuTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
            MenuTitle.TextSize = 16
            MenuTitle.Font = Enum.Font.GothamBold
            MenuTitle.TextXAlignment = Enum.TextXAlignment.Left
            MenuTitle.Parent = MenuHeader
            
            local CloseMenuButton = Instance.new("TextButton")
            CloseMenuButton.Size = UDim2.new(0, 35, 0, 35)
            CloseMenuButton.Position = UDim2.new(1, -45, 0.5, -17.5)
            CloseMenuButton.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
            CloseMenuButton.Text = "X"
            CloseMenuButton.TextColor3 = Color3.fromRGB(255, 255, 255)
            CloseMenuButton.TextSize = 16
            CloseMenuButton.Font = Enum.Font.GothamBold
            CloseMenuButton.BorderSizePixel = 0
            CloseMenuButton.Parent = MenuHeader
            
            local CloseMenuCorner = Instance.new("UICorner")
            CloseMenuCorner.CornerRadius = UDim.new(0, 8)
            CloseMenuCorner.Parent = CloseMenuButton
            
            CloseMenuButton.MouseButton1Click:Connect(function()
                TeleportMenu.Visible = false
                TeleportMenuOpen = false
                TeleportButton.Status.Text = "OFF"
                TeleportButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
            end)
            
            local PlayerList = Instance.new("ScrollingFrame")
            PlayerList.Size = UDim2.new(1, -20, 1, -70)
            PlayerList.Position = UDim2.new(0, 10, 0, 60)
            PlayerList.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
            PlayerList.BorderSizePixel = 0
            PlayerList.ScrollBarThickness = 6
            PlayerList.ScrollBarImageColor3 = Color3.fromRGB(138, 43, 226)
            PlayerList.Parent = TeleportMenu
            
            local PlayerListCorner = Instance.new("UICorner")
            PlayerListCorner.CornerRadius = UDim.new(0, 8)
            PlayerListCorner.Parent = PlayerList
            
            local PlayerListLayout = Instance.new("UIListLayout")
            PlayerListLayout.Padding = UDim.new(0, 5)
            PlayerListLayout.SortOrder = Enum.SortOrder.Name
            PlayerListLayout.Parent = PlayerList
            
            local function UpdatePlayerList()
                for _, child in pairs(PlayerList:GetChildren()) do
                    if child:IsA("TextButton") then
                        child:Destroy()
                    end
                end
                
                for _, player in pairs(Players:GetPlayers()) do
                    if player ~= LocalPlayer then
                        local PlayerButton = Instance.new("TextButton")
                        PlayerButton.Size = UDim2.new(1, -10, 0, 45)
                        PlayerButton.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
                        PlayerButton.Text = player.Name
                        PlayerButton.TextColor3 = Color3.fromRGB(255, 255, 255)
                        PlayerButton.TextSize = 14
                        PlayerButton.Font = Enum.Font.Gotham
                        PlayerButton.BorderSizePixel = 0
                        PlayerButton.Parent = PlayerList
                        
                        local PlayerButtonCorner = Instance.new("UICorner")
                        PlayerButtonCorner.CornerRadius = UDim.new(0, 8)
                        PlayerButtonCorner.Parent = PlayerButton
                        
                        PlayerButton.MouseButton1Click:Connect(function()
                            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                                if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                                    LocalPlayer.Character.HumanoidRootPart.CFrame = player.Character.HumanoidRootPart.CFrame
                                    print("📍 Teleportado para " .. player.Name)
                                    
                                    PlayerButton.BackgroundColor3 = Color3.fromRGB(40, 167, 69)
                                    wait(0.3)
                                    PlayerButton.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
                                else
                                    print("❌ Personagem do jogador não encontrado!")
                                end
                            else
                                print("❌ Seu personagem não foi encontrado!")
                            end
                        end)
                    end
                end
                
                PlayerList.CanvasSize = UDim2.new(0, 0, 0, PlayerListLayout.AbsoluteContentSize.Y + 10)
            end
            
            UpdatePlayerList()
            
            Players.PlayerAdded:Connect(UpdatePlayerList)
            Players.PlayerRemoving:Connect(UpdatePlayerList)
        end
        
        TeleportMenu.Visible = true
    else
        TeleportButton.Status.Text = "OFF"
        TeleportButton.Status.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
        if TeleportMenu then
            TeleportMenu.Visible = false
        end
    end
end)

Players.PlayerAdded:Connect(function(player)
    if Config.ESP.Enabled then
        CreateESP(player)
    end
end)

Players.PlayerRemoving:Connect(function(player)
    RemoveESP(player)
end)

RunService.RenderStepped:Connect(function()
    pcall(function()
        if Config.ESP.Enabled then
            UpdateESP()
        end
        
        if Config.NoClip.Enabled then
            ToggleNoClip()
        end
        
        if Config.Aimbot.Enabled then
            AimbotLoop()
        end
        
        if Config.Aimbot.FOVCircle and FOVCircle then
            local camera = workspace.CurrentCamera
            local screenCenter = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)
            FOVCircle.Position = screenCenter
            FOVCircle.Radius = Config.Aimbot.FOVSize
            FOVCircle.Color = Config.Aimbot.FOVColor
            FOVCircle.Visible = true
        elseif FOVCircle then
            FOVCircle.Visible = false
        end
    end)
end)

local dragging
local dragInput
local dragStart
local startPos

local function update(input)
    local delta = input.Position - dragStart
    MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
end

Header.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
        
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

Header.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        update(input)
    end
end)

print("========================================")
print("🎮 ROBLOX EXPLOIT PANEL LOADED!")
print("========================================")
print("✅ Painel carregado com sucesso!")
print("⚡ Clique no botão Power para ativar")
print("========================================")
if DrawingSupported then
    print("✅ Drawing API: SUPORTADA")
else
    print("⚠️ Drawing API: NÃO SUPORTADA")
end
print("========================================")
print("📌 Features:")
print("   👁️ ESP Vision + Range (100-5000)")
print("   🎯 Aimbot + Range (100-2000)")
print("   👻 NoClip")
print("   ⚡ Speed Boost")
print("   🚀 Infinite Jump")
print("   ✈️ Fly Mode")
print("   💡 Full Bright")
print("   ⏰ Anti AFK")
print("   🌾 Auto Farm")
print("   📍 Teleport to Player")
print("========================================")
print("🎮 Bom jogo!")
