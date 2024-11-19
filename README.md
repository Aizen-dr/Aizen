local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()
 
local Window = Fluent:CreateWindow({
   Title = "Nika - Fisch",
   SubTitle = "v.beta",
   TabWidth = 160,
   Size = UDim2.fromOffset(580, 460),
   Acrylic = false, -- The blur may be detectable, setting this to false disables blur entirely
   Theme = "Dark",
   MinimizeKey = Enum.KeyCode.LeftControl -- Used when theres no MinimizeKeybind
})
 
local Enabled = false
local Rod = false
local Casted = false
local Progress = false
local Finished = false
 
local ZoneFish = false
 
local Keybind = Enum.KeyCode.Q
 
local waittime = 0.001
 
local Enabled = false
local Rod = false
local Casted = false
local Progress = false
local Finished = false
 
local ZoneFish = false
 
local Keybind = Enum.KeyCode.Q
 
local waittime = 0.001
 
function ToggleFarm(Name, State, Input)
    if State == Enum.UserInputState.Begin then
        Enabled = not Enabled
 
        if not Enabled then
            Finished = false
            Progress = false
 
            if Rod then
                Rod.events.reset:FireServer()
            end
        end
    end
end
 
LocalPlayer.Character.ChildAdded:Connect(function(Child)
    if Child:IsA('Tool') and Child.Name:lower():find('rod') then
        Rod = Child
    end
end)
 
LocalPlayer.Character.ChildRemoved:Connect(function(Child)
    if Child == Rod then
        Enabled = false
        Finished = false
        Progress = false
        Rod = nil
    end
end)
 
LocalPlayer.PlayerGui.DescendantAdded:Connect(function(Descendant)
    if Enabled and AutoFish then
        if Descendant.Name == 'button' and Descendant.Parent.Name == 'safezone' then
 
            task.wait(waittime)
            local buttonPosition = Descendant.AbsolutePosition
            local buttonSize = Descendant.AbsoluteSize
            local centerX = buttonPosition.X + buttonSize.X / 2
            local centerY = buttonPosition.Y + buttonSize.Y / 2
 
            VirtualInputManager:SendMouseButtonEvent(centerX, centerY, 0, true, game, 0) -- Mouse down (left button)
            VirtualInputManager:SendMouseButtonEvent(centerX, centerY, 0, false, game, 0) -- Mouse up (left button)
 
            task.wait(waittime)
 
        elseif Descendant.Name == 'playerbar' and Descendant.Parent.Name == 'bar' then
            local Finished = true
            Descendant:GetPropertyChangedSignal('Position'):Wait()
            ReplicatedStorage.events.reelfinished:FireServer(100, true)
            task.wait(3.2)
        end
    end
end)
 
 
LocalPlayer.PlayerGui.DescendantRemoving:Connect(function(Descendant)
    if Descendant.Name == 'reel' then
        task.wait(1)
        Finished = false
        Progress = false
    end
end)
 
coroutine.wrap(function()
    while true do
        if Enabled and AutoFish and not Progress then
            if Rod then
                Progress = true
 
                task.wait(1)
 
                Rod.events.reset:FireServer()
                Rod.events.cast:FireServer(100.5)
 
                task.wait(1)
            end
        end
 
        task.wait()
    end
end)()
 
local NewRod = LocalPlayer.Character:FindFirstChildWhichIsA('Tool')
 
if NewRod and NewRod.Name:lower():find('rod') then
    Rod = NewRod
end
 
if not UserInputService.KeyboardEnabled then
    ContextActionService:BindAction('ToggleFarm', ToggleFarm, false, Keybind, Enum.UserInputType.Touch)
    ContextActionService:SetTitle('ToggleFarm', 'Toggle Farm')
    ContextActionService:SetPosition('ToggleFarm', UDim2.new(0.9, -50, 0.9, -150))
else
    ContextActionService:BindAction('ToggleFarm', ToggleFarm, false, Keybind)
end
