local Players = game:GetService("Players")
local player = Players.LocalPlayer

local removed = {}

local targetNames = {
	Trees = true,
	Built = true,
	GrassBottom = true,
	GrassTop = true
}

local function deleteObj(obj)
	if (obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Beam") or obj:IsA("Explosion") or obj:IsA("Smoke")) and not removed[obj] then
		removed[obj] = true
		pcall(function() obj.Enabled = false end)
		pcall(function() obj:Destroy() end)
	end

	if obj:IsA("ParticleEmitter") and obj.Name == "Light" then
		pcall(function() obj.Enabled = false end)
		pcall(function() obj:Destroy() end)
	end

	if (obj:IsA("Folder") or obj:IsA("Model")) and targetNames[obj.Name] then
		pcall(function() obj:Destroy() end)
	end

	if obj:IsA("BasePart") then
		pcall(function()
			obj.Color = Color3.fromRGB(200,200,200)
			obj.Material = Enum.Material.Plastic
		end)
	end

	if obj:IsA("Decal") or obj:IsA("Texture") then
		pcall(function() obj:Destroy() end)
	end

	if obj:IsA("ColorCorrectionEffect") or obj:IsA("BloomEffect") or obj:IsA("SunRaysEffect") or obj:IsA("DepthOfFieldEffect") then
		pcall(function() obj:Destroy() end)
	end
end

local function scan(folder)
	for _, v in ipairs(folder:GetDescendants()) do
		deleteObj(v)
	end

	folder.DescendantAdded:Connect(function(v)
		task.wait()
		deleteObj(v)
	end)
end

scan(workspace)
scan(game:GetService("ReplicatedStorage"))
scan(game:GetService("Lighting"))
scan(game:GetService("StarterPlayer"))

if player.Character then
	scan(player.Character)
end

player.CharacterAdded:Connect(function(char)
	scan(char)
end)
