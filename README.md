local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
local camera = workspace.CurrentCamera

for _, name in ipairs({"NexusMod", "UI_Module"}) do
    local old = playerGui:FindFirstChild(name)
    if old then old:Destroy() end
end

local CONFIG = {
    WindowSize = Vector2.new(950, 620),
    MinSize    = Vector2.new(300, 200),
    MaxSize    = Vector2.new(1600, 1000),
    Theme = {
        BgDeep      = Color3.fromRGB(6, 4, 6),
        Panel       = Color3.fromRGB(18, 14, 16),
        Red         = Color3.fromRGB(140, 20, 30),
        RedDark     = Color3.fromRGB(80, 10, 18),
        RedBright   = Color3.fromRGB(180, 30, 45),
        Green       = Color3.fromRGB(60, 160, 90),
        GreenDark   = Color3.fromRGB(30, 90, 50),
        TabIdle     = Color3.fromRGB(22, 16, 18),
        TabHover    = Color3.fromRGB(38, 18, 22),
        Text        = Color3.fromRGB(235, 225, 225),
        TextDim     = Color3.fromRGB(140, 120, 125),
        TextRed     = Color3.fromRGB(220, 90, 100),
        Success     = Color3.fromRGB(100, 200, 130),
        Error       = Color3.fromRGB(220, 70, 80),
        Warning     = Color3.fromRGB(220, 170, 80),
    }
}

local ThemeGradients = {
    Main      = {Color3.fromRGB(90, 15, 25), Color3.fromRGB(15, 5, 8)},
    TitleBar  = {Color3.fromRGB(20, 12, 14), Color3.fromRGB(10, 6, 8)},
    Buttons   = {Color3.fromRGB(180, 30, 45), Color3.fromRGB(80, 10, 18)},
    Category  = {Color3.fromRGB(90, 15, 25), Color3.fromRGB(15, 5, 8)},
}

local function buildGradient(c1, c2)
    return ColorSequence.new(c1, c2)
end

local function corner(inst, r)
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, r or 8)
    c.Parent = inst
    return c
end

local function stroke(inst, color, thick, transp)
    local s = Instance.new("UIStroke")
    s.Color = color
    s.Thickness = thick or 1.5
    s.Transparency = transp or 0
    s.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    s.Parent = inst
    return s
end

local function gradient(inst, seq, rot)
    local g = Instance.new("UIGradient")
    g.Color = seq
    g.Rotation = rot or 45
    g.Parent = inst
    return g
end

local UI = {}
local States = {}
local CatButtons = {}
local CurrentCat = nil

UI.ScreenGui = Instance.new("ScreenGui")
UI.ScreenGui.Name = "NexusMod"
UI.ScreenGui.ResetOnSpawn = false
UI.ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
UI.ScreenGui.IgnoreGuiInset = true
UI.ScreenGui.Parent = playerGui
UI.ScreenGui.Enabled = false

UI.MainFrame = Instance.new("Frame")
UI.MainFrame.Name = "MainFrame"
UI.MainFrame.Size = UDim2.new(0, CONFIG.WindowSize.X, 0, CONFIG.WindowSize.Y)
UI.MainFrame.Position = UDim2.new(0.5, -CONFIG.WindowSize.X/2, 0.5, -CONFIG.WindowSize.Y/2)
UI.MainFrame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
UI.MainFrame.BorderSizePixel = 0
UI.MainFrame.Active = true
UI.MainFrame.Visible = false
UI.MainFrame.Parent = UI.ScreenGui
corner(UI.MainFrame, 12)
UI.MainGrad = gradient(UI.MainFrame, buildGradient(ThemeGradients.Main[1], ThemeGradients.Main[2]), 45)
stroke(UI.MainFrame, CONFIG.Theme.RedBright, 1.5, 0.3)

UI.TitleBar = Instance.new("Frame")
UI.TitleBar.Size = UDim2.new(1, 0, 0, 38)
UI.TitleBar.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
UI.TitleBar.BorderSizePixel = 0
UI.TitleBar.Parent = UI.MainFrame
corner(UI.TitleBar, 12)
UI.TitleGrad = gradient(UI.TitleBar, buildGradient(ThemeGradients.TitleBar[1], ThemeGradients.TitleBar[2]), 90)

UI.TitleFix = Instance.new("Frame")
UI.TitleFix.Size = UDim2.new(1, -4, 0, 16)
UI.TitleFix.Position = UDim2.new(0, 2, 1, -16)
UI.TitleFix.BackgroundColor3 = CONFIG.Theme.BgDeep
UI.TitleFix.BorderSizePixel = 0
UI.TitleFix.ZIndex = 1
UI.TitleFix.Parent = UI.TitleBar

UI.TitleLine = Instance.new("Frame")
UI.TitleLine.Size = UDim2.new(1, -20, 0, 2)
UI.TitleLine.Position = UDim2.new(0, 10, 1, -2)
UI.TitleLine.BackgroundColor3 = CONFIG.Theme.RedBright
UI.TitleLine.BorderSizePixel = 0
UI.TitleLine.ZIndex = 2
UI.TitleLine.Parent = UI.TitleBar
corner(UI.TitleLine, 1)
UI.LineGrad = gradient(UI.TitleLine, buildGradient(CONFIG.Theme.RedBright, CONFIG.Theme.RedDark), 0)

UI.TitleLabel = Instance.new("TextLabel")
UI.TitleLabel.Size = UDim2.new(1, -280, 1, 0)
UI.TitleLabel.Position = UDim2.new(0, 14, 0, 0)
UI.TitleLabel.BackgroundTransparency = 1
UI.TitleLabel.Text = "⚡ NEXUS MOD  •  [K]"
UI.TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
UI.TitleLabel.TextSize = 16
UI.TitleLabel.Font = Enum.Font.GothamBold
UI.TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
UI.TitleLabel.ZIndex = 3
UI.TitleLabel.Parent = UI.TitleBar
gradient(UI.TitleLabel, buildGradient(Color3.fromRGB(255, 130, 140), Color3.fromRGB(180, 30, 45)), 0)

UI.DevLabel = Instance.new("TextLabel")
UI.DevLabel.Size = UDim2.new(0, 240, 1, 0)
UI.DevLabel.Position = UDim2.new(1, -280, 0, 0)
UI.DevLabel.BackgroundTransparency = 1
UI.DevLabel.Text = "White/DEV • AZERTY/DEV"
UI.DevLabel.TextColor3 = CONFIG.Theme.TextDim
UI.DevLabel.TextSize = 11
UI.DevLabel.Font = Enum.Font.Gotham
UI.DevLabel.TextXAlignment = Enum.TextXAlignment.Right
UI.DevLabel.ZIndex = 3
UI.DevLabel.Parent = UI.TitleBar

UI.CloseBtn = Instance.new("TextButton")
UI.CloseBtn.Size = UDim2.new(0, 28, 0, 28)
UI.CloseBtn.Position = UDim2.new(1, -34, 0, 5)
UI.CloseBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
UI.CloseBtn.Text = "✕"
UI.CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
UI.CloseBtn.TextSize = 14
UI.CloseBtn.Font = Enum.Font.GothamBold
UI.CloseBtn.BorderSizePixel = 0
UI.CloseBtn.ZIndex = 3
UI.CloseBtn.Parent = UI.TitleBar
corner(UI.CloseBtn, 8)
gradient(UI.CloseBtn, buildGradient(CONFIG.Theme.RedBright, CONFIG.Theme.RedDark), 90)

UI.CategoryBar = Instance.new("Frame")
UI.CategoryBar.Size = UDim2.new(1, -24, 0, 40)
UI.CategoryBar.Position = UDim2.new(0, 12, 0, 48)
UI.CategoryBar.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
UI.CategoryBar.BorderSizePixel = 0
UI.CategoryBar.Parent = UI.MainFrame
corner(UI.CategoryBar, 8)
gradient(UI.CategoryBar, buildGradient(ThemeGradients.Category[1], ThemeGradients.Category[2]), 90)

UI.CategoryScroll = Instance.new("ScrollingFrame")
UI.CategoryScroll.Size = UDim2.fromScale(1, 1)
UI.CategoryScroll.BackgroundTransparency = 1
UI.CategoryScroll.BorderSizePixel = 0
UI.CategoryScroll.ScrollBarThickness = 0
UI.CategoryScroll.ScrollingDirection = Enum.ScrollingDirection.X
UI.CategoryScroll.CanvasSize = UDim2.new(0, 0, 0, 0)
UI.CategoryScroll.AutomaticCanvasSize = Enum.AutomaticSize.X
UI.CategoryScroll.Parent = UI.CategoryBar

UI.CatLayout = Instance.new("UIListLayout")
UI.CatLayout.FillDirection = Enum.FillDirection.Horizontal
UI.CatLayout.VerticalAlignment = Enum.VerticalAlignment.Center
UI.CatLayout.Padding = UDim.new(0, 6)
UI.CatLayout.Parent = UI.CategoryScroll

UI.CatPad = Instance.new("UIPadding")
UI.CatPad.PaddingLeft = UDim.new(0, 8)
UI.CatPad.PaddingRight = UDim.new(0, 8)
UI.CatPad.Parent = UI.CategoryScroll

UI.ContentArea = Instance.new("Frame")
UI.ContentArea.Size = UDim2.new(1, -24, 1, -145)
UI.ContentArea.Position = UDim2.new(0, 12, 0, 96)
UI.ContentArea.BackgroundTransparency = 1
UI.ContentArea.Parent = UI.MainFrame

local function makeRowBase(parent)
    local f = Instance.new("Frame")
    f.Size = UDim2.new(1, -10, 0, 56)
    f.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    f.BorderSizePixel = 0
    f.Parent = parent
    corner(f, 8)
    gradient(f, buildGradient(ThemeGradients.TitleBar[1], ThemeGradients.TitleBar[2]), 90)
    stroke(f, CONFIG.Theme.RedDark, 1, 0.5)
    return f
end

local function makeRowTitle(f, name, desc)
    local t = Instance.new("TextLabel")
    t.Size = UDim2.new(1, -130, 0, 24)
    t.Position = UDim2.new(0, 14, 0, 6)
    t.BackgroundTransparency = 1
    t.Text = name
    t.TextColor3 = CONFIG.Theme.Text
    t.TextSize = 14
    t.Font = Enum.Font.GothamBold
    t.TextXAlignment = Enum.TextXAlignment.Left
    t.Parent = f

    local d = Instance.new("TextLabel")
    d.Size = UDim2.new(1, -130, 0, 18)
    d.Position = UDim2.new(0, 14, 0, 30)
    d.BackgroundTransparency = 1
    d.Text = desc or ""
    d.TextColor3 = CONFIG.Theme.TextDim
    d.TextSize = 11
    d.Font = Enum.Font.Gotham
    d.TextXAlignment = Enum.TextXAlignment.Left
    d.Parent = f
end

