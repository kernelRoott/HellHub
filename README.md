--[[
    ╔══════════════════════════════════╗
    ║   Steal a Brainrot — Protector  ║
    ║   ProtectName v2.1 — Fixed      ║
    ╚══════════════════════════════════╝
]]

local Players          = game:GetService("Players")
local TweenService     = game:GetService("TweenService")
local StarterGui       = game:GetService("StarterGui")
local CoreGui          = game:GetService("CoreGui")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer      = Players.LocalPlayer

local Config = {
    FakeName = "ProtectedUser",
    Enabled  = false,
}

local RealName        = LocalPlayer.Name
local RealDisplayName = LocalPlayer.DisplayName

local T = {
    Bg        = Color3.fromRGB(30, 36, 46),
    Surface   = Color3.fromRGB(38, 45, 58),
    Hover     = Color3.fromRGB(46, 55, 70),
    Accent    = Color3.fromRGB(100, 160, 210),
    AccentDim = Color3.fromRGB(70, 120, 170),
    Text1     = Color3.fromRGB(220, 230, 240),
    Text2     = Color3.fromRGB(140, 155, 175),
    Border    = Color3.fromRGB(55, 65, 80),
    TogOn     = Color3.fromRGB(100, 180, 220),
    TogOff    = Color3.fromRGB(60, 70, 85),
    OK        = Color3.fromRGB(90, 200, 150),
    Shadow    = Color3.fromRGB(15, 18, 24),
}

local function tw(obj, props, dur)
    return TweenService:Create(obj, TweenInfo.new(dur or 0.25, Enum.EasingStyle.Quint, Enum.EasingDirection.Out), props)
end

local function corner(p, r)
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, r or 8)
    c.Parent = p
end

local function stroke(p, col, th)
    local s = Instance.new("UIStroke")
    s.Color = col or T.Border
    s.Thickness = th or 1
    s.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    s.Parent = p
    return s
end

local function padding(p, t, b, l, r)
    local pd = Instance.new("UIPadding")
    pd.PaddingTop = UDim.new(0, t or 0)
    pd.PaddingBottom = UDim.new(0, b or 0)
    pd.PaddingLeft = UDim.new(0, l or 0)
    pd.PaddingRight = UDim.new(0, r or 0)
    pd.Parent = p
end

-- Очистка
local OLD = CoreGui:FindFirstChild("PN_Cheat")
if OLD then OLD:Destroy() end

-- ScreenGui
local Gui = Instance.new("ScreenGui")
Gui.Name = "PN_Cheat"
Gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
Gui.ResetOnSpawn = false
Gui.DisplayOrder = 999
pcall(function() Gui.Parent = CoreGui end)
if not Gui.Parent then Gui.Parent = LocalPlayer:WaitForChild("PlayerGui") end

-- Тень
local Shadow = Instance.new("Frame", Gui)
Shadow.Name = "Shadow"
Shadow.Size = UDim2.new(0, 330, 0, 340)
Shadow.Position = UDim2.new(0.5, -165, 0.5, -170)
Shadow.BackgroundColor3 = T.Shadow
Shadow.BackgroundTransparency = 0.5
Shadow.BorderSizePixel = 0
corner(Shadow, 14)

-- Главное окно
local Main = Instance.new("Frame", Gui)
Main.Name = "Main"
Main.Size = UDim2.new(0, 320, 0, 330)
Main.Position = UDim2.new(0.5, -160, 0.5, -165)
Main.BackgroundColor3 = T.Bg
Main.BorderSizePixel = 0
Main.ClipsDescendants = true
corner(Main, 12)
stroke(Main)

Main.BackgroundTransparency = 1
Shadow.BackgroundTransparency = 1
tw(Main, {BackgroundTransparency = 0}, 0.45):Play()
tw(Shadow, {BackgroundTransparency = 0.5}, 0.45):Play()

-- Drag
do
    local drag, dStart, mStart, sStart
    Main.InputBegan:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
            drag = true
            dStart = i.Position
            mStart = Main.Position
            sStart = Shadow.Position
            i.Changed:Connect(function()
                if i.UserInputState == Enum.UserInputState.End then drag = false end
            end)
        end
    end)
    UserInputService.InputChanged:Connect(function(i)
        if drag and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
            local d = i.Position - dStart
            Main.Position = UDim2.new(mStart.X.Scale, mStart.X.Offset + d.X, mStart.Y.Scale, mStart.Y.Offset + d.Y)
            Shadow.Position = UDim2.new(sStart.X.Scale, sStart.X.Offset + d.X, sStart.Y.Scale, sStart.Y.Offset + d.Y)
        end
    end)
