--//Força Do Caralho pra jogar longe\\-- 
local StrenghtMultiplier = 10000 -- Valor inicial
--\\End//--

--//Não sei que porra é essa mas eu fiz kkkkkk\\-- 
local UserInputService = game:GetService("UserInputService")
local Workspace = game:GetService("Workspace")
local Debris = game:GetService("Debris")
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
--\\End//--

--//Interface\\--
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "StrenghtDisplay"
screenGui.ResetOnSpawn = false
screenGui.Parent = playerGui

local textLabel = Instance.new("TextLabel")
textLabel.Size = UDim2.new(0, 200, 0, 50)
textLabel.Position = UDim2.new(0, 20, 0, 20)
textLabel.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
textLabel.TextColor3 = Color3.new(1, 1, 1)
textLabel.TextScaled = true
textLabel.Font = Enum.Font.SourceSansBold
textLabel.Text = "Strenght: " .. StrenghtMultiplier
textLabel.Parent = screenGui
--\\End//--

--//Função para atualizar a porra da UI\\--
local function updateLabel()
	textLabel.Text = "Strenght: " .. StrenghtMultiplier
end

--//n sei\\--
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end

	if input.KeyCode == Enum.KeyCode.R then
		StrenghtMultiplier = StrenghtMultiplier + 100
		updateLabel()
	elseif input.KeyCode == Enum.KeyCode.Q then
		StrenghtMultiplier = math.max(100, StrenghtMultiplier - 100)
		updateLabel()
	end
end)
--\\End//--

--//Fling\\--
Workspace.ChildAdded:Connect(function(NewModel)
	if NewModel.Name == "GrabParts" then
		local PartToImpulse = NewModel["GrabPart"]["WeldConstraint"].Part1
		if PartToImpulse then
			local VelocityObject = Instance.new("BodyVelocity", PartToImpulse)
			NewModel:GetPropertyChangedSignal("Parent"):Connect(function()
				if not NewModel.Parent then
					if UserInputService:GetLastInputType() == Enum.UserInputType.MouseButton2 then
						VelocityObject.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
						VelocityObject.Velocity = workspace.CurrentCamera.CFrame.lookVector * StrenghtMultiplier
						Debris:AddItem(VelocityObject, 1)
					else
						VelocityObject:Destroy()
					end
				end
			end)
		end
	end
end)
--\\End//--
