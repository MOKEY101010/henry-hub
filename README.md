-- Tabela ofuscada inspirada no trecho fornecido
local __ = {
    ['\242'] = function() return -400 end, -- Força do pulo
    ['\173'] = function(str)
        local o, l = {}, 1
        for i in str:gmatch('%d+') do
            o[l], l = i + 0, l + 1
        end
        return table.concat(o, " ")
    end,
    ['\192'] = '800', -- Gravidade (como string, para decodificação)
    ['\111'] = function(p, v) p.velocityY = v end -- Função para aplicar velocidade
}

-- Configurações do jogador
local player = {
    x = 400,
    y = 500,
    width = 50,
    height = 50,
    velocityY = 0,
    isJumping = false,
    infiniteJump = false
}

-- Configurações do botão
local button = {
    x = 350,
    y = 50,
    width = 120,
    height = 50,
    text = "Pulo Infinito",
    color = {0, 0.5, 1}, -- Azul
    hoverColor = {0, 0.7, 1}, -- Azul mais claro para hover
    isHovered = false
}

-- Decodificar gravidade da tabela ofuscada
local gravity = tonumber(__['\173'](__['\192']))

function love.load()
    love.window.setMode(800, 600)
    love.window.setTitle("Jogo de Pulo Infinito")
    love.graphics.setBackgroundColor(0.2, 0.2, 0.2) -- Fundo cinza escuro
end

function love.update(dt)
    -- Aplicar gravidade
    if not player.infiniteJump or not player.isJumping then
        player.velocityY = player.velocityY + gravity * dt
    end
    player.y = player.y + player.velocityY * dt

    -- Limitar à tela
    if player.y > love.graphics.getHeight() - player.height then
        player.y = love.graphics.getHeight() - player.height
        player.velocityY = 0
        player.isJumping = false
    end

    -- Verificar hover do botão
    local mx, my = love.mouse.getPosition()
    button.isHovered = mx >= button.x and mx <= button.x + button.width and
                       my >= button.y and my <= button.y + button.height
end

function love.draw()
    -- Desenhar jogador
    love.graphics.setColor(1, 0, 0) -- Vermelho
    love.graphics.rectangle("fill", player.x, player.y, player.width, player.height)

    -- Desenhar botão
    love.graphics.setColor(button.isHovered and button.hoverColor or button.color)
    love.graphics.rectangle("fill", button.x, button.y, button.width, button.height)
    love.graphics.setColor(1, 1, 1) -- Texto branco
    love.graphics.printf(button.text, button.x, button.y + 15, button.width, "center")

    -- Desenhar status
    love.graphics.setColor(1, 1, 1)
    love.graphics.print("Pulo Infinito: " .. (player.infiniteJump and "ON" or "OFF"), 10, 10)
end

function love.mousepressed(x, y, buttonPressed)
    if buttonPressed == 1 and button.isHovered then
        player.infiniteJump = not player.infiniteJump
    end
end

function love.keypressed(key)
    if key == "space" then
        if not player.isJumping or player.infiniteJump then
            __['\111'](player, __['\242']()) -- Usar função ofuscada para aplicar força do pulo
            player.isJumping = true
        end
    end
end
