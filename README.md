repeat task.wait() until game:IsLoaded()
task.wait(1)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace = game:GetService("Workspace")
local Camera = Workspace.CurrentCamera

local LocalPlayer = Players.LocalPlayer

local Config = {
    AimbotEnabled = true,
    AimbotKey = Enum.UserInputType.MouseButton2,
    SmoothAim = true,
    Smoothness = 8,
    AimPart = "Head",
    FOVSize = 150,
    FOVVisible = true,
    TeamCheck = true,
    WallCheck = false,
    Prediction = true,
    PredictionAmount = 0.165,
    AimbotMaxDistance = 500,
    
    ESPEnabled = true,
    ESPBoxes = true,
    ESPTracers = true,
    ESPNames = true,
    ESPDistance = true,
    ESPHealth = true,
    ESPHealthBar = true,
    BoxColor = Color3.fromRGB(255, 0, 0),
    BoxThickness = 2,
    TracerOrigin = "Bottom",
    ESPMaxDistance = 1000,
}

local State = {
    AimbotActive = false,
    CurrentTarget = nil,
    ESPDrawings = {},
    FOVCircle = nil,
    MouseDown = false,
}

local function IsAlive(player)
    if not player or not player.Character then return false end
    local hum = player.Character:FindFirstChildOfClass("Humanoid")
    return hum and hum.Health > 0
end

local function IsTeammate(player)
    if not Config.TeamCheck then return false end
    return player.Team == LocalPlayer.Team and player.Team ~= nil
end

local function GetDistance(pos1, pos2)
    return (pos1 - pos2).Magnitude
end

local function IsVisible(targetPart)
    if not Config.WallCheck then return true end
    
    local char = LocalPlayer.Character
    if not char then return false end
    
    local origin = Camera.CFrame.Position
    local direction = (targetPart.Position - origin).Unit * 500
    
    local rayParams = RaycastParams.new()
    rayParams.FilterDescendantsInstances = {char, Camera}
    rayParams.FilterType = Enum.RaycastFilterType.Exclude
    rayParams.IgnoreWater = true
    
    local result = Workspace:Raycast(origin, direction, rayParams)
    
    if result then
        return result.Instance:IsDescendantOf(targetPart.Parent)
    end
    
    return true
end

local function WorldToScreen(pos)
    local screenPos, onScreen = Camera:WorldToViewportPoint(pos)
    return Vector2.new(screenPos.X, screenPos.Y), onScreen, screenPos.Z
end

local FOVCircle = Drawing.new("Circle")
FOVCircle.Thickness = 2
FOVCircle.NumSides = 100
FOVCircle.Radius = Config.FOVSize
FOVCircle.Filled = false
FOVCircle.Transparency = 1
FOVCircle.Color = Color3.new(1, 1, 1)
FOVCircle.Visible = Config.FOVVisible
FOVCircle.ZIndex = 999

State.FOVCircle = FOVCircle

local function GetClosestPlayerToMouse()
    local closest = nil
    local shortestDistance = math.huge
    local mousePos = UserInputService:GetMouseLocation()
    
    if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        return nil
    end
    
    local myPos = LocalPlayer.Character.HumanoidRootPart.Position
    
    for _, player in pairs(Players:GetPlayers()) do
        if player == LocalPlayer then continue end
        if not IsAlive(player) then continue end
        if IsTeammate(player) then continue end
        
        local char = player.Character
        if not char then continue end
        
        local targetPart = char:FindFirstChild(Config.AimPart)
        if not targetPart then continue end
        
        local worldDistance = GetDistance(myPos, targetPart.Position)
        if worldDistance > Config.AimbotMaxDistance then continue end
        
        local screenPos, onScreen = WorldToScreen(targetPart.Position)
        if not onScreen then continue end
        
        if Config.WallCheck and not IsVisible(targetPart) then continue end
        
        local distance = (Vector2.new(screenPos.X, screenPos.Y) - mousePos).Magnitude
        
        if distance < Config.FOVSize and distance < shortestDistance then
            closest = player
            shortestDistance = distance
        end
    end
    
    return closest
end

