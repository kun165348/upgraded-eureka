local ReplicatedStorage = game:GetService("ReplicatedStorage")
local event = ReplicatedStorage:WaitForChild("FarmEvent")
local collectEvent = ReplicatedStorage:WaitForChild("CollectCoin")

local FARM_COOLDOWN = 1
local BASE_RATE = 1
local playerFarming = {}
local ALLOW_FARM = { [123456789] = true } -- ใส่ UserId ตัวเอง

-- ฟาร์มอัตโนมัติ
event.OnServerEvent:Connect(function(player, start)
    if not ALLOW_FARM[player.UserId] then return end

    local coins = player:FindFirstChild("leaderstats") and player.leaderstats:FindFirstChild("Coins")
    local level = player:FindFirstChild("leaderstats") and player.leaderstats:FindFirstChild("Level")
    if not coins or not level then return end

    if start then
        if playerFarming[player] then return end
        playerFarming[player] = true

        spawn(function()
            while playerFarming[player] do
                local rate = BASE_RATE + (level.Value * 2)
                coins.Value += rate
                task.wait(FARM_COOLDOWN)
            end
        end)
    else
        playerFarming[player] = false
    end
end)

-- เก็บไอเทม Coin
collectEvent.OnServerEvent:Connect(function(player, coin)
    if coin and coin.Parent then
        local coins = player:FindFirstChild("leaderstats") and player.leaderstats:FindFirstChild("Coins")
        if coins then
            coins.Value += 1 -- จำนวนเงินจากไอเทม
        end
        coin:Destroy()
    end
end)
