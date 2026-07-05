-- AutoJoiner Steal a Brainrot v3.6 - Stable Clean
local p = game:GetService("Players").LocalPlayer
local tp = game:GetService("TeleportService")
local http = game:GetService("HttpService")
local placeId = game.PlaceId
local playerGui = p:WaitForChild("PlayerGui")
local uis = game:GetService("UserInputService")

-- ===== CONFIG =====
local targetFill = 0.8
local refreshTime = 10
local maxPages = 5

-- ===== FUNZIONI =====
local function getServers(pid, cursor)
    local url = "https://games.roblox.com/v1/games/" .. tostring(pid) .. "/servers/Public?limit=50"
    if cursor then url = url .. "&cursor=" .. cursor end
    local requestFunc = request or http_request or (syn and syn.request)
    if not requestFunc then return {}, nil end
    local success, result = pcall(function()
        return requestFunc({Url = url, Method = "GET"})
    end)
    if success and result and result.Body then
        local data = http:JSONDecode(result.Body)
        return data.data or {}, data.nextPageCursor
    end
    return {}, nil
end

local function getAllServers(pid)
    local allServers = {}
    local cursor = nil
    for i = 1, maxPages do
        local servers, nextCursor = getServers(pid, cursor)
        for _, srv in ipairs(servers) do
            table.insert(allServers, srv)
        end
        if not nextCursor then break end
        cursor = nextCursor
        task.wait(0.05) -- Ridotto a 0.05 per velocizzare lo scan delle pagine su Mobile
    end
    return allServers
end

local function findBestServer(pid)
    local best = nil
    local bestFill = 0
    local servers = getAllServers(pid)
    for _, srv in ipairs(servers) do
        local playing = srv.playing or 0
        local maxPlayers = srv.maxPlayers or 100
        local fill = playing / maxPlayers
        if fill >= targetFill and fill > bestFill and srv.id ~= game.JobId and playing < maxPlayers then
            bestFill = fill
            best = srv
        end
    end
    return best, bestFill, best and best.playing or 0, best and best.maxPlayers or 0
end

local function joinServer(server)
    if server and server.id then
        tp:TeleportToPlaceInstance(placeId, server.id, p)
    end
end

-- ===== UI =====
local sg = Instance.new("ScreenGui")
sg.Parent = playerGui
sg.ResetOnSpawn = false

local main = Instance.new("Frame")
main.Size = UDim2.new(0, 300, 0, 210)
main.Position = UDim2.new(0.5, -150, 0.3, 0)
main.BackgroundColor3 = Color3.fromRGB(15, 15, 30)
main.BackgroundTransparency = 0.1
main.BorderSizePixel = 2
main.BorderColor3 = Color3.fromRGB(130, 50, 250)
main.Active = true
main.Parent = sg

-- Trascinamento Universale (Mouse + Touch Mobile)
local dragData = nil
main.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragData = {start = input.Position, pos = main.Position}
    end
end)
main.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragData = nil
    end
end)
uis.InputChanged:Connect(function(input)
    if dragData and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local delta = input.Position - dragData.start
        main.Position = UDim2.new(dragData.pos.X.Scale, dragData.pos.X.Offset + delta.X, dragData.pos.Y.Scale, dragData.pos.Y.Offset + delta.Y)
    end
end)

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 30)
title.Text = "AutoJoiner SAB v3.6"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.BackgroundTransparency = 1
title.TextScaled = true
title.Font = Enum.Font.GothamBold
title.Parent = main

local status = Instance.new("TextLabel")
status.Size = UDim2.new(1, 0, 0, 25)
status.Position = UDim2.new(0, 0, 0, 30)
status.Text = "🔍 Pronto"
status.TextColor3 = Color3.fromRGB(200, 200, 255)
status.BackgroundTransparency = 1
status.TextScaled = true
status.Font = Enum.Font.Gotham
status.Parent = main

local infoLabel = Instance.new("TextLabel")
infoLabel.Size = UDim2.new(1, 0, 0, 20)
infoLabel.Position = UDim2.new(0, 0, 0, 55)
infoLabel.Text = "Target: ≥ 80% riempimento"
infoLabel.TextColor3 = Color3.fromRGB(150, 150, 200)
infoLabel.BackgroundTransparency = 1
infoLabel.TextScaled = true
infoLabel.Font = Enum.Font.Gotham
infoLabel.Parent = main

local serverLabel = Instance.new("TextLabel")
serverLabel.Size = UDim2.new(1, 0, 0, 20)
serverLabel.Position = UDim2.new(0, 0, 0, 75)
serverLabel.Text = "Server: -"
serverLabel.TextColor3 = Color3.fromRGB(150, 150, 200)
serverLabel.BackgroundTransparency = 1
serverLabel.TextScaled = true
serverLabel.Font = Enum.Font.Gotham
serverLabel.Parent = main

local fillLabel = Instance.new("TextLabel")
fillLabel.Size = UDim2.new(1, 0, 0, 20)
fillLabel.Position = UDim2.new(0, 0, 0, 95)
fillLabel.Text = "Riempimento: -"
fillLabel.TextColor3 = Color3.fromRGB(150, 150, 200)
fillLabel.BackgroundTransparency = 1
fillLabel.TextScaled = true
fillLabel.Font = Enum.Font.Gotham
fillLabel.Parent = main

