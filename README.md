-- ======================================
-- MINI HUB BOXE LEAGUE
-- Bota flutuante abre/fecha | 3 botoes
-- Funciona Delta / Android | Sem erros
-- ======================================

local pegarDado = rawget
local Players = game:GetService("Players")
local jogador = Players.LocalPlayer
local gui = jogador:WaitForChild("PlayerGui")
local _ENV = nil

-- Dados sem caracteres especiais
local DadosLutadores = {
    {Nome = "Canelo Alvarez", Nivel = 18, XP = 7850, ProxXP = 8500, Status = "Ativo"},
    {Nome = "Tyson Fury", Nivel = 15, XP = 5200, ProxXP = 6000, Status = "Ativo"},
    {Nome = "Oleksandr Usyk", Nivel = 13, XP = 4100, ProxXP = 5000, Status = "Ativo"}
}

local function buscar(nome)
    if type(nome) ~= "string" or nome == "" then return nil end
    local busca = nome:upper()
    for _, l in ipairs(DadosLutadores) do
        if pegarDado(l, "Nome"):upper() == busca then return l end
    end
    return nil
end

-- 1. EDITAR NIVEL
local function editarNivel(nome, novoNivel)
    local l = buscar(nome)
    if not l then print("[INFO] Lutador nao encontrado!") return end
    if novoNivel < 1 or novoNivel > 100 then print("[INFO] Nivel entre 1 e 100!") return end
    l.Nivel = novoNivel
    l.ProxXP = math.floor(1000 * (1.2 ^ (novoNivel - 1)))
    l.XP = 0
    print("[OK] "..nome.." agora e Nivel "..novoNivel)
end

-- 2. FARMAR XP
local farmLigado = false
local function farmXP()
    farmLigado = not farmLigado
    if farmLigado then
        print("[+] Farm XP INICIADO! Clica de novo para parar")
        task.spawn(function()
            while farmLigado do
                for _, l in ipairs(DadosLutadores) do
                    l.XP += math.random(300, 800)
                    if l.XP >= l.ProxXP then
                        l.XP -= l.ProxXP
                        l.Nivel += 1
                        l.ProxXP = math.floor(l.ProxXP * 1.2)
                    end
                end
                task.wait(2)
            end
            print("[-] Farm XP PARADO!")
        end)
    end
end

-- 3. EDITAR STATUS
local function editarStatus(nome, novoStatus)
    local l = buscar(nome)
    if not l then print("[INFO] Lutador nao encontrado!") return end
    local opcoes = {"Ativo", "Lesionado", "Suspenso", "Aposentado"}
    local valido = false
    for _, s in ipairs(opcoes) do if s == novoStatus then valido = true end end
    if not valido then print("[INFO] Use: "..table.concat(opcoes, ", ")) return end
    l.Status = novoStatus
    print("[OK] Status alterado para: "..novoStatus)
end

-- ======================================
-- TELA E BOTOES
-- ======================================
local Tela = Instance.new("ScreenGui")
Tela.Name = "MiniHubBoxe"
Tela.ResetOnSpawn = false
Tela.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
Tela.Parent = gui

-- BOTAO FLUTUANTE
local BtnMenu = Instance.new("TextButton")
BtnMenu.Size = UDim2.new(0, 50, 0, 50)
BtnMenu.Position = UDim2.new(0.02, 0, 0.85, 0)
BtnMenu.BackgroundColor3 = Color3.fromHex("#222222")
BtnMenu.Text = "M"
BtnMenu.TextColor3 = Color3.new(1,1,1)
BtnMenu.TextScaled = true
BtnMenu.Font = Enum.Font.GothamBold
Instance.new("UICorner", BtnMenu).CornerRadius = UDim.new(1,0)
BtnMenu.Active = true
BtnMenu.Draggable = true
BtnMenu.Parent = Tela

-- MENU PRINCIPAL
local Menu = Instance.new("Frame")
Menu.Size = UDim2.new(0, 220, 0, 260)
Menu.Position = UDim2.new(0.09, 0, 0.85, 0)
Menu.BackgroundColor3 = Color3.fromHex("#181818")
Menu.Visible = false
Instance.new("UICorner", Menu).CornerRadius = UDim.new(0, 12)
Menu.Parent = Tela

-- TITULO
local Titulo = Instance.new("TextLabel")
Titulo.Size = UDim2.new(1, 0, 0, 45)
Titulo.BackgroundColor3 = Color3.fromHex("#252525")
Titulo.Text = "MINI HUB BOXE"
Titulo.TextColor3 = Color3.new(1,1,1)
Titulo.TextScaled = true
Titulo.Font = Enum.Font.GothamBold
Instance.new("UICorner", Titulo).CornerRadius = UDim.new(0,12)
Titulo.Parent = Menu

-- ABRIR/FECHAR
local aberto = false
BtnMenu.MouseButton1Click:Connect(function()
    aberto = not aberto
    Menu.Visible = aberto
    BtnMenu.BackgroundColor3 = aberto and Color3.fromHex("#007855") or Color3.fromHex("#222222")
end)

-- CRIADOR DE BOTOES
local function botao(texto, pos, acao)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.9, 0, 0, 40)
    btn.Position = pos
    btn.BackgroundColor3 = Color3.fromHex("#2a2a2a")
    btn.Text = texto
    btn.TextColor3 = Color3.new(1,1,1)
    btn.TextScaled = true
    btn.Font = Enum.Font.Gotham
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0,8)
    btn.MouseButton1Click:Connect(acao)
    btn.Parent = Menu
end

-- ADICIONA OS BOTOES
botao("Editar Nivel", UDim2.new(0.05,0,0,55), function()
    editarNivel("Tyson Fury", 20)
end)

botao("Ligar/Desligar Farm XP", UDim2.new(0.05,0,0,105), function()
    farmXP()
end)

botao("Mudar Status", UDim2.new(0.05,0,0,155), function()
    editarStatus("Oleksandr Usyk", "Lesionado")
end)
