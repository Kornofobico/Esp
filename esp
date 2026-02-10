-- Script de ESP com Salvamento JSON (Persistence)
-- Lógica original por Kipressao11 | Ajustes por Manus

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")
local RunService = game:GetService("RunService")
local TextService = game:GetService("TextService")
local HttpService = game:GetService("HttpService")

local FILE_NAME = "Kipressao_ESP_Config.json"

----------------------------------------------------------------
-- ESTADOS E CONFIGURAÇÕES PADRÃO
----------------------------------------------------------------
local Config = {
    ESP_Highlight_Enabled = false,
    ESP_Nick_Enabled = false,
    ESP_HealthBar_Enabled = false,
    Highlight_Transparency = 0.5,
    Panel_Pos = {0.5, -110, 0.5, -130},
    Minimized_Pos = {0, 10, 0.5, -25}
}

local is_sliding = false
_G.FriendColor = Color3.fromRGB(0, 0, 255)
_G.EnemyColor = Color3.fromRGB(255, 0, 0)
_G.HealthBarColor = Color3.fromRGB(0, 255, 0)
_G.UseTeamColor = true
local MINIMIZED_TRANSPARENCY = 0.5 

----------------------------------------------------------------
-- SISTEMA DE SALVAMENTO JSON
----------------------------------------------------------------
local function SaveConfig()
    local success, err = pcall(function()
        if writefile then
            writefile(FILE_NAME, HttpService:JSONEncode(Config))
        end
    end)
end

local function LoadConfig()
    local success, content = pcall(function()
        if readfile and isfile and isfile(FILE_NAME) then
            return readfile(FILE_NAME)
        end
    end)
    
    if success and content then
        local ok, decoded = pcall(function() return HttpService:JSONDecode(content) end)
        if ok then
            for k, v in pairs(decoded) do
                Config[k] = v
            end
        end
    end
end

LoadConfig() -- Carrega as configurações ao iniciar

----------------------------------------------------------------
-- NOTIFICAÇÃO INICIAL
----------------------------------------------------------------
local function showNotification()
    local screenGui = Instance.new("ScreenGui", Players.LocalPlayer:WaitForChild("PlayerGui"))
    screenGui.Name = "Kipressao11_Notification"
    local frame = Instance.new("Frame", screenGui)
    frame.Size = UDim2.new(0, 250, 0, 50)
    frame.Position = UDim2.new(0.5, -125, 0.8, 0)
    frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    frame.BackgroundTransparency = 1
    Instance.new("UICorner", frame)
    local textLabel = Instance.new("TextLabel", frame)
    textLabel.Size = UDim2.new(1, 0, 1, 0)
    textLabel.BackgroundTransparency = 1
    textLabel.Text = "Script feito por Kipressao11"
    textLabel.TextColor3 = Color3.new(1, 1, 1)
    textLabel.TextScaled = true
    textLabel.Font = Enum.Font.SourceSansBold
    textLabel.TextTransparency = 1
    task.spawn(function()
        for i = 1, 10 do frame.BackgroundTransparency = 1 - i * 0.08; textLabel.TextTransparency = 1 - i * 0.1; task.wait(0.05) end
        wait(2)
        for i = 1, 10 do frame.BackgroundTransparency = i * 0.1; textLabel.TextTransparency = i * 0.1; task.wait(0.05) end
        screenGui:Destroy()
    end)
end
showNotification()

----------------------------------------------------------------
-- LÓGICA DE ESP
----------------------------------------------------------------
local Holder = Instance.new("Folder", CoreGui)
Holder.Name = "ESP_Holder_JSON"

local function determineNameText(v, character)
    local humanoid = character:FindFirstChildWhichIsA("Humanoid")
    if humanoid and humanoid.DisplayName ~= "" then return humanoid.DisplayName end
    if v.DisplayName ~= "" then return v.DisplayName end
    for _, child in pairs(character:GetDescendants()) do
        if child:IsA("TextLabel") and child.Text ~= "" then return child.Text end
    end
    return v.Name
end

