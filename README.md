# myscript-- 📜 PIANO PLAYER AUTOMÁTICO + ESP
-- Coloque este script em StarterPlayerScripts ou ServerScriptService

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

-- 🎵 CONFIGURAÇÕES DO PIANO
local PIANO_KEYS = {} -- Será preenchido automaticamente
local currentSong = nil
local isPlaying = false
local songSpeed = 1

-- 🎶 MÚSICAS DISPONÍVEIS
local SONGS = {
    Megalovania = {
        {key = "C5", time = 0.15},
        {key = "C5", time = 0.15},
        {key = "C5", time = 0.15},
        {key = "Eb5", time = 0.15},
        {key = "G4", time = 0.30},
        {key = "Bb4", time = 0.15},
        {key = "C5", time = 0.15},
        {key = "Eb5", time = 0.30},
        {key = "G4", time = 0.15},
        {key = "Bb4", time = 0.15},
        {key = "C5", time = 0.30},
        {key = "Eb5", time = 0.15},
        {key = "G4", time = 0.15},
        {key = "Bb4", time = 0.15},
        {key = "C5", time = 0.45},
        -- Mais notas da Megalovania (simplificado)
        {key = "Eb5", time = 0.15},
        {key = "G5", time = 0.15},
        {key = "Bb5", time = 0.15},
        {key = "C6", time = 0.45}
    },
    
    Minecraft = {
        {key = "F4", time = 0.4},
        {key = "G4", time = 0.4},
        {key = "A4", time = 0.4},
        {key = "Bb4", time = 0.4},
        {key = "C5", time = 0.8},
        {key = "Bb4", time = 0.4},
        {key = "A4", time = 0.4},
        {key = "G4", time = 0.4},
        {key = "F4", time = 0.8},
        {key = "Eb4", time = 0.4},
        {key = "D4", time = 0.4},
        {key = "Eb4", time = 0.4},
        {key = "F4", time = 1.2}
    }
}

-- 🔍 ESP CONFIG
local ESP_ENABLED = true
local ESP_COLOR = Color3.fromRGB(255, 0, 0)
local ESP_TRANSPARENCY = 0.5

-- 🎹 FUNÇÕES DO PIANO
local function findPiano()
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name:lower():find("piano") or obj.Name:lower():find("key") then
            if obj:IsA("BasePart") and obj.Parent:FindFirstChild("ClickDetector") then
                PIANO_KEYS = obj.Parent:GetChildren()
                print("🎹 Piano encontrado! " .. #PIANO_KEYS .. " teclas detectadas")
                return true
            end
        end
    end
    return false
end

local function playNote(keyName)
    for _, key in pairs(PIANO_KEYS) do
        if key.Name:upper():find(keyName) or key.Name:lower() == keyName:lower() then
            if key:IsA("BasePart") then
                fireclickdetector(key:FindFirstChild("ClickDetector"))
                key.Color = Color3.fromRGB(255, 255, 0) -- Tecla amarela
                wait(0.05)
                key.Color = Color3.fromRGB(255, 255, 255) -- Volta ao branco
            end
            break
        end
    end
end

local function playSong(songName)
    if not PIANO_KEYS[1] then
        warn("❌ Nenhum piano encontrado!")
        return
    end
    
    currentSong = SONGS[songName]
    if not currentSong then
        print("❌ Música '" .. songName .. "' não encontrada!")
        return
    end
    
    isPlaying = true
    print("🎵 Tocando: " .. songName)
    
    spawn(function()
        for i, note in pairs(currentSong) do
            if not isPlaying then break end
            playNote(note.key)
            wait(note.time * songSpeed)
        end
        isPlaying = false
        print("✅ Música terminada!")
    end)
end

-- 🔍 FUNÇÕES ESP
local ESP_Folder = Drawing.new("Square")
ESP_Folder.Visible = false
ESP_Folder.Color = ESP_COLOR
ESP_Folder.Thickness = 2
ESP_Folder.Transparency = ESP_TRANSPARENCY

local function createESP(part)
    local billboard = Instance.new("BillboardGui")
    billboard.Size = UDim2.new(0, 100, 0, 50)
    billboard.StudsOffset = Vector3.new(0, 3, 0)
    billboard.Parent = part
    
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, 0, 1, 0)
    frame.BackgroundColor3 = ESP_COLOR
    frame.BackgroundTransparency = 0.3
    frame.BorderSizePixel = 2
    frame.BorderColor3 = Color3.fromRGB(255, 255, 255)
    frame.Parent = billboard
    
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.Text = part.Name
    label.TextColor3 = Color3.fromRGB(255, 255, 255)
    label.TextScaled = true
    label.Font = Enum.Font.SourceSansBold
    label.Parent = frame
end

local function toggleESP()
    ESP_ENABLED = not ESP_ENABLED
    print("🔍 ESP: " .. (ESP_ENABLED and "ATIVADO" or "DESATIVADO"))
end

-- 🎮 CONTROLES
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    
    if input.KeyCode == Enum.KeyCode.M then
        playSong("Megalovania")
    elseif input.KeyCode == Enum.KeyCode.N then
        playSong("Minecraft")
    elseif input.KeyCode == Enum.KeyCode.E then
        toggleESP()
    elseif input.KeyCode == Enum.KeyCode.P then
        findPiano()
    end
end)

-- 🔄 LOOP PRINCIPAL
RunService.Heartbeat:Connect(function()
    -- Auto-detect pianos
    if #PIANO_KEYS == 0 then
        findPiano()
    end
    
    -- ESP para pianos
    if ESP_ENABLED then
        for _, obj in pairs(workspace:GetDescendants()) do
            if obj.Name:lower():find("piano") and obj:IsA("BasePart") then
                createESP(obj)
            end
        end
    end
end)

-- 📋 INSTRUÇÕES NO CONSOLE
print("🎹 PIANO PLAYER + ESP CARREGADO!")
print("🔑 TECLAS:")
print("  M = Tocar Megalovania")
print("  N = Tocar Minecraft")
print("  E = Toggle ESP")
print("  P = Procurar Piano")
print("🎵 Aguardando piano...")