local function createToggle(parent, name, desc, cb)
    local f = makeRowBase(parent)
    makeRowTitle(f, name, desc)

    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0, 110, 0, 32)
    b.Position = UDim2.new(1, -120, 0.5, -16)
    b.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    b.Text = "OFF"
    b.TextColor3 = Color3.fromRGB(255, 255, 255)
    b.TextSize = 12
    b.Font = Enum.Font.GothamBold
    b.BorderSizePixel = 0
    b.Parent = f
    corner(b, 6)
    local g = gradient(b, buildGradient(ThemeGradients.Buttons[1], ThemeGradients.Buttons[2]), 90)

    States[name] = false

    b.MouseButton1Click:Connect(function()
        local a = not States[name]
        States[name] = a
        if a then
            b.Text = "ON"
            b.BackgroundColor3 = CONFIG.Theme.Green
            g.Color = buildGradient(CONFIG.Theme.Green, CONFIG.Theme.GreenDark)
        else
            b.Text = "OFF"
            b.BackgroundColor3 = CONFIG.Theme.Red
            g.Color = buildGradient(ThemeGradients.Buttons[1], ThemeGradients.Buttons[2])
        end
        if cb then cb(a) end
    end)

    return f, b
end

local function createInput(parent, name, desc, default, cb)
    local f = makeRowBase(parent)
    makeRowTitle(f, name, desc)

    local i = Instance.new("TextBox")
    i.Size = UDim2.new(0, 110, 0, 32)
    i.Position = UDim2.new(1, -120, 0.5, -16)
    i.BackgroundColor3 = Color3.fromRGB(10, 6, 8)
    i.Text = tostring(default)
    i.TextColor3 = CONFIG.Theme.Text
    i.TextSize = 13
    i.Font = Enum.Font.GothamBold
    i.BorderSizePixel = 0
    i.Parent = f
    corner(i, 6)
    stroke(i, CONFIG.Theme.RedDark, 1, 0.5)

    i.FocusLost:Connect(function()
        local n = tonumber(i.Text)
        if n and cb then cb(n) end
    end)

    return f, i
end

local function createAction(parent, name, desc, cb)
    local f = makeRowBase(parent)
    makeRowTitle(f, name, desc)

    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0, 110, 0, 32)
    b.Position = UDim2.new(1, -120, 0.5, -16)
    b.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    b.Text = "LANCER"
    b.TextColor3 = Color3.fromRGB(255, 255, 255)
    b.TextSize = 12
    b.Font = Enum.Font.GothamBold
    b.BorderSizePixel = 0
    b.Parent = f
    corner(b, 6)
    gradient(b, buildGradient(ThemeGradients.Buttons[1], ThemeGradients.Buttons[2]), 90)

    b.MouseButton1Click:Connect(function() if cb then cb() end end)
    return f, b
end

local function createDropdown(parent, name, desc, options, default, cb)
    local f = makeRowBase(parent)
    makeRowTitle(f, name, desc)

    local d = Instance.new("TextButton")
    d.Size = UDim2.new(0, 110, 0, 32)
    d.Position = UDim2.new(1, -120, 0.5, -16)
    d.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    d.Text = default
    d.TextColor3 = Color3.fromRGB(255, 255, 255)
    d.TextSize = 11
    d.Font = Enum.Font.GothamBold
    d.BorderSizePixel = 0
    d.Parent = f
    corner(d, 6)
    gradient(d, buildGradient(ThemeGradients.Buttons[1], ThemeGradients.Buttons[2]), 90)

    local idx = 1
    for i, o in ipairs(options) do if o == default then idx = i end end

    d.MouseButton1Click:Connect(function()
        idx = idx + 1
        if idx > #options then idx = 1 end
        d.Text = options[idx]
        if cb then cb(options[idx]) end
    end)

    return f, d
end

local Pages = {}

local function createPage(key, title)
    local p = Instance.new("Frame")
    p.Size = UDim2.fromScale(1, 1)
    p.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    p.BorderSizePixel = 0
    p.Visible = false
    p.Parent = UI.ContentArea
    corner(p, 10)
    gradient(p, buildGradient(CONFIG.Theme.Panel, CONFIG.Theme.BgDeep), 135)

    local h = Instance.new("TextLabel")
    h.Size = UDim2.new(1, 0, 0, 36)
    h.BackgroundTransparency = 1
    h.Text = title
    h.TextColor3 = CONFIG.Theme.TextRed
    h.TextSize = 15
    h.Font = Enum.Font.GothamBold
    h.Parent = p

    local s = Instance.new("ScrollingFrame")
    s.Size = UDim2.new(1, -20, 1, -46)
    s.Position = UDim2.new(0, 10, 0, 40)
    s.BackgroundTransparency = 1
    s.BorderSizePixel = 0
    s.ScrollBarThickness = 4
    s.ScrollBarImageColor3 = CONFIG.Theme.RedBright
    s.CanvasSize = UDim2.new(0, 0, 0, 0)
    s.AutomaticCanvasSize = Enum.AutomaticSize.Y
    s.Parent = p

    local l = Instance.new("UIListLayout")
    l.Padding = UDim.new(0, 8)
    l.SortOrder = Enum.SortOrder.LayoutOrder
    l.Parent = s

    Pages[key] = {frame = p, scroll = s}
end

createPage("move", "🏃 MOUVEMENT")
createPage("vision", "👁️ VISION")
createPage("aimbot", "🎯 AIMBOT")
createPage("troll", "😈 TROLL")
createPage("player", "👤 JOUEUR")
createPage("kick", "🚫 KICK")
createPage("executor", "💻 EXECUTOR")
createPage("settings", "⚙️ PARAMÈTRES")
createPage("server", "🌐 SERVEUR")

local MoveScroll = Pages.move.scroll
local VisionScroll = Pages.vision.scroll
local AimbotScroll = Pages.aimbot.scroll
local TrollScroll = Pages.troll.scroll
local PlayerScroll = Pages.player.scroll
local KickScroll = Pages.kick.scroll
local ExecutorScroll = Pages.executor.scroll
local SettingsScroll = Pages.settings.scroll
local ServerScroll = Pages.server.scroll

local cachedChar, cachedHum, cachedRoot

local function getChar()
    if cachedChar and cachedChar.Parent then return cachedChar end
    cachedChar = player.Character
    cachedHum = nil
    cachedRoot = nil
    return cachedChar
end

local function getHumanoid()
    local c = getChar()
    if not c then return nil end
    if cachedHum and cachedHum.Parent then return cachedHum end
    cachedHum = c:FindFirstChildOfClass("Humanoid")
    return cachedHum
end

local function getRoot()
    local c = getChar()
    if not c then return nil end
    if cachedRoot and cachedRoot.Parent then return cachedRoot end
    cachedRoot = c:FindFirstChild("HumanoidRootPart")
    return cachedRoot
end

player.CharacterAdded:Connect(function(c)
    cachedChar = c
    cachedHum = nil
    cachedRoot = nil
end)

local lastAction = {}
local function throttle(key, cd)
    local now = tick()
    if lastAction[key] and now - lastAction[key] < cd then return false end
    lastAction[key] = now
    return true
end

local selectedPlayer = nil

local function applyTheme(color1, color2)
    ThemeGradients.Main = {color1, color2}
    ThemeGradients.TitleBar = {color2, Color3.new(
        math.max(0, color2.R * 0.5),
        math.max(0, color2.G * 0.5),
        math.max(0, color2.B * 0.5)
    )}
    ThemeGradients.Buttons = {color1, color2}
    ThemeGradients.Category = {color1, color2}

    if UI.MainGrad then UI.MainGrad.Color = buildGradient(color1, color2) end
    if UI.TitleGrad then UI.TitleGrad.Color = buildGradient(color2, Color3.new(
        math.max(0, color2.R * 0.5),
        math.max(0, color2.G * 0.5),
        math.max(0, color2.B * 0.5)
    )) end
    if UI.LineGrad then UI.LineGrad.Color = buildGradient(color1, color2) end

    local tGrad = UI.TitleLabel:FindFirstChildOfClass("UIGradient")
    if tGrad then
        tGrad.Color = buildGradient(
            Color3.new(math.min(1, color1.R + 0.3), math.min(1, color1.G + 0.3), math.min(1, color1.B + 0.3)),
            color2
        )
    end

    local catGrad = UI.CategoryBar:FindFirstChildOfClass("UIGradient")
    if catGrad then catGrad.Color = buildGradient(color1, color2) end

    for catId, btn in pairs(CatButtons) do
        local g = btn:FindFirstChildOfClass("UIGradient")
        if g then
            if CurrentCat == catId then
                g.Color = buildGradient(color1, color2)
            else
                g.Color = buildGradient(CONFIG.Theme.TabIdle, CONFIG.Theme.TabIdle)
            end
        end
    end
end

local flyConn, flyBV, flyBG = nil, nil, nil
local flyEnabled = false
local FLY_SPEED = 50

local function startFly()
    if flyEnabled then return end
    local char = getChar()
    if not char then return end
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return end
    flyEnabled = true
    local hum = char:FindFirstChildOfClass("Humanoid")
    if hum then hum.PlatformStand = true end

    flyBV = Instance.new("BodyVelocity")
    flyBV.MaxForce = Vector3.new(9e9, 9e9, 9e9)
    flyBV.Velocity = Vector3.zero
    flyBV.P = 1250
    flyBV.Parent = root

    flyBG = Instance.new("BodyGyro")
    flyBG.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
    flyBG.P = 10000
    flyBG.D = 500
    flyBG.CFrame = root.CFrame
    flyBG.Parent = root

    flyConn = RunService.RenderStepped:Connect(function()
        local cam = workspace.CurrentCamera
        if not cam or not flyBV or not flyBV.Parent or not flyBG or not flyBG.Parent then return end
        local speed = FLY_SPEED
        if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then speed *= 2 end
        local fwd = cam.CFrame.LookVector
        local rgt = cam.CFrame.RightVector
        local mv = Vector3.zero
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then mv += fwd end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then mv -= fwd end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then mv += rgt end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then mv -= rgt end
        if UserInputService:IsKeyDown(Enum.KeyCode.Space) then mv += Vector3.yAxis end
        if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then mv -= Vector3.yAxis end
        flyBV.Velocity = mv.Magnitude > 0 and mv.Unit * speed or Vector3.zero
        flyBG.CFrame = CFrame.new(root.Position) * (cam.CFrame - cam.CFrame.Position)
    end)
end

local function stopFly()
    if not flyEnabled then return end
    flyEnabled = false
    if flyConn then flyConn:Disconnect() flyConn = nil end
    if flyBV then flyBV:Destroy() flyBV = nil end
    if flyBG then flyBG:Destroy() flyBG = nil end
    local char = getChar()
    if char then
        local hum = char:FindFirstChildOfClass("Humanoid")
        if hum then hum.PlatformStand = false end
    end
end

local function toggleFly() if flyEnabled then stopFly() else startFly() end end

local function setSpeed(v) local h = getHumanoid() if h then h.WalkSpeed = math.clamp(v, 16, 100) end end
local function setJump(v) local h = getHumanoid() if h then h.UseJumpPower = true; h.JumpPower = math.clamp(v, 50, 150) end end
local function setGravity(v) Workspace.Gravity = v end

