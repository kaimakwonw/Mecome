-- Brookhaven Hub Completo
-- Feito para executores Roblox

-- Carregar biblioteca de UI
local library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()

-- Criar janela principal
local window = library.CreateLib("Brookhaven Hub Premium", "Ocean")

-- Abas principais
local playerTab = window:NewTab("Jogador")
local teleportTab = window:NewTab("Teleporte")
local vehicleTab = window:NewTab("Veículos")
local espTab = window:NewTab("ESP")
local adminTab = window:NewTab("Admin")
local miscTab = window:NewTab("Diversos")

-- Seções da aba Jogador
local playerSection = playerTab:NewSection("Modificações de Jogador")
local animationsSection = playerTab:NewSection("Animações")

-- Seções da aba Teleporte
local teleportSection = teleportTab:NewSection("Locais")
local playerTeleportSection = teleportTab:NewSection("Jogadores")

-- Seções da aba Veículos
local vehicleSection = vehicleTab:NewSection("Spawnar Veículos")
local vehicleModSection = vehicleTab:NewSection("Modificações")

-- Seções da aba ESP
local espSection = espTab:NewSection("Recursos ESP")

-- Seções da aba Admin
local adminSection = adminTab:NewSection("Comandos de Admin")

-- Seções da aba Diversos
local miscSection = miscTab:NewSection("Utilitários")
local creditSection = miscTab:NewSection("Créditos")

-- Variáveis globais
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local allESP = false
local playerESP = false
local itemESP = false
local vehicleESP = false

-- Funções da aba Jogador
playerSection:NewSlider("Velocidade", "Muda a velocidade do seu personagem", 500, 16, function(s)
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = s
    end
end)

playerSection:NewSlider("Pulo", "Muda a altura do pulo do seu personagem", 500, 50, function(s)
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.JumpPower = s
    end
end)

playerSection:NewButton("Dinheiro Infinito", "Tenta dar dinheiro infinito (pode não funcionar)", function()
    local args = {
        [1] = "ATM",
        [2] = "DepositAll"
    }
    game:GetService("ReplicatedStorage").RE:FireServer(unpack(args))
    wait(1)
    local args = {
        [1] = "ATM",
        [2] = "WithdrawAll"
    }
    game:GetService("ReplicatedStorage").RE:FireServer(unpack(args))
end)

playerSection:NewToggle("Voar", "Permite que você voe pelo mapa", function(state)
    local character = LocalPlayer.Character
    local humanoid = character:FindFirstChildOfClass("Humanoid")
    
    if state then
        local flyPart = Instance.new("BodyVelocity")
        flyPart.Name = "FlyPart"
        flyPart.Parent = character.HumanoidRootPart
        flyPart.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        
        RunService:BindToRenderStep("Fly", 1, function()
            local camera = workspace.CurrentCamera
            local direction = character.HumanoidRootPart.CFrame.LookVector
            
            if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.W) then
                flyPart.Velocity = direction * 50
            elseif game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.S) then
                flyPart.Velocity = direction * -50
            else
                flyPart.Velocity = Vector3.new(0, 0, 0)
            end
            
            if game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.Space) then
                flyPart.Velocity = Vector3.new(flyPart.Velocity.X, 50, flyPart.Velocity.Z)
            elseif game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.LeftControl) then
                flyPart.Velocity = Vector3.new(flyPart.Velocity.X, -50, flyPart.Velocity.Z)
            end
        end)
    else
        if character.HumanoidRootPart:FindFirstChild("FlyPart") then
            character.HumanoidRootPart.FlyPart:Destroy()
        end
        RunService:UnbindFromRenderStep("Fly")
    end
end)

-- Funções da aba Teleporte
local locations = {
    ["Banco"] = CFrame.new(-361.732788, 34.6904411, -178.387344),
    ["Hospital"] = CFrame.new(-795.581055, 42.2032967, -10.3202972),
    ["Escola"] = CFrame.new(-425.770721, 37.6253815, 560.004272),
    ["Delegacia"] = CFrame.new(-245.976822, 30.4487782, -119.651665),
    ["Centro da Cidade"] = CFrame.new(-190.692307, 30.5018978, -3.41802573),
    ["Mansões"] = CFrame.new(782.492615, 62.5329704, 307.009644),
    ["Praia"] = CFrame.new(-703.123962, 28.2350883, 336.282898),
    ["Cinema"] = CFrame.new(-1000.01263, 30.6804523, -168.261307)
}