local function UpdateESPState(v)
    local vHolder = Holder:FindFirstChild(v.Name)
    if not vHolder then return end
    local tag = vHolder:FindFirstChild(v.Name .. "NameTag")
    if tag then 
        tag.Enabled = Config.ESP_Nick_Enabled or Config.ESP_HealthBar_Enabled
        local nickLabel = tag:FindFirstChild("Nick", true)
        if nickLabel then nickLabel.Visible = Config.ESP_Nick_Enabled end
        local hBG = tag:FindFirstChild("HealthBG", true)
        if hBG then 
            hBG.Visible = Config.ESP_HealthBar_Enabled 
            hBG.Position = Config.ESP_Nick_Enabled and UDim2.new(0.5, 0, 0, 16) or UDim2.new(0.5, 0, 0, 0)
        end
    end
    if v.Character then
        local highlight = v.Character:FindFirstChild("KipressaoHighlight")
        if highlight then
            highlight.Enabled = Config.ESP_Highlight_Enabled
            highlight.FillTransparency = Config.Highlight_Transparency
            highlight.OutlineTransparency = Config.Highlight_Transparency
        end
        pcall(function() v.Character.Humanoid.DisplayDistanceType = Enum.HumanoidDisplayDistanceType.None end)
    end
end

local function LoadCharacter(v)
    repeat task.wait() until v.Character ~= nil and v.Character:FindFirstChild("Head") and v.Character:FindFirstChildWhichIsA("Humanoid")
    local character = v.Character
    local humanoid = character:FindFirstChildWhichIsA("Humanoid")
    local vHolder = Holder:FindFirstChild(v.Name) or Instance.new("Folder", Holder)
    vHolder.Name = v.Name
    vHolder:ClearAllChildren()

    local highlight = Instance.new("Highlight")
    highlight.Name = "KipressaoHighlight"
    highlight.Adornee = character
    highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    highlight.Enabled = Config.ESP_Highlight_Enabled
    highlight.FillTransparency = Config.Highlight_Transparency
    highlight.OutlineTransparency = Config.Highlight_Transparency
    highlight.Parent = character

    local t = Instance.new("BillboardGui")
    t.Name = v.Name .. "NameTag"
    t.Size = UDim2.new(0, 200, 0, 60)
    t.AlwaysOnTop = true
    t.StudsOffset = Vector3.new(0, 3, 0)
    t.Enabled = Config.ESP_Nick_Enabled or Config.ESP_HealthBar_Enabled
    t.Adornee = character.Head
    t.Parent = vHolder
    
    local container = Instance.new("Frame", t)
    container.Size = UDim2.new(1, 0, 1, 0)
    container.BackgroundTransparency = 1

    local tagLabel = Instance.new("TextLabel", container)
    tagLabel.Name = "Nick"
    tagLabel.BackgroundTransparency = 1
    tagLabel.Size = UDim2.new(1, 0, 0, 14)
    tagLabel.Font = Enum.Font.SourceSansBold
    tagLabel.TextSize = 14
    tagLabel.TextStrokeTransparency = 0.5
    tagLabel.Visible = Config.ESP_Nick_Enabled
    
    local healthBG = Instance.new("Frame", container)
    healthBG.Name = "HealthBG"
    healthBG.BackgroundColor3 = Color3.fromRGB(50, 0, 0)
    healthBG.BorderSizePixel = 0
    healthBG.Size = UDim2.new(0, 50, 0, 3)
    healthBG.AnchorPoint = Vector2.new(0.5, 0)
    healthBG.Visible = Config.ESP_HealthBar_Enabled
    healthBG.Position = Config.ESP_Nick_Enabled and UDim2.new(0.5, 0, 0, 16) or UDim2.new(0.5, 0, 0, 0)
    
    local healthFill = Instance.new("Frame", healthBG)
    healthFill.Name = "HealthFill"
    healthFill.BackgroundColor3 = _G.HealthBarColor
    healthFill.BorderSizePixel = 0
    healthFill.Size = UDim2.new(1, 0, 1, 0)

    local function refresh()
        if not character.Parent or not humanoid then return end
        local name = determineNameText(v, character)
        tagLabel.Text = name
        local textSize = TextService:GetTextSize(name, tagLabel.TextSize, tagLabel.Font, Vector2.new(1000, 1000))
        healthBG.Size = UDim2.new(0, textSize.X, 0, 3)
        local healthPercent = math.clamp(humanoid.Health / humanoid.MaxHealth, 0, 1)
        healthFill.Size = UDim2.new(healthPercent, 0, 1, 0)
        healthFill.BackgroundColor3 = Color3.fromRGB(255 * (1 - healthPercent), 255 * healthPercent, 0)
        local color = v.TeamColor.Color
        highlight.FillColor = color; highlight.OutlineColor = color; tagLabel.TextColor3 = color
        pcall(function() character.Humanoid.DisplayDistanceType = Enum.HumanoidDisplayDistanceType.None end)
    end
    refresh()
    v:GetPropertyChangedSignal("TeamColor"):Connect(refresh)
    humanoid.HealthChanged:Connect(refresh)
    humanoid:GetPropertyChangedSignal("MaxHealth"):Connect(refresh)
    task.spawn(function()
        while character.Parent do pcall(function() character.Humanoid.DisplayDistanceType = Enum.HumanoidDisplayDistanceType.None end); task.wait(1) end
    end)