local function AimbotLogic()
    if not Config.AimbotEnabled then return end
    if not State.MouseDown then 
        State.CurrentTarget = nil
        return 
    end
    
    local target = GetClosestPlayerToMouse()
    
    if target and IsAlive(target) then
        State.CurrentTarget = target
        
        local char = target.Character
        local targetPart = char:FindFirstChild(Config.AimPart)
        
        if targetPart then
            local targetPos = targetPart.Position
            
            if Config.Prediction then
                local velocity = targetPart.AssemblyLinearVelocity
                if velocity then
                    targetPos = targetPos + (velocity * Config.PredictionAmount)
                end
            end
            
            local currentCFrame = Camera.CFrame
            local targetCFrame = CFrame.new(currentCFrame.Position, targetPos)
            
            if Config.SmoothAim then
                local smoothFactor = 1 / Config.Smoothness
                Camera.CFrame = currentCFrame:Lerp(targetCFrame, smoothFactor)
            else
                Camera.CFrame = targetCFrame
            end
        end
    else
        State.CurrentTarget = nil
    end
end

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    
    if input.UserInputType == Config.AimbotKey then
        State.MouseDown = true
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Config.AimbotKey then
        State.MouseDown = false
        State.CurrentTarget = nil
    end
end)

local function CreateESPBox(player)
    if player == LocalPlayer then return end
    if State.ESPDrawings[player] then return end
    
    local drawings = {
        TopLine = Drawing.new("Line"),
        BottomLine = Drawing.new("Line"),
        LeftLine = Drawing.new("Line"),
        RightLine = Drawing.new("Line"),
        Tracer = Drawing.new("Line"),
        NameText = Drawing.new("Text"),
        DistanceText = Drawing.new("Text"),
        HealthText = Drawing.new("Text"),
        HealthBarOutline = Drawing.new("Line"),
        HealthBarInside = Drawing.new("Line"),
    }
    
    for _, line in pairs({drawings.TopLine, drawings.BottomLine, drawings.LeftLine, drawings.RightLine}) do
        line.Thickness = Config.BoxThickness
        line.Color = Config.BoxColor
        line.Visible = false
        line.ZIndex = 2
    end
    
    drawings.Tracer.Thickness = 1
    drawings.Tracer.Color = Config.BoxColor
    drawings.Tracer.Visible = false
    drawings.Tracer.ZIndex = 1
    
    for _, text in pairs({drawings.NameText, drawings.DistanceText, drawings.HealthText}) do
        text.Size = 13
        text.Center = true
        text.Outline = true
        text.Color = Color3.new(1, 1, 1)
        text.Visible = false
        text.ZIndex = 3
    end
    
    drawings.NameText.Size = 14
    
    drawings.HealthBarOutline.Thickness = 3
    drawings.HealthBarOutline.Color = Color3.new(0, 0, 0)
    drawings.HealthBarOutline.Visible = false
    drawings.HealthBarOutline.ZIndex = 2
    
    drawings.HealthBarInside.Thickness = 2
    drawings.HealthBarInside.Color = Color3.new(0, 1, 0)
    drawings.HealthBarInside.Visible = false
    drawings.HealthBarInside.ZIndex = 3
    
    State.ESPDrawings[player] = drawings
end

