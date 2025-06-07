-- carregar biblioteca Fluent
local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()

-- aviso de execução
Fluent:Notify({ Title = "executado!", Content = "executando com sucesso" })


local Window = Fluent:CreateWindow({
    Title = "TESTE SCRIPT " .. Fluent.Version,
    TabWidth = 160,
    Size = UDim2.fromOffset(580, 460),
    Theme = "Dark"
})

-- abas organizadas corretamente
local Tabs = {
    Main = Window:AddTab({ Title = "menu", Icon = "package" }),
    Player = Window:AddTab({ Title = "Player", Icon = "as configurações do jogador ficará aqui" })
}
-- parágrafo inicial
Tabs.Main:AddParagraph({ Title = "script", Content = "scripts aqui" })
Tabs.Player:AddParagraph({ Title = "player settings", Content = "as configurações do seu jogador sera ajustada aqui!" })

-- input para altura do pulo
Tabs.Player:AddInput({
    Title = "Altura do Pulo",
    Placeholder = "Digite valor do pulo",
    Numeric = true,
    Changed = function(value)
        local num = tonumber(value)
        if num then
            local player = game.Players.LocalPlayer
            local character = player.Character or player.CharacterAdded:Wait()
            local humanoid = character:WaitForChild("Humanoid")
            humanoid.JumpPower = num * 10
        end
    end
})

-- input para velocidade
Tabs.Player:AddInput({
    Title = "Velocidade do Jogador",
    Placeholder = "Digite a velocidade",
    Numeric = true,
    Changed = function(value)
        local num = tonumber(value)
        if num then
            local player = game.Players.LocalPlayer
            local character = player.Character or player.CharacterAdded:Wait()
            local humanoid = character:WaitForChild("Humanoid")
            humanoid.WalkSpeed = num * 10
        end
    end
})
