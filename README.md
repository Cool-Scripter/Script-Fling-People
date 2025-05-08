
--//Forças\\--
local StrenghtMultiplier = 450
--\\End//--

--//Var\\-- 
local UserInputService = game:GetService("UserInputService")
local Workspace = game:GetService("Workspace")
local Debris = game:GetService("Debris")
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
--\\End//--

--//Interface UI\\--
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

local function updateLabel()
	textLabel.Text = "Strenght: " .. StrenghtMultiplier
end

--//Botões Touch para Celular\\--
local increaseButton = Instance.new("TextButton")
increaseButton.Size = UDim2.new(0, 100, 0, 50)
increaseButton.Position = UDim2.new(1, -220, 1, -120)
increaseButton.AnchorPoint = Vector2.new(0, 1)
increaseButton.Text = "+ Força"
increaseButton.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
increaseButton.TextColor3 = Color3.new(1, 1, 1)
increaseButton.Parent = screenGui

local decreaseButton = Instance.new("TextButton")
decreaseButton.Size = UDim2.new(0, 100, 0, 50)
decreaseButton.Position = UDim2.new(1, -110, 1, -120)
decreaseButton.AnchorPoint = Vector2.new(0, 1)
decreaseButton.Text = "- Força"
decreaseButton.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
decreaseButton.TextColor3 = Color3.new(1, 1, 1)
decreaseButton.Parent = screenGui

--//Funções dos Botões\\--
increaseButton.MouseButton1Click:Connect(function()
	StrenghtMultiplier += 100
	updateLabel()
end)

decreaseButton.MouseButton1Click:Connect(function()
	StrenghtMultiplier = math.max(100, StrenghtMultiplier - 100)
	updateLabel()
end)

--//Teclas para PC\\--
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end
	if input.KeyCode == Enum.KeyCode.R then
		StrenghtMultiplier += 100
		updateLabel()
	elseif input.KeyCode == Enum.KeyCode.Q then
		StrenghtMultiplier = math.max(100, StrenghtMultiplier - 100)
		updateLabel()
	end
end)

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

--// Aimbot Integrado \\--
local function getClosestTarget()
	local closestTarget = nil
	local shortestDistance = math.huge
	local camera = workspace.CurrentCamera

	for _, targetPlayer in ipairs(Players:GetPlayers()) do
		if targetPlayer ~= player and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
			local targetPos = targetPlayer.Character.HumanoidRootPart.Position
			local screenPoint, onScreen = camera:WorldToViewportPoint(targetPos)
			if onScreen then
				local distance = (Vector2.new(screenPoint.X, screenPoint.Y) - UserInputService:GetMouseLocation()).Magnitude
				if distance < shortestDistance then
					shortestDistance = distance
					closestTarget = targetPlayer
				end
			end
		end
	end
	return closestTarget
end

-- Substituir a direção do impulso se um alvo válido for encontrado
Workspace.ChildAdded:Connect(function(NewModel)
	if NewModel.Name == "GrabParts" then
		local PartToImpulse = NewModel:FindFirstChild("GrabPart") and NewModel.GrabPart:FindFirstChild("WeldConstraint") and NewModel.GrabPart.WeldConstraint.Part1
		if PartToImpulse then
			local VelocityObject = Instance.new("BodyVelocity", PartToImpulse)
			NewModel:GetPropertyChangedSignal("Parent"):Connect(function()
				if not NewModel.Parent then
					local target = getClosestTarget()
					if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
						local direction = (target.Character.HumanoidRootPart.Position - workspace.CurrentCamera.CFrame.Position).Unit
						VelocityObject.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
						VelocityObject.Velocity = direction * StrenghtMultiplier
						Debris:AddItem(VelocityObject, 1)
					else
						VelocityObject:Destroy()
					end
				end
			end)
		end
	end
end)