local function UpdateESP()
    if not Config.ESPEnabled then 
        for _, drawings in pairs(State.ESPDrawings) do
            for _, drawing in pairs(drawings) do
                drawing.Visible = false
            end
        end
        return 
    end
    
    if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        return
    end
    
    local myPos = LocalPlayer.Character.HumanoidRootPart.Position
    
    for player, drawings in pairs(State.ESPDrawings) do
        if not IsAlive(player) or IsTeammate(player) then
            for _, drawing in pairs(drawings) do
                drawing.Visible = false
            end
            continue
        end
        
        local char = player.Character
        local hrp = char:FindFirstChild("HumanoidRootPart")
        local head = char:FindFirstChild("Head")
        local hum = char:FindFirstChildOfClass("Humanoid")
        
        if not hrp or not head or not hum then 
            for _, drawing in pairs(drawings) do
                drawing.Visible = false
            end
            continue 
        end
        
        local distance = GetDistance(myPos, hrp.Position)
        
        if distance > Config.ESPMaxDistance then
            for _, drawing in pairs(drawings) do
                drawing.Visible = false
            end
            continue
        end
        
        local rootPos = hrp.Position
        local headPos = head.Position + Vector3.new(0, 0.5, 0)
        local legPos = rootPos - Vector3.new(0, 3, 0)
        
        local topScreen, topOnScreen = WorldToScreen(headPos)
        local bottomScreen, bottomOnScreen = WorldToScreen(legPos)
        
        if not topOnScreen or not bottomOnScreen then
            for _, drawing in pairs(drawings) do
                drawing.Visible = false
            end
            continue
        end
        
        local height = (bottomScreen - topScreen).Magnitude
        local width = height * 0.5
        
        local healthPercent = math.clamp(hum.Health / hum.MaxHealth, 0, 1)
        local healthColor = Color3.new(1 - healthPercent, healthPercent, 0)
        
        if Config.ESPBoxes then
            local topLeft = Vector2.new(topScreen.X - width / 2, topScreen.Y)
            local topRight = Vector2.new(topScreen.X + width / 2, topScreen.Y)
            local bottomLeft = Vector2.new(bottomScreen.X - width / 2, bottomScreen.Y)
            local bottomRight = Vector2.new(bottomScreen.X + width / 2, bottomScreen.Y)
            
            drawings.TopLine.From = topLeft
            drawings.TopLine.To = topRight
            drawings.TopLine.Color = Config.BoxColor
            drawings.TopLine.Visible = true
            
            drawings.BottomLine.From = bottomLeft
            drawings.BottomLine.To = bottomRight
            drawings.BottomLine.Color = Config.BoxColor
            drawings.BottomLine.Visible = true
            
            drawings.LeftLine.From = topLeft
            drawings.LeftLine.To = bottomLeft
            drawings.LeftLine.Color = Config.BoxColor
            drawings.LeftLine.Visible = true
            
            drawings.RightLine.From = topRight
            drawings.RightLine.To = bottomRight
            drawings.RightLine.Color = Config.BoxColor
            drawings.RightLine.Visible = true
        else
            drawings.TopLine.Visible = false
            drawings.BottomLine.Visible = false
            drawings.LeftLine.Visible = false
            drawings.RightLine.Visible = false
        end
        
        if Config.ESPTracers then
            local tracerStart
            local screenSize = Camera.ViewportSize
            
            if Config.TracerOrigin == "Bottom" then
                tracerStart = Vector2.new(screenSize.X / 2, screenSize.Y)
            elseif Config.TracerOrigin == "Middle" then
                tracerStart = Vector2.new(screenSize.X / 2, screenSize.Y / 2)
            else
                tracerStart = Vector2.new(screenSize.X / 2, 0)
            end
            
            drawings.Tracer.From = tracerStart
            drawings.Tracer.To = Vector2.new(bottomScreen.X, bottomScreen.Y)
            drawings.Tracer.Color = Config.BoxColor
            drawings.Tracer.Visible = true
        else
            drawings.Tracer.Visible = false
        end
        
        if Config.ESPNames then
            drawings.NameText.Text = player.Name
            drawings.NameText.Position = Vector2.new(topScreen.X, topScreen.Y - 18)
            drawings.NameText.Visible = true
        else
            drawings.NameText.Visible = false
        end
        
        if Config.ESPDistance then
            local dist = math.floor(distance)
            drawings.DistanceText.Text = tostring(dist) .. "m"
            drawings.DistanceText.Position = Vector2.new(bottomScreen.X, bottomScreen.Y + 5)
            drawings.DistanceText.Visible = true
        else
            drawings.DistanceText.Visible = false
        end
        
        if Config.ESPHealth then
            local health = math.floor(hum.Health)
            drawings.HealthText.Text = tostring(health) .. " HP"
            drawings.HealthText.Position = Vector2.new(bottomScreen.X, bottomScreen.Y + 20)
            drawings.HealthText.Color = healthColor
            drawings.HealthText.Visible = true
        else
            drawings.HealthText.Visible = false
        end
        
        if Config.ESPHealthBar then
            local barWidth = width
            local barX = topScreen.X - barWidth / 2
            local barY = topScreen.Y - 25
            
            drawings.HealthBarOutline.From = Vector2.new(barX, barY)
            drawings.HealthBarOutline.To = Vector2.new(barX + barWidth, barY)
            drawings.HealthBarOutline.Visible = true
            
            local healthBarWidth = barWidth * healthPercent
            drawings.HealthBarInside.From = Vector2.new(barX, barY)
            drawings.HealthBarInside.To = Vector2.new(barX + healthBarWidth, barY)
            drawings.HealthBarInside.Color = healthColor
            drawings.HealthBarInside.Visible = true
        else
            drawings.HealthBarOutline.Visible = false
            drawings.HealthBarInside.Visible = false
        end
    end
