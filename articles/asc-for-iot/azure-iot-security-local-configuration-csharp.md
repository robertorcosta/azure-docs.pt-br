---
title: Configuração local do agente de segurança (C#)
description: Saiba mais sobre o Azure Security Center para serviço de segurança IoT, arquivo de configuração local do agente de segurança para C#.
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
ms.openlocfilehash: adf0d72763e0cb1892d64c68a6dce05abbf6f582
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311670"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Entendendo o arquivo de configuração local (agente C#)

O Azure Security Center para agente de segurança IoT usa configurações de um arquivo de configuração local.

O agente de segurança lê o arquivo de configuração uma vez quando o agente é iniciado. As configurações encontradas no arquivo de configuração local contêm tanto a configuração de autenticação quanto outras configurações relacionadas ao agente.

O agente de segurança C# usa vários arquivos de configuração:

- **General.config** - Configurações relacionadas ao agente.
- **Autenticação.config** - Configuração relacionada à autenticação (incluindo detalhes de autenticação).
- **SecurityIotInterface.config** - Configurações relacionadas à IoT.

Os arquivos de configuração contêm a configuração padrão. A configuração de autenticação é preenchida durante a instalação do agente e as alterações no arquivo de configuração são feitas quando o agente é reiniciado.

## <a name="configuration-file-location"></a>Localização do arquivo de configuração

Para Linux:

- Os arquivos de configuração `/var/ASCIoTAgent`do sistema operacional estão localizados em .

Para Windows:

- Os arquivos de configuração do sistema operacional estão localizados dentro do diretório do agente de segurança.

### <a name="generalconfig-configurations"></a>Configurações geral.config

| Nome da configuração | Valores possíveis | Detalhes |
|:-----------|:---------------|:--------|
| Agentid | GUID | Identificador exclusivo do agente |
| lerConfiguraçãoremotaTimeout | TimeSpan | Período de tempo para buscar configuração remota do IoT Hub. Se o agente não conseguir buscar a configuração dentro do tempo especificado, a operação ficará sem tempo.|
| schedulerInterval | TimeSpan | Intervalo interno do agendador. |
| produtorInterval | TimeSpan | Intervalo de trabalhador do produtor de eventos. |
| consumidorInterval | TimeSpan | Intervalo de consumo de eventos. |
| highPriorityQueueSizePercentage | 0 < número < 1 | A parte do cache total dedicada a mensagens de alta prioridade. |
| logLevel | "Desligado", "Fatal", "Erro", "Aviso", "Informação", "Depuração"  | Mensagens de log iguais e acima dessa gravidade são registradas para depurar console (Syslog no Linux). |
| fileLogLevel |  "Desligado", "Fatal", "Erro", "Aviso", "Informação", "Depuração"| As mensagens de log iguais e acima dessa gravidade são registradas no arquivo (Syslog no Linux). |
| diagnosticVerbosityLevel | "Nenhum", "Alguns", "Todos", | Nível de verbosidade de eventos diagnósticos. Nenhum - eventos diagnósticos não são enviados, Alguns - Apenas eventos diagnósticos de alta importância são enviados, todos - todos os registros também são enviados como eventos diagnósticos. |
| Logfilepath | Caminho para arquivo | Se fileLogLevel > Off, os logs serão gravados neste arquivo. |
| padrãoEventPriority | "Alto", "Baixo", "Desligado" | Prioridade de evento padrão. |

### <a name="generalconfig-example"></a>Exemplo geral.config

```XML
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Autenticação.config

| Nome da configuração | Valores possíveis | Detalhes |
|:-----------|:---------------|:--------|
| moduleName | string | Nome da identidade do módulo de segurança. Este nome deve corresponder ao nome de identidade do módulo no dispositivo. |
| deviceId | string | ID do dispositivo (conforme registrado no Azure IoT Hub). || schedulerInterval | Seqüência timespan | Intervalo interno do agendador. |
| nome de gatewayHost | string | Nome de hospedagem do Azure Iot Hub. Normalmente <meu hub>.azure-devices.net |
| filePath | string - caminho para arquivo | Caminho para o arquivo que contém o segredo de autenticação.|
| type | "SimmetricKey", "SelfSignedCertificate" | O segredo do usuário para autenticação. Escolha *simetricKey* se o segredo do usuário for uma chave Simétrica, escolha *o certificado auto-assinado* se o segredo for um certificado auto-assinado. |
| identidade | "DPS", "Módulo", "Dispositivo" | Identidade de autenticação - DPS se a autenticação for feita através de DPS, Módulo se a autenticação for feita usando credenciais de módulo ou dispositivo se a autenticação for feita usando credenciais do dispositivo.
| certificateLocationKind |  "LocalFile", "Loja" | LocalFile se o certificado estiver armazenado em um arquivo, armazene se o certificado estiver localizado em uma loja de certificados. |
| idScope | string | Escopo de ID do DPS |
| registroId | string  | DP de registro de dispositivo. |
|

### <a name="authenticationconfig-example"></a>Exemplo de autenticação.config

```XML
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| Nome da configuração | Valores possíveis | Detalhes |
|:-----------|:---------------|:--------|
| Transporttype | "Ampq" "Mqtt" | Tipo de transporte IoT Hub. |
|

### <a name="securityiotinterfaceconfig-example"></a>Exemplo de SegurançaIotInterface.config

```XML
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Próximas etapas

- Leia o Centro de Segurança do Azure para visão geral do serviço [de IoT](overview.md)
- Saiba mais sobre o Azure Security Center for IoT [Architecture](architecture.md)
- Habilite o Centro de Segurança do Azure para [serviço](quickstart-onboard-iot-hub.md) de IoT
- Leia o Azure Security Center for IoT service [FAQ](resources-frequently-asked-questions.md)
- Aprenda a acessar [dados brutos de segurança](how-to-security-data-access.md)
- Entenda as [recomendações](concept-recommendations.md)
- Entenda [os alertas de](concept-security-alerts.md) segurança
