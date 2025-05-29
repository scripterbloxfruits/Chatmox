-- Chatmox Hub Open Source for Blox Fruits
-- Author: SeuUsuario + ChatGPT
-- License: MIT

local Players = game:GetService("Players")
local VirtualUser = game:GetService("VirtualUser")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Remove GUI antiga
local oldGui = playerGui:FindFirstChild("ChatmoxHub")
if oldGui then oldGui:Destroy() end

-- Cria ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ChatmoxHub"
screenGui.ResetOnSpawn = false
screenGui.Parent = playerGui

-- Cria Frame principal
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 350, 0, 480)
mainFrame.Position = UDim2.new(0.5, -175, 0.5, -240)
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.Parent = screenGui

-- Aba Botões
local tabBar = Instance.new("Frame")
tabBar.Size = UDim2.new(1, 0, 0, 50)
tabBar.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
tabBar.Parent = mainFrame

local contentFrame = Instance.new("Frame")
contentFrame.Size = UDim2.new(1, 0, 1, -50)
contentFrame.Position = UDim2.new(0, 0, 0, 50)
contentFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
contentFrame.Parent = mainFrame

local tabs = {}
local tabNames = {
    "Auto Farm",
    "Auto Quest",
    "Fruit Sniper",
    "Server Hop",
    "Boss Farm",
    "PvP",
    "Misc",
    "Settings"
}

local selectedTab = nil

local function clearContent()
    for _, child in pairs(contentFrame:GetChildren()) do
        child:Destroy()
    end
end

local function createTabButton(name, xPos)
    local btn = Instance.new("TextButton")
    btn.Text = name
    btn.Size = UDim2.new(0, 100, 1, 0)
    btn.Position = UDim2.new(0, xPos, 0, 0)
    btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    btn.TextColor3 = Color3.fromRGB(230, 230, 230)
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 18
    btn.Parent = tabBar
    return btn
end

for i, name in ipairs(tabNames) do
    tabs[name] = createTabButton(name, (i-1)*100)
end

local states = {
    autoFarm = false,
    autoQuest = false,
    fruitSniper = false,
    serverHop = false,
    bossFarm = false,
    antiAFK = false,
    pvp = false,
    misc = {}
}

local function selectTab(name)
    if selectedTab then
        tabs[selectedTab].BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    end
    tabs[name].BackgroundColor3 = Color3.fromRGB(90, 90, 90)
    selectedTab = name
end

-- Função para exibir cada aba (aqui só um exemplo para Auto Farm)

local function showAutoFarm()
    clearContent()
    local label = Instance.new("TextLabel")
    label.Text = "Auto Farm"
    label.Size = UDim2.new(1, -20, 0, 40)
    label.Position = UDim2.new(0, 10, 0, 10)
    label.TextColor3 = Color3.new(1,1,1)
    label.BackgroundTransparency = 1
    label.Font = Enum.Font.SourceSansBold
    label.TextScaled = true
    label.Parent = contentFrame

    local toggleBtn = Instance.new("TextButton")
    toggleBtn.Text = "Toggle Auto Farm: OFF"
    toggleBtn.Size = UDim2.new(1, -20, 0, 50)
    toggleBtn.Position = UDim2.new(0, 10, 0, 60)
    toggleBtn.BackgroundColor3 = Color3.fromRGB(70,70,70)
    toggleBtn.TextColor3 = Color3.new(1,1,1)
    toggleBtn.Font = Enum.Font.SourceSansBold
    toggleBtn.TextSize = 20
    toggleBtn.Parent = contentFrame

    toggleBtn.MouseButton1Click:Connect(function()
        states.autoFarm = not states.autoFarm
        toggleBtn.Text = "Toggle Auto Farm: " .. (states.autoFarm and "ON" or "OFF")
        if states.autoFarm then
            spawn(function()
                while states.autoFarm do
                    -- Aqui coloca o código do Auto Farm real
                    wait(1)
                end
            end)
        end
    end)
end

-- Conecta as abas
for name, btn in pairs(tabs) do
    btn.MouseButton1Click:Connect(function()
        selectTab(name)
        if name == "Auto Farm" then
            showAutoFarm()
        else
            clearContent()
            local label = Instance.new("TextLabel")
            label.Text = name .. " - Work in progress"
            label.Size = UDim2.new(1, -20, 0, 40)
            label.Position = UDim2.new(0, 10, 0, 10)
            label.TextColor3 = Color3.new(1,1,1)
            label.BackgroundTransparency = 1
            label.Font = Enum.Font.SourceSansBold
            label.TextScaled = true
            label.Parent = contentFrame
        end
    end)
end

-- Seleciona a primeira aba por padrão
selectTab("Auto Farm")
showAutoFarm()

-- Anti AFK simples (roda em background)
spawn(function()
    while true do
        wait(60)
        VirtualUser:CaptureController()
        VirtualUser:ClickButton2(Vector2.new())
    end
end)