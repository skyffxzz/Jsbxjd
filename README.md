-- carregar biblioteca 
local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()

-- aviso ao executar
Fluent:Notify({ Title = "executado!", Content = "executado com sucesso" })


local Window = Fluent:CreateWindow({
    Title = "SCRIPT V1" .. Fluent.Version,
    TabWidth = 160, 
    Size = UDim2.fromOffset(580, 460), 
    Theme = "white"
})

local Tabs = {
    Main = Window:AddTab({ Title = "MENU" }),
    Misc = Window:AddTab({ Title = "configurações", Icon = "configurações" })
}
ESP = Window:AddTab({ Title = "esps", Icon = "MISC ESP" })
}
-- parágrafos 
Tabs.Main:AddParagraph({ Title = "teste", Content = "TESTE" })

-- botões 
Tabs.Main:AddButton({ Title = "pulos infinitos", Callback = function() 
loadstring(game:HttpGet("https://raw.githubusercontent.com/HeyGyt/infjump/main/main"))()
end })

-- alterador 
local Toggle = Tabs.Main:AddToggle("autofarm", 
{
    Title = "autofarm", 
    Description = "ele vai farmar automático para voce",
    Default = false, -- esse "," e preciso coloque em qualquer situação 
    Callback = function(state)
	if state then
	    Fluent:Notify({ Title = "autofarm ligado", Content = "auto farm ligado" })
	else
	    Fluent:Notify({ Title = "auto farm desligado desligado", Content = "auto farm desligado" })
        end
    end 
})

local localPlayer = game.Players.LocalPlayer

local Toggle = Tabs.ESP:AddToggle("esp", 
{
    Title = "esp", 
    Description = "Mostra linha, marcador verde e nome (exceto em você)",
    Default = false,
    Callback = function(state)
        if state then
            Fluent:Notify({ Title = "ESP Ativado", Content = "Linhas, marcadores e nomes ativados (exceto você)" })

            local function isNotLocalPlayer(player)
                return player ~= localPlayer
            end

            local function addLineToHead(player)
                if not isNotLocalPlayer(player) then return end

                local character = player.Character
                if character and character:FindFirstChild("Head") then
                    local head = character.Head
                    if head:FindFirstChild("RedLineGui") then return end

                    local billboardGui = Instance.new("BillboardGui")
                    billboardGui.Name = "RedLineGui"
                    billboardGui.Adornee = head
                    billboardGui.Size = UDim2.new(0, 100, 0, 185) -- largura maior para o nome
                    billboardGui.AlwaysOnTop = true
                    billboardGui.ExtentsOffset = Vector3.new(0, 2, 0)

                    -- Linha vermelha (3px largura, 150px altura)
                    local redLine = Instance.new("Frame", billboardGui)
                    redLine.BackgroundColor3 = Color3.new(1, 0, 0)
                    redLine.Size = UDim2.new(0, 3, 0, 150)
                    redLine.AnchorPoint = Vector2.new(0.5, 0) -- centralizado horizontalmente, topo verticalmente
                    redLine.Position = UDim2.new(0.5, 0, 0, 0)
                    redLine.BorderSizePixel = 0

                    -- Retângulo verde (25px x 35px)
                    local greenBox = Instance.new("Frame", billboardGui)
                    greenBox.BackgroundColor3 = Color3.new(0, 1, 0)
                    greenBox.Size = UDim2.new(0, 25, 0, 35)
                    greenBox.AnchorPoint = Vector2.new(0.5, 1) -- centralizado horizontalmente, base verticalmente
                    greenBox.Position = UDim2.new(0.5, 0, 1, 0)
                    greenBox.BorderSizePixel = 0

                    -- Nome do jogador (texto azul)
                    local nameLabel = Instance.new("TextLabel", billboardGui)
                    nameLabel.Text = player.Name
                    nameLabel.TextColor3 = Color3.new(0, 0.6, 1)
                    nameLabel.BackgroundTransparency = 1
                    nameLabel.Size = UDim2.new(0, 100, 0, 20)
                    nameLabel.AnchorPoint = Vector2.new(0, 1) -- esquerda, base vertical
                    nameLabel.Position = UDim2.new(0.5, 15, 1, 0) -- ao lado direito do retângulo verde
                    nameLabel.TextXAlignment = Enum.TextXAlignment.Left
                    nameLabel.Font = Enum.Font.SourceSansBold
                    nameLabel.TextSize = 14

                    billboardGui.Parent = head
                end
            end

            for _, player in pairs(game.Players:GetPlayers()) do
                addLineToHead(player)
            end

            local playerAddedConnection
            playerAddedConnection = game.Players.PlayerAdded:Connect(function(player)
                player.CharacterAdded:Connect(function()
                    if Toggle:Get() then
                        task.wait(1)
                        addLineToHead(player)
                    end
                end)
            end)
            Toggle.PlayerAddedConnection = playerAddedConnection

        else
            Fluent:Notify({ Title = "ESP Desativado", Content = "Removido de todos os jogadores" })

            for _, player in pairs(game.Players:GetPlayers()) do
                if player.Character and player.Character:FindFirstChild("Head") then
                    local gui = player.Character.Head:FindFirstChild("RedLineGui")
                    if gui then
                        gui:Destroy()
                    end
                end
            end

            if Toggle.PlayerAddedConnection then
                Toggle.PlayerAddedConnection:Disconnect()
                Toggle.PlayerAddedConnection = nil
            end
        end
    end
})

