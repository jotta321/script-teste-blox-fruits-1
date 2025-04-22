-- 🧠 Variáveis importantes
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local root = character:WaitForChild("HumanoidRootPart")
local TweenService = game:GetService("TweenService")

-- 🔧 Configurações do executor Solaris
getgenv().SolarisLoaded = true -- Indica que o script foi carregado no Solaris
local SolarisLib = loadstring(game:HttpGet("https://raw.githubusercontent.com/jotta321/script-teste-blox-fruits-1/refs/heads/main/README.md"))() -- Carrega a biblioteca Solaris

-- 🏝️ Lista de ilhas organizadas por nível
local islands = {
    {name = "Starter Island", level = 1, pos = Vector3.new(1056, 16, 1426)},
    {name = "Jungle", level = 15, pos = Vector3.new(-1200, 16, 340)},
    {name = "Pirate Village", level = 30, pos = Vector3.new(-1120, 5, 3850)},
    {name = "Desert", level = 60, pos = Vector3.new(1050, 15, 4350)},
    {name = "Frozen Village", level = 90, pos = Vector3.new(1150, 15, -1300)},
    {name = "Marine Fortress", level = 120, pos = Vector3.new(-4500, 50, 4300)},
    {name = "Sky Islands", level = 150, pos = Vector3.new(-2900, 700, 1400)},
    {name = "Prison", level = 190, pos = Vector3.new(5150, 400, 4800)},
    {name = "Colosseum", level = 225, pos = Vector3.new(-1420, 7, -3000)},
    {name = "Magma Village", level = 300, pos = Vector3.new(-5200, 80, -5500)},
    {name = "Underwater City", level = 375, pos = Vector3.new(61150, 20, 1550)},
    {name = "Fountain City", level = 625, pos = Vector3.new(5250, 70, 400)},
}

-- 🚶 Função de movimento suave
function moveTo(position, duration)
    if not position or not root then return end -- Verifica se a posição e root existem
    local goal = {CFrame = CFrame.new(position)}
    local tween = TweenService:Create(root, TweenInfo.new(duration or 2, Enum.EasingStyle.Linear), goal)
    tween:Play()
    tween.Completed:Wait()
end

-- 🛠️ Função para verificar se o jogador tem uma ferramenta equipada
local function hasTool(toolName)
    return player.Backpack:FindFirstChild(toolName) or character:FindFirstChild(toolName)
end

-- 🥋 Auto Farm com flutuação sobre NPC e ataque
local autoFarm = false
local selectedWeapon = nil

function startAutoFarm()
    autoFarm = true
    while autoFarm do
        local enemies = workspace:FindFirstChild("Enemies")
        if enemies then
            local closest = nil
            local shortest = math.huge
            for _, npc in pairs(enemies:GetChildren()) do
                if npc:FindFirstChild("Humanoid") and npc.Humanoid.Health > 0 and npc:FindFirstChild("HumanoidRootPart") then
                    local distance = (npc.HumanoidRootPart.Position - root.Position).Magnitude
                    if distance < shortest then
                        closest = npc
                        shortest = distance
                    end
                end
            end
            if closest then
                -- Selecionar arma/fruta
                local tool = player.Backpack:FindFirstChildOfClass("Tool") or character:FindFirstChildOfClass("Tool")
                if tool then
                    selectedWeapon = tool.Name
                    if not character:FindFirstChild(tool.Name) then
                        tool.Parent = character
                    end
                    player.Character.Humanoid:EquipTool(tool)
                end

                -- Mover até o NPC flutuando
                local above = closest.HumanoidRootPart.Position + Vector3.new(0, 5, 0)
                moveTo(above, 1.5)

                -- Atacar enquanto vivo
                repeat
                    task.wait(0.3)
                    local equipped = character:FindFirstChildOfClass("Tool")
                    if equipped then
                        equipped:Activate()
                    end
                until not closest or not closest:FindFirstChild("Humanoid") or closest.Humanoid.Health <= 0 or not autoFarm
            end
        end
        task.wait(0.5)
    end
end

function stopAutoFarm()
    autoFarm = false
end

-- 🔘 Interface simples temporária com ilhas e auto farm
local gui = SolarisLib:New({Name = "Blox Fruits Script"})
local farmTab = gui:AddTab("Auto Farm")
local teleportTab = gui:AddTab("Teleport")

-- Botão Auto Farm
farmTab:AddToggle({
    Text = "Auto Farm",
    Callback = function(value)
        if value then
            startAutoFarm()
        else
            stopAutoFarm()
        end
    end,
})

-- Botões de teleporte manual
for _, island in ipairs(islands) do
    teleportTab:AddButton({
        Text = "🏝️ " .. island.name .. " (Lv. " .. island.level .. ")",
        Callback = function()
            moveTo(island.pos, 3)
        end,
    })
end

-- 🔄 Função para resgatar códigos
local x2Code = {"JULYUPDATE_RESET", "staffbattle", "Sub2CaptainMaui", "SUB2GAMERROBOT_RESET1", "KittGaming", "Sub2Fer999", "Enyu_is_Pro", "Magicbus", "ENYU_IS_PRO", "FUDD10", "BIGNEWS", "THEGREATACE", "SUB2GAMERROBOT_EXP1", "STRAWHATMAIME", "SUB2OFFICIALNOOBIE", "SUB2NOOBMASTER123", "SUB2DAIGROCK", "AXIORE", "TANTAIGAMIMG"}
local function redeemCodes()
    for _, code in pairs(x2Code) do
        game:GetService("ReplicatedStorage").Remotes.Redeem:InvokeServer(code)
    end
end

-- Botão Redeem Codes
teleportTab:AddButton({
    Text = "Redeem All Codes",
    Callback = function()
        redeemCodes()
    end,
})

-- 🌟 Função para atualizar ESP (Exemplo para frutas do diabo)
local devilFruitESPEnabled = false
local function updateDevilFruitESP()
    for _, v in pairs(workspace:GetChildren()) do
        if devilFruitESPEnabled and string.find(v.Name, "Fruit") and v:IsA("Tool") then
            if not v.Handle:FindFirstChild("NameEsp") then
                local bill = Instance.new("BillboardGui", v.Handle)
                bill.Name = "NameEsp"
                bill.Size = UDim2.new(0, 100, 0, 50)
                bill.Adornee = v.Handle
                bill.AlwaysOnTop = true

                local name = Instance.new("TextLabel", bill)
                name.Text = v.Name
                name.Font = Enum.Font.GothamSemibold
                name.TextSize = 14
                name.TextColor3 = Color3.fromRGB(255, 0, 0)
                name.BackgroundTransparency = 1
            end
        elseif v.Handle and v.Handle:FindFirstChild("NameEsp") then
            v.Handle.NameEsp:Destroy()
        end
    end
end

-- Toggle para Devil Fruit ESP
farmTab:AddToggle({
    Text = "Devil Fruit ESP",
    Callback = function(value)
        devilFruitESPEnabled = value
        updateDevilFruitESP()
    end,
})