local noclipConn
local function toggleNoclip(enabled)
    if enabled then
        noclipConn = RunService.Stepped:Connect(function()
            local c = getChar()
            if not c then return end
            for _, p in ipairs(c:GetDescendants()) do
                if p:IsA("BasePart") then p.CanCollide = false end
            end
        end)
    elseif noclipConn then
        noclipConn:Disconnect()
        noclipConn = nil
    end
end

local infJumpConn
local function toggleInfJump(enabled)
    if enabled then
        infJumpConn = UserInputService.JumpRequest:Connect(function()
            local h = getHumanoid()
            if h then h:ChangeState(Enum.HumanoidStateType.Jumping) end
        end)
    elseif infJumpConn then
        infJumpConn:Disconnect()
        infJumpConn = nil
    end
end

local invisLoopConn = nil
local invisEnabled = false

local function makeInvisible(character)
    if not character then return end
    for _, part in ipairs(character:GetDescendants()) do
        if part:IsA("BasePart") or part:IsA("Decal") or part:IsA("Texture") then
            if not part:GetAttribute("NexusOrigTrans") then
                part:SetAttribute("NexusOrigTrans", part.Transparency)
            end
            part.Transparency = 1
            part.LocalTransparencyModifier = 1
        end
        if part:IsA("BillboardGui") or part:IsA("SurfaceGui") then
            pcall(function() part.Enabled = false end)
        end
    end
    local hum = character:FindFirstChildOfClass("Humanoid")
    if hum then
        for _, acc in ipairs(hum:GetChildren()) do
            if acc:IsA("Accessory") then
                local handle = acc:FindFirstChild("Handle")
                if handle then
                    if not handle:GetAttribute("NexusOrigTrans") then
                        handle:SetAttribute("NexusOrigTrans", handle.Transparency)
                    end
                    handle.Transparency = 1
                    handle.LocalTransparencyModifier = 1
                end
            end
        end
    end
end

local function makeVisible(character)
    if not character then return end
    for _, part in ipairs(character:GetDescendants()) do
        if part:IsA("BasePart") or part:IsA("Decal") or part:IsA("Texture") then
            local orig = part:GetAttribute("NexusOrigTrans")
            if orig ~= nil then
                part.Transparency = orig
                part:SetAttribute("NexusOrigTrans", nil)
            else
                part.Transparency = 0
            end
            part.LocalTransparencyModifier = 0
        end
        if part:IsA("BillboardGui") or part:IsA("SurfaceGui") then
            pcall(function() part.Enabled = true end)
        end
    end
    local hum = character:FindFirstChildOfClass("Humanoid")
    if hum then
        for _, acc in ipairs(hum:GetChildren()) do
            if acc:IsA("Accessory") then
                local handle = acc:FindFirstChild("Handle")
                if handle then
                    local orig = handle:GetAttribute("NexusOrigTrans")
                    handle.Transparency = orig ~= nil and orig or 0
                    handle.LocalTransparencyModifier = 0
                    handle:SetAttribute("NexusOrigTrans", nil)
                end
            end
        end
    end
end

local function startInvisLoop(character)
    if invisLoopConn then invisLoopConn:Disconnect() end
    invisLoopConn = RunService.RenderStepped:Connect(function()
        if not invisEnabled then return end
        if not character or not character.Parent then return end
        for _, part in ipairs(character:GetDescendants()) do
            if part:IsA("BasePart") then
                if part.Transparency < 1 then part.Transparency = 1 end
                part.LocalTransparencyModifier = 1
            end
        end
    end)
end

local function toggleInvisibleSelf(enabled)
    invisEnabled = enabled
    local char = getChar()
    if not char then return end
    if enabled then
        makeInvisible(char)
        startInvisLoop(char)
    else
        if invisLoopConn then invisLoopConn:Disconnect() invisLoopConn = nil end
        makeVisible(char)
    end
end

player.CharacterAdded:Connect(function(c)
    if invisEnabled then
        task.wait(0.5)
        makeInvisible(c)
        startInvisLoop(c)
    end
end)

local tpTool = nil
local tpConnection = nil
local tpActive = false
local tpIndicator = nil

local function createTPTool()
    if tpTool then tpTool:Destroy() tpTool = nil end
    if tpIndicator then tpIndicator:Destroy() tpIndicator = nil end

    local backpack = player:FindFirstChild("Backpack")
    if not backpack then return nil end

    local tool = Instance.new("Tool")
    tool.Name = "TP Tool"
    tool.RequiresHandle = false
    tool.CanBeDropped = false
    tool.ToolTip = "Clique pour te téléporter"

    tool.Equipped:Connect(function(mouse)
        tpActive = true
        if not tpIndicator then
            tpIndicator = Instance.new("Part")
            tpIndicator.Shape = Enum.PartType.Cylinder
            tpIndicator.Size = Vector3.new(0.2, 4, 4)
            tpIndicator.Anchored = true
            tpIndicator.CanCollide = false
            tpIndicator.Transparency = 0.5
            tpIndicator.Color = Color3.fromRGB(180, 30, 45)
            tpIndicator.Material = Enum.Material.Neon
            tpIndicator.Parent = workspace
        end
        tpConnection = RunService.RenderStepped:Connect(function()
            if not tpActive or not tpIndicator then return end
            local hit = mouse.Hit
            if hit then
                tpIndicator.CFrame = CFrame.new(hit.Position + Vector3.new(0, 0.1, 0)) * CFrame.Angles(0, 0, math.rad(90))
            end
        end)
        mouse.Button1Down:Connect(function()
            if not tpActive then return end
            local root = getRoot()
            if not root then return end
            local hit = mouse.Hit
            if hit then
                root.CFrame = CFrame.new(hit.Position + Vector3.new(0, 3, 0))
            end
        end)
        mouse.Button2Down:Connect(function()
            if not tpActive then return end
            local root = getRoot()
            if not root then return end
            local hit = mouse.Hit
            if hit then
                root.CFrame = CFrame.new(hit.Position + Vector3.new(0, 3, 0))
            end
        end)
    end)

    tool.Unequipped:Connect(function()
        tpActive = false
        if tpConnection then tpConnection:Disconnect() tpConnection = nil end
        if tpIndicator then tpIndicator:Destroy() tpIndicator = nil end
    end)

    tool.Parent = backpack
    tpTool = tool
    return tool
end

local function removeTPTool()
    if tpConnection then tpConnection:Disconnect() tpConnection = nil end
    if tpIndicator then tpIndicator:Destroy() tpIndicator = nil end
    if tpTool then tpTool:Destroy() tpTool = nil end
    local char = getChar()
    if char then
        local t = char:FindFirstChild("TP Tool")
        if t then t:Destroy() end
    end
end

local espObjects = {}
local espConn

local function createESP(target)
    if not target.Character then return end
    local root = target.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    local existing = espObjects[target]
    if existing and existing.Parent then
        existing.Adornee = root
        return
    end
    local box = Instance.new("BoxHandleAdornment")
    box.Name = "NexusESP"
    box.Adornee = root
    box.AlwaysOnTop = true
    box.ZIndex = 5
    box.Size = Vector3.new(2.5, 6, 2.5)
    box.Transparency = 0.5
    box.Color3 = Color3.fromRGB(220, 50, 60)
    box.Parent = root
    espObjects[target] = box
end

local function toggleESP(enabled)
    if enabled then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= player then createESP(p) end
        end
        espConn = Players.PlayerAdded:Connect(function(p)
            if p ~= player then
                p.CharacterAdded:Connect(function()
                    task.wait(0.5)
                    if States["ESP Joueurs"] then createESP(p) end
                end)
            end
        end)
    else
        if espConn then espConn:Disconnect() espConn = nil end
        for _, obj in pairs(espObjects) do obj:Destroy() end
        espObjects = {}
    end
end

local originalLighting = {
    Ambient = Lighting.Ambient,
    OutdoorAmbient = Lighting.OutdoorAmbient,
    Brightness = Lighting.Brightness,
    ClockTime = Lighting.ClockTime,
}

local function toggleFullbright(enabled)
    if enabled then
        Lighting.Ambient = Color3.fromRGB(200, 200, 210)
        Lighting.OutdoorAmbient = Color3.fromRGB(200, 200, 210)
        Lighting.Brightness = 1.8
    else
        Lighting.Ambient = originalLighting.Ambient
        Lighting.OutdoorAmbient = originalLighting.OutdoorAmbient
        Lighting.Brightness = originalLighting.Brightness
        Lighting.ClockTime = originalLighting.ClockTime
    end
end

local function setFOV(v)
    if camera then camera.FieldOfView = math.clamp(v, 60, 110) end
end

local Aim = {
    Enabled = false, Camlock = false, WallCheck = true, TeamCheck = true,
    Smoothness = 0.5, FOV = 100, TargetPart = "Head", MaxDistance = 300,
    TriggerKey = Enum.UserInputType.MouseButton2, ShowFOV = false, Prediction = 0,
}

local aimConn, curTarget, fovCircle

local function makeFOV()
    if fovCircle then fovCircle:Destroy() end
    fovCircle = Instance.new("Frame")
    fovCircle.AnchorPoint = Vector2.new(0.5, 0.5)
    fovCircle.BackgroundTransparency = 1
    fovCircle.BorderSizePixel = 0
    fovCircle.Size = UDim2.new(0, Aim.FOV * 2, 0, Aim.FOV * 2)
    fovCircle.Position = UDim2.new(0.5, 0, 0.5, 0)
    fovCircle.ZIndex = 998
    fovCircle.Visible = false
    fovCircle.Parent = UI.ScreenGui
    corner(fovCircle, 9999)
    stroke(fovCircle, CONFIG.Theme.RedBright, 1.5, 0.4)
end

local function updateFOV()
    if not fovCircle then return end
    fovCircle.Size = UDim2.new(0, Aim.FOV * 2, 0, Aim.FOV * 2)
    fovCircle.Visible = Aim.ShowFOV and Aim.Enabled
end

local function getTargetPoint(c)
    return c:FindFirstChild(Aim.TargetPart) or c:FindFirstChild("Head") or c:FindFirstChild("HumanoidRootPart")
end

local function validTarget(p, myRoot)
    if p == player then return false end
    local c = p.Character
    if not c then return false end
    local r = c:FindFirstChild("HumanoidRootPart")
    if not r then return false end
    local h = c:FindFirstChildOfClass("Humanoid")
    if not h or h.Health <= 0 then return false end
    if (r.Position - myRoot.Position).Magnitude > Aim.MaxDistance then return false end
    if Aim.TeamCheck and p.Team and player.Team and p.Team == player.Team then return false end
    if Aim.WallCheck then
        local params = RaycastParams.new()
        params.FilterDescendantsInstances = {player.Character}
        params.FilterType = Enum.RaycastFilterType.Exclude
        local res = workspace:Raycast(camera.CFrame.Position, r.Position - camera.CFrame.Position, params)
        if res and not res.Instance:IsDescendantOf(c) then return false end
    end
    return true