end

-- Header
local Header = Instance.new("Frame", Main)
Header.Size = UDim2.new(1, 0, 0, 52)
Header.BackgroundColor3 = T.Surface
Header.BorderSizePixel = 0
corner(Header, 12)

local HMask = Instance.new("Frame", Header)
HMask.Size = UDim2.new(1, 0, 0, 14)
HMask.Position = UDim2.new(0, 0, 1, -14)
HMask.BackgroundColor3 = T.Surface
HMask.BorderSizePixel = 0

local AccLine = Instance.new("Frame", Header)
AccLine.Size = UDim2.new(1, 0, 0, 2)
AccLine.Position = UDim2.new(0, 0, 1, 0)
AccLine.BackgroundColor3 = T.Accent
AccLine.BorderSizePixel = 0

local Icon = Instance.new("TextLabel", Header)
Icon.Size = UDim2.new(0, 30, 0, 30)
Icon.Position = UDim2.new(0, 14, 0.5, -15)
Icon.BackgroundTransparency = 1
Icon.Text = "🛡️"
Icon.TextSize = 20

local Title = Instance.new("TextLabel", Header)
Title.Size = UDim2.new(1, -100, 0, 20)
Title.Position = UDim2.new(0, 48, 0, 8)
Title.BackgroundTransparency = 1
Title.Text = "PROTECT NAME"
Title.TextColor3 = T.Text1
Title.TextSize = 16
Title.Font = Enum.Font.GothamBold
Title.TextXAlignment = Enum.TextXAlignment.Left

local Sub = Instance.new("TextLabel", Header)
Sub.Size = UDim2.new(1, -100, 0, 16)
Sub.Position = UDim2.new(0, 48, 0, 28)
Sub.BackgroundTransparency = 1
Sub.Text = "Steal a Brainrot  •  v2.1"
Sub.TextColor3 = T.Text2
Sub.TextSize = 11
Sub.Font = Enum.Font.Gotham
Sub.TextXAlignment = Enum.TextXAlignment.Left

local XBtn = Instance.new("TextButton", Header)
XBtn.Size = UDim2.new(0, 30, 0, 30)
XBtn.Position = UDim2.new(1, -40, 0.5, -15)
XBtn.BackgroundColor3 = T.Hover
XBtn.BorderSizePixel = 0
XBtn.Text = "✕"
XBtn.TextColor3 = T.Text2
XBtn.TextSize = 14
XBtn.Font = Enum.Font.GothamBold
corner(XBtn, 6)

XBtn.MouseEnter:Connect(function()
    tw(XBtn, {BackgroundColor3 = Color3.fromRGB(180, 60, 60), TextColor3 = Color3.new(1, 1, 1)}, 0.2):Play()
end)
XBtn.MouseLeave:Connect(function()
    tw(XBtn, {BackgroundColor3 = T.Hover, TextColor3 = T.Text2}, 0.2):Play()
end)
XBtn.MouseButton1Click:Connect(function()
    tw(Main, {BackgroundTransparency = 1}, 0.3):Play()
    tw(Shadow, {BackgroundTransparency = 1}, 0.3):Play()
    task.delay(0.35, function() Gui:Destroy() end)
end)

-- Content
local Content = Instance.new("Frame", Main)
Content.Size = UDim2.new(1, -24, 1, -66)
Content.Position = UDim2.new(0, 12, 0, 60)
Content.BackgroundTransparency = 1

local Layout = Instance.new("UIListLayout", Content)
Layout.SortOrder = Enum.SortOrder.LayoutOrder
Layout.Padding = UDim.new(0, 10)

-- Status
local StatusCard = Instance.new("Frame", Content)
StatusCard.Size = UDim2.new(1, 0, 0, 44)
StatusCard.BackgroundColor3 = T.Surface
StatusCard.BorderSizePixel = 0
StatusCard.LayoutOrder = 1
corner(StatusCard, 8)
stroke(StatusCard)

local StatusDot = Instance.new("Frame", StatusCard)
StatusDot.Size = UDim2.new(0, 8, 0, 8)
StatusDot.Position = UDim2.new(0, 14, 0.5, -4)
StatusDot.BackgroundColor3 = T.TogOff
StatusDot.BorderSizePixel = 0
corner(StatusDot, 4)