end

local function LoadPlayer(v)
    v.CharacterAdded:Connect(function() LoadCharacter(v) end)
    if v.Character then task.spawn(function() LoadCharacter(v) end) end
end
for _, v in pairs(Players:GetPlayers()) do if v ~= Players.LocalPlayer then LoadPlayer(v) end end
Players.PlayerAdded:Connect(LoadPlayer)

----------------------------------------------------------------
-- SISTEMA DE ARRASTO
----------------------------------------------------------------
local function makeDraggable(frame, configKey)
    local dragging, dragInput, dragStart, startPos
    frame.InputBegan:Connect(function(input)
        if (input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch) and not is_sliding then
            dragging = true; dragStart = input.Position; startPos = frame.Position
            input.Changed:Connect(function() if input.UserInputState == Enum.UserInputState.End then 
                dragging = false 
                -- Salva a nova posição
                Config[configKey] = {frame.Position.X.Scale, frame.Position.X.Offset, frame.Position.Y.Scale, frame.Position.Y.Offset}
                SaveConfig()
            end end)
        end
    end)
    frame.InputChanged:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then dragInput = input end end)
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging and not is_sliding then
            local delta = input.Position - dragStart
            frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end

----------------------------------------------------------------
-- GUI
----------------------------------------------------------------
local ScreenGui = Instance.new("ScreenGui", CoreGui)
local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 220, 0, 260)
MainFrame.Position = UDim2.new(unpack(Config.Panel_Pos))
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30); MainFrame.BorderSizePixel = 0; MainFrame.Active = true
Instance.new("UICorner", MainFrame)

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 40); Title.BackgroundTransparency = 1; Title.Text = "ESP PANEL"; Title.TextColor3 = Color3.new(1, 1, 1); Title.Font = Enum.Font.SourceSansBold; Title.TextSize = 18

local MinimizeBtn = Instance.new("TextButton", MainFrame)
MinimizeBtn.Size = UDim2.new(0, 30, 0, 30); MinimizeBtn.Position = UDim2.new(1, -35, 0, 5); MinimizeBtn.Text = "-"; MinimizeBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60); MinimizeBtn.TextColor3 = Color3.new(1, 1, 1)
Instance.new("UICorner", MinimizeBtn)

local Content = Instance.new("Frame", MainFrame)
Content.Size = UDim2.new(1, -20, 1, -50); Content.Position = UDim2.new(0, 10, 0, 45); Content.BackgroundTransparency = 1
Instance.new("UIListLayout", Content).Padding = UDim.new(0, 8)

local function createToggle(text, configKey, callback)
    local btn = Instance.new("TextButton", Content)
    local state = Config[configKey]
    btn.Size = UDim2.new(1, 0, 0, 35); btn.Text = text .. ": " .. (state and "ON" or "OFF")
    btn.BackgroundColor3 = state and Color3.fromRGB(0, 160, 0) or Color3.fromRGB(160, 0, 0)
    btn.TextColor3 = Color3.new(1, 1, 1); btn.Font = Enum.Font.SourceSansSemibold; btn.TextSize = 16
    Instance.new("UICorner", btn)
    
    btn.MouseButton1Click:Connect(function()
        Config[configKey] = not Config[configKey]
        local newState = Config[configKey]
        btn.Text = text .. ": " .. (newState and "ON" or "OFF")
        btn.BackgroundColor3 = newState and Color3.fromRGB(0, 160, 0) or Color3.fromRGB(160, 0, 0)
        callback(newState)
        SaveConfig()
    end)