end

local function findTarget(myRoot)
    local best, bestDist = nil, Aim.FOV
    local cx, cy = camera.ViewportSize.X * 0.5, camera.ViewportSize.Y * 0.5
    for _, p in ipairs(Players:GetPlayers()) do
        if validTarget(p, myRoot) then
            local part = getTargetPoint(p.Character)
            if part then
                local sp, on = camera:WorldToViewportPoint(part.Position)
                if on then
                    local dx, dy = sp.X - cx, sp.Y - cy
                    local d = math.sqrt(dx*dx + dy*dy)
                    if d < bestDist then best, bestDist = p, d end
                end
            end
        end
    end
    return best
end

local function aimAt(target)
    local c = target.Character
    if not c then return end
    local p = getTargetPoint(c)
    if not p then return end
    local pos = p.Position
    if Aim.Prediction > 0 then
        local h = c:FindFirstChildOfClass("Humanoid")
        if h and h.RootPart then pos += h.RootPart.Velocity * (Aim.Prediction / 100) end
    end
    local curCF = camera.CFrame
    local tgtCF = CFrame.lookAt(curCF.Position, pos)
    camera.CFrame = curCF:Lerp(tgtCF, math.clamp(1 - Aim.Smoothness, 0.05, 1))
end

local function isAiming()
    local k = Aim.TriggerKey
    if k == Enum.UserInputType.MouseButton1 then return UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton1)
    elseif k == Enum.UserInputType.MouseButton2 then return UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2)
    elseif k == Enum.KeyCode.E then return UserInputService:IsKeyDown(Enum.KeyCode.E)
    elseif k == Enum.KeyCode.Q then return UserInputService:IsKeyDown(Enum.KeyCode.Q)
    elseif k == Enum.KeyCode.LeftAlt then return UserInputService:IsKeyDown(Enum.KeyCode.LeftAlt) end
    return true
end

local aimFrame = 0
local function aimLoop()
    if not Aim.Enabled or not isAiming() then curTarget = nil; return end
    aimFrame += 1
    if aimFrame % 3 ~= 0 then return end
    local myRoot = getRoot()
    if not myRoot then return end
    local t = findTarget(myRoot)
    if t then
        curTarget = t
        if Aim.Camlock then aimAt(t) end
    else
        curTarget = nil
    end
end

local function startAim()
    if aimConn then return end
    aimConn = RunService.Heartbeat:Connect(aimLoop)
end

local function stopAim()
    if aimConn then aimConn:Disconnect() aimConn = nil end
    curTarget = nil
end

local function kickPlayer(target)
    if not target or target == player then return end
    if not throttle("kick_" .. target.Name, 2) then return end
    if target.Character then
        local h = target.Character:FindFirstChildOfClass("Humanoid")
        if h then
            pcall(function()
                h:ChangeState(Enum.HumanoidStateType.Dead)
                h.Health = 0
            end)
            pcall(function()
                h.Sit = true
                local r = target.Character:FindFirstChild("HumanoidRootPart")
                if r then r.CFrame = CFrame.new(0, -500, 0) end
            end)
        end
    end
end

local function flingTarget(targetRoot)
    if not targetRoot or not targetRoot.Parent then return end
    local part = Instance.new("Part")
    part.Name = "Touched"
    part.Size = Vector3.new(2, 2, 2)
    part.Transparency = 1
    part.CanCollide = false
    part.CFrame = targetRoot.CFrame
    part.Parent = workspace
    local att = Instance.new("Attachment")
    att.Parent = part
    local vel = Instance.new("LinearVelocity")
    vel.Attachment0 = att
    vel.MaxForce = math.huge
    vel.VectorVelocity = Vector3.new(math.random(-700, 700), math.random(500, 1000), math.random(-700, 700))
    vel.Parent = part
    local weld = Instance.new("WeldConstraint")
    weld.Part0 = part
    weld.Part1 = targetRoot
    weld.Parent = part
    task.delay(0.4, function() if part then part:Destroy() end end)
end

local function getNearestPlayer(maxDist)
    local myRoot = getRoot()
    if not myRoot then return nil, nil end
    maxDist = maxDist or 500
    local np, nr, nd = nil, nil, maxDist
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= player and p.Character then
            local r = p.Character:FindFirstChild("HumanoidRootPart")
            if r then
                local d = (r.Position - myRoot.Position).Magnitude
                if d < nd then np, nr, nd = p, r, d end
            end
        end
    end
    return np, nr
end

local function flingNearest()
    if not throttle("fling", 1) then return end
    local np, nr = getNearestPlayer(500)
    if not np or not nr then return end
    for i = 1, 5 do
        task.spawn(function() flingTarget(nr) end)
    end
end

local floatingTargets = {}
local function startFloat(target)
    if not target or not target.Character then return end
    if floatingTargets[target] then return end
    local root = target.Character:FindFirstChild("HumanoidRootPart")
    if not root then return end
    local bv = Instance.new("BodyVelocity")
    bv.MaxForce = Vector3.new(9e9, 9e9, 9e9)
    bv.Velocity = Vector3.new(0, 30, 0)
    bv.P = 5000
    bv.Parent = root
    local bg = Instance.new("BodyGyro")
    bg.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
    bg.P = 10000
    bg.D = 500
    bg.CFrame = root.CFrame
    bg.Parent = root
    floatingTargets[target] = {bv = bv, bg = bg}
end

local function stopFloat(target)
    if not floatingTargets[target] then return end
    local d = floatingTargets[target]
    if d.bv then d.bv:Destroy() end
    if d.bg then d.bg:Destroy() end
    floatingTargets[target] = nil
end

local function playSound(id, pitch)
    local s = Instance.new("Sound")
    s.SoundId = id
    s.Volume = 3
    s.PlaybackSpeed = pitch or 1
    s.Parent = getRoot() or workspace
    s:Play()
    task.delay(5, function() s:Destroy() end)
end

local function healSelf()
    local h = getHumanoid()
    if h then h.Health = h.MaxHealth end
end

local function teleportToCursor()
    local m = player:GetMouse()
    local r = getRoot()
    if r and m.Hit then
        r.CFrame = CFrame.new(m.Hit.Position + Vector3.new(0, 3, 0))
    end
end

local function rejoin()
    game:GetService("TeleportService"):Teleport(game.PlaceId, player)
end

local followConn = nil
local followTarget = nil

local function stopFollow()
    if followConn then
        followConn:Disconnect()
        followConn = nil
    end
    followTarget = nil
    local hum = getHumanoid()
    if hum then hum.PlatformStand = false end
end

local function startFollow(target)
    if not target or not target.Character then return end
    stopFollow()
    followTarget = target

    followConn = RunService.Heartbeat:Connect(function()
        local myRoot = getRoot()
        if not myRoot then return end
        if not followTarget or not followTarget.Character then return end
        local targetRoot = followTarget.Character:FindFirstChild("HumanoidRootPart")
        if not targetRoot then return end
        local targetPos = targetRoot.Position
        local behindPos = targetPos - (targetRoot.CFrame.LookVector * 3)
        myRoot.CFrame = CFrame.new(behindPos, targetPos)
        local hum = getHumanoid()
        if hum then hum.PlatformStand = true end
    end)
end

player.CharacterRemoving:Connect(function()
    stopFollow()
end)