local StatusLbl = Instance.new("TextLabel", StatusCard)
StatusLbl.Size = UDim2.new(1, -80, 1, 0)
StatusLbl.Position = UDim2.new(0, 30, 0, 0)
StatusLbl.BackgroundTransparency = 1
StatusLbl.Text = "Protection: OFF"
StatusLbl.TextColor3 = T.Text2
StatusLbl.TextSize = 13
StatusLbl.Font = Enum.Font.GothamMedium
StatusLbl.TextXAlignment = Enum.TextXAlignment.Left

-- Input
local InputSec = Instance.new("Frame", Content)
InputSec.Size = UDim2.new(1, 0, 0, 68)
InputSec.BackgroundTransparency = 1
InputSec.LayoutOrder = 2

local InputLbl = Instance.new("TextLabel", InputSec)
InputLbl.Size = UDim2.new(1, 0, 0, 18)
InputLbl.BackgroundTransparency = 1
InputLbl.Text = "FAKE DISPLAY NAME"
InputLbl.TextColor3 = T.Text2
InputLbl.TextSize = 10
InputLbl.Font = Enum.Font.GothamBold
InputLbl.TextXAlignment = Enum.TextXAlignment.Left

local InputBox = Instance.new("TextBox", InputSec)
InputBox.Name = "NameInput"
InputBox.Size = UDim2.new(1, 0, 0, 40)
InputBox.Position = UDim2.new(0, 0, 0, 22)
InputBox.BackgroundColor3 = T.Surface
InputBox.BorderSizePixel = 0
InputBox.Text = Config.FakeName
InputBox.PlaceholderText = "Enter fake name..."
InputBox.PlaceholderColor3 = T.Text2
InputBox.TextColor3 = T.Text1
InputBox.TextSize = 14
InputBox.Font = Enum.Font.Gotham
InputBox.ClearTextOnFocus = false
corner(InputBox, 8)
local iStr = stroke(InputBox)
padding(InputBox, 0, 0, 12, 12)

InputBox.Focused:Connect(function()
    tw(iStr, {Color = T.Accent, Thickness = 1.5}, 0.2):Play()
end)
InputBox.FocusLost:Connect(function()
    tw(iStr, {Color = T.Border, Thickness = 1}, 0.2):Play()
    Config.FakeName = InputBox.Text ~= "" and InputBox.Text or "ProtectedUser"
end)

-- Toggle
local TogCard = Instance.new("Frame", Content)
TogCard.Size = UDim2.new(1, 0, 0, 44)
TogCard.BackgroundColor3 = T.Surface
TogCard.BorderSizePixel = 0
TogCard.LayoutOrder = 3
corner(TogCard, 8)
stroke(TogCard)

local TogLbl = Instance.new("TextLabel", TogCard)
TogLbl.Size = UDim2.new(1, -70, 1, 0)
TogLbl.Position = UDim2.new(0, 14, 0, 0)
TogLbl.BackgroundTransparency = 1
TogLbl.Text = "Enable ProtectName"
TogLbl.TextColor3 = T.Text1
TogLbl.TextSize = 13
TogLbl.Font = Enum.Font.GothamMedium
TogLbl.TextXAlignment = Enum.TextXAlignment.Left

local TogBg = Instance.new("Frame", TogCard)
TogBg.Size = UDim2.new(0, 44, 0, 24)
TogBg.Position = UDim2.new(1, -56, 0.5, -12)
TogBg.BackgroundColor3 = T.TogOff
TogBg.BorderSizePixel = 0
corner(TogBg, 12)

local TogCircle = Instance.new("Frame", TogBg)
TogCircle.Size = UDim2.new(0, 18, 0, 18)
TogCircle.Position = UDim2.new(0, 3, 0.5, -9)
TogCircle.BackgroundColor3 = T.Text2
TogCircle.BorderSizePixel = 0
corner(TogCircle, 9)

local function updateVisual()
    if Config.Enabled then
        tw(TogBg, {BackgroundColor3 = T.TogOn}, 0.25):Play()
        tw(TogCircle, {Position = UDim2.new(1, -21, 0.5, -9), BackgroundColor3 = Color3.new(1, 1, 1)}, 0.25):Play()
        tw(StatusDot, {BackgroundColor3 = T.OK}, 0.25):Play()
        StatusLbl.Text = "Protection: ON"
        tw(StatusLbl, {TextColor3 = T.OK}, 0.25):Play()
    else
        tw(TogBg, {BackgroundColor3 = T.TogOff}, 0.25):Play()
        tw(TogCircle, {Position = UDim2.new(0, 3, 0.5, -9), BackgroundColor3 = T.Text2}, 0.25):Play()
        tw(StatusDot, {BackgroundColor3 = T.TogOff}, 0.25):Play()
        StatusLbl.Text = "Protection: OFF"
        tw(StatusLbl, {TextColor3 = T.Text2}, 0.25):Play()
    end
