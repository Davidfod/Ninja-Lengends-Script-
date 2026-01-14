-- DIREITOS AUTORAIS: CyberNoDry
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()

-- Configurações de Pastas e Objetos
local COIN_FOLDER = workspace:WaitForChild("spawnedCoins")
local ISLAND_FOLDER = workspace:WaitForChild("islandUnlockParts")
local HILL_PART_NAME = "kingOfTheHillPart"

-- Estados
local farmingCoins = false
local unlockingIslands = false
local stayAtHill = false

-- --- INTERFACE GRÁFICA (GUI) ---
local ScreenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
ScreenGui.Name = "CyberNoDry_Hub_V3"
ScreenGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 220, 0, 280) -- Aumentado para 3 botões
MainFrame.Position = UDim2.new(0.5, -110, 0.5, -140)
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.BorderSizePixel = 2
MainFrame.Active = true
MainFrame.Draggable = true 

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 35)
Title.Text = "CyberNoDry Ultimate Hub"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
Title.TextSize = 14

-- BOTÃO 1: FARM DE MOEDAS (LOOP)
local CoinBtn = Instance.new("TextButton", MainFrame)
CoinBtn.Size = UDim2.new(0.85, 0, 0, 40)
CoinBtn.Position = UDim2.new(0.075, 0, 0.18, 0)
CoinBtn.Text = "AUTO FARM MOEDAS"
CoinBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
CoinBtn.TextColor3 = Color3.new(1, 1, 1)

-- BOTÃO 2: DESBLOQUEAR ILHAS (SEQUENCIAL)
local IslandBtn = Instance.new("TextButton", MainFrame)
IslandBtn.Size = UDim2.new(0.85, 0, 0, 40)
IslandBtn.Position = UDim2.new(0.075, 0, 0.38, 0)
IslandBtn.Text = "DESBLOQUEAR ILHAS"
IslandBtn.BackgroundColor3 = Color3.fromRGB(0, 100, 200)
IslandBtn.TextColor3 = Color3.new(1, 1, 1)

-- BOTÃO 3: REI DA MONTANHA (FIXO/PERSISTENTE)
local HillBtn = Instance.new("TextButton", MainFrame)
HillBtn.Size = UDim2.new(0.85, 0, 0, 40)
HillBtn.Position = UDim2.new(0.075, 0, 0.58, 0)
HillBtn.Text = "REI DA MONTANHA"
HillBtn.BackgroundColor3 = Color3.fromRGB(150, 150, 0)
HillBtn.TextColor3 = Color3.new(0, 0, 0)
HillBtn.Font = Enum.Font.SourceSansBold

-- CONTROLES
local MiniBtn = Instance.new("TextButton", MainFrame)
MiniBtn.Size = UDim2.new(0, 25, 0, 25)
MiniBtn.Position = UDim2.new(0.7, 0, 0, 5)
MiniBtn.Text = "-"
MiniBtn.BackgroundColor3 = Color3.fromRGB(100, 100, 100)

local CloseBtn = Instance.new("TextButton", MainFrame)
CloseBtn.Size = UDim2.new(0, 25, 0, 25)
CloseBtn.Position = UDim2.new(0.85, 0, 0, 5)
CloseBtn.Text = "X"
CloseBtn.BackgroundColor3 = Color3.fromRGB(150, 0, 0)

local Credit = Instance.new("TextLabel", MainFrame)
Credit.Size = UDim2.new(1, 0, 0, 20)
Credit.Position = UDim2.new(0, 0, 0.90, 0)
Credit.Text = "BY: CYBERNODRY"
Credit.BackgroundTransparency = 1
Credit.TextColor3 = Color3.new(0.7, 0.7, 0.7)
Credit.TextSize = 11

-- --- LÓGICAS ---

-- 1. Farm Moedas
local function startCoinFarm()
    farmingCoins = true
    CoinBtn.Text = "PARAR FARM"
    CoinBtn.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
    task.spawn(function()
        while farmingCoins do
            local items = COIN_FOLDER:GetDescendants()
            for _, obj in ipairs(items) do
                if not farmingCoins then break end
                if obj:IsA("BasePart") then
                    local hrp = character:FindFirstChild("HumanoidRootPart")
                    if hrp then hrp.CFrame = obj.CFrame task.wait(0.15) end
                end
            end
            task.wait(1)
        end
    end)
end

-- 2. Desbloquear Ilhas
local function startIslandUnlock()
    unlockingIslands = true
    IslandBtn.Text = "CANCELAR ILHAS"
    IslandBtn.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
    task.spawn(function()
        local parts = ISLAND_FOLDER:GetChildren()
        for _, obj in ipairs(parts) do
            if not unlockingIslands then break end
            if obj:IsA("BasePart") then
                local hrp = character:FindFirstChild("HumanoidRootPart")
                if hrp then hrp.CFrame = obj.CFrame task.wait(0.6) end
            end
        end
        unlockingIslands = false
        IslandBtn.Text = "DESBLOQUEAR ILHAS"
        IslandBtn.BackgroundColor3 = Color3.fromRGB(0, 100, 200)
    end)
end

-- 3. Manter na Montanha (Loop constante para persistência)
RunService.Heartbeat:Connect(function()
    if stayAtHill then
        local hillPart = workspace:FindFirstChild(HILL_PART_NAME)
        if hillPart then
            local hrp = character:FindFirstChild("HumanoidRootPart")
            if hrp then
                -- Mantém o CFrame na parte, mesmo se tentar sair ou morrer
                hrp.CFrame = hillPart.CFrame + Vector3.new(0, 3, 0)
            end
        end
    end
end)

-- --- EVENTOS DE BOTÃO ---

CoinBtn.MouseButton1Click:Connect(function()
    if farmingCoins then
        farmingCoins = false
        CoinBtn.Text = "AUTO FARM MOEDAS"
        CoinBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
    else
        startCoinFarm()
    end
end)

IslandBtn.MouseButton1Click:Connect(function()
    if unlockingIslands then
        unlockingIslands = false
        IslandBtn.Text = "DESBLOQUEAR ILHAS"
        IslandBtn.BackgroundColor3 = Color3.fromRGB(0, 100, 200)
    else
        startIslandUnlock()
    end
end)

HillBtn.MouseButton1Click:Connect(function()
    stayAtHill = not stayAtHill
    if stayAtHill then
        HillBtn.Text = "SAIR DA MONTANHA"
        HillBtn.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
        HillBtn.TextColor3 = Color3.new(1, 1, 1)
    else
        HillBtn.Text = "REI DA MONTANHA"
        HillBtn.BackgroundColor3 = Color3.fromRGB(150, 150, 0)
        HillBtn.TextColor3 = Color3.new(0, 0, 0)
    end
end)

MiniBtn.MouseButton1Click:Connect(function()
    local isMin = MainFrame.Size.Y.Offset < 50
    MainFrame:TweenSize(isMin and UDim2.new(0, 220, 0, 280) or UDim2.new(0, 220, 0, 35), "Out", "Quad", 0.3, true)
    CoinBtn.Visible = isMin; IslandBtn.Visible = isMin; HillBtn.Visible = isMin; Credit.Visible = isMin
end)

CloseBtn.MouseButton1Click:Connect(function()
    farmingCoins = false; stayAtHill = false
    ScreenGui:Destroy()
end)

player.CharacterAdded:Connect(function(newChar)
    character = newChar
end)