local scanBtn = Instance.new("TextButton")
scanBtn.Size = UDim2.new(0.4, 0, 0, 30)
scanBtn.Position = UDim2.new(0.05, 0, 0, 130)
scanBtn.BackgroundColor3 = Color3.fromRGB(130, 50, 250)
scanBtn.Text = "SCAN"
scanBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
scanBtn.TextScaled = true
scanBtn.Font = Enum.Font.GothamBold
scanBtn.Parent = main

local joinBtn = Instance.new("TextButton")
joinBtn.Size = UDim2.new(0.4, 0, 0, 30)
joinBtn.Position = UDim2.new(0.55, 0, 0, 130)
joinBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
joinBtn.Text = "N/A"
joinBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
joinBtn.TextScaled = true
joinBtn.Font = Enum.Font.GothamBold
joinBtn.Parent = main

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0.3, 0, 0, 25)
closeBtn.Position = UDim2.new(0.35, 0, 0, 172)
closeBtn.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
closeBtn.Text = "CHIUDI"
closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
closeBtn.TextScaled = true
closeBtn.Font = Enum.Font.GothamBold
closeBtn.Parent = main
closeBtn.MouseButton1Click:Connect(function() sg:Destroy() end)

-- ===== LOGICA STRUTTURATA =====
local foundServer = nil
local isScanning = false
local scanToken = 0
local cancelRequested = false

local function resetUI()
    status.Text = "🔍 Pronto"
    serverLabel.Text = "Server: -"
    fillLabel.Text = "Riempimento: -"
    scanBtn.Text = "SCAN"
    joinBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 80) -- Disattiva visivamente il tasto JOIN (Grigio)
    joinBtn.Text = "N/A"
    foundServer = nil
end

scanBtn.MouseButton1Click:Connect(function()
    if isScanning then
        cancelRequested = true
        isScanning = false
        resetUI()
        status.Text = "🔍 Annullato"
        return
    end

    isScanning = true
    cancelRequested = false
    scanToken = scanToken + 1
    local currentToken = scanToken
    scanBtn.Text = "⏳ ANNULLA" -- Mostra chiaramente che un secondo click interrompe l'azione
    status.Text = "🔍 Scansione..."
    serverLabel.Text = "Scansionando..."
    fillLabel.Text = "Attendi..."

    task.spawn(function()
        local server, fill, playing, maxPlayers = findBestServer(placeId)

        if currentToken ~= scanToken or cancelRequested then
            isScanning = false
            resetUI()
            return
        end

        if server then
            foundServer = server
            status.Text = "✅ Trovato! " .. math.floor(fill * 100) .. "%"
            serverLabel.Text = "ID: " .. server.id:sub(1, 10) .. "..."
            fillLabel.Text = "Giocatori: " .. playing .. "/" .. maxPlayers
            joinBtn.BackgroundColor3 = Color3.fromRGB(0, 200, 0) -- Attiva il tasto JOIN (Verde)
            joinBtn.Text = "▶ JOIN"
            scanBtn.Text = "SCAN"
            isScanning = false
        else
            foundServer = nil
            status.Text = "❌ Nessun server >= 80%"
            fillLabel.Text = "Premi ANNULLA per fermare"
            joinBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 80) -- Disattiva subito il tasto JOIN
            joinBtn.Text = "N/A"

            local startTime = tick()
            while not cancelRequested and currentToken == scanToken and tick() - startTime < refreshTime do
                serverLabel.Text = "Riprova tra " .. math.ceil(refreshTime - (tick() - startTime)) .. "s"
                task.wait(0.2)
            end

            if currentToken ~= scanToken or cancelRequested then
                isScanning = false
                resetUI()
                return
            end

            resetUI()
            isScanning = false
        end
    end)
end)

joinBtn.MouseButton1Click:Connect(function()
    if foundServer then
        joinBtn.Text = "⏳ JOIN..."
        joinBtn.BackgroundColor3 = Color3.fromRGB(200, 150, 0) -- Feedback visivo di caricamento giallo/arancione
        joinServer(foundServer)
    end
end)

-- ===== AUTO SCAN ALL'INIEZIONE =====
task.spawn(function()
    status.Text = "🔍 Scansione iniziale..."
    local server, fill, playing, maxPlayers = findBestServer(placeId)
    if server then
        foundServer = server
        status.Text = "✅ Trovato! " .. math.floor(fill * 100) .. "%"
        serverLabel.Text = "ID: " .. server.id:sub(1, 10) .. "..."
        fillLabel.Text = "Giocatori: " .. playing .. "/" .. maxPlayers
        joinBtn.BackgroundColor3 = Color3.fromRGB(0, 200, 0)
        joinBtn.Text = "▶ JOIN"
    else
        status.Text = "🔍 Premi SCAN"
        serverLabel.Text = "Target: ≥ 80% riempimento"
    end
end)

print("AutoJoiner SAB v3.6 DEFINITIVO caricato correttamente! - by samu")
