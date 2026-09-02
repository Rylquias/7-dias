--[[
    Script: Aimbot + ESP + Fulbright + Scrap Teleport
    Descrição: Script para jogos Roblox com interface moderna e funcionalidades avançadas.
    Desenvolvido por: SeuNome
]]

-- // Configurações Iniciais
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- // Variáveis Globais
local AimbotEnabled = false
local ESPEnabled = false
local FulbrightEnabled = false
local SelectedPart = "Head"
local FOVRadius = 200
local ScrapTeleportEnabled = false

-- // Interface GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ModernGUI"
ScreenGui.Parent = game.CoreGui

-- // Frame Principal (Drag)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 380, 0, 480)
MainFrame.Position = UDim2.new(0.5, -190, 0.5, -240)
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 35)
MainFrame.BackgroundTransparency = 0.15
MainFrame.BorderSizePixel = 0
MainFrame.ClipsDescendants = true
MainFrame.Parent = ScreenGui

-- // Arredondamento e Sombra
local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 12)
UICorner.Parent = MainFrame

local Shadow = Instance.new("ImageLabel")
Shadow.Name = "Shadow"
Shadow.Size = UDim2.new(1, 20, 1, 20)
Shadow.Position = UDim2.new(0, -10, 0, -10)
Shadow.BackgroundTransparency = 1
Shadow.Image = "rbxassetid://1316044724"
Shadow.ImageColor3 = Color3.fromRGB(0, 0, 0)
Shadow.ImageTransparency = 0.6
Shadow.Parent = MainFrame

-- // Barra de Título (Drag)
local TitleBar = Instance.new("Frame")
TitleBar.Name = "TitleBar"
TitleBar.Size = UDim2.new(1, 0, 0, 40)
TitleBar.BackgroundColor3 = Color3.fromRGB(45, 45, 50)
TitleBar.BackgroundTransparency = 0.3
TitleBar.BorderSizePixel = 0
TitleBar.Parent = MainFrame

local TitleUICorner = Instance.new("UICorner")
TitleUICorner.CornerRadius = UDim.new(0, 12)
TitleUICorner.Parent = TitleBar

local TitleText = Instance.new("TextLabel")
TitleText.Size = UDim2.new(1, -50, 1, 0)
TitleText.Position = UDim2.new(0, 10, 0, 0)
TitleText.BackgroundTransparency = 1
TitleText.Text = "🔥  Script Pro"
TitleText.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleText.TextSize = 18
TitleText.TextXAlignment = Enum.TextXAlignment.Left
TitleText.Font = Enum.Font.GothamBold
TitleText.Parent = TitleBar

-- // Botão Fechar
local CloseButton = Instance.new("ImageButton")
CloseButton.Name = "CloseButton"
CloseButton.Size = UDim2.new(0, 30, 0, 30)
CloseButton.Position = UDim2.new(1, -35, 0, 5)
CloseButton.BackgroundColor3 = Color3.fromRGB(255, 70, 70)
CloseButton.BackgroundTransparency = 0.2
CloseButton.Image = "rbxassetid://6031091008"
CloseButton.ImageColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.Parent = TitleBar

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 8)
CloseCorner.Parent = CloseButton

CloseButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = false
    OpenButton.Visible = true
end)

-- // Botão Abrir (ImageButton)
local OpenButton = Instance.new("ImageButton")
OpenButton.Name = "OpenButton"
OpenButton.Size = UDim2.new(0, 50, 0, 50)
OpenButton.Position = UDim2.new(0, 10, 0, 10)
OpenButton.BackgroundColor3 = Color3.fromRGB(30, 30, 35)
OpenButton.BackgroundTransparency = 0.2
OpenButton.Image = "rbxassetid://6031090994"
OpenButton.ImageColor3 = Color3.fromRGB(255, 255, 255)
OpenButton.Visible = false
OpenButton.Parent = ScreenGui