end

local function InitESP()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            CreateESPBox(player)
        end
    end
    
    Players.PlayerAdded:Connect(function(player)
        task.wait(1)
        CreateESPBox(player)
    end)
    
    Players.PlayerRemoving:Connect(function(player)
        if State.ESPDrawings[player] then
            for _, drawing in pairs(State.ESPDrawings[player]) do
                pcall(function() drawing:Remove() end)
            end
            State.ESPDrawings[player] = nil
        end
    end)
end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "FPSPanelPRO"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.IgnoreGuiInset = true

pcall(function()
    ScreenGui.Parent = game:GetService("CoreGui")
end)

if not ScreenGui.Parent then
    ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
end

local OpenButton = Instance.new("TextButton")
OpenButton.Size = UDim2.new(0, 70, 0, 70)
OpenButton.Position = UDim2.new(0, 15, 0.5, -35)
OpenButton.BackgroundColor3 = Color3.fromRGB(220, 50, 80)
OpenButton.Font = Enum.Font.GothamBold
OpenButton.Text = "🎯"
OpenButton.TextSize = 35
OpenButton.TextColor3 = Color3.new(1, 1, 1)
OpenButton.BorderSizePixel = 0
OpenButton.Active = true
OpenButton.Draggable = true
OpenButton.ZIndex = 10000
OpenButton.Parent = ScreenGui

local OpenCorner = Instance.new("UICorner")
OpenCorner.CornerRadius = UDim.new(1, 0)
OpenCorner.Parent = OpenButton

local OpenStroke = Instance.new("UIStroke")
OpenStroke.Color = Color3.new(1, 1, 1)
OpenStroke.Thickness = 3
OpenStroke.Parent = OpenButton

local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 480, 0, 700)
MainFrame.Position = UDim2.new(0.5, -240, 0.5, -350)
MainFrame.BackgroundColor3 = Color3.fromRGB(18, 18, 25)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Visible = true
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 15)
MainCorner.Parent = MainFrame

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Color3.fromRGB(220, 50, 80)
MainStroke.Thickness = 3
MainStroke.Parent = MainFrame

local Header = Instance.new("Frame")
Header.Size = UDim2.new(1, 0, 0, 60)
Header.BackgroundColor3 = Color3.fromRGB(220, 50, 80)
Header.BorderSizePixel = 0
Header.Parent = MainFrame

local HeaderCorner = Instance.new("UICorner")
HeaderCorner.CornerRadius = UDim.new(0, 15)
HeaderCorner.Parent = Header

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -70, 1, 0)
Title.BackgroundTransparency = 1
Title.Font = Enum.Font.GothamBold
Title.Text = "🎯 FPS PANEL PRO"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.TextSize = 24
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Position = UDim2.new(0, 20, 0, 0)
Title.Parent = Header

local CloseBtn = Instance.new("TextButton")
CloseBtn.Size = UDim2.new(0, 45, 0, 45)
CloseBtn.Position = UDim2.new(1, -52, 0, 7.5)
CloseBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.Text = "×"
CloseBtn.TextSize = 28
CloseBtn.TextColor3 = Color3.fromRGB(220, 50, 80)
CloseBtn.BorderSizePixel = 0
CloseBtn.Parent = Header

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(1, 0)
CloseCorner.Parent = CloseBtn

CloseBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = false
end)

OpenButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
end)

local Content = Instance.new("ScrollingFrame")
Content.Size = UDim2.new(1, -20, 1, -80)
Content.Position = UDim2.new(0, 10, 0, 70)
Content.BackgroundTransparency = 1
Content.BorderSizePixel = 0
Content.ScrollBarThickness = 6
Content.CanvasSize = UDim2.new(0, 0, 0, 0)
Content.Parent = MainFrame

local yPos = 10

