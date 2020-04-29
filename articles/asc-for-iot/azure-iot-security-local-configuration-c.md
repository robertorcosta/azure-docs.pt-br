---
title: Configuração local do agente de segurança (C)
description: Saiba mais sobre a central de segurança do Azure para configurações locais do agente para C.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: cd344b9bebb69af210c482f46af6b2dd7edf7816
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311698"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Noções básicas sobre o arquivo LocalConfiguration.json – agente do C

A central de segurança do Azure para o agente de segurança de IoT usa configurações de um arquivo de configuração local.
O agente de segurança lê a configuração uma vez, na inicialização do agente.
A configuração encontrada no arquivo de configuração local contém a configuração de autenticação e outras configurações relacionadas ao agente.
O arquivo contém configurações em pares de "chave-valor" na notação JSON e as configurações são preenchidas quando o agente é instalado.

Por padrão, o arquivo está localizado em:/var/ASCIoTAgent/LocalConfiguration.json

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
| HostName | cadeia de caracteres | O nome do host do Hub IOT do Azure. geralmente <meu Hub>. azure-devices.net |
| DeviceId | cadeia de caracteres | A ID do dispositivo (como registrado no Hub IoT do Azure) |
| DPS | JsonObject | Configurações relacionadas a DPS |
| IDScope | cadeia de caracteres | Escopo da ID do DPS |
| RegistrationId | cadeia de caracteres  | ID de registro do dispositivo DPS |
| Registrando em log | JsonObject | Configurações relacionadas ao agente de log |
| SystemLoggerMinimumSeverity | 0 <= número <= 4 | mensagens de log iguais e superiores a essa gravidade serão registradas em/var/log/syslog (0 é a severidade mais baixa) |
| DiagnosticEventMinimumSeverity | 0 <= número <= 4 | as mensagens de log iguais e superiores a essa gravidade serão enviadas como eventos de diagnóstico (0 é a severidade mais baixa) |

## <a name="security-agent-configurations-code-example"></a>Exemplo de código de configurações do agente de segurança

```JSON
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

- Leia a [visão geral](overview.md) da central de segurança do Azure para serviços de IOT
- Saiba mais sobre a [arquitetura](architecture.md) da central de segurança do Azure para IOT
- Habilitar a central de segurança do Azure para o [serviço](quickstart-onboard-iot-hub.md) de IOT
- Leia as [perguntas frequentes sobre](resources-frequently-asked-questions.md) a central de segurança do Azure para o serviço IOT
- Aprenda a acessar [dados brutos de segurança](how-to-security-data-access.md)
- Entenda as [recomendações](concept-recommendations.md)
- Entender os [alertas](concept-security-alerts.md) de segurança
