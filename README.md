
--[[
    Aroy Sync - Pet Spawner UI (Simulation Only)

    Setup:
    StarterPlayer > StarterPlayerScripts

    RightShift = Toggle UI
    Enter = Start progress

    No pets are spawned or cloned.
]]

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer

--------------------------------------------------
-- CONFIG
--------------------------------------------------

local COLORS = {
    window = Color3.fromRGB(24, 28, 40),
    panel = Color3.fromRGB(18, 21, 31),
    button = Color3.fromRGB(30, 35, 50),
    accent = Color3.fromRGB(56, 174, 255),
    text = Color3.fromRGB(235, 240, 250),
    subtext = Color3.fromRGB(150, 160, 180),
}

local SEARCH_SECONDS = 120

--------------------------------------------------
-- PET NAMES
--------------------------------------------------

local PET_NAMES = {
    "2021 Uplift Butterfly",
    "2022 Uplift Butterfly",
    "2025 Birthday Butterfly",
    "2026 Birthday Butterfly",
    "2D Doggy",
    "2D Kitty",
    "Abyssinian Cat",
    "African Wild Dog",
    "Albatross",
    "Albino Bat",
    "Albino Gorilla",
    "Albino Monkey",
    "Alicorn",
    "Alley Cat",
    "Alpaca",
    "Amami Rabbit",
    "Amber Butterfly",
    "Amethyst Penguin",
    "Ancient Dragon",
    "Angelfish",
    "Angler Fish",
    "Ankylosaurus",
    "Ant",
    "Apple Owl",
    "Arctic Fox",
    "Arctic Hare",
    "Arctic Reindeer",
    "Armadillo",
    "Ash Zebra",
    "Aurora Fox",
    "Australian Kelpie",
    "Axolotl",
    "Aye Aye",
    "Badger",
    "Bakeneko",
    "Baku",
    "Bald Eagle",
    "Bali Starling",
    "Ballet Swan",
    "Balloon Unicorn",
    "Bandicoot",
    "Basilisk",
    "Bat",
    "Bat Dragon",
    "Beaver",
    "Bee",
    "Beluga Whale",
    "Berry Cool Cube",
    "Billy Goat",
    "Binturong",
    "Bird of Paradise",
    "Bison",
    "Black Chow-Chow",
    "Black Panther",
    "Black Rhino",
    "Black Tiger",
    "Blazing Lion",
    "Bloodhound",
    "Blue Betta Fish",
    "Blue Butterfly",
    "Blue Cat",
    "Blue Dog",
    "Blue Jay",
    "Blue Whale",
    "Border Collie",
    "Brachiosaurus",
    "Frost Dragon",
    "Giraffe",
    "Shadow Dragon",
}

local allowed = {}

local function normalize(str)
    return string.lower(
        string.gsub(
            string.match(str, "^%s*(.-)%s*$"),
            "%s+",
            " "
        )
    )
end

for _, name in ipairs(PET_NAMES) do
    allowed[normalize(name)] = true
end

--------------------------------------------------
-- UI HELPERS
--------------------------------------------------

local function create(class, props)
    local obj = Instance.new(class)

    for key, value in pairs(props) do
        obj[key] = value
    end

    return obj
end

local function round(obj, radius)
    create("UICorner", {
        CornerRadius = UDim.new(0, radius),
        Parent = obj,
    })
end

local function makeButton(parent, text, size)
    local button = create("TextButton", {
        Parent = parent,
        Size = size,
        Text = text,
        BackgroundColor3 = COLORS.button,
        TextColor3 = COLORS.subtext,
        Font = Enum.Font.GothamMedium,
        TextSize = 12,
        AutoButtonColor = false,
    })

    round(button, 6)

    return button
end

--------------------------------------------------
-- MAIN GUI
--------------------------------------------------

local gui = create("ScreenGui", {
    Name = "AroySync",
    ResetOnSpawn = false,
    Parent = player:WaitForChild("PlayerGui"),
    ZIndexBehavior = Enum.ZIndexBehavior.Sibling,
})

local window = create("Frame", {
    Parent = gui,
    Size = UDim2.fromOffset(600, 470),
    AnchorPoint = Vector2.new(0.5, 0.5),
    Position = UDim2.fromScale(0.5, 0.5),
    BackgroundColor3 = COLORS.window,
})

round(window, 12)

--------------------------------------------------
-- TITLE BAR
--------------------------------------------------

