-- Advanced Mobile ESP UI v2 (Street Life & Universal)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

-- Global ESP Ayarları
_G.ESP_Settings = {
    Name = false,
    Health = false,
    Distance = false,
    Box = false,
    Size = 14,
    Color = Color3.fromRGB(255, 255, 255),
    IsRGB = false
}

-- Ana UI Kurulumu
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "StreetLifeMobileESP_V2"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
pcall(function() ScreenGui.Parent = game:GetService("CoreGui") end)
if not ScreenGui.Parent then ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui") end

-- SÜRÜKLENME FONKSİYONU (Mobil Dokunmatik Uyumlu)
local function makeDraggable(frame, handle)
    local dragToggle = nil
    local dragStart = nil
    local startPos = nil
    local dragHandle = handle or frame

    dragHandle.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragToggle = true
            dragStart = input.Position
            startPos = frame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragToggle = false
                end
            end)
        end
    end)

    dragHandle.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            if dragToggle then
                local delta = input.Position - dragStart
                frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
            end
        end
    end)
end

-- 1. KÜÇÜK YUVARLAK AÇMA/KAPATMA BUTONU (30x30)
local ToggleBtn = Instance.new("TextButton")
ToggleBtn.Name = "ToggleBtn"
ToggleBtn.Size = UDim2.new(0, 30, 0, 30) -- Tam 30x30 boyutunda
ToggleBtn.Position = UDim2.new(0, 20, 0, 150)
ToggleBtn.BackgroundColor3 = Color3.fromRGB(255, 60, 60)
ToggleBtn.Text = "ESP"
ToggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleBtn.TextSize = 10 -- Küçük boyuta uygun yazı
ToggleBtn.Font = Enum.Font.SourceSansBold
ToggleBtn.Parent = ScreenGui

local ToggleCorner = Instance.new("UICorner")
ToggleCorner.CornerRadius = UDim.new(1, 0)
ToggleCorner.Parent = ToggleBtn

makeDraggable(ToggleBtn)

-- 2. ANA MENÜ PANELİ (Boyut yeni özellikler için büyütüldü)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 260, 0, 420)
MainFrame.Position = UDim2.new(0.5, -130, 0.5, -210)
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
MainFrame.Visible = true
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 12)
MainCorner.Parent = MainFrame

makeDraggable(MainFrame)

-- Menü Başlığı
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 40)
Title.BackgroundTransparency = 1
Title.Text = "STREET LIFE ESP V2"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 16
Title.Font = Enum.Font.SourceSansBold
Title.Parent = MainFrame

-- Düzenleyici
local ListLayout = Instance.new("UIListLayout")
ListLayout.Parent = MainFrame
ListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
ListLayout.SortOrder = Enum.SortOrder.LayoutOrder
ListLayout.Padding = UDim.new(0, 8)

local Spacer = Instance.new("Frame")
Spacer.Size = UDim2.new(1, 0, 0, 35)
Spacer.BackgroundTransparency = 1
Spacer.LayoutOrder = 0
Spacer.Parent = MainFrame

-- Buton Oluşturucu Fonksiyon
local function createToggleBtn(text, layoutOrder, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 220, 0, 36)
    btn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    btn.Text = text .. " [KAPALI]"
    btn.TextColor3 = Color3.fromRGB(200, 200, 200)
    btn.TextSize = 13
    btn.Font = Enum.Font.SourceSansBold
    btn.LayoutOrder = layoutOrder
    btn.Parent = MainFrame

    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 6)
    btnCorner.Parent = btn

    local active = false
    btn.MouseButton1Click:Connect(function()
        active = not active
        if active then
            btn.BackgroundColor3 = Color3.fromRGB(35, 130, 60)
            btn.TextColor3 = Color3.fromRGB(255, 255, 255)
            btn.Text = text .. " [AKTİF]"
        else
            btn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
            btn.TextColor3 = Color3.fromRGB(200, 200, 200)
            btn.Text = text .. " [KAPALI]"
        end
        callback(active)
    end)
end

-- Menü Elemanları
createToggleBtn("İsim ESP", 1, function(val) _G.ESP_Settings.Name = val end)
createToggleBtn("Can ESP", 2, function(val) _G.ESP_Settings.Health = val end)
createToggleBtn("Mesafe ESP", 3, function(val) _G.ESP_Settings.Distance = val end)
createToggleBtn("Kutu ESP (İnce)", 4, function(val) _G.ESP_Settings.Box = val end)

-- 3. KAYDIRMALI YAZI BOYUTU (SLIDER)
local SliderFrame = Instance.new("Frame")
SliderFrame.Size = UDim2.new(0, 220, 0, 45)
SliderFrame.BackgroundTransparency = 1
SliderFrame.LayoutOrder = 5
SliderFrame.Parent = MainFrame