local function openPlayerPicker(title, onSelect)
    local existing = UI.ScreenGui:FindFirstChild("PlayerPicker")
    if existing then existing:Destroy() end

    local picker = Instance.new("Frame")
    picker.Name = "PlayerPicker"
    picker.Size = UDim2.new(0, 340, 0, 420)
    picker.Position = UDim2.new(0.5, -170, 0.5, -210)
    picker.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    picker.BorderSizePixel = 0
    picker.ZIndex = 200
    picker.Parent = UI.ScreenGui
    corner(picker, 12)
    gradient(picker, buildGradient(ThemeGradients.Main[1], ThemeGradients.Main[2]), 45)
    stroke(picker, CONFIG.Theme.RedBright, 2, 0)

    local header = Instance.new("Frame")
    header.Size = UDim2.new(1, 0, 0, 40)
    header.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    header.BorderSizePixel = 0
    header.ZIndex = 201
    header.Parent = picker
    corner(header, 12)
    gradient(header, buildGradient(ThemeGradients.TitleBar[1], ThemeGradients.TitleBar[2]), 90)

    local headerFix = Instance.new("Frame")
    headerFix.Size = UDim2.new(1, -4, 0, 15)
    headerFix.Position = UDim2.new(0, 2, 1, -15)
    headerFix.BackgroundColor3 = Color3.fromRGB(12, 8, 10)
    headerFix.BorderSizePixel = 0
    headerFix.ZIndex = 202
    headerFix.Parent = header

    local titleLbl = Instance.new("TextLabel")
    titleLbl.Size = UDim2.new(1, -50, 1, 0)
    titleLbl.Position = UDim2.new(0, 14, 0, 0)
    titleLbl.BackgroundTransparency = 1
    titleLbl.Text = title
    titleLbl.TextColor3 = CONFIG.Theme.TextRed
    titleLbl.TextSize = 15
    titleLbl.Font = Enum.Font.GothamBold
    titleLbl.TextXAlignment = Enum.TextXAlignment.Left
    titleLbl.ZIndex = 203
    titleLbl.Parent = header

    local closeP = Instance.new("TextButton")
    closeP.Size = UDim2.new(0, 26, 0, 26)
    closeP.Position = UDim2.new(1, -32, 0, 7)
    closeP.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    closeP.Text = "✕"
    closeP.TextColor3 = Color3.fromRGB(255, 255, 255)
    closeP.TextSize = 12
    closeP.Font = Enum.Font.GothamBold
    closeP.BorderSizePixel = 0
    closeP.ZIndex = 203
    closeP.Parent = header
    corner(closeP, 6)
    gradient(closeP, buildGradient(ThemeGradients.Buttons[1], ThemeGradients.Buttons[2]), 90)

    closeP.MouseButton1Click:Connect(function() picker:Destroy() end)

    local scroll = Instance.new("ScrollingFrame")
    scroll.Size = UDim2.new(1, -20, 1, -55)
    scroll.Position = UDim2.new(0, 10, 0, 45)
    scroll.BackgroundTransparency = 1
    scroll.BorderSizePixel = 0
    scroll.ScrollBarThickness = 4
    scroll.ScrollBarImageColor3 = CONFIG.Theme.RedBright
    scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
    scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
    scroll.ZIndex = 201
    scroll.Parent = picker

    local layout = Instance.new("UIListLayout")
    layout.Padding = UDim.new(0, 6)
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout.Parent = scroll

    local others = {}
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= player then others[#others + 1] = p end
    end

    if #others == 0 then
        local empty = Instance.new("TextLabel")
        empty.Size = UDim2.new(1, 0, 0, 60)
        empty.BackgroundTransparency = 1
        empty.Text = "Aucun joueur disponible"
        empty.TextColor3 = CONFIG.Theme.TextDim
        empty.TextSize = 13
        empty.Font = Enum.Font.Gotham
        empty.ZIndex = 202
        empty.Parent = scroll
        return
    end

    for _, p in ipairs(others) do
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(1, -6, 0, 42)
        btn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        btn.Text = ""
        btn.AutoButtonColor = false
        btn.BorderSizePixel = 0
        btn.ZIndex = 202
        btn.Parent = scroll
        corner(btn, 6)
        gradient(btn, buildGradient(ThemeGradients.TitleBar[1], ThemeGradients.TitleBar[2]), 90)

        local nameLbl = Instance.new("TextLabel")
        nameLbl.Size = UDim2.new(1, -20, 1, 0)
        nameLbl.Position = UDim2.new(0, 12, 0, 0)
        nameLbl.BackgroundTransparency = 1
        nameLbl.Text = p.Name
        nameLbl.TextColor3 = CONFIG.Theme.Text
        nameLbl.TextSize = 13
        nameLbl.Font = Enum.Font.GothamBold
        nameLbl.TextXAlignment = Enum.TextXAlignment.Left
        nameLbl.TextTruncate = Enum.TextTruncate.AtEnd
        nameLbl.ZIndex = 203
        nameLbl.Parent = btn

        btn.MouseButton1Click:Connect(function()
            picker:Destroy()
            if onSelect then onSelect(p) end
        end)
    end
end

local ExecStatus = nil

local ExecHeader = Instance.new("TextLabel")
ExecHeader.Size = UDim2.new(1, -20, 0, 24)
ExecHeader.BackgroundTransparency = 1
ExecHeader.Text = "💻 MINI EXECUTOR  •  Clique dans la zone pour écrire  •  [Ctrl+Entrée] = exécuter"
ExecHeader.TextColor3 = CONFIG.Theme.TextRed
ExecHeader.TextSize = 12
ExecHeader.Font = Enum.Font.GothamBold
ExecHeader.TextXAlignment = Enum.TextXAlignment.Left
ExecHeader.LayoutOrder = -10
ExecHeader.Parent = ExecutorScroll

local ExecContainer = Instance.new("Frame")
ExecContainer.Size = UDim2.new(1, -10, 0, 500)
ExecContainer.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
ExecContainer.BorderSizePixel = 0
ExecContainer.LayoutOrder = 0
ExecContainer.Parent = ExecutorScroll
corner(ExecContainer, 8)
gradient(ExecContainer, buildGradient(CONFIG.Theme.Panel, CONFIG.Theme.BgDeep), 135)

local ExecEditorArea = Instance.new("Frame")
ExecEditorArea.Size = UDim2.new(1, -16, 1, -70)
ExecEditorArea.Position = UDim2.new(0, 8, 0, 8)
ExecEditorArea.BackgroundColor3 = Color3.fromRGB(8, 5, 6)
ExecEditorArea.BorderSizePixel = 0
ExecEditorArea.ClipsDescendants = true
ExecEditorArea.Parent = ExecContainer
corner(ExecEditorArea, 6)

local ExecGutter = Instance.new("Frame")
ExecGutter.Size = UDim2.new(0, 40, 1, 0)
ExecGutter.BackgroundColor3 = Color3.fromRGB(12, 8, 10)
ExecGutter.BorderSizePixel = 0
ExecGutter.Parent = ExecEditorArea

local ExecGutterBorder = Instance.new("Frame")
ExecGutterBorder.Size = UDim2.new(0, 1, 1, 0)
ExecGutterBorder.Position = UDim2.new(1, -1, 0, 0)
ExecGutterBorder.BackgroundColor3 = CONFIG.Theme.RedDark
ExecGutterBorder.BorderSizePixel = 0
ExecGutterBorder.Parent = ExecGutter

local ExecLineNums = Instance.new("TextLabel")
ExecLineNums.Size = UDim2.new(1, -6, 1, -12)
ExecLineNums.Position = UDim2.new(0, 0, 0, 6)
ExecLineNums.BackgroundTransparency = 1
ExecLineNums.Text = "1"
ExecLineNums.TextColor3 = Color3.fromRGB(100, 80, 85)
ExecLineNums.Font = Enum.Font.Code
ExecLineNums.TextSize = 13
ExecLineNums.TextXAlignment = Enum.TextXAlignment.Right
ExecLineNums.TextYAlignment = Enum.TextYAlignment.Top
ExecLineNums.Parent = ExecGutter

local ExecTextBox = Instance.new("TextBox")
ExecTextBox.Size = UDim2.new(1, -48, 1, -12)
ExecTextBox.Position = UDim2.new(0, 44, 0, 6)
ExecTextBox.BackgroundTransparency = 1
ExecTextBox.Text = "print('Hello NEXUS!')"
ExecTextBox.TextColor3 = CONFIG.Theme.Text
ExecTextBox.PlaceholderText = "-- Écris ton code Lua ici..."
ExecTextBox.PlaceholderColor3 = Color3.fromRGB(90, 70, 75)
ExecTextBox.Font = Enum.Font.Code
ExecTextBox.TextSize = 13
ExecTextBox.TextXAlignment = Enum.TextXAlignment.Left
ExecTextBox.TextYAlignment = Enum.TextYAlignment.Top
ExecTextBox.TextWrapped = false
ExecTextBox.ClearTextOnFocus = false
ExecTextBox.MultiLine = true
ExecTextBox.RichText = false
ExecTextBox.TextEditable = true
ExecTextBox.Parent = ExecEditorArea

local function updateExecLines()
    local text = ExecTextBox.Text
    local count = 1
    for _ in text:gmatch("\n") do count += 1 end
    local lines = table.create(count)
    for i = 1, count do lines[i] = tostring(i) end
    ExecLineNums.Text = table.concat(lines, "\n")
end

updateExecLines()
ExecTextBox:GetPropertyChangedSignal("Text"):Connect(updateExecLines)

local ExecActions = Instance.new("Frame")
ExecActions.Size = UDim2.new(1, -16, 0, 36)
ExecActions.Position = UDim2.new(0, 8, 1, -44)
ExecActions.BackgroundTransparency = 1
ExecActions.Parent = ExecContainer

ExecStatus = Instance.new("TextLabel")
ExecStatus.Size = UDim2.new(1, -240, 1, 0)
ExecStatus.BackgroundTransparency = 1
ExecStatus.Text = "Prêt"
ExecStatus.TextColor3 = CONFIG.Theme.TextDim
ExecStatus.Font = Enum.Font.Gotham
ExecStatus.TextSize = 11
ExecStatus.TextXAlignment = Enum.TextXAlignment.Left
ExecStatus.Parent = ExecActions

local ExecClearBtn = Instance.new("TextButton")
ExecClearBtn.Size = UDim2.new(0, 100, 1, 0)
ExecClearBtn.Position = UDim2.new(1, -210, 0, 0)
ExecClearBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
ExecClearBtn.Text = "⌫ EFFACER"
ExecClearBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ExecClearBtn.Font = Enum.Font.GothamBold
ExecClearBtn.TextSize = 11
ExecClearBtn.BorderSizePixel = 0
ExecClearBtn.AutoButtonColor = false
ExecClearBtn.Parent = ExecActions
corner(ExecClearBtn, 6)
gradient(ExecClearBtn, buildGradient(Color3.fromRGB(45, 25, 30), Color3.fromRGB(25, 12, 15)), 90)

local ExecRunBtn = Instance.new("TextButton")
ExecRunBtn.Size = UDim2.new(0, 110, 1, 0)
ExecRunBtn.Position = UDim2.new(1, -110, 0, 0)
ExecRunBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
ExecRunBtn.Text = "▶ EXÉCUTER"
ExecRunBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ExecRunBtn.Font = Enum.Font.GothamBold
ExecRunBtn.TextSize = 12
ExecRunBtn.BorderSizePixel = 0
ExecRunBtn.AutoButtonColor = false
ExecRunBtn.Parent = ExecActions
corner(ExecRunBtn, 6)
gradient(ExecRunBtn, buildGradient(ThemeGradients.Buttons[1], ThemeGradients.Buttons[2]), 90)

local function executeLuaCode(code)
    if not code or code:gsub("%s", "") == "" then
        if ExecStatus then
            ExecStatus.Text = "Script vide"
            ExecStatus.TextColor3 = CONFIG.Theme.Warning
        end
        return
    end
    if ExecStatus then
        ExecStatus.Text = "Exécution..."
        ExecStatus.TextColor3 = CONFIG.Theme.Warning
    end
    local t0 = os.clock()
    local fn, err = loadstring(code)
    if not fn then
        if ExecStatus then
            ExecStatus.Text = "Erreur compilation"
            ExecStatus.TextColor3 = CONFIG.Theme.Error
        end
        return
    end
    local ok, rerr = pcall(fn)
    local dur = (os.clock() - t0) * 1000
    if ok then
        if ExecStatus then
            ExecStatus.Text = string.format("✓ Exécuté en %.2f ms", dur)
            ExecStatus.TextColor3 = CONFIG.Theme.Success
        end
    else
        if ExecStatus then
            ExecStatus.Text = "✗ Erreur d'exécution"
            ExecStatus.TextColor3 = CONFIG.Theme.Error
        end
    end
end

ExecRunBtn.MouseButton1Click:Connect(function()
    executeLuaCode(ExecTextBox.Text)
end)

ExecClearBtn.MouseButton1Click:Connect(function()
    ExecTextBox.Text = ""
    if ExecStatus then
        ExecStatus.Text = "Éditeur effacé"
        ExecStatus.TextColor3 = CONFIG.Theme.TextDim
    end
end)

UserInputService.InputBegan:Connect(function(input, processed)
    if processed then return end
    if input.KeyCode == Enum.KeyCode.Return
        and UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
        if UserInputService:GetFocusedTextBox() == ExecTextBox then
            ExecRunBtn:Activate()
        end
    end
end)

local ColorPalette = {
    {name = "Rouge vif",     color = Color3.fromRGB(255, 40, 40)},
    {name = "Rouge",         color = Color3.fromRGB(220, 40, 50)},
    {name = "Rouge foncé",   color = Color3.fromRGB(140, 20, 30)},
    {name = "Cramoisi",      color = Color3.fromRGB(180, 30, 60)},
    {name = "Orange",        color = Color3.fromRGB(255, 140, 0)},
    {name = "Orange foncé",  color = Color3.fromRGB(200, 90, 0)},
    {name = "Jaune",         color = Color3.fromRGB(255, 220, 40)},
    {name = "Ambre",         color = Color3.fromRGB(220, 160, 30)},
    {name = "Vert",          color = Color3.fromRGB(60, 200, 90)},
    {name = "Vert foncé",    color = Color3.fromRGB(30, 120, 60)},
    {name = "Menthe",        color = Color3.fromRGB(100, 240, 180)},
    {name = "Olive",         color = Color3.fromRGB(140, 160, 30)},
    {name = "Bleu",          color = Color3.fromRGB(60, 120, 240)},
    {name = "Bleu foncé",    color = Color3.fromRGB(30, 60, 160)},
    {name = "Cyan",          color = Color3.fromRGB(40, 220, 240)},
    {name = "Turquoise",     color = Color3.fromRGB(40, 180, 180)},
    {name = "Violet",        color = Color3.fromRGB(160, 60, 220)},
    {name = "Violet foncé",  color = Color3.fromRGB(90, 30, 140)},
    {name = "Magenta",       color = Color3.fromRGB(240, 40, 200)},
    {name = "Rose",          color = Color3.fromRGB(255, 130, 180)},
    {name = "Blanc",         color = Color3.fromRGB(240, 240, 240)},
    {name = "Gris clair",    color = Color3.fromRGB(180, 180, 190)},
    {name = "Gris",          color = Color3.fromRGB(120, 120, 130)},
    {name = "Gris foncé",    color = Color3.fromRGB(60, 60, 70)},
    {name = "Noir",          color = Color3.fromRGB(15, 15, 18)},
    {name = "Noir profond",  color = Color3.fromRGB(5, 5, 8)},
    {name = "Nuit",          color = Color3.fromRGB(20, 20, 40)},
    {name = "Charbon",       color = Color3.fromRGB(35, 25, 30)},
}

local selectedColor1 = CONFIG.Theme.Red
local selectedColor2 = CONFIG.Theme.RedDark

local function createColorButton(parent, name, color, onClick)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 80, 0, 34)
    btn.BackgroundColor3 = color
    btn.Text = ""
    btn.BorderSizePixel = 0
    btn.AutoButtonColor = false
    btn.Parent = parent
    corner(btn, 6)
    stroke(btn, Color3.fromRGB(200, 200, 200), 1, 0.5)

    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.fromScale(1, 1)
    lbl.BackgroundTransparency = 1
    lbl.Text = name
    lbl.TextColor3 = (color.R + color.G + color.B > 1.5)
        and Color3.fromRGB(0, 0, 0) or Color3.fromRGB(255, 255, 255)
    lbl.TextSize = 10
    lbl.Font = Enum.Font.GothamBold
    lbl.TextScaled = false
    lbl.Parent = btn

    btn.MouseButton1Click:Connect(function()
        if onClick then onClick(color) end
    end)

    return btn
