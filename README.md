-- Script Local - Menu de Farm de Doces
local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Cria o menu
local gui = Instance.new("ScreenGui", playerGui)
gui.Name = "FarmMenu"

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 200, 0, 150)
frame.Position = UDim2.new(0.05, 0, 0.25, 0)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 2
frame.Active = true
frame.Draggable = true

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 40)
title.Text = "🍭 Farm de Doces"
title.TextColor3 = Color3.new(1, 1, 1)
title.BackgroundTransparency = 1
title.Font = Enum.Font.GothamBold
title.TextSize = 20

local button = Instance.new("TextButton", frame)
button.Size = UDim2.new(1, -20, 0, 40)
button.Position = UDim2.new(0, 10, 0, 60)
button.Text = "Ativar Farm"
button.Font = Enum.Font.GothamBold
button.TextSize = 18
button.BackgroundColor3 = Color3.fromRGB(60, 200, 100)

local farmAtivo = false
local flying = false
local speed = 80 -- velocidade de voo

button.MouseButton1Click:Connect(function()
	farmAtivo = not farmAtivo
	if farmAtivo then
		button.Text = "Desativar Farm"
		button.BackgroundColor3 = Color3.fromRGB(200, 60, 60)
	else
		button.Text = "Ativar Farm"
		button.BackgroundColor3 = Color3.fromRGB(60, 200, 100)
	end
end)

-- Função de voo até os doces
local function voarAteDoces()
	while farmAtivo do
		local root = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
		if not root then
			task.wait(1)
			continue
		end

		local docesFolder = workspace:FindFirstChild("Doces")
		if not docesFolder then
			warn("❌ Pasta 'Doces' não encontrada no workspace.")
			return
		end

		local alvoMaisProximo
		local menorDist = math.huge

		for _, doce in pairs(docesFolder:GetChildren()) do
			if doce:IsA("Part") and doce.CanTouch and doce.Transparency < 1 then
				local dist = (doce.Position - root.Position).Magnitude
				if dist < menorDist then
					menorDist = dist
					alvoMaisProximo = doce
				end
			end
		end

		if alvoMaisProximo then
			flying = true
			local dir = (alvoMaisProximo.Position - root.Position).Unit
			root.Velocity = dir * speed
		else
			flying = false
		end

		task.wait(0.1)
	end

	if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
		player.Character.HumanoidRootPart.Velocity = Vector3.new(0, 0, 0)
	end
end

-- Loop para checar farm
task.spawn(function()
	while true do
		if farmAtivo then
			voarAteDoces()
		end
		task.wait(0.2)
	end
end)