local OpenCorner = Instance.new("UICorner")
OpenCorner.CornerRadius = UDim.new(0, 12)
OpenCorner.Parent = OpenButton

OpenButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = true
    OpenButton.Visible = false
end)

-- // Sistema de Drag
local Dragging = false
local DragStart = nil
local StartPos = nil

TitleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        Dragging = true
        DragStart = input.Position
        StartPos = MainFrame.Position
    end
end)

TitleBar.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        Dragging = false
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if Dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - DragStart
        MainFrame.Position = UDim2.new(
            StartPos.X.Scale,
            StartPos.X.Offset + delta.X,
            StartPos.Y.Scale,
            StartPos.Y.Offset + delta.Y
        )
    end
end)

-- // Função para Criar Toggles
local function CreateToggle(parent, text, yPos, default)
    local Frame = Instance.new("Frame")
    Frame.Size = UDim2.new(1, -40, 0, 40)
    Frame.Position = UDim2.new(0, 20, 0, yPos)
    Frame.BackgroundTransparency = 1
    Frame.Parent = parent

    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(0.7, 0, 1, 0)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.TextColor3 = Color3.fromRGB(220, 220, 220)
    Label.TextSize = 15
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Font = Enum.Font.Gotham
    Label.Parent = Frame

    local ToggleButton = Instance.new("ImageButton")
    ToggleButton.Size = UDim2.new(0, 40, 0, 22)
    ToggleButton.Position = UDim2.new(1, -45, 0.5, -11)
    ToggleButton.BackgroundColor3 = Color3.fromRGB(60, 60, 65)
    ToggleButton.BackgroundTransparency = 0.2
    ToggleButton.Image = "rbxassetid://6031091008"
    ToggleButton.ImageColor3 = Color3.fromRGB(255, 255, 255)
    ToggleButton.Parent = Frame

    local ToggleCorner = Instance.new("UICorner")
    ToggleCorner.CornerRadius = UDim.new(1, 0)
    ToggleCorner.Parent = ToggleButton

    local Circle = Instance.new("ImageLabel")
    Circle.Size = UDim2.new(0, 18, 0, 18)
    Circle.Position = UDim2.new(0, 2, 0.5, -9)
    Circle.BackgroundTransparency = 1
    Circle.Image = "rbxassetid://6031090994"
    Circle.ImageColor3 = Color3.fromRGB(255, 255, 255)
    Circle.Parent = ToggleButton

    local isOn = default or false

    local function UpdateToggle()
        if isOn then
            ToggleButton.BackgroundColor3 = Color3.fromRGB(0, 120, 255)
            Circle.Position = UDim2.new(1, -20, 0.5, -9)
        else
            ToggleButton.BackgroundColor3 = Color3.fromRGB(60, 60, 65)
            Circle.Position = UDim2.new(0, 2, 0.5, -9)
        end
    end

    UpdateToggle()

    ToggleButton.MouseButton1Click:Connect(function()
        isOn = not isOn
        UpdateToggle()
        return isOn
    end)

    return ToggleButton, function()
        return isOn
    end
end

-- // Criar Elementos da GUI
local ScrollFrame = Instance.new("ScrollingFrame")
ScrollFrame.Size = UDim2.new(1, 0, 1, -40)
ScrollFrame.Position = UDim2.new(0, 0, 0, 40)
ScrollFrame.BackgroundTransparency = 1
ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, 400)
ScrollFrame.ScrollBarThickness = 6
ScrollFrame.Parent = MainFrame

local UIList = Instance.new("UIListLayout")
UIList.Padding = UDim.new(0, 8)
UIList.Parent = ScrollFrame

-- // Aimbot Toggle
local AimbotToggle, GetAimbotState = CreateToggle(ScrollFrame, "🎯 Aimbot", 0, false)

-- // Seleção de Parte
local PartFrame = Instance.new("Frame")
PartFrame.Size = UDim2.new(1, -40, 0, 40)
PartFrame.Position = UDim2.new(0, 20, 0, 50)
PartFrame.BackgroundTransparency = 1
PartFrame.Parent = ScrollFrame

