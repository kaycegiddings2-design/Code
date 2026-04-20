local player = game.Players.LocalPlayer
local camera = workspace.CurrentCamera
local userInput = game:GetService("UserInputService")
local runService = game:GetService("RunService")

local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = character:WaitForChild("HumanoidRootPart")

local shiftLockEnabled = false

-- Settings
local offset = Vector3.new(2, 0, 0) -- camera offset to the right

-- Toggle with Shift
userInput.InputBegan:Connect(function(input, processed)
	if processed then return end
	
	if input.KeyCode == Enum.KeyCode.LeftShift then
		shiftLockEnabled = not shiftLockEnabled
		
		if shiftLockEnabled then
			humanoid.AutoRotate = false
		else
			humanoid.AutoRotate = true
		end
	end
end)

-- Update camera + rotation
runService.RenderStepped:Connect(function()
	if shiftLockEnabled and character then
		local lookVector = camera.CFrame.LookVector
		
		-- Rotate character to camera direction
		rootPart.CFrame = CFrame.new(rootPart.Position, rootPart.Position + Vector3.new(lookVector.X, 0, lookVector.Z))
		
		-- Offset camera (over shoulder)
		camera.CFrame = camera.CFrame * CFrame.new(offset)
	end
end)

-- Update character on respawn
player.CharacterAdded:Connect(function(char)
	character = char
	humanoid = character:WaitForChild("Humanoid")
	rootPart = character:WaitForChild("HumanoidRootPart")
end)