local SliderLabel = Instance.new("TextLabel")
SliderLabel.Size = UDim2.new(1, 0, 0, 15)
SliderLabel.BackgroundTransparency = 1
SliderLabel.Text = "Yazı Boyutu: 14"
SliderLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
SliderLabel.TextSize = 12
SliderLabel.Font = Enum.Font.SourceSansBold
SliderLabel.Parent = SliderFrame

local SliderTrack = Instance.new("Frame")
SliderTrack.Size = UDim2.new(1, 0, 0, 6)
SliderTrack.Position = UDim2.new(0, 0, 0, 25)
SliderTrack.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
SliderTrack.Parent = SliderFrame

local SliderKnob = Instance.new("TextButton")
SliderKnob.Size = UDim2.new(0, 14, 0, 14)
SliderKnob.Position = UDim2.new(0.26, -7, 0.5, -7)
SliderKnob.BackgroundColor3 = Color3.fromRGB(255, 60, 60)
SliderKnob.Text = ""
SliderKnob.Parent = SliderTrack

local KnobCorner = Instance.new("UICorner")
KnobCorner.CornerRadius = UDim.new(1, 0)
KnobCorner.Parent = SliderKnob

local isSliding = false
SliderKnob.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then isSliding = true end
end)

UserInputService.InputChanged:Connect(function(input)
    if isSliding and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local trackPos = SliderTrack.AbsolutePosition.X
        local trackWidth = math.max(SliderTrack.AbsoluteSize.X, 1)
        local percentage = math.clamp((input.Position.X - trackPos) / trackWidth, 0, 1)
        SliderKnob.Position = UDim2.new(percentage, -7, 0.5, -7)
        local newSize = math.round(10 + (percentage * 16))
        _G.ESP_Settings.Size = newSize
        SliderLabel.Text = "Yazı Boyutu: " .. tostring(newSize)
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then isSliding = false end
end)

-- 4. RENK SEÇİM ALANI
local ColorLabel = Instance.new("TextLabel")
ColorLabel.Size = UDim2.new(0, 220, 0, 15)
ColorLabel.BackgroundTransparency = 1
ColorLabel.Text = "ESP Renk Seçimi"
ColorLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
ColorLabel.TextSize = 12
ColorLabel.Font = Enum.Font.SourceSansBold
ColorLabel.LayoutOrder = 6
ColorLabel.Parent = MainFrame

local ColorContainer = Instance.new("Frame")
ColorContainer.Size = UDim2.new(0, 220, 0, 35)
ColorContainer.BackgroundTransparency = 1
ColorContainer.LayoutOrder = 7
ColorContainer.Parent = MainFrame

local ColorLayout = Instance.new("UIListLayout")
ColorLayout.FillDirection = Enum.FillDirection.Horizontal
ColorLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
ColorLayout.Padding = UDim.new(0, 6)
ColorLayout.Parent = ColorContainer

local colorPalette = {
    {Color = Color3.fromRGB(255, 0, 0), IsRGB = false},    -- Kırmızı
    {Color = Color3.fromRGB(0, 120, 255), IsRGB = false},  -- Mavi
    {Color = Color3.fromRGB(0, 255, 100), IsRGB = false},  -- Yeşil
    {Color = Color3.fromRGB(255, 105, 180), IsRGB = false},-- Pembe
    {Color = Color3.fromRGB(255, 220, 0), IsRGB = false},  -- Sarı
    {Color = "RGB", IsRGB = true}                          -- Rengarenk RGB
}

for _, choice in ipairs(colorPalette) do
    local cBtn = Instance.new("TextButton")
    cBtn.Size = UDim2.new(0, 30, 0, 30)
    cBtn.Text = choice.IsRGB and "RGB" or ""
    cBtn.TextSize = 9
    cBtn.Font = Enum.Font.SourceSansBold
    
    if choice.IsRGB then
        cBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        cBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    else
        cBtn.BackgroundColor3 = choice.Color
    end
    
    local cCorner = Instance.new("UICorner")
    cCorner.CornerRadius = UDim.new(1, 0) -- Yuvarlak butonlar
    cCorner.Parent = cBtn
    cBtn.Parent = ColorContainer

    cBtn.MouseButton1Click:Connect(function()
        _G.ESP_Settings.IsRGB = choice.IsRGB
        if not choice.IsRGB then
            _G.ESP_Settings.Color = choice.Color
        end
    end)
end

ToggleBtn.MouseButton1Click:Connect(function() MainFrame.Visible = not MainFrame.Visible end)