local PartLabel = Instance.new("TextLabel")
PartLabel.Size = UDim2.new(0.5, 0, 1, 0)
PartLabel.BackgroundTransparency = 1
PartLabel.Text = "📍 Parte:"
PartLabel.TextColor3 = Color3.fromRGB(220, 220, 220)
PartLabel.TextSize = 15
PartLabel.TextXAlignment = Enum.TextXAlignment.Left
PartLabel.Font = Enum.Font.Gotham
PartLabel.Parent = PartFrame

local PartDropdown = Instance.new("TextButton")
PartDropdown.Size = UDim2.new(0.4, 0, 1, 0)
PartDropdown.Position = UDim2.new(0.6, 0, 0, 0)
PartDropdown.BackgroundColor3 = Color3.fromRGB(45, 45, 50)
PartDropdown.Text = "Cabeça"
PartDropdown.TextColor3 = Color3.fromRGB(255, 255, 255)
PartDropdown.TextSize = 14
PartDropdown.Font = Enum.Font.Gotham
PartDropdown.Parent = PartFrame

local PartCorner = Instance.new("UICorner")
PartCorner.CornerRadius = UDim.new(0, 6)
PartCorner.Parent = PartDropdown

local PartList = {"Cabeça", "Torso", "Braço Esquerdo", "Braço Direito", "Perna Esquerda", "Perna Direita"}
local PartIndex = 1

PartDropdown.MouseButton1Click:Connect(function()
    PartIndex = PartIndex % #PartList + 1
    SelectedPart = PartList[PartIndex]
    PartDropdown.Text = SelectedPart
end)

-- // FOV Slider
local FOVFrame = Instance.new("Frame")
FOVFrame.Size = UDim2.new(1, -40, 0, 50)
FOVFrame.Position = UDim2.new(0, 20, 0, 100)
FOVFrame.BackgroundTransparency = 1
FOVFrame.Parent = ScrollFrame

local FOVLabel = Instance.new("TextLabel")
FOVLabel.Size = UDim2.new(0.5, 0, 0.5, 0)
FOVLabel.Position = UDim2.new(0, 0, 0, 0)
FOVLabel.BackgroundTransparency = 1
FOVLabel.Text = "👁️ FOV: 200"
FOVLabel.TextColor3 = Color3.fromRGB(220, 220, 220)
FOVLabel.TextSize = 15
FOVLabel.TextXAlignment = Enum.TextXAlignment.Left
FOVLabel.Font = Enum.Font.Gotham
FOVLabel.Parent = FOVFrame

local FOVSlider = Instance.new("Frame")
FOVSlider.Size = UDim2.new(0.8, 0, 0.3, 0)
FOVSlider.Position = UDim2.new(0, 0, 0.6, 0)
FOVSlider.BackgroundColor3 = Color3.fromRGB(60, 60, 65)
FOVSlider.BackgroundTransparency = 0.3
FOVSlider.Parent = FOVFrame

local SliderCorner = Instance.new("UICorner")
SliderCorner.CornerRadius = UDim.new(0, 8)
SliderCorner.Parent = FOVSlider

local SliderFill = Instance.new("Frame")
SliderFill.Size = UDim2.new(0.5, 0, 1, 0)
SliderFill.BackgroundColor3 = Color3.fromRGB(0, 120, 255)
SliderFill.BackgroundTransparency = 0.3
SliderFill.Parent = FOVSlider

local FillCorner = Instance.new("UICorner")
FillCorner.CornerRadius = UDim.new(0, 8)
FillCorner.Parent = SliderFill

local SliderButton = Instance.new("ImageButton")
SliderButton.Size = UDim2.new(0, 16, 0, 16)
SliderButton.Position = UDim2.new(0.5, -8, 0.5, -8)
SliderButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
SliderButton.Image = "rbxassetid://6031090994"
SliderButton.Parent = FOVSlider

