local player = game.Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local hum = char:WaitForChild("Humanoid")
local hrp = char:WaitForChild("HumanoidRootPart")

-- واجهة
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "PowerGUI"
gui.ResetOnSpawn = false

-- وظيفة صنع زر
local function createButton(name, posX, text, color, onClick)
	local btn = Instance.new("TextButton", gui)
	btn.Size = UDim2.new(0, 150, 0, 40)
	btn.Position = UDim2.new(posX, 0, 0.8, 0)
	btn.Text = text
	btn.BackgroundColor3 = color
	btn.TextScaled = true
	btn.MouseButton1Click:Connect(onClick)
end

-- زر السرعة
createButton("Speed", 0.1, "⚡ سرعة", Color3.fromRGB(0, 170, 255), function()
	hum.WalkSpeed = 100
end)

-- زر القفز العالي
createButton("Jump", 0.3, "🪂 قفزة عالية", Color3.fromRGB(255, 140, 0), function()
	hum.JumpPower = 150
end)

-- زر الطيران
local flying = false
local bodyGyro, bodyVel

createButton("Fly", 0.5, "🚀 طيران", Color3.fromRGB(140, 0, 255), function()
	flying = not flying
	if flying then
		bodyGyro = Instance.new("BodyGyro", hrp)
		bodyGyro.P = 9e4
		bodyGyro.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
		bodyGyro.CFrame = hrp.CFrame

		bodyVel = Instance.new("BodyVelocity", hrp)
		bodyVel.Velocity = Vector3.new(0, 0, 0)
		bodyVel.MaxForce = Vector3.new(9e9, 9e9, 9e9)

		game:GetService("RunService").RenderStepped:Connect(function()
			if flying then
				bodyGyro.CFrame = workspace.CurrentCamera.CFrame
				bodyVel.Velocity = workspace.CurrentCamera.CFrame.LookVector * 50
			end
		end)
	else
		if bodyGyro then bodyGyro:Destroy() end
		if bodyVel then bodyVel:Destroy() end
	end
end)

-- زر عبور الجدران (noclip مشروع)
local canNoclip = false
createButton("Noclip", 0.7, "🌀 عبور الجدران", Color3.fromRGB(50, 200, 50), function()
	canNoclip = not canNoclip
	for _, part in pairs(char:GetDescendants()) do
		if part:IsA("BasePart") then
			part.CanCollide = not canNoclip
		end
	end
end)
