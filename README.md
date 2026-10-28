
--[[
    STEAL AN EGG | VISUAL HUB
    Version: 1.0.0
    Type: Visual-only demonstration
    No gameplay automation or remote calls.
]]

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Remove previous demo
local previous = playerGui:FindFirstChild("EggVisualHub")
if previous then
    previous:Destroy()
end

local Theme = {
    Background = Color3.fromRGB(17, 17, 27),
    Panel = Color3.fromRGB(26, 25, 40),
    Button = Color3.fromRGB(36, 34, 53),
    Purple = Color3.fromRGB(139, 92, 246),
    Text = Color3.fromRGB(245, 243, 255),
    Muted = Color3.fromRGB(164, 158, 184),
    Green = Color3.fromRGB(74, 222, 128),
    Red = Color3.fromRGB(248, 113, 113),
}

local gui = Instance.new("ScreenGui")
gui.Name = "EggVisualHub"
gui.ResetOnSpawn = false
gui.DisplayOrder = 50
gui.Parent = playerGui

local function create(class, properties, parent)
    local object = Instance.new(class)

    for key, value in pairs(properties) do
        object[key] = value
    end

    object.Parent = parent
    return object
end

local function corner(object, radius)
    create("UICorner", {
        CornerRadius = UDim.new(0, radius or 8)
    }, object)
end

local function stroke(object, color, transparency)
    create("UIStroke", {
        Color = color or Theme.Purple,
        Transparency = transparency or 0.5,
        Thickness = 1
    }, object)
end

local main = create("Frame", {
    Name = "Main",
    Size = UDim2.fromOffset(570, 390),
    Position = UDim2.new(0.5, -285, 0.5, -195),
    BackgroundColor3 = Theme.Background,
    BorderSizePixel = 0,
}, gui)
corner(main, 14)
stroke(main, Theme.Purple, 0.35)

-- Header
local header = create("Frame", {
    Size = UDim2.new(1, 0, 0, 57),
    BackgroundColor3 = Theme.Panel,
    BorderSizePixel = 0,
}, main)
corner(header, 14)

create("Frame", {
    Position = UDim2.new(0, 0, 1, -14),
    Size = UDim2.new(1, 0, 0, 14),
    BackgroundColor3 = Theme.Panel,
    BorderSizePixel = 0,
}, header)

create("TextLabel", {
    Position = UDim2.fromOffset(17, 7),
    Size = UDim2.new(1, -115, 0, 25),
    BackgroundTransparency = 1,
    Text = "🥚  STEAL AN EGG",
    TextColor3 = Theme.Text,
    Font = Enum.Font.GothamBold,
    TextSize = 18,
    TextXAlignment = Enum.TextXAlignment.Left,
}, header)

create("TextLabel", {
    Position = UDim2.fromOffset(19, 32),
    Size = UDim2.new(1, -130, 0, 16),
    BackgroundTransparency = 1,
    Text = "VISUAL HUB  •  DEMO v1.0",
    TextColor3 = Theme.Muted,
    Font = Enum.Font.Gotham,
    TextSize = 10,
    TextXAlignment = Enum.TextXAlignment.Left,
}, header)

local minimize = create("TextButton", {
    Position = UDim2.new(1, -79, 0, 12),
    Size = UDim2.fromOffset(30, 30),
    BackgroundColor3 = Theme.Button,
    Text = "—",
    TextColor3 = Theme.Text,
    Font = Enum.Font.GothamBold,
    TextSize = 17,
    AutoButtonColor = true,
}, header)
corner(minimize, 8)

local close = create("TextButton", {
    Position = UDim2.new(1, -42, 0, 12),
    Size = UDim2.fromOffset(30, 30),
    BackgroundColor3 = Color3.fromRGB(100, 38, 65),
    Text = "×",
    TextColor3 = Theme.Text,
    Font = Enum.Font.GothamBold,
    TextSize = 19,
}, header)
corner(close, 8)

-- Sidebar
local sidebar = create("Frame", {
    Position = UDim2.fromOffset(12, 70),
    Size = UDim2.new(0, 155, 1, -82),
    BackgroundColor3 = Theme.Panel,
    BorderSizePixel = 0,
}, main)
corner(sidebar, 10)

create("TextLabel", {
    Position = UDim2.fromOffset(12, 10),
    Size = UDim2.new(1, -24, 0, 22),
    BackgroundTransparency = 1,
    Text = "MAIN MENU",
    TextColor3 = Theme.Muted,
    Font = Enum.Font.GothamBold,
    TextSize = 10,
    TextXAlignment = Enum.TextXAlignment.Left,
}, sidebar)

