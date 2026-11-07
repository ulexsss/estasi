local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Crear el ScreenGui principal
local gui = Instance.new("ScreenGui")
gui.Name = "TradeVisualSimulator"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.Parent = playerGui

-- Panel principal (El cartel de Objeto Desconocido)
local panel = Instance.new("Frame")
panel.Size = UDim2.fromOffset(270, 190)
panel.BackgroundColor3 = Color3.fromRGB(20, 29, 38)
panel.BorderSizePixel = 0
panel.Visible = false
panel.Parent = gui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 9)
corner.Parent = panel

-- Encabezado
local header = Instance.new("TextLabel")
header.Size = UDim2.new(1, 0, 0, 35)
header.BackgroundColor3 = Color3.fromRGB(55, 75, 88)
header.Text = "Objeto Desconocido"
header.TextColor3 = Color3.new(1, 1, 1)
header.TextSize = 17
header.Font = Enum.Font.GothamBold
header.Parent = panel

-- Información
local info = Instance.new("TextLabel")
info.Position = UDim2.fromOffset(5, 43)
info.Size = UDim2.new(1, -10, 0, 70)
info.BackgroundTransparency = 1
info.Text = "Sin Verificar\n\nValor: ???"
info.TextColor3 = Color3.fromRGB(255, 220, 40)
info.TextSize = 17
info.Font = Enum.Font.GothamBold
info.Parent = panel

-- Mensaje
local description = Instance.new("TextLabel")
description.Position = UDim2.fromOffset(10, 112)
description.Size = Instance.new("UISizeConstraint") and UDim2.new(1, -20, 0, 42) -- Asegurando tamaño
description.BackgroundTransparency = 1
description.TextWrapped = true
description.Text = "La vista previa del objeto no está disponible debido a modificaciones visuales activas."
description.TextColor3 = Color3.fromRGB(220, 220, 220)
description.TextSize = 10
description.Parent = panel

-- Aviso
local warning = Instance.new("TextLabel")
warning.Position = UDim2.fromOffset(5, 143)
warning.Size = UDim2.new(1, -10, 0, 38)
warning.BackgroundColor3 = Color3.fromRGB(150, 45, 45)
warning.Text = "SIMULACIÓN: Si experimentas errores visuales en la oferta, visita la Plaza de intercambio para continuar con seguridad."
warning.TextColor3 = Color3.new(1, 1, 1)
warning.TextSize = 9
warning.TextWrapped = true
warning.Font = Enum.Font.GothamBold
warning.Parent = panel

local warningCorner = Instance.new("UICorner")
warningCorner.CornerRadius = UDim.new(0, 5)
warningCorner.Parent = warning

local menuActivo = false

-- Función para hacer invisibles los brainrots y sus tarjetas originales
local function actualizarInvisibilidadBrainrots(activo)
	for _, descendant in ipairs(playerGui:GetDescendants()) do
		if activo then
			-- Ocultar iconos de imágenes de los brainrots
			if descendant:IsA("ImageLabel") and descendant ~= header and descendant.Parent ~= panel then
				if descendant.AbsoluteSize.X > 25 and descendant.AbsoluteSize.Y > 25 then
					descendant.ImageTransparency = 1
				end
			-- Ocultar carteles de nombres y estadísticas originales de los ítems
			elseif descendant:IsA("TextLabel") and (descendant.Text:find("M/s") or descendant.Text:find("K/s") or descendant.Text:find("español") or descendant.Text:find("combinación")) then
				local framePadre = descendant.Parent
				if framePadre and framePadre:IsA("Frame") and framePadre ~= panel then
					framePadre.Visible = false
				end
			end
		else
			-- Restaurar al desactivar
			if descendant:IsA("ImageLabel") then
				descendant.ImageTransparency = 0
			elseif descendant:IsA("Frame") then
				descendant.Visible = true
			end
		end
	end
end

-- Tecla H para alternar el menú y la invisibilidad
UserInputService.InputBegan:Connect(function(input, processed)
	if processed then return end

	if input.KeyCode == Enum.KeyCode.H then
		menuActivo = not menuActivo
		panel.Visible = menuActivo
		actualizarInvisibilidadBrainrots(menuActivo)
	end
end)

-- Mantener el panel posicionado limpiamente arriba del cursor y los brainrots invisibles
RunService.RenderStepped:Connect(function()
	if not menuActivo then return end

	local mousePosition = UserInputService:GetMouseLocation()

	-- Ajustado para que aparezca arriba/al lado de forma ordenada
	local x = math.clamp(mousePosition.X + 15, 0, workspace.CurrentCamera.ViewportSize.X - 270)
	local y = math.clamp(mousePosition.Y - 120, 0, workspace.CurrentCamera.ViewportSize.Y - 190)

	panel.Position = UDim2.fromOffset(x, y)
	
	-- Forzar que se mantenga invisible en tiempo real
	actualizarInvisibilidadBrainrots(true)
end)
