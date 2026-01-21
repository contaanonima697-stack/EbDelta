-- ===============================
-- SAFE LOAD
-- ===============================
repeat task.wait() until game:IsLoaded()

local success, Rayfield = pcall(function()
	return loadstring(game:HttpGet("https://sirius.menu/rayfield"))()
end)

if not success then
	warn("Rayfield não carregou")
	return
end

-- ===============================
-- WINDOW
-- ===============================
local Window = Rayfield:CreateWindow({
	Name = "EB do Delta | Full Menu",
	LoadingTitle = "EB Delta",
	LoadingSubtitle = "All-in-One",
	ConfigurationSaving = { Enabled = false }
})

-- ===============================
-- SERVICES
-- ===============================
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Backpack = LocalPlayer:WaitForChild("Backpack")

-- ===============================
-- FLAGS
-- ===============================
local InfiniteAmmo = false
local NoReload = false
local RapidFire = false
local LongRange = false

-- ===============================
-- WEAPON LIST (AJUSTE SE PRECISAR)
-- ===============================
local EB_Weapons = {
	"FAL",
	"M4A1",
	"AK-47",
	"GLOCK",
	"PISTOL",
	"SNIPER",
	"MP5",
	"SHOTGUN"
}

local SelectedWeapon = EB_Weapons[1]

-- ===============================
-- TABS
-- ===============================
local CombatTab = Window:CreateTab("Combat", 4483362458)
local FarmTab = Window:CreateTab("Weapons / Farm", 4483362458)

-- ===============================
-- COMBAT (GENÉRICO)
-- ===============================
local function enhanceTool(tool)
	for _,v in ipairs(tool:GetDescendants()) do
		if v:IsA("NumberValue") or v:IsA("IntValue") then
			local n = string.lower(v.Name)

			if InfiniteAmmo and (n:find("ammo") or n:find("clip")) then
				v.Value = math.huge
			end

			if NoReload and n:find("reload") then
				v.Value = 0
			end

			if RapidFire and (n:find("firerate") or n:find("delay")) then
				v.Value = 0
			end

			if LongRange and (n:find("range") or n:find("distance")) then
				v.Value = 999999
			end
		end
	end
end

local function setupChar(char)
	char.ChildAdded:Connect(function(c)
		if c:IsA("Tool") then
			task.wait(0.1)
			enhanceTool(c)
		end
	end)
end

if LocalPlayer.Character then
	setupChar(LocalPlayer.Character)
end
LocalPlayer.CharacterAdded:Connect(setupChar)

-- ===============================
-- COMBAT TOGGLES
-- ===============================
CombatTab:CreateToggle({
	Name = "Infinite Ammo",
	Callback = function(v) InfiniteAmmo = v end
})

CombatTab:CreateToggle({
	Name = "No Reload",
	Callback = function(v) NoReload = v end
})

CombatTab:CreateToggle({
	Name = "Rapid Fire",
	Callback = function(v) RapidFire = v end
})

CombatTab:CreateToggle({
	Name = "Range Máximo",
	Callback = function(v) LongRange = v end
})

-- ===============================
-- FARM / WEAPONS
-- ===============================

FarmTab:CreateButton({
	Name = "Puxar TODAS as Armas",
	Callback = function()
		for _,v in ipairs(game:GetDescendants()) do
			if v:IsA("Tool") then
				pcall(function()
					v:Clone().Parent = Backpack
				end)
			end
		end
	end
})

FarmTab:CreateButton({
	Name = "Puxar Armas EB Delta",
	Callback = function()
		for _,v in ipairs(game:GetDescendants()) do
			if v:IsA("Tool") then
				for _,name in ipairs(EB_Weapons) do
					if string.upper(v.Name) == name then
						pcall(function()
							v:Clone().Parent = Backpack
						end)
					end
				end
			end
		end
	end
})

FarmTab:CreateDropdown({
	Name = "Selecionar Arma",
	Options = EB_Weapons,
	CurrentOption = SelectedWeapon,
	Callback = function(v)
		SelectedWeapon = v
	end
})

FarmTab:CreateButton({
	Name = "Puxar Arma Selecionada",
	Callback = function()
		for _,v in ipairs(game:GetDescendants()) do
			if v:IsA("Tool") and string.upper(v.Name) == SelectedWeapon then
				pcall(function()
					v:Clone().Parent = Backpack
				end)
			end
		end
	end
})

FarmTab:CreateButton({
	Name = "Auto Equipar Última Arma",
	Callback = function()
		local t = Backpack:GetChildren()
		if #t > 0 then
			LocalPlayer.Character.Humanoid:EquipTool(t[#t])
		end
	end
})

FarmTab:CreateButton({
	Name = "Limpar Mochila",
	Callback = function()
		for _,v in ipairs(Backpack:GetChildren()) do
			if v:IsA("Tool") then
				v:Destroy()
			end
		end
	end
})