local ButtonCorner = Instance.new("UICorner")
ButtonCorner.CornerRadius = UDim.new(1, 0)
ButtonCorner.Parent = SliderButton

local draggingSlider = false
SliderButton.MouseButton1Down:Connect(function()
    draggingSlider = true
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        draggingSlider = false
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if draggingSlider and input.UserInputType == Enum.UserInputType.MouseMovement then
        local pos = (input.Position.X - FOVSlider.AbsolutePosition.X) / FOVSlider.AbsoluteSize.X
        pos = math.clamp(pos, 0, 1)
        SliderFill.Size = UDim2.new(pos, 0, 1, 0)
        SliderButton.Position = UDim2.new(pos, -8, 0.5, -8)
        FOVRadius = math.floor(50 + pos * 350)
        FOVLabel.Text = "👁️ FOV: " .. FOVRadius
    end
end)

-- // ESP Toggle
local ESPToggle, GetESPState = CreateToggle(ScrollFrame, "👁️ ESP (Wallhack)", 160, false)

-- // Fulbright Toggle
local FulbrightToggle, GetFulbrightState = CreateToggle(ScrollFrame, "💡 Fulbright", 210, false)

-- // Scrap Teleport Toggle
local ScrapToggle, GetScrapState = CreateToggle(ScrollFrame, "📦 Teleportar para Scrap", 260, false)

-- // Botão de Ativar/Desativar Tudo
local ActionButton = Instance.new("TextButton")
ActionButton.Size = UDim2.new(0.8, 0, 0, 40)
ActionButton.Position = UDim2.new(0.1, 0, 0, 320)
ActionButton.BackgroundColor3 = Color3.fromRGB(0, 120, 255)
ActionButton.Text = "✔️ Aplicar Configurações"
ActionButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ActionButton.TextSize = 16
ActionButton.Font = Enum.Font.GothamBold
ActionButton.Parent = ScrollFrame

local ActionCorner = Instance.new("UICorner")
ActionCorner.CornerRadius = UDim.new(0, 8)
ActionCorner.Parent = ActionButton

ActionButton.MouseButton1Click:Connect(function()
    AimbotEnabled = GetAimbotState()
    ESPEnabled = GetESPState()
    FulbrightEnabled = GetFulbrightState()
    ScrapTeleportEnabled = GetScrapState()
    
    -- Feedback visual
    ActionButton.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
    wait(0.2)
    ActionButton.BackgroundColor3 = Color3.fromRGB(0, 120, 255)
end)

-- // Sistema de ESP
local ESPObjects = {}

local function CreateESP(object, color)
    if not ESPEnabled then return end
    
    local Billboard = Instance.new("BillboardGui")
    Billboard.Size = UDim2.new(0, 200, 0, 50)
    Billboard.AlwaysOnTop = true
    Billboard.Parent = object
    
    local Frame = Instance.new("Frame")
    Frame.Size = UDim2.new(1, 0, 1, 0)
    Frame.BackgroundTransparency = 0.5
    Frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    Frame.Parent = Billboard
    
    local Text = Instance.new("TextLabel")
    Text.Size = UDim2.new(1, 0, 0.6, 0)
    Text.Position = UDim2.new(0, 0, 0.2, 0)
    Text.BackgroundTransparency = 1
    Text.Text = "Nome: " .. object.Name
    Text.TextColor3 = color
    Text.TextSize = 12
    Text.Font = Enum.Font.GothamBold
    Text.TextStrokeTransparency = 0.5
    Text.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
    Text.Parent = Frame
    
    local DistText = Instance.new("TextLabel")
    DistText.Size = UDim2.new(1, 0, 0.4, 0)
    DistText.Position = UDim2.new(0, 0, 0.6, 0)
    DistText.BackgroundTransparency = 1
    DistText.Text = "Distância: 0m"
    DistText.TextColor3 = Color3.fromRGB(200, 200, 200)
    DistText.TextSize = 11
    DistText.Font = Enum.Font.Gotham
    DistText.TextStrokeTransparency = 0.5
    DistText.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
    DistText.Parent = Frame
    
    table.insert(ESPObjects, {Billboard = Billboard, Frame = Frame, Text = Text, DistText = DistText, Object = object})
