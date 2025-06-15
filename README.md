-- Script do Servidor (ServerScriptService)

local Players = game:GetService("Players")
local DISTANCE_THRESHOLD = 50 -- Distância mínima para considerar um teleporte legítimo
local TIME_THRESHOLD = 5 -- Tempo em segundos para verificar teleporte

local playerPositions = {}

Players.PlayerAdded:Connect(function(player)
    playerPositions[player.UserId] = {position = player.Character.HumanoidRootPart.Position, lastCheck = tick()}
end)

Players.PlayerRemoving:Connect(function(player)
    playerPositions[player.UserId] = nil
end)

game:GetService("RunService").Heartbeat:Connect(function()
    for _, player in pairs(Players:GetPlayers()) do
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local currentPosition = player.Character.HumanoidRootPart.Position
            local lastCheck = playerPositions[player.UserId].lastCheck
            local previousPosition = playerPositions[player.UserId].position

            local distanceMoved = (currentPosition - previousPosition).Magnitude
            local timeElapsed = tick() - lastCheck

            if distanceMoved > DISTANCE_THRESHOLD and timeElapsed < TIME_THRESHOLD then
                -- Possível teleporte não autorizado detectado
                print("Jogador " .. player.Name .. " pode ter usado um exploit de teleporte.")
                -- Ação a ser tomada, como kick ou log
            end

            -- Atualiza a posição e o tempo de verificação
            playerPositions[player.UserId] = {position = currentPosition, lastCheck = tick()}
        end
    end
end)
