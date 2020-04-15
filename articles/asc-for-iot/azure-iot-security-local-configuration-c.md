---
title: Configuração local do agente de segurança (C)
description: Saiba mais sobre o Azure Security Center para configurações locais de agentes para C.
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311698"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Noções básicas sobre o arquivo LocalConfiguration.json – agente do C

O Azure Security Center para agente de segurança IoT usa configurações de um arquivo de configuração local.
O agente de segurança lê a configuração uma vez, na inicialinicialdo agente.
A configuração encontrada no arquivo de configuração local contém configuração de autenticação e outras configurações relacionadas ao agente.
O arquivo contém configurações em pares "Key-Value" na notação JSON e as configurações são preenchidas quando o agente é instalado.

Por padrão, o arquivo está localizado em: /var/ASCIoTAgent/LocalConfiguration.json

As alterações no arquivo de configuração ocorrem quando o agente é reiniciado.

## <a name="security-agent-configurations-for-c"></a>Configurações do agente de segurança para C

| Nome da configuração | Valores possíveis | Detalhes |
|:-----------|:---------------|:--------|
| AgentId | GUID | O identificador exclusivo do agente |
| Intervalado de eventos acionados | Seqüência ISO8601 | Intervalo de agendador para coleta de eventos acionados |
| ConnectionTimeout | Seqüência ISO8601 | Período de tempo antes que a conexão com o IoThub seja cronometrada |
| Autenticação | JsonObject | Configuração de autenticação. Este objeto contém todas as informações necessárias para autenticação contra o IoTHub |
| Identidade | "DPS", "SecurityModule", "Device" | Identidade de autenticação - DPS se a autenticação for feita através de DPS, SecurityModule se a autenticação for feita através de credenciais de módulo de segurança ou dispositivo se a autenticação for feita com credenciais do dispositivo |
| Authenticationmethod | "SasToken", "SelfSignedCertificate" | o segredo do usuário para autenticação - Escolha SasToken se o segredo de uso for uma chave Simétrica, escolha o certificado auto-assinado se o segredo for um certificado auto-assinado  |
| FilePath | Caminho para arquivo (string) | Caminho para o arquivo que contém o segredo de autenticação |
| HostName | string | O nome de hospedagem do hub iot azul. geralmente <meu hub>.azure-devices.net |
| deviceId | string | O ID do dispositivo (conforme registrado no Azure IoT Hub) |
| DPS | JsonObject | Configurações relacionadas ao DPS |
| IDScope | string | Escopo de ID do DPS |
| RegistrationId | string  | ID de registro de dispositivo DPS |
| Registro em log | JsonObject | Configurações relacionadas ao logger do agente |
| SystemLoggerMinimumGravidade | 0 <= número <= 4 | as mensagens de registro iguais e acima dessa gravidade serão registradas em /var/log/syslog (0 é a menor gravidade) |
| DiagnosticEventMinimumSeverity | 0 <= número <= 4 | mensagens de registro iguais e acima dessa gravidade serão enviadas como eventos diagnósticos (0 é a menor gravidade) |

## <a name="security-agent-configurations-code-example"></a>Exemplo de código de configurações de configurações de agentes de segurança

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

- Leia o Centro de Segurança do Azure para visão geral do serviço [de IoT](overview.md)
- Saiba mais sobre o Azure Security Center for IoT [Architecture](architecture.md)
- Habilite o Centro de Segurança do Azure para [serviço](quickstart-onboard-iot-hub.md) de IoT
- Leia o Azure Security Center for IoT service [FAQ](resources-frequently-asked-questions.md)
- Aprenda a acessar [dados brutos de segurança](how-to-security-data-access.md)
- Entenda as [recomendações](concept-recommendations.md)
- Entenda [os alertas de](concept-security-alerts.md) segurança