local function CreateSection(text)
    local Section = Instance.new("Frame")
    Section.Size = UDim2.new(1, -10, 0, 35)
    Section.Position = UDim2.new(0, 5, 0, yPos)
    Section.BackgroundColor3 = Color3.fromRGB(220, 50, 80)
    Section.BorderSizePixel = 0
    Section.Parent = Content
    
    local SectionCorner = Instance.new("UICorner")
    SectionCorner.CornerRadius = UDim.new(0, 8)
    SectionCorner.Parent = Section
    
    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, 0, 1, 0)
    Label.BackgroundTransparency = 1
    Label.Font = Enum.Font.GothamBold
    Label.Text = text
    Label.TextColor3 = Color3.new(1, 1, 1)
    Label.TextSize = 16
    Label.TextXAlignment = Enum.TextXAlignment.Center
    Label.Parent = Section
    
    yPos = yPos + 45
end

local function CreateToggle(text, default, callback)
    local Toggle = Instance.new("Frame")
    Toggle.Size = UDim2.new(1, -10, 0, 50)
    Toggle.Position = UDim2.new(0, 5, 0, yPos)
    Toggle.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
    Toggle.BorderSizePixel = 0
    Toggle.Parent = Content
    
    local ToggleCorner = Instance.new("UICorner")
    ToggleCorner.CornerRadius = UDim.new(0, 10)
    ToggleCorner.Parent = Toggle
    
    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, -75, 1, 0)
    Label.BackgroundTransparency = 1
    Label.Font = Enum.Font.GothamBold
    Label.Text = text
    Label.TextColor3 = Color3.new(1, 1, 1)
    Label.TextSize = 15
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Position = UDim2.new(0, 15, 0, 0)
    Label.Parent = Toggle
    
    local Button = Instance.new("TextButton")
    Button.Size = UDim2.new(0, 55, 0, 32)
    Button.Position = UDim2.new(1, -65, 0.5, -16)
    Button.BackgroundColor3 = default and Color3.fromRGB(50, 200, 100) or Color3.fromRGB(220, 50, 80)
    Button.Font = Enum.Font.GothamBold
    Button.Text = default and "ON" or "OFF"
    Button.TextColor3 = Color3.new(1, 1, 1)
    Button.TextSize = 14
    Button.BorderSizePixel = 0
    Button.Parent = Toggle
    
    local ButtonCorner = Instance.new("UICorner")
    ButtonCorner.CornerRadius = UDim.new(0, 8)
    ButtonCorner.Parent = Button
    
    local state = default
    
    Button.MouseButton1Click:Connect(function()
        state = not state
        Button.Text = state and "ON" or "OFF"
        Button.BackgroundColor3 = state and Color3.fromRGB(50, 200, 100) or Color3.fromRGB(220, 50, 80)
        callback(state)
    end)
    
    yPos = yPos + 60
end

local function CreateSlider(text, min, max, default, callback)
    local Slider = Instance.new("Frame")
    Slider.Size = UDim2.new(1, -10, 0, 70)
    Slider.Position = UDim2.new(0, 5, 0, yPos)
    Slider.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
    Slider.BorderSizePixel = 0
    Slider.Parent = Content
    
    local SliderCorner = Instance.new("UICorner")
    SliderCorner.CornerRadius = UDim.new(0, 10)
    SliderCorner.Parent = Slider
    
    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, -20, 0, 25)
    Label.Position = UDim2.new(0, 10, 0, 5)
    Label.BackgroundTransparency = 1
    Label.Font = Enum.Font.GothamBold
    Label.Text = text .. ": " .. default
    Label.TextColor3 = Color3.new(1, 1, 1)
    Label.TextSize = 14
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = Slider
    
    local SliderBar = Instance.new("Frame")
    SliderBar.Size = UDim2.new(1, -20, 0, 10)
    SliderBar.Position = UDim2.new(0, 10, 1, -22)
    SliderBar.BackgroundColor3 = Color3.fromRGB(50, 50, 60)
    SliderBar.BorderSizePixel = 0
    SliderBar.Parent = Slider
    
    local SliderBarCorner = Instance.new("UICorner")
    SliderBarCorner.CornerRadius = UDim.new(0, 5)
    SliderBarCorner.Parent = SliderBar
    
    local Fill = Instance.new("Frame")
    Fill.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
    Fill.BackgroundColor3 = Color3.fromRGB(220, 50, 80)
    Fill.BorderSizePixel = 0
    Fill.Parent = SliderBar
    
    local FillCorner = Instance.new("UICorner")
    FillCorner.CornerRadius = UDim.new(0, 5)
    FillCorner.Parent = Fill
    
    local Dragging = false
    
    SliderBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            Dragging = true
        end
    end)
    
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            Dragging = false
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if Dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local mousePos = UserInputService:GetMouseLocation().X
            local sliderPos = SliderBar.AbsolutePosition.X
            local sliderSize = SliderBar.AbsoluteSize.X
            
            local value = math.clamp((mousePos - sliderPos) / sliderSize, 0, 1)
            local finalValue = min + (value * (max - min))
            
            if max <= 2 then
                finalValue = math.floor(finalValue * 100) / 100
            elseif max <= 10 then
                finalValue = math.floor(finalValue * 10) / 10
            elseif max <= 100 then
                finalValue = math.floor(finalValue)
            else
                finalValue = math.floor(finalValue / 10) * 10
            end
            
            Fill.Size = UDim2.new(value, 0, 1, 0)
            Label.Text = text .. ": " .. finalValue
            
            callback(finalValue)
        end
    end)
    
    yPos = yPos + 80