-- 5. ESP TAKİP VE OLUŞTURMA SİSTEMİ
local function createESPTrack(player)
    if player == LocalPlayer then return end

    local function setupChar(char)
        local hrp = char:WaitForChild("HumanoidRootPart", 10)
        local head = char:WaitForChild("Head", 10)
        if not hrp or not head then return end
        
        -- Temizlik
        if head:FindFirstChild("MobileESP") then head.MobileESP:Destroy() end
        if hrp:FindFirstChild("MobileBoxESP") then hrp.MobileBoxESP:Destroy() end

        -- YAZI (Yazı ESP)
        local bbg = Instance.new("BillboardGui")
        bbg.Name = "MobileESP"
        bbg.AlwaysOnTop = true
        bbg.Size = UDim2.new(0, 200, 0, 60)
        bbg.StudsOffset = Vector3.new(0, 3.5, 0)
        bbg.Parent = head

        local label = Instance.new("TextLabel")
        label.Name = "ESPLabel"
        label.BackgroundTransparency = 1
        label.Size = UDim2.new(1, 0, 1, 0)
        label.TextColor3 = _G.ESP_Settings.Color
        label.TextStrokeTransparency = 0
        label.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
        label.Font = Enum.Font.SourceSansBold
        label.TextSize = _G.ESP_Settings.Size
        label.Parent = bbg

        -- KUTU (Box ESP - İnce Tasarım)
        local boxBbg = Instance.new("BillboardGui")
        boxBbg.Name = "MobileBoxESP"
        boxBbg.AlwaysOnTop = true
        boxBbg.Size = UDim2.new(4, 0, 5.8, 0) -- Standart R15/R6 karakter boyutlarına tam oturur
        boxBbg.Adornee = hrp
        boxBbg.Parent = hrp

        local boxFrame = Instance.new("Frame")
        boxFrame.Name = "BoxFrame"
        boxFrame.Size = UDim2.new(1, 0, 1, 0)
        boxFrame.BackgroundTransparency = 1
        boxFrame.Parent = boxBbg

        local stroke = Instance.new("UIStroke")
        stroke.Name = "BoxStroke"
        stroke.Thickness = 1.2 -- İnce ve şık çizgi boyutu
        stroke.Color = _G.ESP_Settings.Color
        stroke.Parent = boxFrame
    end

    player.CharacterAdded:Connect(setupChar)
    if player.Character then setupChar(player.Character) end
end

for _, p in ipairs(Players:GetPlayers()) do createESPTrack(p) end
Players.PlayerAdded:Connect(createESPTrack)

-- ANLIK GÜNCELLEME VE RGB DÖNGÜSÜ (RenderStepped)
RunService.RenderStepped:Connect(function()
    -- Gökkuşağı Renk Hesaplaması (Her 4 saniyede bir tam tur döner)
    local rainbowColor = Color3.fromHSV((tick() % 4) / 4, 1, 1)
    local activeColor = _G.ESP_Settings.IsRGB and rainbowColor or _G.ESP_Settings.Color

    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character then
            -- 1. Yazıları Güncelle
            local head = p.Character:FindFirstChild("Head")
            if head then
                local bbg = head:FindFirstChild("MobileESP")
                local label = bbg and bbg:FindFirstChild("ESPLabel")
                if label then
                    label.TextSize = _G.ESP_Settings.Size
                    label.TextColor3 = activeColor
                    
                    local textLine1 = ""
                    local textLine2 = ""
                    
                    if _G.ESP_Settings.Name then textLine1 = p.Name .. " " end
                    
                    if _G.ESP_Settings.Distance and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and p.Character:FindFirstChild("HumanoidRootPart") then
                        local dist = math.round((LocalPlayer.Character.HumanoidRootPart.Position - p.Character.HumanoidRootPart.Position).Magnitude)
                        textLine1 = textLine1 .. "[" .. tostring(dist) .. "m]"
                    end
                    
                    if _G.ESP_Settings.Health and p.Character:FindFirstChild("Humanoid") then
                        textLine2 = "\nHP: " .. math.round(p.Character.Humanoid.Health)
                    end
                    
                    label.Text = textLine1 .. textLine2
                    label.Visible = (_G.ESP_Settings.Name or _G.ESP_Settings.Distance or _G.ESP_Settings.Health)
                end
            end

            -- 2. Kutuyu Güncelle
            local hrp = p.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                local boxBbg = hrp:FindFirstChild("MobileBoxESP")
                local boxFrame = boxBbg and boxBbg:FindFirstChild("BoxFrame")
                local stroke = boxFrame and boxFrame:FindFirstChild("BoxStroke")
                if stroke then
                    stroke.Color = activeColor
                    boxBbg.Enabled = _G.ESP_Settings.Box
                end
            end
        end
    end
end)