for name, location in pairs(locations) do
    teleportSection:NewButton(name, "Teleporta para " .. name, function()
        if LocalPlayer.Character then
            LocalPlayer.Character.HumanoidRootPart.CFrame = location
        end
    end)
end

playerTeleportSection:NewButton("Atualizar Lista", "Atualiza a lista de jogadores", function()
    playerTeleportSection:ClearButtons()
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            playerTeleportSection:NewButton(player.Name, "Teleporta para " .. player.Name, function()
                if player.Character and LocalPlayer.Character then
                    LocalPlayer.Character.HumanoidRootPart.CFrame = player.Character.HumanoidRootPart.CFrame
                end
            end)
        end
    end
end)

-- Funções da aba Veículos
local vehicles = {
    "Basic Car", "Sports Car", "SUV", "Pickup Truck", "Ambulance", 
    "Police Car", "Fire Truck", "Bus", "Helicopter", "Luxury Car"
}

for _, vehicle in pairs(vehicles) do
    vehicleSection:NewButton(vehicle, "Spawna um " .. vehicle, function()
        local args = {
            [1] = "SpawnVehicle",
            [2] = vehicle
        }
        game:GetService("ReplicatedStorage").RE:FireServer(unpack(args))
    end)
end

vehicleModSection:NewToggle("Velocidade Máxima", "Aumenta a velocidade de qualquer veículo", function(state)
    if state then
        -- Conectar um evento para modificar veículos
        RunService.Heartbeat:Connect(function()
            local currentVehicle = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid").SeatPart
            if currentVehicle then
                local vehicleModel = currentVehicle:FindFirstAncestorOfClass("Model")
                if vehicleModel then
                    -- Modificar propriedades do veículo
                    for _, part in pairs(vehicleModel:GetDescendants()) do
                        if part:IsA("BodyGyro") or part:IsA("BodyVelocity") then
                            part.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                            part.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                            part.P = 100000
                        end
                    end
                end
            end
        end)
    end
end)

-- Funções da aba ESP
espSection:NewToggle("Jogadores ESP", "Veja jogadores através das paredes", function(state)
    playerESP = state
    
    if state then
        -- Criar ESP para jogadores
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character then
                local highlight = Instance.new("Highlight")
                highlight.Name = "ESPHighlight"
                highlight.FillColor = Color3.fromRGB(255, 0, 0)
                highlight.OutlineColor = Color3.fromRGB(255, 0, 0)
                highlight.FillTransparency = 0.5
                highlight.OutlineTransparency = 0
                highlight.Parent = player.Character
            end
        end
        
        -- Conectar para novos personagens
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer then
                player.CharacterAdded:Connect(function(character)
                    if playerESP then
                        wait(1)
                        local highlight = Instance.new("Highlight")
                        highlight.Name = "ESPHighlight"
                        highlight.FillColor = Color3.fromRGB(255, 0, 0)
                        highlight.OutlineColor = Color3.fromRGB(255, 0, 0)
                        highlight.FillTransparency = 0.5
                        highlight.OutlineTransparency = 0
                        highlight.Parent = character
                    end
                end)
            end
        end
    else
        -- Remover ESP de jogadores
        for _, player in pairs(Players:GetPlayers()) do
            if player.Character then
                local highlight = player.Character:FindFirstChild("ESPHighlight")
                if highlight then
                    highlight:Destroy()
                end
            end
        end
    end
end)

