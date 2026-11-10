
-- [[ سكربت moh - Stable 50s FPS Lock ]] --

local CoreGui = game:GetService("CoreGui")
local RunService = game:GetService("RunService")
local Stats = game:GetService("Stats")
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")

-- إزالة أي واجهة قديمة لمنع التداخل
pcall(function()
    if CoreGui:FindFirstChild("moh_Stable50_UI") then
        CoreGui.moh_Stable50_UI:Destroy()
    end
    if gethui and gethui():FindFirstChild("moh_Stable50_UI") then
        gethui().moh_Stable50_UI:Destroy()
    end
end)

-- قفل الفريمات على 60 عشان يثبت على الخمسينات الحقيقية بدون تذبذب
pcall(function()
    if setfpscap then
        setfpscap(60)
    end
end)

-- إنشاء واجهة moh
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "moh_Stable50_UI"
ScreenGui.ResetOnSpawn = false

if syn and syn.protect_gui then
    syn.protect_gui(ScreenGui)
    ScreenGui.Parent = CoreGui
elseif gethui then
    ScreenGui.Parent = gethui()
else
    ScreenGui.Parent = CoreGui
end

local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.AnchorPoint = Vector2.new(0.5, 0)
MainFrame.Position = UDim2.new(0.5, 0, 0, 10)
MainFrame.Size = UDim2.new(0, 320, 0, 125)
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 22)
MainFrame.BorderSizePixel = 0

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 12)
UICorner.Parent = MainFrame

local UIStroke = Instance.new("UIStroke")
UIStroke.Parent = MainFrame
UIStroke.Color = Color3.fromRGB(140, 0, 255)
UIStroke.Thickness = 2

local TitleLabel = Instance.new("TextLabel")
TitleLabel.Parent = MainFrame
TitleLabel.Position = UDim2.new(0, 12, 0, 8)
TitleLabel.Size = UDim2.new(0, 90, 0, 25)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Text = "moh 50s FPS"
TitleLabel.TextColor3 = Color3.fromRGB(180, 100, 255)
TitleLabel.TextSize = 18
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left

local InfoLabel = Instance.new("TextLabel")
InfoLabel.Parent = MainFrame
InfoLabel.Position = UDim2.new(0, 110, 0, 8)
InfoLabel.Size = UDim2.new(1, -122, 0, 25)
InfoLabel.BackgroundTransparency = 1
InfoLabel.Text = "FPS: -- | Ping: -- ms"
InfoLabel.TextColor3 = Color3.fromRGB(240, 240, 240)
InfoLabel.TextSize = 13
InfoLabel.Font = Enum.Font.GothamMedium
InfoLabel.TextXAlignment = Enum.TextXAlignment.Right

-- زر تفعيل وضع الثبات والتحسين
local BoostBtn = Instance.new("TextButton")
BoostBtn.Parent = MainFrame
BoostBtn.Position = UDim2.new(0, 12, 0, 42)
BoostBtn.Size = UDim2.new(1, -24, 0, 32)
BoostBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 50)
BoostBtn.Text = "Stable Mode (OFF)"
BoostBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
BoostBtn.Font = Enum.Font.GothamBold
BoostBtn.TextSize = 13
BoostBtn.AutoButtonColor = false

local BtnCorner1 = Instance.new("UICorner")
BtnCorner1.CornerRadius = UDim.new(0, 8)
BtnCorner1.Parent = BoostBtn

-- زر إخفاء وإظهار الواجهة
local ToggleUIBtn = Instance.new("TextButton")
ToggleUIBtn.Parent = MainFrame
ToggleUIBtn.Position = UDim2.new(0, 12, 0, 82)
ToggleUIBtn.Size = UDim2.new(1, -24, 0, 32)
ToggleUIBtn.BackgroundColor3 = Color3.fromRGB(50, 20, 70)
ToggleUIBtn.Text = "Hide/Show Menu (Open)"
ToggleUIBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleUIBtn.Font = Enum.Font.GothamBold
ToggleUIBtn.TextSize = 13
ToggleUIBtn.AutoButtonColor = false

local BtnCorner2 = Instance.new("UICorner")
BtnCorner2.CornerRadius = UDim.new(0, 8)
BtnCorner2.Parent = ToggleUIBtn

-- عداد حي وسريع
local lastUpdate = tick()
local frameCount = 0

RunService.RenderStepped:Connect(function()
    frameCount = frameCount + 1
    local currentTime = tick()
    
    if currentTime - lastUpdate >= 0.3 then
        local fps = math.floor(frameCount / (currentTime - lastUpdate))
        local ping = 0
        
        pcall(function()
            ping = math.floor(Stats.Network.ServerStatsItem["Data Ping"]:GetValue())
        end)
        
        InfoLabel.Text = string.format("FPS: %d | Ping: %d ms", fps, ping)
        
        -- تلوين العداد باللون الأخضر الممتاز للخمسينات
        if fps >= 50 then
            InfoLabel.TextColor3 = Color3.fromRGB(100, 255, 150)
        elseif fps >= 30 then
            InfoLabel.TextColor3 = Color3.fromRGB(255, 200, 100)
        else
            InfoLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
        end
        
        frameCount = 0
        lastUpdate = currentTime
    end
end)

-- وظيفة زر الثبات
local stableActive = false
BoostBtn.MouseButton1Click:Connect(function()
    stableActive = not stableActive
    if stableActive then
        pcall(function()
            Lighting.GlobalShadows = false
            for _, v in pairs(Workspace:GetDescendants()) do
                if v:IsA("BasePart") then
                    v.Material = Enum.Material.SmoothPlastic
                    v.Reflectance = 0
                elseif v:IsA("Decal") or v:IsA("Texture") then
                    v.Transparency = 1
                end
            end
        end)
        BoostBtn.Text = "Stable Mode: ON ⚡"
        BoostBtn.BackgroundColor3 = Color3.fromRGB(140, 0, 255)
        BoostBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    else
        BoostBtn.Text = "Stable Mode (OFF)"
        BoostBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 50)
        BoostBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
    end
end)

-- زر إخفاء وإظهار الواجهة
local uiVisible = true
ToggleUIBtn.MouseButton1Click:Connect(function()
    uiVisible = not uiVisible
    for _, child in ipairs(MainFrame:GetChildren()) do
        if child ~= ToggleUIBtn and child.ClassName ~= "UICorner" and child.ClassName ~= "UIStroke" then
            child.Visible = uiVisible
        end
    end
    
    if uiVisible then
        MainFrame.Size = UDim2.new(0, 320, 0, 125)
        ToggleUIBtn.Position = UDim2.new(0, 12, 0, 82)
        ToggleUIBtn.Text = "Hide/Show Menu (Open)"
        ToggleUIBtn.BackgroundColor3 = Color3.fromRGB(50, 20, 70)
    else
        MainFrame.Size = UDim2.new(0, 320, 0, 50)
        ToggleUIBtn.Position = UDim2.new(0, 12, 0, 9)
        ToggleUIBtn.Text = "Show Menu (Closed)"
        ToggleUIBtn.BackgroundColor3 = Color3.fromRGB(100, 0, 150)
    end
end)