end

-- // Atualizar ESP
RunService.RenderStepped:Connect(function()
    if ESPEnabled then
        for _, v in pairs(workspace:GetDescendants()) do
            if v:IsA("BasePart") and v.Parent and not v.Parent:IsA("Player") then
                local isObject = v.Name:find("Scrap") or v.Name:find("Item") or v.Name:find("Structure")
                if isObject and not v:FindFirstChild("BillboardGui") then
                    local color = Color3.fromRGB(255, 200, 0)
                    if v.Name:find("Scrap") then
                        color = Color3.fromRGB(0, 255, 200)
                    elseif v.Name:find("Structure") then
                        color = Color3.fromRGB(0, 200, 255)
                    end
                    CreateESP(v, color)
                end
            end
        end
        
        -- Atualizar distâncias
        for _, esp in pairs(ESPObjects) do
            if esp.Object and esp.Object.Parent then
                local dist = (esp.Object.Position - Camera.CFrame.Position).Magnitude
                esp.DistText.Text = string.format("Distância: %.1fm", dist)
            end
        end
    else
        -- Limpar ESP
        for _, esp in pairs(ESPObjects) do
            if esp.Billboard then esp.Billboard:Destroy() end
        end
        ESPObjects = {}
    end
end)

-- // Sistema de Aimbot
RunService.RenderStepped:Connect(function()
    if AimbotEnabled and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        local target = nil
        local shortestDist = FOVRadius
        
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild(SelectedPart) then
                local part = player.Character[SelectedPart]
                local screenPos, onScreen = Camera:WorldToViewportPoint(part.Position)
                if onScreen then
                    local dist = (Vector2.new(screenPos.X, screenPos.Y) - Vector2.new(Mouse.X, Mouse.Y)).Magnitude
                    if dist < shortestDist then
                        shortestDist = dist
                        target = part
                    end
                end
            end
        end
        
        if target then
            local dir = (target.Position - Camera.CFrame.Position).Unit
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, Camera.CFrame.Position + dir * 10)
        end
    end
end)

-- // Sistema de Fulbright
local function ToggleFulbright()
    if FulbrightEnabled then
        local Lighting = game:GetService("Lighting")
        Lighting.Brightness = 10
        Lighting.Ambient = Color3.fromRGB(255, 255, 255)
        Lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
    else
        local Lighting = game:GetService("Lighting")
        Lighting.Brightness = 1
        Lighting.Ambient = Color3.fromRGB(0, 0, 0)
        Lighting.OutdoorAmbient = Color3.fromRGB(0, 0, 0)
    end
end

-- // Sistema de Teleporte para Scrap
local function TeleportToScrap()
    if ScrapTeleportEnabled and LocalPlayer.Character then
        local scrapParts = {}
        for _, v in pairs(workspace:GetDescendants()) do
            if v:IsA("BasePart") and v.Name:find("Scrap") and v.Parent then
                table.insert(scrapParts, v)
            end
        end
        
        if #scrapParts > 0 then
            local nearest = nil
            local nearestDist = math.huge
            for _, part in pairs(scrapParts) do
                local dist = (part.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
                if dist < nearestDist then
                    nearestDist = dist
                    nearest = part
                end
            end
            
            if nearest then
                LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(nearest.Position + Vector3.new(0, 3, 0))
            end
        end
    end
end

-- // Executar Teleporte a cada 2 segundos
spawn(function()
    while wait(2) do
        TeleportToScrap()
    end
end)

-- // Atualizar Fulbright automaticamente
spawn(function()
    while wait(0.5) do
        ToggleFulbright()
    end
end)

-- // Inicialização
print("🔥 Script carregado com sucesso!")
print("Use o botão no canto superior esquerdo para abrir/fechar o menu.")