end

local PreviewFrame = Instance.new("Frame")
PreviewFrame.Size = UDim2.new(1, -10, 0, 60)
PreviewFrame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
PreviewFrame.BorderSizePixel = 0
PreviewFrame.LayoutOrder = -5
PreviewFrame.Parent = SettingsScroll
corner(PreviewFrame, 8)
local previewGrad = gradient(PreviewFrame, buildGradient(selectedColor1, selectedColor2), 45)
stroke(PreviewFrame, CONFIG.Theme.RedBright, 1.5, 0.4)

local PreviewLabel = Instance.new("TextLabel")
PreviewLabel.Size = UDim2.fromScale(1, 1)
PreviewLabel.BackgroundTransparency = 1
PreviewLabel.Text = "APERÇU DU THÈME"
PreviewLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
PreviewLabel.TextSize = 14
PreviewLabel.Font = Enum.Font.GothamBold
PreviewLabel.Parent = PreviewFrame

local Color1Frame = Instance.new("Frame")
Color1Frame.Size = UDim2.new(1, -10, 0, 44)
Color1Frame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
Color1Frame.BorderSizePixel = 0
Color1Frame.Parent = SettingsScroll
corner(Color1Frame, 8)
gradient(Color1Frame, buildGradient(ThemeGradients.TitleBar[1], ThemeGradients.TitleBar[2]), 90)

local Color1Label = Instance.new("TextLabel")
Color1Label.Size = UDim2.new(1, -130, 1, 0)
Color1Label.Position = UDim2.new(0, 14, 0, 0)
Color1Label.BackgroundTransparency = 1
Color1Label.Text = "Couleur 1 (haut du dégradé)"
Color1Label.TextColor3 = CONFIG.Theme.Text
Color1Label.TextSize = 13
Color1Label.Font = Enum.Font.GothamBold
Color1Label.TextXAlignment = Enum.TextXAlignment.Left
Color1Label.Parent = Color1Frame

local Color1Preview = Instance.new("Frame")
Color1Preview.Size = UDim2.new(0, 100, 0, 28)
Color1Preview.Position = UDim2.new(1, -110, 0.5, -14)
Color1Preview.BackgroundColor3 = selectedColor1
Color1Preview.BorderSizePixel = 0
Color1Preview.Parent = Color1Frame
corner(Color1Preview, 6)
stroke(Color1Preview, Color3.fromRGB(200, 200, 200), 1, 0.5)

local Color2Frame = Instance.new("Frame")
Color2Frame.Size = UDim2.new(1, -10, 0, 44)
Color2Frame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
Color2Frame.BorderSizePixel = 0
Color2Frame.Parent = SettingsScroll
corner(Color2Frame, 8)
gradient(Color2Frame, buildGradient(ThemeGradients.TitleBar[1], ThemeGradients.TitleBar[2]), 90)

local Color2Label = Instance.new("TextLabel")
Color2Label.Size = UDim2.new(1, -130, 1, 0)
Color2Label.Position = UDim2.new(0, 14, 0, 0)
Color2Label.BackgroundTransparency = 1
Color2Label.Text = "Couleur 2 (bas du dégradé)"
Color2Label.TextColor3 = CONFIG.Theme.Text
Color2Label.TextSize = 13
Color2Label.Font = Enum.Font.GothamBold
Color2Label.TextXAlignment = Enum.TextXAlignment.Left
Color2Label.Parent = Color2Frame

local Color2Preview = Instance.new("Frame")
Color2Preview.Size = UDim2.new(0, 100, 0, 28)
Color2Preview.Position = UDim2.new(1, -110, 0.5, -14)
Color2Preview.BackgroundColor3 = selectedColor2
Color2Preview.BorderSizePixel = 0
Color2Preview.Parent = Color2Frame
corner(Color2Preview, 6)
stroke(Color2Preview, Color3.fromRGB(200, 200, 200), 1, 0.5)

local function openColorPicker(title, onSelect)
    local existing = UI.ScreenGui:FindFirstChild("ColorPicker")
    if existing then existing:Destroy() end

    local picker = Instance.new("Frame")
    picker.Name = "ColorPicker"
    picker.Size = UDim2.new(0, 560, 0, 460)
    picker.Position = UDim2.new(0.5, -280, 0.5, -230)
    picker.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    picker.BorderSizePixel = 0
    picker.ZIndex = 300
    picker.Parent = UI.ScreenGui
    corner(picker, 12)
    gradient(picker, buildGradient(ThemeGradients.Main[1], ThemeGradients.Main[2]), 45)
    stroke(picker, CONFIG.Theme.RedBright, 2, 0)

    local header = Instance.new("Frame")
    header.Size = UDim2.new(1, 0, 0, 40)
    header.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    header.BorderSizePixel = 0
    header.ZIndex = 301
    header.Parent = picker
    corner(header, 12)
    gradient(header, buildGradient(ThemeGradients.TitleBar[1], ThemeGradients.TitleBar[2]), 90)

    local headerFix = Instance.new("Frame")
    headerFix.Size = UDim2.new(1, -4, 0, 15)
    headerFix.Position = UDim2.new(0, 2, 1, -15)
    headerFix.BackgroundColor3 = Color3.fromRGB(12, 8, 10)
    headerFix.BorderSizePixel = 0
    headerFix.ZIndex = 302
    headerFix.Parent = header

    local titleLbl = Instance.new("TextLabel")
    titleLbl.Size = UDim2.new(1, -50, 1, 0)
    titleLbl.Position = UDim2.new(0, 14, 0, 0)
    titleLbl.BackgroundTransparency = 1
    titleLbl.Text = title
    titleLbl.TextColor3 = CONFIG.Theme.TextRed
    titleLbl.TextSize = 15
    titleLbl.Font = Enum.Font.GothamBold
    titleLbl.TextXAlignment = Enum.TextXAlignment.Left
    titleLbl.ZIndex = 303
    titleLbl.Parent = header

    local closeP = Instance.new("TextButton")
    closeP.Size = UDim2.new(0, 26, 0, 26)
    closeP.Position = UDim2.new(1, -32, 0, 7)
    closeP.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    closeP.Text = "✕"
    closeP.TextColor3 = Color3.fromRGB(255, 255, 255)
    closeP.TextSize = 12
    closeP.Font = Enum.Font.GothamBold
    closeP.BorderSizePixel = 0
    closeP.ZIndex = 303
    closeP.Parent = header
    corner(closeP, 6)
    gradient(closeP, buildGradient(ThemeGradients.Buttons[1], ThemeGradients.Buttons[2]), 90)

    closeP.MouseButton1Click:Connect(function() picker:Destroy() end)

    local scroll = Instance.new("ScrollingFrame")
    scroll.Size = UDim2.new(1, -20, 1, -55)
    scroll.Position = UDim2.new(0, 10, 0, 45)
    scroll.BackgroundTransparency = 1
    scroll.BorderSizePixel = 0
    scroll.ScrollBarThickness = 4
    scroll.ScrollBarImageColor3 = CONFIG.Theme.RedBright
    scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
    scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
    scroll.ZIndex = 301
    scroll.Parent = picker

    local grid = Instance.new("UIGridLayout")
    grid.CellSize = UDim2.new(0, 90, 0, 40)
    grid.CellPadding = UDim2.new(0, 6, 0, 6)
    grid.SortOrder = Enum.SortOrder.LayoutOrder
    grid.Parent = scroll

    local padding = Instance.new("UIPadding")
    padding.PaddingLeft = UDim.new(0, 8)
    padding.PaddingTop = UDim.new(0, 8)
    padding.PaddingRight = UDim.new(0, 8)
    padding.Parent = scroll

    for _, entry in ipairs(ColorPalette) do
        createColorButton(scroll, entry.name, entry.color, function(c)
            picker:Destroy()
            if onSelect then onSelect(c) end
        end)
    end
end

local Color1Btn = Instance.new("TextButton")
Color1Btn.Size = UDim2.new(0, 100, 0, 28)
Color1Btn.Position = UDim2.new(1, -110, 0.5, -14)
Color1Btn.BackgroundColor3 = selectedColor1
Color1Btn.Text = "Choisir"
Color1Btn.TextColor3 = Color3.fromRGB(255, 255, 255)
Color1Btn.TextSize = 11
Color1Btn.Font = Enum.Font.GothamBold
Color1Btn.BorderSizePixel = 0
Color1Btn.Parent = Color1Frame
corner(Color1Btn, 6)

local Color2Btn = Instance.new("TextButton")
Color2Btn.Size = UDim2.new(0, 100, 0, 28)
Color2Btn.Position = UDim2.new(1, -110, 0.5, -14)
Color2Btn.BackgroundColor3 = selectedColor2
Color2Btn.Text = "Choisir"
Color2Btn.TextColor3 = Color3.fromRGB(255, 255, 255)
Color2Btn.TextSize = 11
Color2Btn.Font = Enum.Font.GothamBold
Color2Btn.BorderSizePixel = 0
Color2Btn.Parent = Color2Frame
corner(Color2Btn, 6)