local localPlayer = game.Players.LocalPlayer
local camera = workspace.CurrentCamera
local runService = game:GetService("RunService")

local Toggle = Tabs.ESP:AddToggle("esp_circle", {
    Title = "Círculo com Cruz",
    Description = "Círculo vermelho no centro da tela com cruz em jogadores dentro do raio",
    Default = false,
    Callback = function(state)
        if state then
            Fluent:Notify({ Title = "Ativado", Content = "Círculo e rastreamento ativados." })

            local gui = Instance.new("ScreenGui")
            gui.Name = "ESP_CircleGui"
            gui.ResetOnSpawn = false
            gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
            gui.Parent = game:GetService("CoreGui")

            -- Círculo vermelho central
            local circle = Instance.new("Frame")
            circle.Name = "Circle"
            circle.Size = UDim2.new(0, 200, 0, 200)
            circle.Position = UDim2.new(0.5, -100, 0.5, -100)
            circle.BackgroundTransparency = 1
            circle.BorderSizePixel = 0

            local uiCorner = Instance.new("UICorner")
            uiCorner.CornerRadius = UDim.new(1, 0)
            uiCorner.Parent = circle

            local stroke = Instance.new("UIStroke")
            stroke.Color = Color3.new(1, 0, 0)
            stroke.Thickness = 2
            stroke.Parent = circle

            circle.Parent = gui

            -- Cruz branca
            local cross = Instance.new("Frame")
            cross.Name = "Cross"
            cross.Size = UDim2.new(0, 30, 0, 30)
            cross.AnchorPoint = Vector2.new(0.5, 0.5)
            cross.Position = UDim2.new(0.5, 0, 0.5, 0)
            cross.BackgroundTransparency = 1
            cross.Visible = false
            cross.Parent = gui

            -- Linha vertical
            local vert = Instance.new("Frame")
            vert.Size = UDim2.new(0, 4, 1, 0)
            vert.Position = UDim2.new(0.5, -2, 0, 0)
            vert.BackgroundColor3 = Color3.new(1, 1, 1)
            vert.BorderSizePixel = 0
            vert.Parent = cross

            -- Linha horizontal
            local horz = Instance.new("Frame")
            horz.Size = UDim2.new(1, 0, 0, 4)
            horz.Position = UDim2.new(0, 0, 0.5, -2)
            horz.BackgroundColor3 = Color3.new(1, 1, 1)
            horz.BorderSizePixel = 0
            horz.Parent = cross

            -- Atualizador contínuo
            Toggle.RenderStep = runService.RenderStepped:Connect(function()
                local closestPlayer, closestDistance = nil, math.huge
                for _, player in pairs(game.Players:GetPlayers()) do
                    if player ~= localPlayer and player.Character and player.Character:FindFirstChild("Head") then
                        local head = player.Character.Head
                        local screenPos, onScreen = camera:WorldToViewportPoint(head.Position)
                        if onScreen then
                            local dx = screenPos.X - camera.ViewportSize.X / 2
                            local dy = screenPos.Y - camera.ViewportSize.Y / 2
                            local distance = math.sqrt(dx * dx + dy * dy)
                            if distance < 100 and distance < closestDistance then
                                closestPlayer = player
                                closestDistance = distance
                            end
                        end
                    end
                end

                if closestPlayer and closestPlayer.Character and closestPlayer.Character:FindFirstChild("Head") then
                    local screenPos = camera:WorldToViewportPoint(closestPlayer.Character.Head.Position)
                    cross.Position = UDim2.new(0, screenPos.X, 0, screenPos.Y)
                    cross.Visible = true
                else
                    cross.Visible = false
                end
            end)

        else
            Fluent:Notify({ Title = "Desativado", Content = "Círculo e cruz removidos." })

            -- Desliga a renderização e remove os elementos
            if Toggle.RenderStep then
                Toggle.RenderStep:Disconnect()
                Toggle.RenderStep = nil
            end

            local gui = game:GetService("CoreGui"):FindFirstChild("ESP_CircleGui")
            if gui then
                gui:Destroy()
            end
        end
    end
})