end

CreateSection("🎯 AIMBOT FREE FIRE")

CreateToggle("Aimbot Ativado", true, function(state)
    Config.AimbotEnabled = state
end)

CreateToggle("Smooth Aim", true, function(state)
    Config.SmoothAim = state
end)

CreateSlider("Suavidade", 1, 20, 8, function(value)
    Config.Smoothness = value
end)

CreateSlider("Alcance Aimbot (studs)", 100, 2000, 500, function(value)
    Config.AimbotMaxDistance = value
end)

CreateSlider("Tamanho FOV", 50, 500, 150, function(value)
    Config.FOVSize = value
end)

CreateToggle("Mostrar FOV Circle", true, function(state)
    Config.FOVVisible = state
end)

CreateToggle("Team Check", true, function(state)
    Config.TeamCheck = state
end)

CreateToggle("Wall Check", false, function(state)
    Config.WallCheck = state
end)

CreateToggle("Predição", true, function(state)
    Config.Prediction = state
end)

CreateSlider("Força Predição", 0.05, 0.3, 0.165, function(value)
    Config.PredictionAmount = value
end)

CreateSection("👁️ ESP BOX 2D")

CreateToggle("ESP Ativado", true, function(state)
    Config.ESPEnabled = state
end)

CreateSlider("Alcance ESP (studs)", 100, 5000, 1000, function(value)
    Config.ESPMaxDistance = value
end)

CreateToggle("Boxes 2D", true, function(state)
    Config.ESPBoxes = state
end)

CreateToggle("Tracers", true, function(state)
    Config.ESPTracers = state
end)

CreateToggle("Nomes", true, function(state)
    Config.ESPNames = state
end)

CreateToggle("Distância", true, function(state)
    Config.ESPDistance = state
end)

CreateToggle("Vida", true, function(state)
    Config.ESPHealth = state
end)

CreateToggle("Barra de Vida", true, function(state)
    Config.ESPHealthBar = state
end)

CreateSlider("Espessura Box", 1, 4, 2, function(value)
    Config.BoxThickness = value
end)

Content.CanvasSize = UDim2.new(0, 0, 0, yPos + 10)

InitESP()

RunService.RenderStepped:Connect(function()
    local mousePos = UserInputService:GetMouseLocation()
    FOVCircle.Position = mousePos
    FOVCircle.Radius = Config.FOVSize
    FOVCircle.Visible = Config.FOVVisible and Config.AimbotEnabled
    
    if State.MouseDown and State.CurrentTarget then
        FOVCircle.Color = Color3.new(0, 1, 0)
        FOVCircle.Thickness = 3
    else
        FOVCircle.Color = Color3.new(1, 1, 1)
        FOVCircle.Thickness = 2
    end
    
    AimbotLogic()
    UpdateESP()
end)

UserInputService.InputBegan:Connect(function(input, processed)
    if processed then return end
    
    if input.KeyCode == Enum.KeyCode.Insert then
        MainFrame.Visible = not MainFrame.Visible
    end
end)

print([[
✅ FPS PANEL PRO - ALCANCE CONFIGURÁVEL

🎯 AIMBOT:
• Alcance padrão: 500 studs (ajustável 100-2000)
• Segure BOTÃO DIREITO DO MOUSE
• FOV segue o cursor

👁️ ESP:
• Alcance padrão: 1000 studs (ajustável 100-5000)
• Mostra apenas jogadores dentro do alcance

🎮 Tecla INSERT = Menu
]])
