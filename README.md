--[[
    SCRIPT CUSTOMIZADO PARA ANIME FINAL QUEST
    Autor: Personalizado para você
    Funcionalidades: Auto Farm, Auto Heal, Auto Dodge, Auto Kill, Auto Replay
--]]

local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

-- ============ CONFIGURAÇÕES (VOCÊ MUDA AQUI) ============
local config = {
    autoFarm = true,           -- Bater sozinho (Auto Farm / Auto Kill)
    autoHeal = true,           -- Curar automaticamente
    autoDodge = true,          -- Disperta automático
    autoReplay = true,         -- Refazer a fase sozinho
    healPercent = 40,          -- Cura quando vida < 40%
    dodgeInterval = 1.5,       -- Disperta a cada 1.5 segundos
    attackInterval = 0.3,      -- Ataca a cada 0.3 segundos
    replayDelay = 3,           -- Espera 3 segundos antes de replay
}

-- ============ FUNÇÃO PARA ENCONTRAR INIMIGO MAIS PRÓXIMO ============
local function getClosestEnemy()
    local closest = nil
    local shortestDistance = math.huge
    
    for _, enemy in ipairs(workspace:GetDescendants()) do
        if enemy:IsA("Model") and enemy:FindFirstChild("Humanoid") and enemy:FindFirstChild("HumanoidRootPart") then
            if enemy ~= character and enemy.Name ~= player.Name then
                local hrp = enemy:FindFirstChild("HumanoidRootPart")
                if hrp and enemy.Humanoid.Health > 0 then
                    local distance = (hrp.Position - character.HumanoidRootPart.Position).Magnitude
                    if distance < shortestDistance then
                        shortestDistance = distance
                        closest = enemy
                    end
                end
            end
        end
    end
    return closest
end

-- ============ AUTO FARM / AUTO KILL ============
spawn(function()
    while config.autoFarm and task.wait(config.attackInterval) do
        local enemy = getClosestEnemy()
        if enemy and enemy:FindFirstChild("HumanoidRootPart") then
            -- Olha para o inimigo
            character.HumanoidRootPart.CFrame = CFrame.new(character.HumanoidRootPart.Position, enemy.HumanoidRootPart.Position)
            
            -- Simula o clique de ataque (botão M1)
            local VirtualUser = game:GetService("VirtualUser")
            VirtualUser:CaptureController()
            VirtualUser:ClickButton1(Vector2.new(0, 0))
        end
    end
end)

-- ============ AUTO HEAL (CURA AUTOMÁTICA) ============
spawn(function()
    while config.autoHeal and task.wait(1) do
        local healthPercent = (humanoid.Health / humanoid.MaxHealth) * 100
        if healthPercent < config.healPercent then
            -- Procura poção no inventário
            local backpack = player.Backpack
            for _, item in ipairs(backpack:GetChildren()) do
                if item:IsA("Tool") and (item.Name:lower():find("potion") or item.Name:lower():find("cura") or item.Name:lower():find("health")) then
                    -- Equipa e usa a poção
                    item.Parent = character
                    task.wait(0.1)
                    local tool = character:FindFirstChild(item.Name)
                    if tool then
                        tool:Activate()
                    end
                    break
                end
            end
        end
    end
end)

-- ============ AUTO DODGE (DISPERA AUTOMÁTICO) ============
spawn(function()
    while config.autoDodge and task.wait(config.dodgeInterval) do
        -- Simula o dash (tecla Q no PC, botão no celular)
        local VirtualUser = game:GetService("VirtualUser")
        VirtualUser:CaptureController()
        VirtualUser:ClickButton2(Vector2.new(0, 0))
    end
end)

-- ============ AUTO REPLAY (REFAZER A FASE) ============
spawn(function()
    while config.autoReplay and task.wait(config.replayDelay) do
        -- Procura o botão de replay na tela
        local replayButton = nil
        for _, v in ipairs(workspace:GetDescendants()) do
            if v:IsA("TextButton") and (v.Name:lower():find("replay") or v.Name:lower():find("again") or v.Name:lower():find("restart")) then
                replayButton = v
                break
            end
        end
        
        if replayButton then
            replayButton:Click()
        end
    end
end)

-- ============ MENSAGEM DE CONFIRMAÇÃO ============
print("✅ SCRIPT CARREGADO COM SUCESSO!")
print("Auto Farm:", config.autoFarm)
print("Auto Heal:", config.autoHeal, "- Vida <", config.healPercent, "%")
print("Auto Dodge:", config.autoDodge, "- Intervalo:", config.dodgeInterval, "s")
print("Auto Replay:", config.autoReplay)
