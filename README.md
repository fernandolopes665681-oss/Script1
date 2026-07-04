-- Delta Executor Compatible Roblox GUI Script
-- Modern WalkSpeed & JumpPower Controller

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")

local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")

-- Configuration
local CONFIG = {
    MinWalkSpeed = 0,
    MaxWalkSpeed = 500,
    DefaultWalkSpeed = 16,
    MinJumpPower = 0,
    MaxJumpPower = 500,
    DefaultJumpPower = 50,
    UICornerRadius = 8,
    PrimaryColor = Color3.fromRGB(45, 45, 55),
    SecondaryColor = Color3.fromRGB(35, 35, 45),
    AccentColor = Color3.fromRGB(88, 101, 242),
    TextColor = Color3.fromRGB(255, 255, 255),
    SliderBackground = Color3.fromRGB(60, 60, 70),
    SliderFill = Color3.fromRGB(88, 101, 242)
}

-- Create ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "SpeedControllerGUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = game:GetService("CoreGui")

-- Main Frame
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 320, 0, 280)
MainFrame.Position = UDim2.new(0.5, -160, 0.5, -140)
MainFrame.BackgroundColor3 = CONFIG.PrimaryColor
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, CONFIG.UICornerRadius)
MainCorner.Parent = MainFrame

-- Title Bar
local TitleBar = Instance.new("Frame")
TitleBar.Name = "TitleBar"
TitleBar.Size = UDim2.new(1, 0, 0, 40)
TitleBar.BackgroundColor3 = CONFIG.SecondaryColor
TitleBar.BorderSizePixel = 0
TitleBar.Parent = MainFrame

local TitleCorner = Instance.new("UICorner")
TitleCorner.CornerRadius = UDim.new(0, CONFIG.UICornerRadius)
TitleCorner.Parent = TitleBar

local TitleText = Instance.new("TextLabel")
TitleText.Name = "TitleText"
TitleText.Size = UDim2.new(1, -80, 1, 0)
TitleText.Position = UDim2.new(0, 15, 0, 0)
TitleText.BackgroundTransparency = 1
TitleText.Text = "Speed Controller"
TitleText.TextColor3 = CONFIG.TextColor
TitleText.TextSize = 18
TitleText.Font = Enum.Font.GothamBold
TitleText.TextXAlignment = Enum.TextXAlignment.Left
TitleText.Parent = TitleBar

-- Minimize Button
local MinimizeButton = Instance.new("TextButton")
MinimizeButton.Name = "MinimizeButton"
MinimizeButton.Size = UDim2.new(0, 30, 0, 30)
MinimizeButton.Position = UDim2.new(1, -70, 0.5, -15)
MinimizeButton.BackgroundColor3 = CONFIG.AccentColor
MinimizeButton.Text = "-"
MinimizeButton.TextColor3 = CONFIG.TextColor
MinimizeButton.TextSize = 20
MinimizeButton.Font = Enum.Font.GothamBold
MinimizeButton.Parent = TitleBar

local MinimizeCorner = Instance.new("UICorner")
MinimizeCorner.CornerRadius = UDim.new(0, 6)
MinimizeCorner.Parent = MinimizeButton

-- Close Button
local CloseButton = Instance.new("TextButton")
CloseButton.Name = "CloseButton"
CloseButton.Size = UDim2.new(0, 30, 0, 30)
CloseButton.Position = UDim2.new(1, -35, 0.5, -15)
CloseButton.BackgroundColor3 = Color3.fromRGB(220, 60, 60)
CloseButton.Text = "X"
CloseButton.TextColor3 = CONFIG.TextColor
CloseButton.TextSize = 16
CloseButton.Font = Enum.Font.GothamBold
CloseButton.Parent = TitleBar

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 6)
CloseCorner.Parent = CloseButton

-- Content Frame
local ContentFrame = Instance.new("Frame")
ContentFrame.Name = "ContentFrame"
ContentFrame.Size = UDim2.new(1, -20, 1, -50)
ContentFrame.Position = UDim2.new(0, 10, 0, 45)
ContentFrame.BackgroundTransparency = 1
ContentFrame.Parent = MainFrame

