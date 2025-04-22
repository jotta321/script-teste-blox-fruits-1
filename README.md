--[[
  Unified Script para Blox Fruits
  (Integração do script open source by tsuo com as atualizações da Update 26)
  Data: 22/04/2025

  Funcionalidades incluídas:
    • Configuração e uso das habilidades atualizadas para as frutas:
       - Gravity Fruit, Eagle Fruit e Creation Fruit.
    • Sistema de level up, com nível máximo definido.
    • Sistema de evolução de frutas baseado em coleta de materiais.
    • Função de auto-farming (funcionalidade típica presente no script TSUO)
    • Função de inicialização do jogador (exemplo de setup do script original)
--]]

--------------------------------------------------------------------------------
-- Configurações e constantes

local MAX_LEVEL = 2650

--------------------------------------------------------------------------------
-- Habilidades atualizadas para cada fruta

local GravityFruitAbilities = {
    Singularity = { key = "Z", damage = 150, cooldown = 5, description = "Cria um vórtice gravitacional devastador." },
    OrbitalChain = { key = "X", damage = 170, cooldown = 6, description = "Lança correntes gravitacionais que imobilizam inimigos." },
    GravitationalPrison = { key = "C", damage = 200, cooldown = 8, description = "Prende o adversário em uma prisão gravitacional." },
    AsteroidCrash = { key = "V", damage = 250, cooldown = 10, description = "Invoca asteroides que causam dano massivo." },
    ShootingStar = { key = "F", damage = 220, cooldown = 7, description = "Permite deslocar-se rapidamente enquanto atinge inimigos no trajeto." }
}

local EagleFruitAbilities = {
    FeatherBlast = { key = "E", damage = 130, cooldown = 4, description = "Dispara penas explosivas a distância." },
    SoaringDive = { key = "R", damage = 160, cooldown = 7, description = "Realiza um mergulho aéreo com ataque devastador." }
}

local CreationFruitAbilities = {
    BarrierBuild = { key = "Q", effect = "Cria um escudo temporário", cooldown = 10, description = "Constrói barreiras para proteção." },
    StructureCreate = { key = "W", effect = "Cria plataformas ou estruturas", cooldown = 11, description = "Constrói estruturas para estratégias de combate." }
}

--------------------------------------------------------------------------------
-- Funções principais

-- Função para uso de habilidades com base no tipo de fruta e tecla pressionada
function useAbility(fruitType, key)
    local ability = nil

    if fruitType == "Gravity" then
        for name, data in pairs(GravityFruitAbilities) do
            if data.key == key then
                ability = data
                break
            end
        end
    elseif fruitType == "Eagle" then
        for name, data in pairs(EagleFruitAbilities) do
            if data.key == key then
                ability = data
                break
            end
        end
    elseif fruitType == "Creation" then
        for name, data in pairs(CreationFruitAbilities) do
            if data.key == key then
                ability = data
                break
            end
        end
    end

    if ability then
        print("Usando habilidade (" .. fruitType .. "): " .. key .. " - " .. ability.description)
        -- Aqui deve ser inserida a lógica real para aplicação da habilidade:
        -- dano, efeitos, animações, gerenciador de cooldowns etc.
    else
        print("Nenhuma habilidade atribuída à tecla " .. key .. " para a fruta " .. fruitType)
    end
end

-- Função para simular o level up do jogador conforme acúmulo de experiência
function levelUp(player)
    while player.exp >= player.requiredExp do
        player.level = player.level + 1
        player.exp = player.exp - player.requiredExp
        print("Parabéns, " .. player.name .. "! Você atingiu o nível " .. player.level .. "!")
        
        -- Atualiza o exp necessário para o próximo nível (fórmula de exemplo)
        player.requiredExp = math.floor(player.requiredExp * 1.1)

        if player.level >= MAX_LEVEL then
            player.level = MAX_LEVEL
            print("Você alcançou o nível máximo: " .. MAX_LEVEL)
            break
        end
    end
end

-- Função para evoluir a fruta do jogador por meio do novo sistema de evolução
function evolveFruit(player, fruitType, materials)
    -- Verificação simples dos materiais necessários
    if materials.FireFeather and materials.Moonstone then
        print("Evoluindo a fruta " .. fruitType .. " para o jogador " .. player.name .. "...")
        local evolutionKey = fruitType .. "EvolutionLevel"
        player[evolutionKey] = (player[evolutionKey] or 0) + 1
        print("Nível de evolução da fruta " .. fruitType .. ": " .. player[evolutionKey])
        -- No jogo real, aqui seria aplicado aprimoramentos nos atributos e/ou novas habilidades.
    else
        print("Materiais insuficientes para evoluir a fruta " .. fruitType)
    end
end

--------------------------------------------------------------------------------
-- Funcionalidades extra (Recursos do script TSUO)

-- Inicialização do jogador (setup inicial típico do TSUO)
function initializePlayer(name)
    local player = {
        name = name,
        level = 2600,
        exp = 500,
        requiredExp = 200,
        GravityEvolutionLevel = 0,
        EagleEvolutionLevel = 0,
        CreationEvolutionLevel = 0
    }
    return player
end

-- Função de auto-farming para ganho automático de experiência (exemplo simples)
function autoFarm(player)
    print("Iniciando auto-farming para o jogador " .. player.name)
    -- Esta função simula o ganho de experiência ao 'farmar' inimigos/recursos
    for i = 1, 5 do
        print("[AutoFarm] Farmando... Iteração " .. i)
        player.exp = player.exp + 100
    end
    print("Auto-farming concluído para o jogador " .. player.name)
end

--------------------------------------------------------------------------------
-- Exemplo de utilização do script unificado

local player = initializePlayer("Player1")

print("===== Iniciando Unified Script para Blox Fruits =====")

-- Uso de habilidades
useAbility("Gravity", "Z")      -- Exemplo: Singularity da Gravity Fruit
useAbility("Eagle", "E")        -- Exemplo: FeatherBlast da Eagle Fruit
useAbility("Creation", "Q")     -- Exemplo: BarrierBuild da Creation Fruit

-- Evolução de fruta (exemplo utilizando os materiais necessários)
local materials = { FireFeather = true, Moonstone = true }
evolveFruit(player, "Gravity", materials)

-- Simulação de auto-farming (ganho de experiência)
autoFarm(player)

-- Processamento do level up após auto-farming e outras ações
levelUp(player)

print("===== Fim do Unified Script =====")