local content = create("Frame", {
    Position = UDim2.fromOffset(180, 70),
    Size = UDim2.new(1, -192, 1, -82),
    BackgroundTransparency = 1,
}, main)

local pages = {}
local tabButtons = {}
local currentPage

local function makePage(name)
    local page = create("ScrollingFrame", {
        Name = name,
        Size = UDim2.fromScale(1, 1),
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ScrollBarThickness = 3,
        ScrollBarImageColor3 = Theme.Purple,
        CanvasSize = UDim2.new(),
        AutomaticCanvasSize = Enum.AutomaticSize.Y,
        Visible = false,
    }, content)

    create("UIListLayout", {
        Padding = UDim.new(0, 9),
        SortOrder = Enum.SortOrder.LayoutOrder,
    }, page)

    create("UIPadding", {
        PaddingTop = UDim.new(0, 2),
        PaddingLeft = UDim.new(0, 2),
        PaddingRight = UDim.new(0, 7),
        PaddingBottom = UDim.new(0, 8),
    }, page)

    pages[name] = page
    return page
end

local function showPage(name)
    currentPage = name

    for pageName, page in pairs(pages) do
        page.Visible = pageName == name
    end

    for tabName, button in pairs(tabButtons) do
        local active = tabName == name

        TweenService:Create(button, TweenInfo.new(0.15), {
            BackgroundColor3 = active and Theme.Purple or Theme.Button
        }):Play()
    end
end

local tabNames = {
    "Automation",
    "Egg Tracker",
    "Teleport",
    "Settings",
}

for index, name in ipairs(tabNames) do
    local button = create("TextButton", {
        Position = UDim2.fromOffset(8, 39 + (index - 1) * 43),
        Size = UDim2.new(1, -16, 0, 36),
        BackgroundColor3 = Theme.Button,
        BorderSizePixel = 0,
        Text = "   " .. name,
        TextColor3 = Theme.Text,
        Font = Enum.Font.GothamMedium,
        TextSize = 12,
        TextXAlignment = Enum.TextXAlignment.Left,
    }, sidebar)

    corner(button, 7)
    tabButtons[name] = button

    button.Activated:Connect(function()
        showPage(name)
    end)

    makePage(name)
end

local function addTitle(page, text)
    return create("TextLabel", {
        Size = UDim2.new(1, 0, 0, 30),
        BackgroundTransparency = 1,
        Text = text,
        TextColor3 = Theme.Text,
        Font = Enum.Font.GothamBold,
        TextSize = 16,
        TextXAlignment = Enum.TextXAlignment.Left,
    }, page)
end

local function addInfo(page, text)
    local label = create("TextLabel", {
        Size = UDim2.new(1, 0, 0, 42),
        BackgroundColor3 = Theme.Panel,
        BorderSizePixel = 0,
        Text = "  " .. text,
        TextColor3 = Theme.Muted,
        Font = Enum.Font.Gotham,
        TextSize = 11,
        TextWrapped = true,
        TextXAlignment = Enum.TextXAlignment.Left,
    }, page)

    corner(label, 8)
    return label
end

local toggles = {}

local function addToggle(page, name, description)
    local row = create("Frame", {
        Size = UDim2.new(1, 0, 0, 61),
        BackgroundColor3 = Theme.Panel,
        BorderSizePixel = 0,
    }, page)
    corner(row, 9)

    create("TextLabel", {
        Position = UDim2.fromOffset(11, 8),
        Size = UDim2.new(1, -82, 0, 22),
        BackgroundTransparency = 1,
        Text = name,
        TextColor3 = Theme.Text,
        Font = Enum.Font.GothamSemibold,
        TextSize = 12,
        TextXAlignment = Enum.TextXAlignment.Left,
    }, row)

    create("TextLabel", {
        Position = UDim2.fromOffset(11, 31),
        Size = UDim2.new(1, -82, 0, 19),
        BackgroundTransparency = 1,
        Text = description,
        TextColor3 = Theme.Muted,
        Font = Enum.Font.Gotham,
        TextSize = 9,
        TextXAlignment = Enum.TextXAlignment.Left,
        TextTruncate = Enum.TextTruncate.AtEnd,
    }, row)

    local enabled = false

    local button = create("TextButton", {
        Position = UDim2.new(1, -66, 0.5, -13),
        Size = UDim2.fromOffset(55, 26),
        BackgroundColor3 = Color3.fromRGB(62, 59, 77),
        BorderSizePixel = 0,
        Text = "OFF",
        TextColor3 = Theme.Text,
        Font = Enum.Font.GothamBold,
        TextSize = 10,
    }, row)
    corner(button, 7)

    local function update(value)
        enabled = value
        button.Text = enabled and "ON" or "OFF"

        TweenService:Create(button, TweenInfo.new(0.15), {
            BackgroundColor3 = enabled and Theme.Purple
                or Color3.fromRGB(62, 59, 77)
        }):Play()
    end

    button.Activated:Connect(function()
        update(not enabled)

        -- Visual only: no gameplay actions are performed.
        print("[Visual Demo] " .. name .. ": " ..
            (enabled and "ON" or "OFF"))
    end)

    toggles[name] = {
        Set = update,
        Get = function()
            return enabled
        end,
    }