local ApplyBtn = Instance.new("TextButton")
ApplyBtn.Size = UDim2.new(1, -10, 0, 40)
ApplyBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
ApplyBtn.Text = "✓ APPLIQUER LE THÈME"
ApplyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ApplyBtn.TextSize = 14
ApplyBtn.Font = Enum.Font.GothamBold
ApplyBtn.BorderSizePixel = 0
ApplyBtn.Parent = SettingsScroll
corner(ApplyBtn, 8)
gradient(ApplyBtn, buildGradient(ThemeGradients.Buttons[1], ThemeGradients.Buttons[2]), 90)

local function updatePreview()
    previewGrad.Color = buildGradient(selectedColor1, selectedColor2)
end

Color1Btn.MouseButton1Click:Connect(function()
    openColorPicker("Couleur 1", function(c)
        selectedColor1 = c
        Color1Preview.BackgroundColor3 = c
        Color1Btn.BackgroundColor3 = c
        updatePreview()
    end)
end)

Color2Btn.MouseButton1Click:Connect(function()
    openColorPicker("Couleur 2", function(c)
        selectedColor2 = c
        Color2Preview.BackgroundColor3 = c
        Color2Btn.BackgroundColor3 = c
        updatePreview()
    end)
end)

ApplyBtn.MouseButton1Click:Connect(function()
    applyTheme(selectedColor1, selectedColor2)
end)

local PresetLabel = Instance.new("TextLabel")
PresetLabel.Size = UDim2.new(1, -10, 0, 24)
PresetLabel.BackgroundTransparency = 1
PresetLabel.Text = "Thèmes prédéfinis"
PresetLabel.TextColor3 = CONFIG.Theme.TextDim
PresetLabel.TextSize = 12
PresetLabel.Font = Enum.Font.GothamBold
PresetLabel.TextXAlignment = Enum.TextXAlignment.Left
PresetLabel.Parent = SettingsScroll

local Presets = {
    {name = "Rouge NEXUS",   c1 = Color3.fromRGB(140, 20, 30),  c2 = Color3.fromRGB(15, 5, 8)},
    {name = "Bleu Océan",    c1 = Color3.fromRGB(40, 90, 180),  c2 = Color3.fromRGB(8, 15, 30)},
    {name = "Vert Matrix",   c1 = Color3.fromRGB(30, 160, 70),  c2 = Color3.fromRGB(5, 15, 8)},
    {name = "Violet Néon",   c1 = Color3.fromRGB(150, 50, 210), c2 = Color3.fromRGB(20, 8, 30)},
    {name = "Orange Feu",    c1 = Color3.fromRGB(220, 100, 20), c2 = Color3.fromRGB(25, 8, 5)},
    {name = "Cyan Cyber",    c1 = Color3.fromRGB(30, 200, 220), c2 = Color3.fromRGB(5, 20, 25)},
    {name = "Or Royal",      c1 = Color3.fromRGB(200, 160, 30), c2 = Color3.fromRGB(20, 15, 5)},
    {name = "Rose Néon",     c1 = Color3.fromRGB(220, 50, 140), c2 = Color3.fromRGB(25, 5, 15)},
}

local presetGrid = Instance.new("Frame")
presetGrid.Size = UDim2.new(1, -10, 0, 90)
presetGrid.BackgroundTransparency = 1
presetGrid.Parent = SettingsScroll

local presetLayout = Instance.new("UIGridLayout")
presetLayout.CellSize = UDim2.new(0, 105, 0, 38)
presetLayout.CellPadding = UDim2.new(0, 6, 0, 6)
presetLayout.Parent = presetGrid

for _, preset in ipairs(Presets) do
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 105, 0, 38)
    btn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    btn.Text = preset.name
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.TextSize = 11
    btn.Font = Enum.Font.GothamBold
    btn.BorderSizePixel = 0
    btn.Parent = presetGrid
    corner(btn, 6)
    gradient(btn, buildGradient(preset.c1, preset.c2), 90)

    btn.MouseButton1Click:Connect(function()
        selectedColor1 = preset.c1
        selectedColor2 = preset.c2
        Color1Preview.BackgroundColor3 = preset.c1
        Color2Preview.BackgroundColor3 = preset.c2
        Color1Btn.BackgroundColor3 = preset.c1
        Color2Btn.BackgroundColor3 = preset.c2
        updatePreview()
        applyTheme(preset.c1, preset.c2)
    end)
end

local ResetThemeBtn = Instance.new("TextButton")
ResetThemeBtn.Size = UDim2.new(1, -10, 0, 36)
ResetThemeBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
ResetThemeBtn.Text = "🔄 Réinitialiser le thème (Rouge NEXUS)"
ResetThemeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ResetThemeBtn.TextSize = 12
ResetThemeBtn.Font = Enum.Font.GothamBold
ResetThemeBtn.BorderSizePixel = 0
ResetThemeBtn.Parent = SettingsScroll
corner(ResetThemeBtn, 8)
gradient(ResetThemeBtn, buildGradient(Color3.fromRGB(45, 25, 30), Color3.fromRGB(25, 12, 15)), 90)

ResetThemeBtn.MouseButton1Click:Connect(function()
    selectedColor1 = Color3.fromRGB(140, 20, 30)
    selectedColor2 = Color3.fromRGB(15, 5, 8)
    Color1Preview.BackgroundColor3 = selectedColor1
    Color2Preview.BackgroundColor3 = selectedColor2
    Color1Btn.BackgroundColor3 = selectedColor1
    Color2Btn.BackgroundColor3 = selectedColor2
    updatePreview()
    applyTheme(selectedColor1, selectedColor2)
end)

createToggle(MoveScroll, "Fly", "WASD • Space/Shift • [E]", function(v)
    if v and not flyEnabled then startFly()
    elseif not v and flyEnabled then stopFly() end
end)
createInput(MoveScroll, "Vitesse", "Vitesse (16-100)", 32, setSpeed)
createInput(MoveScroll, "Puissance de saut", "Saut (50-150)", 50, setJump)
createInput(MoveScroll, "Gravité", "Gravité (196 par défaut)", 196, setGravity)
createToggle(MoveScroll, "Noclip", "Traverser les murs", toggleNoclip)
createToggle(MoveScroll, "Saut infini", "Sauter à l'infini", toggleInfJump)
createToggle(MoveScroll, "🔧 TP Tool", "Ajoute un outil de téléportation dans ton Backpack", function(v)
    if v then createTPTool() else removeTPTool() end
end)

createToggle(VisionScroll, "ESP Joueurs", "Boîtes rouges autour des joueurs", toggleESP)
createToggle(VisionScroll, "Fullbright", "Éclairage augmenté", toggleFullbright)
createInput(VisionScroll, "FOV", "Champ de vision (60-110)", 90, setFOV)
createToggle(VisionScroll, "👻 Invisible TOTAL", "Invisible à tous les yeux (permanent)", toggleInvisibleSelf)

createToggle(AimbotScroll, "Activer l'Aimbot", "Active le système", function(v)
    Aim.Enabled = v
    if v then startAim() else stopAim() end
    updateFOV()
end)
createToggle(AimbotScroll, "Camlock", "Verrouille la caméra", function(v) Aim.Camlock = v end)
createToggle(AimbotScroll, "Wall Check", "Bloquer derrière murs", function(v) Aim.WallCheck = v end)
createToggle(AimbotScroll, "Team Check", "Ignorer alliés", function(v) Aim.TeamCheck = v end)
createToggle(AimbotScroll, "Afficher FOV", "Cercle de portée", function(v)
    Aim.ShowFOV = v
    if v and not fovCircle then makeFOV() end
    updateFOV()
end)
createInput(AimbotScroll, "FOV (rayon)", "Rayon (studs)", 100, function(v) Aim.FOV = v; updateFOV() end)
createInput(AimbotScroll, "Smoothness", "0=instant, 1=lent", 0.5, function(v) Aim.Smoothness = v end)
createInput(AimbotScroll, "Distance max", "Distance max", 300, function(v) Aim.MaxDistance = v end)
createInput(AimbotScroll, "Prédiction", "Anticipation", 0, function(v) Aim.Prediction = v end)
createDropdown(AimbotScroll, "Partie visée", "Où viser",
    {"Head", "HumanoidRootPart", "UpperTorso", "LowerTorso"},
    "Head", function(v) Aim.TargetPart = v end)
createDropdown(AimbotScroll, "Touche d'activation", "Touche pour viser",
    {"Clic droit", "Clic gauche", "E", "Q", "LeftAlt"},
    "Clic droit",
    function(v)
        local map = {
            ["Clic droit"] = Enum.UserInputType.MouseButton2,
            ["Clic gauche"] = Enum.UserInputType.MouseButton1,
            ["E"] = Enum.KeyCode.E,
            ["Q"] = Enum.KeyCode.Q,
            ["LeftAlt"] = Enum.KeyCode.LeftAlt,
        }
        Aim.TriggerKey = map[v] or Enum.UserInputType.MouseButton2
    end)
createAction(AimbotScroll, "Reset Aimbot", "Réinitialiser", function()
    Aim.FOV = 100
    Aim.Smoothness = 0.5
    Aim.MaxDistance = 300
    Aim.Prediction = 0
    Aim.TargetPart = "Head"
    Aim.WallCheck = true
    Aim.TeamCheck = true
    updateFOV()
end)

makeFOV()

createAction(TrollScroll, "Fling le plus proche", "Projette le joueur le plus proche", flingNearest)
createAction(TrollScroll, "🌊 Faire flotter (cible)", "Sélectionne un joueur et le fait flotter", function()
    openPlayerPicker("Faire flotter qui ?", function(target)
        selectedPlayer = target
        startFloat(target)
    end)
end)
createAction(TrollScroll, "🌊 Arrêter TOUS les floats", "Arrête tous les floats", function()
    for t, _ in pairs(floatingTargets) do stopFloat(t) end
end)
createAction(TrollScroll, "🎯 Follow un joueur", "Colle le joueur sélectionné en permanence", function()
    openPlayerPicker("Follow qui ?", function(target)
        startFollow(target)
    end)
end)
createAction(TrollScroll, "🛑 Arrêter le Follow", "Arrête de suivre le joueur", function()
    stopFollow()
end)
createAction(TrollScroll, "Son troll", "Joue un son troll fort", function()
    playSound("rbxassetid://131886985", 2)
end)

createAction(PlayerScroll, "Soigner", "Vie au max", healSelf)
createAction(PlayerScroll, "Téléport curseur", "TP à la souris", teleportToCursor)
createAction(PlayerScroll, "Respawn", "Réapparaître", function()
    local h = getHumanoid()
    if h then h.Health = 0 end
end)

