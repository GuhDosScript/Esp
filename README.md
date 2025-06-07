-- Configurações
local config = {
    showTeamOnly = false,  -- Se verdadeiro, mostra apenas inimigos
    textColor = Color3.fromRGB(255, 0, 0), -- Cor padrão do texto
    teamColorCheck = true, -- Ativar Team Check por cor de equipe
}

-- Função principal do ESP
local function createESP(player)
    if player == game.Players.LocalPlayer then return end

    local esp = Drawing.new("Text")
    esp.Text = player.Name
    esp.Size = 14
    esp.Center = true
    esp.Outline = true
    esp.Font = 2
    esp.Visible = false
    esp.Color = config.textColor

    game:GetService("RunService").RenderStepped:Connect(function()
        if not player.Character or not player.Character:FindFirstChild("HumanoidRootPart") or not player.Character:FindFirstChild("Humanoid") then
            esp.Visible = false
            return
        end

        -- Team check (opcional)
        if config.teamColorCheck then
            if player.Team == game.Players.LocalPlayer.Team then
                esp.Visible = false
                return
            end
        end

        local root = player.Character:FindFirstChild("HumanoidRootPart")
        local pos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(root.Position)

        if onScreen then
            esp.Position = Vector2.new(pos.X, pos.Y)
            esp.Text = player.Name .. " [" .. math.floor(player:DistanceFromCharacter(root.Position)) .. "m]"
            esp.Visible = true
        else
            esp.Visible = false
        end
    end)
end

-- Aplicar ESP a todos os jogadores
for _, player in pairs(game.Players:GetPlayers()) do
    createESP(player)
end

-- Detectar novos jogadores entrando
game.Players.PlayerAdded:Connect(function(player)
    createESP(player)
end)

-- Interface Simples (Opcional)
local screenGui = Instance.new("ScreenGui", game.Players.LocalPlayer:WaitForChild("PlayerGui"))
local toggleButton = Instance.new("TextButton", screenGui)
toggleButton.Text = "Toggle Team Check"
toggleButton.Size = UDim2.new(0, 150, 0, 40)
toggleButton.Position = UDim2.new(0, 10, 0, 10)
toggleButton.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
toggleButton.TextColor3 = Color3.new(1, 1, 1)

toggleButton.MouseButton1Click:Connect(function()
    config.teamColorCheck = not config.teamColorCheck
    toggleButton.Text = "Team Check: " .. tostring(config.teamColorCheck)
end)
