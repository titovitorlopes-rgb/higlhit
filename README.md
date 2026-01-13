local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")

local player = Players.LocalPlayer
local ativado = false
local highlights = {} -- Tabela para guardar os efeitos criados

-- Função para limpar os highlights existentes
local function limparHighlights()
	for _, hl in pairs(highlights) do
		hl:Destroy()
	end
	highlights = {}
end

-- Função para aplicar o highlight nos inimigos
local function aplicarHighlights()
	limparHighlights() -- Garante que não vamos duplicar efeitos
	
	local meuTime = player.Team
	
	for _, outroPlayer in pairs(Players:GetPlayers()) do
		-- Verifica se o jogador não é você e se está em um time diferente
		if outroPlayer ~= player and outroPlayer.Team ~= meuTime then
			if outroPlayer.Character then
				local hl = Instance.new("Highlight")
				hl.Name = "InimigoHighlight"
				hl.FillColor = Color3.fromRGB(255, 0, 0) -- Vermelho
				hl.OutlineColor = Color3.fromRGB(255, 255, 255) -- Contorno Branco
				hl.FillTransparency = 0.5
				hl.Adornee = outroPlayer.Character
				hl.Parent = outroPlayer.Character
				
				table.insert(highlights, hl)
			end
		end
	end
end

-- Detectar o clique da tecla P
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end -- Ignora se estiver digitando no chat
	
	if input.KeyCode == Enum.KeyCode.P then
		ativado = not ativado -- Inverte o estado (liga/desliga)
		
		if ativado then
			print("Highlight de inimigos: ATIVADO")
			aplicarHighlights()
		else
			print("Highlight de inimigos: DESATIVADO")
			limparHighlights()
		end
	end
end)

-- Atualizar caso novos jogadores entrem ou mudem de time enquanto estiver ativado
Players.PlayerAdded:Connect(function()
	if ativado then
		wait(1) -- Espera o personagem carregar
		aplicarHighlights()
	end
end)