end

createToggle("Highlight", "ESP_Highlight_Enabled", function(s) for _, p in pairs(Players:GetPlayers()) do UpdateESPState(p) end end)

local SliderFrame = Instance.new("Frame", Content); SliderFrame.Size = UDim2.new(1, 0, 0, 45); SliderFrame.BackgroundTransparency = 1
local SliderLabel = Instance.new("TextLabel", SliderFrame); SliderLabel.Size = UDim2.new(1, 0, 0, 15); SliderLabel.Text = "Transparency: " .. math.floor(Config.Highlight_Transparency * 100) .. "%"; SliderLabel.TextColor3 = Color3.new(0.9, 0.9, 0.9); SliderLabel.BackgroundTransparency = 1; SliderLabel.TextSize = 12
local SliderBar = Instance.new("Frame", SliderFrame); SliderBar.Size = UDim2.new(0.9, 0, 0, 4); SliderBar.Position = UDim2.new(0.05, 0, 0.7, 0); SliderBar.BackgroundColor3 = Color3.fromRGB(80, 80, 80); SliderBar.BorderSizePixel = 0
Instance.new("UICorner", SliderBar)
local SliderDot = Instance.new("Frame", SliderBar); SliderDot.Size = UDim2.new(0, 16, 0, 16); SliderDot.Position = UDim2.new(Config.Highlight_Transparency, -8, 0.5, -8); SliderDot.BackgroundColor3 = Color3.new(1, 1, 1)
Instance.new("UICorner", SliderDot).CornerRadius = UDim.new(1, 0)

local function updateSlider(input)
    local barPos = SliderBar.AbsolutePosition.X; local barWidth = SliderBar.AbsoluteSize.X
    local inputPos = input.Position.X; local percent = math.clamp((inputPos - barPos) / barWidth, 0, 1)
    SliderDot.Position = UDim2.new(percent, -8, 0.5, -8)
    Config.Highlight_Transparency = percent; SliderLabel.Text = "Transparency: " .. math.floor(percent * 100) .. "%"
    for _, p in pairs(Players:GetPlayers()) do UpdateESPState(p) end
end

SliderBar.InputBegan:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then is_sliding = true; updateSlider(input) end end)
UserInputService.InputChanged:Connect(function(input) if is_sliding and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then updateSlider(input) end end)
UserInputService.InputEnded:Connect(function(input) if is_sliding and (input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch) then is_sliding = false; SaveConfig() end end)

createToggle("Nicks", "ESP_Nick_Enabled", function(s) for _, p in pairs(Players:GetPlayers()) do UpdateESPState(p) end end)
createToggle("Health Bar", "ESP_HealthBar_Enabled", function(s) for _, p in pairs(Players:GetPlayers()) do UpdateESPState(p) end end)

local MinimizedFrame = Instance.new("TextButton", ScreenGui)
MinimizedFrame.Size = UDim2.new(0, 50, 0, 50)
MinimizedFrame.Position = UDim2.new(unpack(Config.Minimized_Pos))
MinimizedFrame.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
MinimizedFrame.BackgroundTransparency = MINIMIZED_TRANSPARENCY
MinimizedFrame.Visible = false; MinimizedFrame.Text = ""
Instance.new("UICorner", MinimizedFrame)

local function toggleMinimize()
    MainFrame.Visible = not MainFrame.Visible
    MinimizedFrame.Visible = not MainFrame.Visible
end

MinimizeBtn.MouseButton1Click:Connect(toggleMinimize)
MinimizedFrame.MouseButton1Click:Connect(toggleMinimize)

makeDraggable(MainFrame, "Panel_Pos")
makeDraggable(MinimizedFrame, "Minimized_Pos")

-- Aplica estados iniciais carregados
for _, p in pairs(Players:GetPlayers()) do task.spawn(function() UpdateESPState(p) end) end

print("Painel ESP com Salvamento JSON Carregado!")