-- Helper function to create slider section
local function createSliderSection(name, defaultValue, minValue, maxValue, yPos, callback)
    local Section = Instance.new("Frame")
    Section.Name = name .. "Section"
    Section.Size = UDim2.new(1, 0, 0, 90)
    Section.Position = UDim2.new(0, 0, 0, yPos)
    Section.BackgroundColor3 = CONFIG.SecondaryColor
    Section.BorderSizePixel = 0
    Section.Parent = ContentFrame

    local SectionCorner = Instance.new("UICorner")
    SectionCorner.CornerRadius = UDim.new(0, CONFIG.UICornerRadius)
    SectionCorner.Parent = Section

    local Label = Instance.new("TextLabel")
    Label.Name = "Label"
    Label.Size = UDim2.new(0, 120, 0, 25)
    Label.Position = UDim2.new(0, 12, 0, 8)
    Label.BackgroundTransparency = 1
    Label.Text = name
    Label.TextColor3 = CONFIG.TextColor
    Label.TextSize = 14
    Label.Font = Enum.Font.GothamSemibold
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.Parent = Section

    local ValueBox = Instance.new("TextBox")
    ValueBox.Name = "ValueBox"
    ValueBox.Size = UDim2.new(0, 60, 0, 25)
    ValueBox.Position = UDim2.new(1, -72, 0, 8)
    ValueBox.BackgroundColor3 = CONFIG.PrimaryColor
    ValueBox.Text = tostring(defaultValue)
    ValueBox.TextColor3 = CONFIG.TextColor
    ValueBox.TextSize = 14
    ValueBox.Font = Enum.Font.Gotham
    ValueBox.ClearTextOnFocus = false
    ValueBox.Parent = Section

    local ValueCorner = Instance.new("UICorner")
    ValueCorner.CornerRadius = UDim.new(0, 4)
    ValueCorner.Parent = ValueBox

    -- Slider Background
    local SliderBg = Instance.new("Frame")
    SliderBg.Name = "SliderBg"
    SliderBg.Size = UDim2.new(1, -24, 0, 12)
    SliderBg.Position = UDim2.new(0, 12, 0, 45)
    SliderBg.BackgroundColor3 = CONFIG.SliderBackground
    SliderBg.BorderSizePixel = 0
    SliderBg.Parent = Section

    local SliderBgCorner = Instance.new("UICorner")
    SliderBgCorner.CornerRadius = UDim.new(0, 6)
    SliderBgCorner.Parent = SliderBg

    -- Slider Fill
    local SliderFill = Instance.new("Frame")
    SliderFill.Name = "SliderFill"
    SliderFill.Size = UDim2.new((defaultValue - minValue) / (maxValue - minValue), 0, 1, 0)
    SliderFill.BackgroundColor3 = CONFIG.SliderFill
    SliderFill.BorderSizePixel = 0
    SliderFill.Parent = SliderBg

    local SliderFillCorner = Instance.new("UICorner")
    SliderFillCorner.CornerRadius = UDim.new(0, 6)
    SliderFillCorner.Parent = SliderFill

    -- Slider Knob
    local SliderKnob = Instance.new("Frame")
    SliderKnob.Name = "SliderKnob"
    SliderKnob.Size = UDim2.new(0, 18, 0, 18)
    SliderKnob.Position = UDim2.new((defaultValue - minValue) / (maxValue - minValue), -9, 0.5, -9)
    SliderKnob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    SliderKnob.BorderSizePixel = 0
    SliderKnob.Parent = SliderBg

    local SliderKnobCorner = Instance.new("UICorner")
    SliderKnobCorner.CornerRadius = UDim.new(0.5, 0)
    SliderKnobCorner.Parent = SliderKnob

    -- Slider Logic
    local isDragging = false
    local function updateSlider(input)
        local pos = math.clamp((input.Position.X - SliderBg.AbsolutePosition.X) / SliderBg.AbsoluteSize.X, 0, 1)
        local value = math.floor(minValue + (pos * (maxValue - minValue)))
        
        SliderFill.Size = UDim2.new(pos, 0, 1, 0)
        SliderKnob.Position = UDim2.new(pos, -9, 0.5, -9)
        ValueBox.Text = tostring(value)
        callback(value)
    end

    SliderBg.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            isDragging = true
            updateSlider(input)
        end
    end)

    SliderKnob.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            isDragging = true
        end
    end)

    game:GetService("UserInputService").InputChanged:Connect(function(input)
        if isDragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            updateSlider(input)
        end
    end)

    game:GetService("UserInputService").InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            isDragging = false
        end
    end)

    ValueBox.FocusLost:Connect(function()
        local value = tonumber(ValueBox.Text)
        if value then
            value = math.clamp(math.floor(value), minValue, maxValue)
            local pos = (value - minValue) / (maxValue - minValue)
            SliderFill.Size = UDim2.new(pos, 0, 1, 0)
            SliderKnob.Position = UDim2.new(pos, -9, 0.5, -9)
            ValueBox.Text = tostring(value)
            callback(value)
        else
            ValueBox.Text = tostring(minValue)
            callback(minValue)
        end
    end)

    return Section
