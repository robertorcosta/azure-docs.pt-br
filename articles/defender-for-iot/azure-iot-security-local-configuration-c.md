---
title: Configuração local do agente de segurança (C)
description: Saiba mais sobre o defender para configurações locais do agente para C.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 7cc6886b5341d8bc8a82288ad8a2a699381a953c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90932957"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Noções básicas sobre o arquivo LocalConfiguration.json – agente do C

O agente de segurança do defender para IoT usa configurações de um arquivo de configuração local.
O agente de segurança lê a configuração uma vez, na inicialização do agente.
A configuração encontrada no arquivo de configuração local contém a configuração de autenticação e outras configurações relacionadas ao agente.
O arquivo contém configurações em pares de "chave-valor" na notação JSON e as configurações são preenchidas quando o agente é instalado.

Por padrão, o arquivo está localizado em:/var/ASCIoTAgent/LocalConfiguration.jsem

As alterações feitas no arquivo de configuração ocorrem quando o agente é reiniciado.

## <a name="security-agent-configurations-for-c"></a>Configurações do agente de segurança para C

| Nome da configuração | Valores possíveis | Detalhes |
|:-----------|:---------------|:--------|
| AgentId | GUID | O identificador exclusivo do agente |
| TriggerdEventsInterval | Cadeia de caracteres ISO8601 | Intervalo do Agendador para coleta de eventos disparados |
| ConnectionTimeout | Cadeia de caracteres ISO8601 | Período de tempo antes que a conexão com o IoThub receba tempo limite |
| Autenticação | JsonObject | Configuração de autenticação. Este objeto contém todas as informações necessárias para a autenticação no IoTHub |
| Identidade | "DPS", "SecurityModule", "dispositivo" | Identidade de autenticação – DPS se a autenticação for feita por meio do DPS, SecurityModule se a autenticação for feita por meio de credenciais do módulo de segurança ou dispositivo se a autenticação for feita com as credenciais do dispositivo |
| AuthenticationMethod | "SasToken", "SelfSignedCertificate" | o segredo do usuário para autenticação-escolha SasToken se o segredo de uso for uma chave simétrica, escolha certificado autoassinado se o segredo for um certificado autoassinado  |
| FilePath | Caminho para o arquivo (cadeia de caracteres) | Caminho para o arquivo que contém o segredo de autenticação |
| HostName | string | O nome do host do Hub IOT do Azure. geralmente <meu Hub>. azure-devices.net |
| DeviceId | string | A ID do dispositivo (como registrado no Hub IoT do Azure) |
| DPS | JsonObject | Configurações relacionadas a DPS |
| IDScope | string | Escopo da ID do DPS |
| RegistrationId | string  | ID de registro do dispositivo DPS |
| Registrando em log | JsonObject | Configurações relacionadas ao agente de log |
| SystemLoggerMinimumSeverity | 0 <= número <= 4 | mensagens de log iguais e superiores a essa gravidade serão registradas em/var/log/syslog (0 é a severidade mais baixa) |
| DiagnosticEventMinimumSeverity | 0 <= número <= 4 | as mensagens de log iguais e superiores a essa gravidade serão enviadas como eventos de diagnóstico (0 é a severidade mais baixa) |

## <a name="security-agent-configurations-code-example"></a>Exemplo de código de configurações do agente de segurança

```json
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

- Leia a [visão geral](overview.md) do serviço defender para IOT
- Saiba mais sobre a [arquitetura](architecture.md) do defender para IOT
- Habilitar o [serviço](quickstart-onboard-iot-hub.md) defender para IOT
- Leia as [perguntas frequentes](resources-frequently-asked-questions.md) do serviço defender para IOT
- Aprenda a acessar [dados brutos de segurança](how-to-security-data-access.md)
- Entenda as [recomendações](concept-recommendations.md)
- Entender os [alertas](concept-security-alerts.md) de segurança