local titleBar = create("Frame", {
    Parent = window,
    Size = UDim2.new(1, 0, 0, 40),
    BackgroundTransparency = 1,
})

local logo = create("TextLabel", {
    Parent = titleBar,
    Size = UDim2.fromOffset(24, 24),
    Position = UDim2.fromOffset(12, 8),
    BackgroundColor3 = COLORS.accent,
    Text = "A",
    TextColor3 = Color3.new(1, 1, 1),
    Font = Enum.Font.GothamBold,
    TextSize = 14,
})

round(logo, 6)

create("TextLabel", {
    Parent = titleBar,
    Size = UDim2.fromOffset(120, 24),
    Position = UDim2.fromOffset(44, 8),
    BackgroundTransparency = 1,
    Text = "Aroy Sync",
    TextColor3 = COLORS.text,
    Font = Enum.Font.GothamBold,
    TextSize = 14,
    TextXAlignment = Enum.TextXAlignment.Left,
})

local tab = create("TextLabel", {
    Parent = titleBar,
    Size = UDim2.fromOffset(80, 26),
    Position = UDim2.fromOffset(160, 7),
    BackgroundColor3 = COLORS.accent,
    Text = "Spawner",
    TextColor3 = Color3.new(1, 1, 1),
    Font = Enum.Font.GothamMedium,
    TextSize = 12,
})

round(tab, 6)

local closeButton = makeButton(
    titleBar,
    "X",
    UDim2.fromOffset(26, 26)
)

closeButton.Position = UDim2.new(1, -38, 0, 7)

closeButton.MouseButton1Click:Connect(function()
    gui.Enabled = false
end)

--------------------------------------------------
-- DRAGGING
--------------------------------------------------

local dragging = false
local dragStart
local startPosition

titleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPosition = window.Position
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart

        window.Position = UDim2.new(
            startPosition.X.Scale,
            startPosition.X.Offset + delta.X,
            startPosition.Y.Scale,
            startPosition.Y.Offset + delta.Y
        )
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

UserInputService.InputBegan:Connect(function(input, processed)
    if not processed and input.KeyCode == Enum.KeyCode.RightShift then
        gui.Enabled = not gui.Enabled
    end
end)

--------------------------------------------------
-- CONTENT
--------------------------------------------------

local content = create("Frame", {
    Parent = window,
    Size = UDim2.new(1, -24, 1, -52),
    Position = UDim2.fromOffset(12, 44),
    BackgroundTransparency = 1,
})

create("UIListLayout", {
    Parent = content,
    SortOrder = Enum.SortOrder.LayoutOrder,
    Padding = UDim.new(0, 6),
})

local order = 0

local function nextOrder()
    order += 1
    return order
end

local function section(text)
    create("TextLabel", {
        Parent = content,
        Size = UDim2.new(1, 0, 0, 16),
        BackgroundTransparency = 1,
        Text = text,
        TextColor3 = COLORS.subtext,
        Font = Enum.Font.GothamMedium,
        TextSize = 12,
        TextXAlignment = Enum.TextXAlignment.Left,
        LayoutOrder = nextOrder(),
    })
end

--------------------------------------------------
-- PET FILTERS
--------------------------------------------------

section("Pet Filters")

local filterRow = create("Frame", {
    Parent = content,
    Size = UDim2.new(1, 0, 0, 28),
    BackgroundTransparency = 1,
    LayoutOrder = nextOrder(),
})

create("UIListLayout", {
    Parent = filterRow,
    FillDirection = Enum.FillDirection.Horizontal,
    Padding = UDim.new(0, 8),
})

for _, name in ipairs({"Fly", "Ride", "Neon", "Mega"}) do
    local button = makeButton(
        filterRow,
        name,
        UDim2.new(0.25, -6, 1, 0)
    )

    local enabled = false

    button.MouseButton1Click:Connect(function()
        enabled = not enabled

        button.BackgroundColor3 = enabled
            and COLORS.accent
            or COLORS.button
    end)
end

--------------------------------------------------
-- PET AGE
--------------------------------------------------

section("Pet Age")

local ageGrid = create("Frame", {
    Parent = content,
    Size = UDim2.new(1, 0, 0, 64),
    BackgroundTransparency = 1,
    LayoutOrder = nextOrder(),
})

create("UIGridLayout", {
    Parent = ageGrid,
    CellSize = UDim2.new(1/3, -6, 0, 28),
    CellPadding = UDim2.fromOffset(8, 8),
})