end

-- Apply button
local ApplyBtn = Instance.new("TextButton", Content)
ApplyBtn.Size = UDim2.new(1, 0, 0, 38)
ApplyBtn.BackgroundColor3 = T.AccentDim
ApplyBtn.BorderSizePixel = 0
ApplyBtn.Text = "⚡  APPLY NOW"
ApplyBtn.TextColor3 = T.Text1
ApplyBtn.TextSize = 13
ApplyBtn.Font = Enum.Font.GothamBold
ApplyBtn.LayoutOrder = 4
corner(ApplyBtn, 8)

ApplyBtn.MouseEnter:Connect(function()
    tw(ApplyBtn, {BackgroundColor3 = T.Accent}, 0.2):Play()
end)
ApplyBtn.MouseLeave:Connect(function()
    tw(ApplyBtn, {BackgroundColor3 = T.AccentDim}, 0.2):Play()
end)

-- Info
local Info = Instance.new("TextLabel", Content)
Info.Size = UDim2.new(1, 0, 0, 30)
Info.BackgroundTransparency = 1
Info.Text = "RightShift — toggle UI"
Info.TextColor3 = T.Text2
Info.TextSize = 10
Info.Font = Enum.Font.Gotham
Info.LayoutOrder = 5

-- ═════════════════════════════════════════════════════
--       CORE ENGINE v2.1 — ТОЛЬКО СВОЙ НИК
-- ═════════════════════════════════════════════════════

local activeConns = {}
local hooked      = {}
local lastText    = {}

local function isOtherPlayerCharacter(obj)
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character then
            if obj:IsDescendantOf(plr.Character) or obj == plr.Character then
                return true
            end
        end
    end
    return false
end

local function isMyCharacter(obj)
    local char = LocalPlayer.Character
    if not char then return false end
    return obj:IsDescendantOf(char) or obj == char
end