end

-- AUTOMATION PAGE
local automation = pages["Automation"]
addTitle(automation, "Automation Hub")
addInfo(automation, "Visual demo only. Toggles do not automate gameplay.")

addToggle(automation, "Auto Steal Egg", "Demo toggle • no egg collection")
addToggle(automation, "Auto Farm", "Demo toggle • no farming")
addToggle(automation, "Auto Quest", "Demo toggle • no quest actions")
addToggle(automation, "Auto Collect", "Demo toggle • no rewards")

-- EGG TRACKER PAGE
local tracker = pages["Egg Tracker"]
addTitle(tracker, "Egg Tracker")
addInfo(tracker, "Sample entries only; no live world scanning.")

for _, egg in ipairs({
    "Golden Egg  •  DEMO",
    "Crystal Egg  •  DEMO",
    "Rainbow Egg  •  DEMO",
    "Mystery Egg  •  DEMO",
}) do
    addInfo(tracker, "🥚  " .. egg)
end

-- TELEPORT PAGE
local teleport = pages["Teleport"]
addTitle(teleport, "Teleport")
addInfo(teleport, "Visual placeholders. No teleportation is performed.")

for _, area in ipairs({
    "Spawn Island",
    "Golden Valley",
    "Crystal Cave",
    "Sky Kingdom",
}) do
    local button = create("TextButton", {
        Size = UDim2.new(1, 0, 0, 38),
        BackgroundColor3 = Theme.Panel,
        BorderSizePixel = 0,
        Text = "  " .. area .. "   •   DEMO",
        TextColor3 = Theme.Text,
        Font = Enum.Font.GothamMedium,
        TextSize = 11,
        TextXAlignment = Enum.TextXAlignment.Left,
    }, teleport)

    corner(button, 8)

    button.Activated:Connect(function()
        print("[Visual Demo] Selected area: " .. area)
    end)
end

-- SETTINGS PAGE
local settings = pages["Settings"]
addTitle(settings, "Settings")
addInfo(settings, "Preferences for this visual session.")

local status = addInfo(settings, "Hub status: ACTIVE")

local reset = create("TextButton", {
    Size = UDim2.new(1, 0, 0, 40),
    BackgroundColor3 = Theme.Purple,
    BorderSizePixel = 0,
    Text = "Reset Visual Toggles",
    TextColor3 = Theme.Text,
    Font = Enum.Font.GothamBold,
    TextSize = 12,
}, settings)
corner(reset, 8)

reset.Activated:Connect(function()
    for _, toggle in pairs(toggles) do
        toggle.Set(false)
    end

    status.Text = "  All visual toggles reset."
end)

-- Minimize and close
local minimized = false
local oldSize = main.Size

minimize.Activated:Connect(function()
    minimized = not minimized

    for _, child in ipairs(main:GetChildren()) do
        if child ~= header then
            child.Visible = not minimized
        end
    end

    main.Size = minimized
        and UDim2.fromOffset(570, 57)
        or oldSize

    minimize.Text = minimized and "+" or "—"
end)

close.Activated:Connect(function()
    gui:Destroy()
end)

-- Drag window using the header.
local dragging = false
local dragStart
local startPosition

header.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPosition = main.Position
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if not dragging then return end

    if input.UserInputType == Enum.UserInputType.MouseMovement
        or input.UserInputType == Enum.UserInputType.Touch then

        local delta = input.Position - dragStart

        main.Position = UDim2.new(
            startPosition.X.Scale,
            startPosition.X.Offset + delta.X,
            startPosition.Y.Scale,
            startPosition.Y.Offset + delta.Y
        )
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
    end
end)

showPage("Automation")

print("Steal an Egg Visual Hub loaded.")
