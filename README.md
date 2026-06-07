--// FLUENT LOAD
local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()

--// SERVICES
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

--// ESP SETTINGS
local ESPEnabled = false
local ESPColor = Color3.fromRGB(255, 0, 0)

--// WINDOW
local Window = Fluent:CreateWindow({
    Title = "Ryvex Hub",
    SubTitle = "by JH",
    TabWidth = 160,
    Size = UDim2.fromOffset(580, 460),
    Acrylic = true,
    Theme = "Dark",
    MinimizeKey = Enum.KeyCode.LeftControl
})

--// TABS
local Tabs = {
    Home = Window:AddTab({ Title = "Home", Icon = "home" }),
    Visuals = Window:AddTab({ Title = "Visuals", Icon = "eye" }),
    Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
}

--// HOME
Tabs.Home:AddParagraph({
    Title = "Ryvex Hub",
    Content = "Sistema carregado com sucesso"
})

--// ESP FUNCTION
local function createHighlight(character)
    if not character then return end
    if character:FindFirstChild("RyvexHighlight") then return end

    local highlight = Instance.new("Highlight")
    highlight.Name = "RyvexHighlight"
    highlight.Adornee = character
    highlight.FillColor = ESPColor
    highlight.OutlineColor = Color3.fromRGB(255,255,255)
    highlight.FillTransparency = 0.5
    highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    highlight.Parent = character
end

local function removeHighlight(character)
    if not character then return end

    local h = character:FindFirstChild("RyvexHighlight")
    if h then h:Destroy() end
end

--// LOOP ESP (OTIMIZADO)
task.spawn(function()
    while true do
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer then
                local char = player.Character

                if char then
                    if ESPEnabled then
                        createHighlight(char)
                    else
                        removeHighlight(char)
                    end
                end
            end
        end
        task.wait(0.2)
    end
end)

--// AUTO RESPWAN SUPPORT
Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(char)
        task.wait(1)
        if ESPEnabled then
            createHighlight(char)
        end
    end)
end)

--// UI CONTROLS
local ESPToggle = Tabs.Visuals:AddToggle("ESP", {
    Title = "ESP Highlight",
    Default = false
})

ESPToggle:OnChanged(function(v)
    ESPEnabled = v
end)

local ColorPicker = Tabs.Visuals:AddColorpicker("ESPColor", {
    Title = "Cor do ESP",
    Default = ESPColor
})

ColorPicker:OnChanged(function(v)
    ESPColor = v
end)

--// NOTIFY
Fluent:Notify({
    Title = "Ryvex Hub",
    Content = "Carregado com sucesso!",
    Duration = 5
})

--// SAVE SYSTEM
SaveManager:SetLibrary(Fluent)
InterfaceManager:SetLibrary(Fluent)

SaveManager:IgnoreThemeSettings()
SaveManager:SetIgnoreIndexes({})

InterfaceManager:SetFolder("RyvexHub")
SaveManager:SetFolder("RyvexHub/Configs")

InterfaceManager:BuildInterfaceSection(Tabs.Settings)
SaveManager:BuildConfigSection(Tabs.Settings)

Window:SelectTab(1)
SaveManager:LoadAutoloadConfig()