local function plainReplace(s, old, new)
    if old == "" then return s end
    local out, i = {}, 1
    while i <= #s do
        local p = string.find(s, old, i, true)
        if not p then
            out[#out + 1] = s:sub(i)
            break
        end
        out[#out + 1] = s:sub(i, p - 1)
        out[#out + 1] = new
        i = p + #old
    end
    return table.concat(out)
end

local function hasReal(text)
    if not text or text == "" then return false end
    if string.find(text, RealName, 1, true) then return true end
    if RealDisplayName ~= RealName and string.find(text, RealDisplayName, 1, true) then return true end
    return false
end

local function doReplace(text)
    local r = text
    if RealDisplayName ~= RealName then
        if #RealDisplayName >= #RealName then
            r = plainReplace(r, RealDisplayName, Config.FakeName)
            r = plainReplace(r, RealName, Config.FakeName)
        else
            r = plainReplace(r, RealName, Config.FakeName)
            r = plainReplace(r, RealDisplayName, Config.FakeName)
        end
    else
        r = plainReplace(r, RealName, Config.FakeName)
    end
    return r
end

local function hookText(obj)
    if hooked[obj] or obj == InputBox then return end
    hooked[obj] = true

    if Config.Enabled and hasReal(obj.Text) then
        local n = doReplace(obj.Text)
        lastText[obj] = n
        pcall(function() obj.Text = n end)
    end

    local c = obj:GetPropertyChangedSignal("Text"):Connect(function()
        if not Config.Enabled then return end
        local t = obj.Text
        if t == lastText[obj] then return end
        if hasReal(t) then
            local n = doReplace(t)
            lastText[obj] = n
            pcall(function() obj.Text = n end)
        end
    end)
    table.insert(activeConns, c)
end

local function hookHumanoid(hum)
    if hooked[hum] then return end
    if not isMyCharacter(hum) then return end
    hooked[hum] = true

    if Config.Enabled then
        pcall(function() hum.DisplayName = Config.FakeName end)
    end

    local c = hum:GetPropertyChangedSignal("DisplayName"):Connect(function()
        if Config.Enabled and hum.DisplayName ~= Config.FakeName then
            pcall(function() hum.DisplayName = Config.FakeName end)
        end
    end)
    table.insert(activeConns, c)
end

local function process(obj, source)
    if obj:IsA("Humanoid") then
        if isMyCharacter(obj) then
            hookHumanoid(obj)
        end
    elseif obj:IsA("TextLabel") or obj:IsA("TextButton") or obj:IsA("TextBox") then
        if source == "workspace" then
            if not isOtherPlayerCharacter(obj) then
                hookText(obj)
            end
        else
            hookText(obj)
        end
    end
end

local function scanOnce(root, source)
    local ok, list = pcall(function() return root:GetDescendants() end)
    if not ok then return end
    for _, obj in ipairs(list) do
        process(obj, source)
    end
end

local function watchNew(root, source)
    local ok, c = pcall(function()
        return root.DescendantAdded:Connect(function(obj)
            if not Config.Enabled then return end
            task.defer(function()
                if obj and obj.Parent then
                    process(obj, source)
                end
            end)
        end)
    end)
    if ok and c then
        table.insert(activeConns, c)
    end
end

local function clearAll()
    for _, c in ipairs(activeConns) do
        pcall(function() c:Disconnect() end)
    end
    activeConns = {}
    hooked = {}
    lastText = {}
end

local function restoreOriginal()
    local ch = LocalPlayer.Character
    if ch then
        local h = ch:FindFirstChildOfClass("Humanoid")
        if h then pcall(function() h.DisplayName = RealDisplayName end) end
    end
end

local function fullSetup()
    clearAll()

    if not Config.Enabled then
        restoreOriginal()
        return
    end

    Config.FakeName = InputBox.Text ~= "" and InputBox.Text or "ProtectedUser"

    scanOnce(workspace, "workspace")
    watchNew(workspace, "workspace")

    pcall(function() scanOnce(CoreGui, "ui") end)
    pcall(function() watchNew(CoreGui, "ui") end)

    local pg = LocalPlayer:FindFirstChild("PlayerGui")
    if pg then
        scanOnce(pg, "ui")
        watchNew(pg, "ui")
    end

    local ch = LocalPlayer.Character
    if ch then
        local h = ch:FindFirstChildOfClass("Humanoid")
        if h then hookHumanoid(h) end
    end
end

-- ═══════════════════════════════════
--  КНОПКИ → ЛОГИКА
-- ═══════════════════════════════════

local TogBtn = Instance.new("TextButton", TogCard)
TogBtn.Size = UDim2.new(1, 0, 1, 0)
TogBtn.BackgroundTransparency = 1
TogBtn.Text = ""

TogBtn.MouseButton1Click:Connect(function()
    Config.FakeName = InputBox.Text ~= "" and InputBox.Text or "ProtectedUser"
    Config.Enabled = not Config.Enabled
    updateVisual()
    fullSetup()
end)

ApplyBtn.MouseButton1Click:Connect(function()
    Config.FakeName = InputBox.Text ~= "" and InputBox.Text or "ProtectedUser"
    Config.Enabled = true
    updateVisual()
    fullSetup()

    ApplyBtn.Text = "✓  APPLIED!"
    tw(ApplyBtn, {BackgroundColor3 = T.OK}, 0.2):Play()
    task.delay(1.2, function()
        ApplyBtn.Text = "⚡  APPLY NOW"
        tw(ApplyBtn, {BackgroundColor3 = T.AccentDim}, 0.3):Play()
    end)
end)

-- Респавн
LocalPlayer.CharacterAdded:Connect(function(char)
    task.wait(1)
    if Config.Enabled then
        local h = char:WaitForChild("Humanoid", 5)
        if h then hookHumanoid(h) end

        local pg = LocalPlayer:FindFirstChild("PlayerGui")
        if pg then
            scanOnce(pg, "ui")
            watchNew(pg, "ui")
        end
    end
end)

-- Safety net — только свой Humanoid, раз в 3 сек
task.spawn(function()
    while Gui and Gui.Parent do
        if Config.Enabled then
            local ch = LocalPlayer.Character
            if ch then
                local h = ch:FindFirstChildOfClass("Humanoid")
                if h and h.DisplayName ~= Config.FakeName then
                    pcall(function() h.DisplayName = Config.FakeName end)
                end
            end
        end
        task.wait(3)
    end
end)

-- RightShift toggle
local visible = true
UserInputService.InputBegan:Connect(function(input, gpe)
    if gpe then return end
    if input.KeyCode == Enum.KeyCode.RightShift then
        visible = not visible
        Main.Visible = visible
        Shadow.Visible = visible
    end
end)

pcall(function()
    StarterGui:SetCore("SendNotification", {
        Title = "🛡️ ProtectName v2.1",
        Text = "Loaded! Only YOUR name is replaced.",
        Duration = 4,
    })
end)

print("[ProtectName v2.1] Loaded — only local player name is spoofed")
