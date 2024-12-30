-- Pegando os serviços necessários
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

-- Tabela para armazenar as linhas criadas
local lines = {}

-- Função para criar ou atualizar a linha entre dois jogadores
local function updateLineBetweenPlayers(player1, player2)
    -- Pegando as posições dos jogadores
    local char1 = player1.Character
    local char2 = player2.Character

    -- Garantindo que ambos os jogadores tenham personagens
    if char1 and char2 then
        local position1 = char1.HumanoidRootPart.Position
        local position2 = char2.HumanoidRootPart.Position

        -- Verificando se já existe uma linha para esses dois jogadores
        local lineKey = player1.UserId .. "-" .. player2.UserId
        if not lines[lineKey] then
            -- Criando a parte visual da linha (usando uma "Part" no Roblox)
            local line = Instance.new("Part")
            line.Anchored = true
            line.CanCollide = false
            line.Size = Vector3.new(0.2, 0.2, (position1 - position2).Magnitude)
            line.Color = Color3.fromRGB(255, 255, 255) -- Cor branca
            line.Position = (position1 + position2) / 2 -- Posição no meio dos dois pontos
            line.CFrame = CFrame.new(position1, position2) -- Rotaciona a parte para conectar os dois pontos

            -- Adicionando a linha ao jogo e armazenando ela
            line.Parent = game.Workspace
            lines[lineKey] = line
        else
            -- Atualizando a linha existente
            local line = lines[lineKey]
            line.Size = Vector3.new(0.2, 0.2, (position1 - position2).Magnitude)
            line.Position = (position1 + position2) / 2
            line.CFrame = CFrame.new(position1, position2)
        end
    end
end

-- Função para garantir que as linhas sigam os jogadores em movimento
local function connectPlayers()
    local localPlayer = Players.LocalPlayer

    -- Garantindo que o personagem do jogador local esteja carregado
    if localPlayer.Character then
        -- Atualizando as linhas a cada quadro (Heartbeat)
        RunService.Heartbeat:Connect(function()
            -- Criar ou atualizar a linha entre o jogador local e os outros jogadores
            for _, player in pairs(Players:GetPlayers()) do
                if player ~= localPlayer and player.Character then
                    updateLineBetweenPlayers(localPlayer, player)
                end
            end
        end)
    end
end

-- Chama a função para conectar os jogadores
connectPlayers()