espSection:NewToggle("Itens ESP", "Veja itens importantes através das paredes", function(state)
    itemESP = state
    
    if state then
        -- Verificar todos os objetos no workspace
        for _, v in pairs(workspace:GetDescendants()) do
            if v:IsA("Model") and (v.Name:lower():find("money") or 
                                 v.Name:lower():find("jewel") or 
                                 v.Name:lower():find("key") or 
                                 v.Name:lower():find("gun")) then
                local highlight = Instance.new("Highlight")
                highlight.Name = "ItemESPHighlight"
                highlight.FillColor = Color3.fromRGB(0, 255, 0)
                highlight.OutlineColor = Color3.fromRGB(0, 255, 0)
                highlight.FillTransparency = 0.5
                highlight.OutlineTransparency = 0
                highlight.Parent = v
            end
        end
    else
        -- Remover ESP de itens
        for _, v in pairs(workspace:GetDescendants()) do
            if v:FindFirstChild("ItemESPHighlight") then
                v.ItemESPHighlight:Destroy()
            end
        end
    end
end)

espSection:NewToggle("Veículos ESP", "Veja veículos através das paredes", function(state)
    vehicleESP = state
    
    if state then
        -- Verificar todos os objetos no workspace
        for _, v in pairs(workspace:GetDescendants()) do
            if v:IsA("Model") and (v:FindFirstChild("Driver") or 
                                 v:FindFirstChild("VehicleSeat") or 
                                 v.Name:lower():find("car") or 
                                 v.Name:lower():find("vehicle")) then
                local highlight = Instance.new("Highlight")
                highlight.Name = "VehicleESPHighlight"
                highlight.FillColor = Color3.fromRGB(0, 0, 255)
                highlight.OutlineColor = Color3.fromRGB(0, 0, 255)
                highlight.FillTransparency = 0.5
                highlight.OutlineTransparency = 0
                highlight.Parent = v
            end
        end
    else
        -- Remover ESP de veículos
        for _, v in pairs(workspace:GetDescendants()) do
            if v:FindFirstChild("VehicleESPHighlight") then
                v.VehicleESPHighlight:Destroy()
            end
        end
    end
end)

-- Funções da aba Admin
adminSection:NewButton("Kick All", "Tenta kickar todos os jogadores", function()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local args = {
                [1] = "Admin",
                [2] = "Kick",
                [3] = player.Name
            }
            game:GetService("ReplicatedStorage").RE:FireServer(unpack(args))
        end
    end
end)

adminSection:NewButton("Kill All", "Tenta matar todos os jogadores", function()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local args = {
                [1] = "Admin",
                [2] = "Kill",
                [3] = player.Name
            }
            game:GetService("ReplicatedStorage").RE:FireServer(unpack(args))
        end
    end
end)

adminSection:NewButton("Btools", "Dá ferramentas de construção", function()
    -- Criar ferramentas de construção
    local backpack = LocalPlayer:FindFirstChildOfClass("Backpack")
    
    local hammer = Instance.new("Tool")
    hammer.Name = "Hammer"
    hammer.RequiresHandle = false
    hammer.Parent = backpack
    
    local delete = Instance.new("Tool")
    delete.Name = "Delete"
    delete.RequiresHandle = false
    delete.Parent = backpack
    
    -- Lógica da ferramenta de deletar
    delete.Activated:Connect(function()
        local mouse = LocalPlayer:GetMouse()
        if mouse.Target then
            mouse.Target:Destroy()
        end
    end)
end)

-- Funções da aba Diversos
miscSection:NewKeybind("Toggle UI", "Oculta/Mostra a interface", Enum.KeyCode.RightControl, function()
    library:ToggleUI()
end)

miscSection:NewButton("Anti-AFK", "Previne ser kickado por inatividade", function()
    for i,v in pairs(getconnections(LocalPlayer.Idled)) do
        v:Disable()
    end
end)

miscSection:NewSlider("Brilho", "Ajusta o brilho do jogo", 100, 0, function(value)
    game:GetService("Lighting").Brightness = value/50
end)

miscSection:NewToggle("Modo Noite", "Torna o jogo escuro", function(state)
    if state then
        game:GetService("Lighting").ClockTime = 0
    else
        game:GetService("Lighting").ClockTime = 14
    end
end)

-- Créditos
creditSection:NewLabel("Script criado para Brookhaven RP")
creditSection:NewLabel("Versão 1.0 Premium")
creditSection:NewLabel("Feito com Kavo UI Library")

-- Inicialização
print("Brookhaven Hub carregado com sucesso!")
print("Pressione RightControl para ocultar/mostrar a interface")
# Mecome
