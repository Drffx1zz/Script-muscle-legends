--// SERVIÇOS
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local lp = Players.LocalPlayer

--// VARIÁVEIS DE CONTROLE
getgenv().AutoFarm = false
getgenv().AutoRebirth = false
getgenv().AutoCollect = false
getgenv().PetBug = false -- Foco em agilizar a evolução

--// CRIAÇÃO DA INTERFACE (GUI MOBILE)
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "MuscleGodMobile"

local Main = Instance.new("Frame", ScreenGui)
Main.Size = UDim2.new(0, 180, 0, 240)
Main.Position = UDim2.new(0.5, -90, 0.4, 0)
Main.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
Main.BorderSizePixel = 0
Main.Active = true
Main.Draggable = true -- Essencial para Mobile

local Corner = Instance.new("UICorner", Main)
local Title = Instance.new("TextLabel", Main)
Title.Size = UDim2.new(1, 0, 0.2, 0)
Title.Text = "BRAINROT HUB: ML"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.BackgroundTransparency = 1
Title.TextScaled = true

--// FUNÇÃO AUXILIAR: BOTÕES
local function AddButton(text, pos, color, callback)
    local btn = Instance.new("TextButton", Main)
    btn.Size = UDim2.new(0.9, 0, 0.15, 0)
    btn.Position = pos
    btn.Text = text
    btn.BackgroundColor3 = color
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.TextScaled = true
    Instance.new("UICorner", btn)
    btn.MouseButton1Click:Connect(callback)
    return btn
end

--// FUNÇÃO AUXILIAR: TELEPORTS
local function TP(cframe)
    if lp.Character and lp.Character:FindFirstChild("HumanoidRootPart") then
        lp.Character.HumanoidRootPart.CFrame = cframe
    end
end

--// BOTÕES DA INTERFACE
local farmBtn = AddButton("Auto-Farm: OFF", UDim2.new(0.05, 0, 0.22, 0), Color3.fromRGB(40, 40, 40), function()
    getgenv().AutoFarm = not getgenv().AutoFarm
end)

local rebBtn = AddButton("Auto-Rebirth: OFF", UDim2.new(0.05, 0, 0.4, 0), Color3.fromRGB(40, 40, 40), function()
    getgenv().AutoRebirth = not getgenv().AutoRebirth
end)

local tpBtn = AddButton("Ilha Final (TP)", UDim2.new(0.05, 0, 0.58, 0), Color3.fromRGB(0, 100, 200), function()
    TP(CFrame.new(-2582, 5752, -553)) -- Coordenadas Ilha Legends
end)

local bugBtn = AddButton("Auto-Bug Pet: OFF", UDim2.new(0.05, 0, 0.76, 0), Color3.fromRGB(150, 0, 255), function()
    getgenv().PetBug = not getgenv().PetBug
end)

--// LÓGICA DO SCRIPT (EXECUÇÃO)
task.spawn(function()
    while task.wait() do
        -- 1. AUTO FARM (PUNCH)
        if getgenv().AutoFarm then
            pcall(function()
                local tool = lp.Backpack:FindFirstChildOfClass("Tool") or lp.Character:FindFirstChildOfClass("Tool")
                if tool then
                    ReplicatedStorage.muscleEvent:FireServer("punch", tool.Name)
                end
            end)
            farmBtn.Text = "Auto-Farm: ON"
            farmBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
        else
            farmBtn.Text = "Auto-Farm: OFF"
            farmBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        end

        -- 2. AUTO REBIRTH
        if getgenv().AutoRebirth then
            ReplicatedStorage.rEvents.rebirthEvent:FireServer("rebirthRequest")
            rebBtn.Text = "Auto-Rebirth: ON"
        else
            rebBtn.Text = "Auto-Rebirth: OFF"
        end

        -- 3. AUTO BUG / EVOLVE (Tenta evoluir pets automaticamente)
        if getgenv().PetBug then
            pcall(function()
                ReplicatedStorage.rEvents.petEvolveEvent:FireServer("evolvePet", "All")
                -- Alguns bugs de pet envolvem equipar/desequipar rápido, mas o auto-evolve é mais seguro no mobile
            end)
        end
    end
end)

--// AUTO COLLECT GEMS & CRYSTALS (AURA)
task.spawn(function()
    while task.wait(0.5) do
        for _, crystal in pairs(workspace.mapCrystals:GetChildren()) do
            if crystal:FindFirstChild("TouchPart") then
                firetouchinterest(lp.Character.HumanoidRootPart, crystal.TouchPart, 0)
                firetouchinterest(lp.Character.HumanoidRootPart, crystal.TouchPart, 1)
            end
        end
    end
end)

print("[!] Muscle God Hub Carregado com Sucesso.")