createAction(KickScroll, "Sélectionner un joueur", "Choisir une cible", function()
    openPlayerPicker("Sélectionner un joueur", function(target)
        selectedPlayer = target
    end)
end)
createAction(KickScroll, "Kick joueur sélectionné", "Expulse le joueur sélectionné", function()
    if not selectedPlayer then return end
    kickPlayer(selectedPlayer)
end)
createAction(KickScroll, "Kick le plus proche", "Expulse le plus proche", function()
    local np = getNearestPlayer(500)
    if np then kickPlayer(np) end
end)

createAction(ServerScroll, "Rejoindre (Rejoin)", "Recharge le serveur", rejoin)
createAction(ServerScroll, "Server Hop", "Change de serveur", rejoin)
createAction(ServerScroll, "Infos serveur", "Affiche les infos", function()
    local info = string.format("PlaceId: %s | JobId: %s | Joueurs: %d", tostring(game.PlaceId), tostring(game.JobId), #Players:GetPlayers())
    if ExecStatus then
        ExecStatus.Text = info
        ExecStatus.TextColor3 = CONFIG.Theme.TextDim
    end
end)

local Cats = {
    { id = "move",     name = "🏃 Mouvement", frame = Pages.move.frame },
    { id = "vision",   name = "👁️ Vision",   frame = Pages.vision.frame },
    { id = "aimbot",   name = "🎯 Aimbot",   frame = Pages.aimbot.frame },
    { id = "troll",    name = "😈 Troll",     frame = Pages.troll.frame },
    { id = "player",   name = "👤 Joueur",    frame = Pages.player.frame },
    { id = "kick",     name = "🚫 Kick",      frame = Pages.kick.frame },
    { id = "executor", name = "💻 Executor",  frame = Pages.executor.frame },
    { id = "settings", name = "⚙️ Paramètres", frame = Pages.settings.frame },
    { id = "server",   name = "🌐 Serveur",   frame = Pages.server.frame },
}

local function showCat(id)
    CurrentCat = id
    for _, c in ipairs(Cats) do c.frame.Visible = (c.id == id) end
    for cid, b in pairs(CatButtons) do
        local g = b:FindFirstChildOfClass("UIGradient")
        if cid == id then
            b.BackgroundColor3 = CONFIG.Theme.Red
            b.TextColor3 = Color3.fromRGB(255, 255, 255)
            if g then g.Color = buildGradient(ThemeGradients.Buttons[1], ThemeGradients.Buttons[2]) end
        else
            b.BackgroundColor3 = CONFIG.Theme.TabIdle
            b.TextColor3 = CONFIG.Theme.TextDim
            if g then g.Color = buildGradient(CONFIG.Theme.TabIdle, CONFIG.Theme.TabIdle) end
        end
    end
end

for _, c in ipairs(Cats) do
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0, 130, 0, 30)
    b.BackgroundColor3 = CONFIG.Theme.TabIdle
    b.Text = c.name
    b.TextColor3 = CONFIG.Theme.TextDim
    b.TextSize = 12
    b.Font = Enum.Font.GothamBold
    b.BorderSizePixel = 0
    b.AutoButtonColor = false
    b.Parent = UI.CategoryScroll
    corner(b, 6)

    b.MouseEnter:Connect(function()
        if CurrentCat ~= c.id then
            b.BackgroundColor3 = CONFIG.Theme.TabHover
        end
    end)
    b.MouseLeave:Connect(function()
        if CurrentCat ~= c.id then
            b.BackgroundColor3 = CONFIG.Theme.TabIdle
        end
    end)

    b.MouseButton1Click:Connect(function() showCat(c.id) end)
    CatButtons[c.id] = b
end

UI.Resize = Instance.new("TextButton")
UI.Resize.Size = UDim2.new(0, 22, 0, 22)
UI.Resize.Position = UDim2.new(1, -26, 1, -26)
UI.Resize.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
UI.Resize.Text = "◢"
UI.Resize.TextColor3 = Color3.fromRGB(255, 255, 255)
UI.Resize.TextSize = 16
UI.Resize.Font = Enum.Font.GothamBold
UI.Resize.BorderSizePixel = 0
UI.Resize.AutoButtonColor = false
UI.Resize.ZIndex = 10
UI.Resize.Parent = UI.MainFrame
corner(UI.Resize, 6)
gradient(UI.Resize, buildGradient(ThemeGradients.Buttons[1], ThemeGradients.Buttons[2]), 90)

UI.ResizeLeft = Instance.new("TextButton")
UI.ResizeLeft.Size = UDim2.new(0, 6, 0, 40)
UI.ResizeLeft.Position = UDim2.new(0, -3, 0.5, -20)
UI.ResizeLeft.BackgroundColor3 = Color3.fromRGB(180, 30, 45)
UI.ResizeLeft.BackgroundTransparency = 0.7
UI.ResizeLeft.Text = ""
UI.ResizeLeft.BorderSizePixel = 0
UI.ResizeLeft.AutoButtonColor = false
UI.ResizeLeft.ZIndex = 10
UI.ResizeLeft.Parent = UI.MainFrame
corner(UI.ResizeLeft, 3)

UI.ResizeRight = Instance.new("TextButton")
UI.ResizeRight.Size = UDim2.new(0, 6, 0, 40)
UI.ResizeRight.Position = UDim2.new(1, -3, 0.5, -20)
UI.ResizeRight.BackgroundColor3 = Color3.fromRGB(180, 30, 45)
UI.ResizeRight.BackgroundTransparency = 0.7
UI.ResizeRight.Text = ""
UI.ResizeRight.BorderSizePixel = 0
UI.ResizeRight.AutoButtonColor = false
UI.ResizeRight.ZIndex = 10
UI.ResizeRight.Parent = UI.MainFrame
corner(UI.ResizeRight, 3)

UI.ResizeBottom = Instance.new("TextButton")
UI.ResizeBottom.Size = UDim2.new(1, -40, 0, 6)
UI.ResizeBottom.Position = UDim2.new(0, 20, 1, -3)
UI.ResizeBottom.BackgroundColor3 = Color3.fromRGB(180, 30, 45)
UI.ResizeBottom.BackgroundTransparency = 0.7
UI.ResizeBottom.Text = ""
UI.ResizeBottom.BorderSizePixel = 0
UI.ResizeBottom.AutoButtonColor = false
UI.ResizeBottom.ZIndex = 10
UI.ResizeBottom.Parent = UI.MainFrame
corner(UI.ResizeBottom, 3)

local function viewport()
    return camera and camera.ViewportSize or Vector2.new(1920, 1080)
end

local function clampPos(pos, size)
    local vp = viewport()
    local x = math.clamp(pos.X.Offset, -size.X/2, vp.X - size.X/2)
    local y = math.clamp(pos.Y.Offset, -size.Y/2, vp.Y - size.Y/2)
    return UDim2.new(0.5, x, 0.5, y)
end

local function clampSize(size)
    local vp = viewport()
    return Vector2.new(
        math.clamp(size.X, CONFIG.MinSize.X, math.min(CONFIG.MaxSize.X, vp.X - 20)),
        math.clamp(size.Y, CONFIG.MinSize.Y, math.min(CONFIG.MaxSize.Y, vp.Y - 20))
    )
end

local act = nil
local actStart, actPos, actSize = nil, nil, nil
local actAxis = nil

UserInputService.InputChanged:Connect(function(input)
    if not act then return end
    if input.UserInputType ~= Enum.UserInputType.MouseMovement
        and input.UserInputType ~= Enum.UserInputType.Touch then return end
    local d = input.Position - actStart
    if act == "resize" then
        local newX = actSize.X + (actAxis ~= "y" and d.X or 0)
        local newY = actSize.Y + (actAxis ~= "x" and d.Y or 0)
        local s = clampSize(Vector2.new(newX, newY))
        UI.MainFrame.Size = UDim2.new(0, s.X, 0, s.Y)
    elseif act == "drag" then
        UI.MainFrame.Position = clampPos(
            UDim2.new(0.5, actPos.X.Offset + d.X, 0.5, actPos.Y.Offset + d.Y),
            UI.MainFrame.AbsoluteSize
        )
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
        act = nil
    end
end)

local function startResize(input, axis)
    act = "resize"
    actStart = input.Position
    actSize = UI.MainFrame.AbsoluteSize
    actAxis = axis or "both"
end

UI.Resize.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
        startResize(input, "both")
    end
end)

UI.ResizeLeft.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        startResize(input, "x")
    end
end)

UI.ResizeRight.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        startResize(input, "x")
    end
end)

UI.ResizeBottom.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        startResize(input, "y")
    end
end)

UI.TitleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        act = "drag"
        actStart = input.Position
        actPos = UI.MainFrame.Position
    end
end)

local isOpen = false

local function openUI()
    if isOpen then return end
    isOpen = true
    UI.ScreenGui.Enabled = true
    UI.MainFrame.Visible = true
    UI.MainFrame.Size = UDim2.new(0, CONFIG.WindowSize.X, 0, CONFIG.WindowSize.Y)
    UI.MainFrame.Position = UDim2.new(0.5, -CONFIG.WindowSize.X/2, 0.5, -CONFIG.WindowSize.Y/2)
end

local function closeUI()
    if not isOpen then return end
    isOpen = false
    UI.ScreenGui.Enabled = false
end

UI.CloseBtn.MouseButton1Click:Connect(closeUI)

local flyBtn = nil
for _, child in ipairs(MoveScroll:GetDescendants()) do
    if child:IsA("TextButton") and (child.Text == "OFF" or child.Text == "ON") then
        local p = child.Parent
        if p then
            for _, c in ipairs(p:GetChildren()) do
                if c:IsA("TextLabel") and c.Text == "Fly" then
                    flyBtn = child
                    break
                end
            end
        end
    end
    if flyBtn then break end
end

local function updateFlyBtn()
    if not flyBtn then return end
    local g = flyBtn:FindFirstChildOfClass("UIGradient")
    if flyEnabled then
        flyBtn.Text = "ON"
        flyBtn.BackgroundColor3 = CONFIG.Theme.Green
        if g then g.Color = buildGradient(CONFIG.Theme.Green, CONFIG.Theme.GreenDark) end
    else
        flyBtn.Text = "OFF"
        flyBtn.BackgroundColor3 = CONFIG.Theme.Red
        if g then g.Color = buildGradient(ThemeGradients.Buttons[1], ThemeGradients.Buttons[2]) end
    end
    States["Fly"] = flyEnabled
end

UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    local k = input.KeyCode
    if k == Enum.KeyCode.K then
        if isOpen then closeUI() else openUI() end
    elseif k == Enum.KeyCode.E then
        if not throttle("flytoggle", 0.3) then return end
        toggleFly()
        updateFlyBtn()
    end
end)

workspace.CurrentCamera:GetPropertyChangedSignal("ViewportSize"):Connect(function()
    if isOpen then
        local s = clampSize(UI.MainFrame.AbsoluteSize)
        UI.MainFrame.Size = UDim2.new(0, s.X, 0, s.Y)
        UI.MainFrame.Position = clampPos(UI.MainFrame.Position, UI.MainFrame.AbsoluteSize)
    end
end)

showCat("move")
