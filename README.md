-- ⚙️ Configurações Iniciais
local library = loadstring(game:HttpGet("https://raw.githubusercontent.com/jotta321/script-teste-blox-fruits-1/refs/heads/main/README.md"))()
local Window = library:CreateWindow("🔥 Blox Fruits Hub 🔥")

-- ✅ Variáveis de Controle
local autoFarmEnabled = false
local teleportEnabled = true
local espEnabled = false
local flyEnabled = false
local collectFruitsEnabled = false

local Players = game:GetService("Players")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
local camera = workspace.CurrentCamera

-- ✅ Função: Teleportar para posição
local function teleportTo(position)
    humanoidRootPart.CFrame = CFrame.new(position)
end

-- ✅ Função: Encontrar inimigo mais próximo
local function getClosestEnemy()
    local closest, dist = nil, math.huge
    for _, npc in pairs(workspace.Enemies:GetChildren()) do
        if npc:FindFirstChild("HumanoidRootPart") and npc:FindFirstChild("Humanoid") and npc.Humanoid.Health > 0 then
            local magnitude = (npc.HumanoidRootPart.Position - humanoidRootPart.Position).Magnitude
            if magnitude < dist then
                closest = npc
                dist = magnitude
            end
        end
    end
    return closest
end

-- ✅ Auto Farm Inteligente
local function autoFarmLoop()
    while autoFarmEnabled do
        local target = getClosestEnemy()
        if target then
            teleportTo(target.HumanoidRootPart.Position + Vector3.new(0, 5, 0))
            repeat
                task.wait(0.1)
                target.Humanoid.Health -= 10 -- ataque simulado
            until not target or target.Humanoid.Health <= 0 or not autoFarmEnabled
        else
            print("Nenhum inimigo encontrado.")
            task.wait(2)
        end
    end
end

-- 🔧 Interface: Auto Farm
local FarmTab = Window:CreateTab("Auto Farm")
FarmTab:CreateToggle("Ativar Auto Farm", function(state)
    autoFarmEnabled = state
    if state then
        print("✅ Auto Farm ativado.")
        task.spawn(autoFarmLoop)
    else
        print("🛑 Auto Farm desativado.")
    end
end)

-- 🔧 Interface: Teleportes
local TeleportTab = Window:CreateTab("Teleportes")
local locations = {
    ["Início"] = Vector3.new(100, 10, 100),
    ["Ilha da Selva"] = Vector3.new(-1200, 50, 340),
    ["Ilha do Deserto"] = Vector3.new(1700, 20, -2000),
    ["Sky Islands"] = Vector3.new(-4800, 1000, -500),
}
for name, pos in pairs(locations) do
    TeleportTab:CreateButton("Ir para " .. name, function()
        teleportTo(pos)
        print("📍 Teleportado para " .. name)
    end)
end

-- 🔧 Interface: ESP
local ESPtab = Window:CreateTab("ESP")
ESPtab:CreateToggle("Ativar ESP", function(state)
    espEnabled = state
    if state then
        print("👁️ ESP ativado.")
        for _, obj in pairs(workspace:GetDescendants()) do
            if obj:IsA("Model") and obj:FindFirstChild("Humanoid") then
                local hl = Instance.new("Highlight")
                hl.FillColor = Color3.new(1, 0, 0)
                hl.OutlineColor = Color3.new(1, 1, 1)
                hl.Parent = obj
            end
        end
    else
        print("❌ ESP desativado.")
        for _, v in pairs(workspace:GetDescendants()) do
            if v:IsA("Highlight") then
                v:Destroy()
            end
        end
    end
end)

-- 🔧 Interface: Voo
local FlyTab = Window:CreateTab("Voo")
FlyTab:CreateToggle("Ativar Voo", function(state)
    flyEnabled = state
    if state then
        print("🕊️ Voo ativado.")
        local bodyGyro = Instance.new("BodyGyro")
        local bodyVelocity = Instance.new("BodyVelocity")

        bodyGyro.P = 9e4
        bodyGyro.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
        bodyGyro.CFrame = humanoidRootPart.CFrame
        bodyGyro.Parent = humanoidRootPart

        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        bodyVelocity.MaxForce = Vector3.new(9e9, 9e9, 9e9)
        bodyVelocity.Parent = humanoidRootPart

        local UIS = game:GetService("UserInputService")
        local flying = true
        UIS.InputBegan:Connect(function(input)
            if not flying then return end
            if input.KeyCode == Enum.KeyCode.Space then
                bodyVelocity.Velocity = Vector3.new(0, 50, 0)
            elseif input.KeyCode == Enum.KeyCode.W then
                bodyVelocity.Velocity = camera.CFrame.LookVector * 60
            end
        end)
        UIS.InputEnded:Connect(function()
            bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        end)

        repeat task.wait() until not flyEnabled
        bodyGyro:Destroy()
        bodyVelocity:Destroy()
    else
        print("🛑 Voo desativado.")
    end
end)

-- 🔧 Interface: Frutas
local FruitTab = Window:CreateTab("Frutas")
FruitTab:CreateToggle("Coletar Frutas Automaticamente", function(state)
    collectFruitsEnabled = state
    if state then
        print("🍎 Coleta de frutas ativada.")
        task.spawn(function()
            while collectFruitsEnabled do
                for _, fruit in pairs(workspace:GetChildren()) do
                    if fruit:IsA("Tool") and fruit.Name:lower():find("fruit") then
                        if (fruit.Position - humanoidRootPart.Position).Magnitude < 1000 then
                            teleportTo(fruit.Position + Vector3.new(0, 3, 0))
                            wait(0.3)
                        end
                    end
                end
                wait(5)
            end
        end)
    else
        print("❌ Coleta de frutas desativada.")
    end
end)

-- 🛡️ Anti-AFK
player.Idled:Connect(function()
    game:GetService("VirtualUser"):Button2Down(Vector2.new(0, 0), camera.CFrame)
    wait(1)
    game:GetService("VirtualUser"):Button2Up(Vector2.new(0, 0), camera.CFrame)
    print("⚙️ Anti-AFK ativado.")
end)

-- 🔚 Mensagem Final
print("✅ Script carregado com sucesso! Divirta-se com responsabilidade.")
