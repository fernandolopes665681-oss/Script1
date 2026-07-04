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