local localPlayer = game.Players.LocalPlayer

local Toggle = Tabs.misc:AddToggle("noclip", {
    Title = "Atravessar Paredes",
    Description = "Permite que você (e somente você) atravesse paredes",
    Default = false,
    Callback = function(state)
        if state then
            Fluent:Notify({ Title = "Noclip Ativado", Content = "Você agora pode atravessar paredes." })

            Toggle.NoclipConnection = game:GetService("RunService").Stepped:Connect(function()
                local character = localPlayer.Character
                if character then
                    for _, part in ipairs(character:GetDescendants()) do
                        if part:IsA("BasePart") then
                            part.CanCollide = false
                        end
                    end
                end
            end)
        else
            Fluent:Notify({ Title = "Noclip Desativado", Content = "Você não pode mais atravessar paredes." })

            if Toggle.NoclipConnection then
                Toggle.NoclipConnection:Disconnect()
                Toggle.NoclipConnection = nil
            end

            -- Reativar colisão para evitar bug
            local character = localPlayer.Character
            if character then
                for _, part in ipairs(character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = true
                    end
                end
            end
        end
    end
})




local localPlayer = game.Players.LocalPlayer
local camera = workspace.CurrentCamera

local Toggle = Tabs.ESP:AddToggle("esp_circle",
{
    Title = "FOV",
    Description = "Mostra um círculo no centro da tela e segue jogadores dentro do raio",
    Default = false,
    Callback = function(state)
        if state then
            Fluent:Notify({ Title = "ESP Ativado", Content = "Círculo ativado, detectando jogadores dentro dele." })

            -- Criar o círculo na tela
            local screenGui = Instance.new("ScreenGui")
            screenGui.Name = "CircleESP"
            screenGui.ResetOnSpawn = false
            screenGui.Parent = game.CoreGui

            local circle = Instance.new("Frame")
            circle.Name = "DetectionCircle"
            circle.Size = UDim2.new(0, 300, 0, 300)
            circle.Position = UDim2.new(0.5, -150, 0.5, -150)
            circle.BackgroundTransparency = 1
            circle.BorderSizePixel = 2
            circle.BorderColor3 = Color3.new(1, 1, 1)
            circle.Parent = screenGui

            local cross = Instance.new("Frame")
            cross.Name = "TrackingCross"
            cross.Size = UDim2.new(0, 10, 0, 10)
            cross.BackgroundTransparency = 1
            cross.Parent = screenGui

            -- Cruz branca (duas linhas)
            local line1 = Instance.new("Frame", cross)
            line1.Size = UDim2.new(0, 10, 0, 2)
            line1.Position = UDim2.new(0, 0, 0.5, -1)
            line1.BackgroundColor3 = Color3.new(1, 1, 1)
            line1.BorderSizePixel = 0

            local line2 = Instance.new("Frame", cross)
            line2.Size = UDim2.new(0, 2, 0, 10)
            line2.Position = UDim2.new(0.5, -1, 0, 0)
            line2.BackgroundColor3 = Color3.new(1, 1, 1)
            line2.BorderSizePixel = 0

            cross.Visible = false

            -- Função de detecção e atualização
            local RunService = game:GetService("RunService")
            Toggle.RenderConnection = RunService.RenderStepped:Connect(function()
                local nearestTarget = nil
                local shortestDistance = math.huge

                for _, player in pairs(game.Players:GetPlayers()) do
                    if player ~= localPlayer and player.Character and player.Character:FindFirstChild("Head") then
                        local headPos, onScreen = camera:WorldToViewportPoint(player.Character.Head.Position)
                        if onScreen then
                            local dx = headPos.X - (camera.ViewportSize.X / 2)
                            local dy = headPos.Y - (camera.ViewportSize.Y / 2)
                            local dist = math.sqrt(dx * dx + dy * dy)

                            if dist <= 150 and dist < shortestDistance then -- 150 é o raio do círculo
                                shortestDistance = dist
                                nearestTarget = headPos
                            end
                        end
                    end
                end

                if nearestTarget then
                    cross.Visible = true
                    cross.Position = UDim2.new(0, nearestTarget.X - 5, 0, nearestTarget.Y - 5)
                else
                    cross.Visible = false
                end
            end)

        else
            Fluent:Notify({ Title = "ESP Desativado", Content = "Círculo e cruz removidos" })

            local gui = game.CoreGui:FindFirstChild("CircleESP")
            if gui then
                gui:Destroy()
            end

            if Toggle.RenderConnection then
                Toggle.RenderConnection:Disconnect()
                Toggle.RenderConnection = nil
            end
        end
    end
})



                -- LocalScript em ToggleStaminaButton
local button = script.Parent
local player = game.Players.LocalPlayer
local enabled = false

button.Text = "Ativar Stamina Infinita"

button.MouseButton1Click:Connect(function()
	enabled = not enabled
	button.Text = enabled and "Desativar Stamina Infinita" or "Ativar Stamina Infinita"

	while enabled do
		wait(0.1)
		
		local character = player.Character or player.CharacterAdded:Wait()
		local stamina = character:FindFirstChild("Stamina") or player:FindFirstChild("Stamina")
		
		if stamina and stamina:IsA("NumberValue") then
			stamina.Value = 100 -- ou o valor máximo que seu jogo usa
		end
	end
end)

                



--sliders

local Slider = Tabs.Main:AddSlider("pulo", 
{
    Title = "Ajusta Pulo",
    Description = "Irá mudar o pulo do jogador com precisão decimal",
    Default = 2.0,     -- valor inicial com decimal
    Min = 0.0,         -- mínimo
    Max = 25.0,        -- máximo
    Rounding = 1,      -- 1 casa decimal. Use 2 para mais precisão
    Callback = function(Value)
        print("Pulo mudou para:", Value)
        -- Aqui você pode ajustar diretamente o JumpPower por exemplo:
        local player = game.Players.LocalPlayer
        local char = player.Character or player.CharacterAdded:Wait()
        local humanoid = char:FindFirstChildWhichIsA("Humanoid")
        if humanoid then
            humanoid.JumpPower = Value
        end
    end
})


-- LocalScript em StarterPlayerScripts

local player = game.Players.LocalPlayer

local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "SpeedUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

local function createInput(labelText, position, defaultText, callback)
	local frame = Instance.new("Frame")
	frame.Size = UDim2.new(0, 200, 0, 60)
	frame.Position = position
	frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
	frame.BorderSizePixel = 0
	frame.Parent = screenGui

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, 0, 0.4, 0)
	label.Position = UDim2.new(0, 0, 0, 0)
	label.BackgroundTransparency = 1
	label.Text = labelText
	label.TextColor3 = Color3.new(1, 1, 1)
	label.TextScaled = true
	label.Parent = frame

	local inputBox = Instance.new("TextBox")
	inputBox.Size = UDim2.new(1, -10, 0.6, -5)
	inputBox.Position = UDim2.new(0, 5, 0.4, 0)
	inputBox.Text = defaultText
	inputBox.TextColor3 = Color3.new(1, 1, 1)
	inputBox.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
	inputBox.BorderSizePixel = 0
	inputBox.TextScaled = true
	inputBox.ClearTextOnFocus = true
	inputBox.Parent = frame

	inputBox.FocusLost:Connect(function(enterPressed)
		if enterPressed then
			local number = tonumber(inputBox.Text)
			if number then
				callback(number)
			end
		end
	end)