end

-- State variables
local currentWalkSpeed = CONFIG.DefaultWalkSpeed
local currentJumpPower = CONFIG.DefaultJumpPower
local isMinimized = false

-- Create WalkSpeed Section
createSliderSection("WalkSpeed", CONFIG.DefaultWalkSpeed, CONFIG.MinWalkSpeed, CONFIG.MaxWalkSpeed, 0, function(value)
    currentWalkSpeed = value
    if Humanoid then
        Humanoid.WalkSpeed = value
    end
end)

-- Create JumpPower Section
createSliderSection("JumpPower", CONFIG.DefaultJumpPower, CONFIG.MinJumpPower, CONFIG.MaxJumpPower, 100, function(value)
    currentJumpPower = value
    if Humanoid then
        Humanoid.JumpPower = value
    end
end)

-- Reset Button
local ResetButton = Instance.new("TextButton")
ResetButton.Name = "ResetButton"
ResetButton.Size = UDim2.new(1, 0, 0, 35)
ResetButton.Position = UDim2.new(0, 0, 0, 200)
ResetButton.BackgroundColor3 = CONFIG.AccentColor
ResetButton.Text = "Reset to Default"
ResetButton.TextColor3 = CONFIG.TextColor
ResetButton.TextSize = 14
ResetButton.Font = Enum.Font.GothamSemibold
ResetButton.Parent = ContentFrame

local ResetCorner = Instance.new("UICorner")
ResetCorner.CornerRadius = UDim.new(0, CONFIG.UICornerRadius)
ResetCorner.Parent = ResetButton

-- Minimize functionality
MinimizeButton.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    if isMinimized then
        MinimizeButton.Text = "+"
        TweenService:Create(ContentFrame, TweenInfo.new(0.3), {Size = UDim2.new(1, -20, 0, 0)}):Play()
        TweenService:Create(MainFrame, TweenInfo.new(0.3), {Size = UDim2.new(0, 320, 0, 50)}):Play()
        ContentFrame.Visible = false
    else
        MinimizeButton.Text = "-"
        ContentFrame.Visible = true
        TweenService:Create(ContentFrame, TweenInfo.new(0.3), {Size = UDim2.new(1, -20, 1, -50)}):Play()
        TweenService:Create(MainFrame, TweenInfo.new(0.3), {Size = UDim2.new(0, 320, 0, 280)}):Play()
    end
end)

-- Close functionality
CloseButton.MouseButton1Click:Connect(function()
    ScreenGui:Destroy()
end)

-- Reset functionality
ResetButton.MouseButton1Click:Connect(function()
    currentWalkSpeed = CONFIG.DefaultWalkSpeed
    currentJumpPower = CONFIG.DefaultJumpPower
    
    -- Update UI elements
    for _, section in pairs(ContentFrame:GetChildren()) do
        if section:IsA("Frame") then