local ages = {
    "Newborn",
    "Junior",
    "Pre-Teen",
    "Teen",
    "Post-Teen",
    "Full Grown",
}

for i, name in ipairs(ages) do
    local button = makeButton(
        ageGrid,
        name,
        UDim2.new()
    )

    button.LayoutOrder = i

    if name == "Full Grown" then
        button.BackgroundColor3 = COLORS.accent
    end

    button.MouseButton1Click:Connect(function()
        for _, child in ipairs(ageGrid:GetChildren()) do
            if child:IsA("TextButton") then
                child.BackgroundColor3 = COLORS.button
            end
        end

        button.BackgroundColor3 = COLORS.accent
    end)
end

--------------------------------------------------
-- SEARCH CONTROLS
--------------------------------------------------

section("Items")

local controls = create("Frame", {
    Parent = content,
    Size = UDim2.new(1, 0, 0, 30),
    BackgroundTransparency = 1,
    LayoutOrder = nextOrder(),
})

create("UIListLayout", {
    Parent = controls,
    FillDirection = Enum.FillDirection.Horizontal,
    Padding = UDim.new(0, 8),
    VerticalAlignment = Enum.VerticalAlignment.Center,
})

local search = create("TextBox", {
    Parent = controls,
    Size = UDim2.new(1, -336, 1, 0),
    BackgroundColor3 = COLORS.panel,
    PlaceholderText = "Search pet... (press Enter)",
    PlaceholderColor3 = COLORS.subtext,
    Text = "",
    TextColor3 = COLORS.text,
    Font = Enum.Font.Gotham,
    TextSize = 12,
    TextXAlignment = Enum.TextXAlignment.Left,
    ClearTextOnFocus = false,
})

round(search, 6)

create("UIPadding", {
    Parent = search,
    PaddingLeft = UDim.new(0, 10),
})

local selectAll = makeButton(
    controls,
    "Select All",
    UDim2.fromOffset(84, 30)
)

local clear = makeButton(
    controls,
    "Clear",
    UDim2.fromOffset(60, 30)
)

local spawn = makeButton(
    controls,
    "Spawn",
    UDim2.fromOffset(76, 30)
)

local despawn = makeButton(
    controls,
    "Despawn",
    UDim2.fromOffset(84, 30)
)

spawn.BackgroundColor3 = COLORS.accent

--------------------------------------------------
-- PROGRESS BAR
--------------------------------------------------

local progressPanel = create("Frame", {
    Parent = content,
    Size = UDim2.new(1, 0, 0, 40),
    BackgroundColor3 = COLORS.panel,
    Visible = false,
    LayoutOrder = nextOrder(),
})

round(progressPanel, 8)

local progressLabel = create("TextLabel", {
    Parent = progressPanel,
    Size = UDim2.new(1, -20, 0, 18),
    Position = UDim2.fromOffset(10, 3),
    BackgroundTransparency = 1,
    Text = "Ready",
    TextColor3 = COLORS.text,
    Font = Enum.Font.GothamMedium,
    TextSize = 12,
    TextXAlignment = Enum.TextXAlignment.Left,
})

local barBack = create("Frame", {
    Parent = progressPanel,
    Size = UDim2.new(1, -20, 0, 8),
    Position = UDim2.new(0, 10, 1, -13),
    BackgroundColor3 = COLORS.button,
    BorderSizePixel = 0,
})

round(barBack, 4)

local barFill = create("Frame", {
    Parent = barBack,
    Size = UDim2.fromScale(0.01, 1),
    BackgroundColor3 = COLORS.accent,
    BorderSizePixel = 0,
})

round(barFill, 4)

--------------------------------------------------
-- PET GRID
--------------------------------------------------

local grid = create("ScrollingFrame", {
    Parent = content,
    Size = UDim2.new(1, 0, 0, 150),
    BackgroundColor3 = COLORS.panel,
    BorderSizePixel = 0,
    ScrollBarThickness = 4,
    AutomaticCanvasSize = Enum.AutomaticSize.Y,
    CanvasSize = UDim2.new(),
    LayoutOrder = nextOrder(),
})

round(grid, 8)

create("UIPadding", {
    Parent = grid,
    PaddingTop = UDim.new(0, 8),
    PaddingLeft = UDim.new(0, 8),
    PaddingRight = UDim.new(0, 0),
    PaddingBottom = UDim.new(0, 8),
})

