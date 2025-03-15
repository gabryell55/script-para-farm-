local Players = game:GetService("Players")
local DataStoreService = game:GetService("DataStoreService")
local BanDataStore = DataStoreService:GetDataStore("BanList")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local function isPlayerBanned(player)
    local success, result = pcall(function()
        return BanDataStore:GetAsync(player.UserId)
    end)
    if success and result then
        return true
    end
    return false
end

local function banPlayer(player, reason)
    pcall(function()
        BanDataStore:SetAsync(player.UserId, reason)
    end)
    player:Kick("Você foi banido! Motivo: " .. reason)
end

local function onPlayerAdded(player)
    if isPlayerBanned(player) then
        player:Kick("Você está banido do jogo!")
        return
    end
    
    local pieces = Instance.new("IntValue")
    pieces.Name = "Pieces"
    pieces.Value = 0
    pieces.Parent = player
    
    while true do
        wait(10) -- Tempo entre cada farm
        
        if not player or not player.Parent then break end -- Se o jogador sair, interrompe
        
        pieces.Value = pieces.Value + math.random(5, 15) -- Adiciona peças aleatoriamente
    end
end

local function onExploitDetected(player)
    banPlayer(player, "Uso de exploit detectado")
end

Players.PlayerAdded:Connect(onPlayerAdded)
ReplicatedStorage:WaitForChild("ExploitDetected").OnServerEvent:Connect(onExploitDetected)