end

createInput("Velocidade do Jogador", UDim2.new(0, 20, 0, 100), tostring(humanoid.WalkSpeed), function(value)
	local clamped = math.clamp(value, 5, 100)
	humanoid.WalkSpeed = clamped
end)

-- Atualiza humanoid se personagem morrer e renascer
player.CharacterAdded:Connect(function(char)
	character = char
	humanoid = character:WaitForChild("Humanoid")
end)


-- LocalScript em StarterPlayerScripts

local player = game.Players.LocalPlayer
local camera = workspace.CurrentCamera

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "FOVUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

local function createInput(labelText, position, defaultText, callback)
	local frame = Instance.new("Frame")
	frame.Size = UDim2.new(0, 200, 0, 60)
	frame.Position = position
	frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
	frame.BorderSizePixel = 0
	frame.Parent = screenGui

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, 0, 0.4, 0)
	label.Position = UDim2.new(0, 0, 0, 0)
	label.BackgroundTransparency = 1
	label.Text = labelText
	label.TextColor3 = Color3.new(1, 1, 1)
	label.TextScaled = true
	label.Parent = frame

	local inputBox = Instance.new("TextBox")
	inputBox.Size = UDim2.new(1, -10, 0.6, -5)
	inputBox.Position = UDim2.new(0, 5, 0.4, 0)
	inputBox.Text = defaultText
	inputBox.TextColor3 = Color3.new(1, 1, 1)
	inputBox.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
	inputBox.BorderSizePixel = 0
	inputBox.TextScaled = true
	inputBox.ClearTextOnFocus = true
	inputBox.Parent = frame

	inputBox.FocusLost:Connect(function(enterPressed)
		if enterPressed then
			local number = tonumber(inputBox.Text)
			if number then
				callback(number)
			end
		end
	end)
end

createInput("Campo de Visão (FOV)", UDim2.new(0, 20, 0, 100), tostring(camera.FieldOfView), function(value)
	local clamped = math.clamp(value, 40, 120)
	camer