create("UIGridLayout", {
    Parent = grid,
    CellSize = UDim2.fromOffset(88, 54),
    CellPadding = UDim2.fromOffset(8, 8),
    SortOrder = Enum.SortOrder.LayoutOrder,
})

--------------------------------------------------
-- PET CARDS
--------------------------------------------------

local cards = {}
local selected = {}

for i, name in ipairs(PET_NAMES) do

    local card = makeButton(
        grid,
        name,
        UDim2.fromOffset(88, 54)
    )

    card.LayoutOrder = i

    -- TEXT SPACING FIX
    card.TextWrapped = true
    card.TextScaled = false
    card.TextSize = 10
    card.TextXAlignment = Enum.TextXAlignment.Center
    card.TextYAlignment = Enum.TextYAlignment.Center
    card.TextTruncate = Enum.TextTruncate.None

    -- Keep text inside its own card.
    card.ClipsDescendants = true

    cards[name] = card

    card.MouseButton1Click:Connect(function()
        selected[name] = not selected[name]

        card.BackgroundColor3 = selected[name]
            and COLORS.accent
            or COLORS.button
    end)
end

--------------------------------------------------
-- SEARCH FILTER
--------------------------------------------------

local function applyFilter()
    local query = normalize(search.Text)

    for name, card in pairs(cards) do
        card.Visible = query == ""
            or string.find(
                normalize(name),
                query,
                1,
                true
            ) ~= nil
    end

    grid.CanvasPosition = Vector2.new(0, 0)
end

search:GetPropertyChangedSignal("Text"):Connect(applyFilter)

--------------------------------------------------
-- SELECT ALL / CLEAR
--------------------------------------------------

selectAll.MouseButton1Click:Connect(function()
    for name, card in pairs(cards) do
        if card.Visible then
            selected[name] = true
            card.BackgroundColor3 = COLORS.accent
        end
    end
end)

clear.MouseButton1Click:Connect(function()
    table.clear(selected)

    for _, card in pairs(cards) do
        card.BackgroundColor3 = COLORS.button
    end
end)

--------------------------------------------------
-- SIMULATED PROGRESS
--------------------------------------------------

local busy = false
local runId = 0

local function startProgress(petName)

    if busy then
        return
    end

    busy = true
    runId += 1

    local thisRun = runId

    progressPanel.Visible = true
    barFill.Size = UDim2.fromScale(0.01, 1)

    progressLabel.Text = "Spawning " .. petName .. "... 1%"

    task.spawn(function()

        local startTime = os.clock()

        while thisRun == runId do

            local elapsed = os.clock() - startTime

            local fraction = math.clamp(
                elapsed / SEARCH_SECONDS,
                0,
                1
            )

            local percent = math.clamp(
                math.floor(fraction * 100),
                1,
                100
            )

            progressLabel.Text = string.format(
                "Spawning %s... %d%%",
                petName,
                percent
            )

            barFill.Size = UDim2.fromScale(
                math.max(fraction, 0.01),
                1
            )

            if fraction >= 1 then
                break
            end

            RunService.Heartbeat:Wait()
        end

        if thisRun ~= runId then
            return
        end

        progressLabel.Text = "Spawner completed!"

        barFill.Size = UDim2.fromScale(1, 1)

        busy = false

        task.wait(2)

        if thisRun == runId and not busy then
            progressPanel.Visible = false
        end

    end)
end

--------------------------------------------------
-- ENTER SEARCH
--------------------------------------------------

search.FocusLost:Connect(function(enterPressed)

    if not enterPressed or busy then
        return
    end

    local query = normalize(search.Text)

    if query == "" then
        return
    end

    if allowed[query] then
        startProgress(search.Text)
    else
        progressPanel.Visible = true

        barFill.Size = UDim2.fromScale(0, 1)

        progressLabel.Text = "Pet not found in the list."
    end

end)

--------------------------------------------------
-- SPAWN / DESPAWN BUTTONS
--------------------------------------------------

-- Intentionally simulation-only.
-- No pet models are cloned or spawned.

spawn.MouseButton1Click:Connect(function()

    progressPanel.Visible = true

    progressLabel.Text = "Use the search bar and press Enter."

    barFill.Size = UDim2.fromScale(0, 1)

end)

despawn.MouseButton1Click:Connect(function()

    progressPanel.Visible = true

    progressLabel.Text = "No spawned pets."

    barFill.Size = UDim2.fromScale(0, 1)

end)

--------------------------------------------------
-- FINISHED
--------------------------------------------------

print("[Aroy Sync] Loaded successfully!")
