--[[
    SCRIPT SIMPLES PARA ANIME FINAL QUEST
    Funcionalidades: Auto Farm, Auto Heal, Auto Dodge
--]]

local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

-- Configurações (você pode mudar os valores)
local config = {
    autoFarm = true,      -- Bater sozinho
    autoHeal = true,      -- Curar automaticamente
    autoDodge = true,     -- Disperta automático
    healPercent = 40,     -- Cura quando vida < 40%
    dodgeInterval = 2,    -- Disperta a cada 2 segundos
    attackInterval = 0.5  -- Ataca a cada 0.5 segundos
}

-- Função para encontrar o inimigo mais próximo
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

-- Auto Farm (bater)
spawn(function()
    while config.autoFarm and task.wait(config.attackInterval) do
        local enemy = getClosestEnemy()
        if enemy and enemy:FindFirstChild("HumanoidRootPart") then
            -- Olha pro inimigo
            character.HumanoidRootPart.CFrame = CFrame.new(character.HumanoidRootPart.Position, enemy.HumanoidRootPart.Position)
            
            -- Ativa o botão de ataque (simula clique)
            local virtualInput = game:GetService("VirtualInputManager")
            virtualInput:SendMouseButtonEvent(0, 0, 0, true, game, 0)
            task.wait(0.05)
            virtualInput:SendMouseButtonEvent(0, 0, 0, false, game, 0)
        end
        task.wait()
    end
end)

-- Auto Heal (cura automática)
spawn(function()
    while config.autoHeal and task.wait(1) do
        local healthPercent = (humanoid.Health / humanoid.MaxHealth) * 100
        if healthPercent < config.healPercent then
            -- Tenta usar poção de cura
            local backpack = player.Backpack
            for _, item in ipairs(backpack:GetChildren()) do
                if item:IsA("Tool") and (item.Name:lower():find("potion") or item.Name:lower():find("cura") or item.Name:lower():find("health")) then
                    player.Character:FindFirstChild(item.Name) or item.Parent = character
                    task.wait(0.1)
                    -- Ativa a poção
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

-- Auto Dodge (disperta automático)
spawn(function()
    while config.autoDodge and task.wait(config.dodgeInterval) do
        -- Simula o dash (normalmente é a tecla Q ou botão na tela)
        local virtualInput = game:GetService("VirtualInputManager")
        virtualInput:SendKeyEvent(true, "Q", false, game)
        task.wait(0.1)
        virtualInput:SendKeyEvent(false, "Q", false, game)
    end
end)

print("Script carregado! Configurações:")
print("Auto Farm:", config.autoFarm)
print("Auto Heal:", config.autoHeal, "- Vida <", config.healPercent, "%")
print("Auto Dodge:", config.autoDodge, "- Intervalo:", config.dodgeInterval, "s")
