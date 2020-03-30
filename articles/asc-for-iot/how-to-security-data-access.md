---
title: Acessando dados usando o Azure Security Center para IoT| Microsoft Docs
description: Saiba como acessar seus dados de alerta de segurança e recomendação ao usar o Azure Security Center para IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3ddd9b2c8373746a65cd78f0a81b60d097cd9f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68597187"
---
# <a name="access-your-security-data"></a>Acesse seus dados de segurança 

O Azure Security Center para IoT armazena alertas de segurança, recomendações e dados de segurança brutos (se você optar por salvá-los) em seu espaço de trabalho do Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Para configurar qual espaço de trabalho do Log Analytics é usado:

1. Abrir seu Hub IoT.
1. Clique na **lâmina Visão geral** na seção **Segurança**
2. Clique **em Configurações**e altere a configuração do espaço de trabalho do Log Analytics.

Para acessar seus alertas e recomendações no espaço de trabalho do Log Analytics após a configuração:

1. Escolha um alerta ou recomendação no Azure Security Center para IoT. 
2. Clique **em mais investigação,** clique **para ver quais dispositivos têm esse alerta clique aqui e visualize a coluna DeviceId**.

Para obter detalhes sobre a consulta de dados do Log Analytics, consulte [Iniciar com consultas no Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Alertas de segurança

Os alertas de segurança são armazenados na tabela _AzureSecurityOfThings.SecurityAlert_ no espaço de trabalho log analytics configurado para a solução Azure Security Center for IoT.

Nós fornecemos uma série de consultas úteis para ajudá-lo a começar a explorar alertas de segurança.

### <a name="sample-records"></a>Registros de amostras

Selecione alguns registros aleatórios

```
// Select a few random records
//
SecurityAlert
| project 
    TimeGenerated, 
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity, 
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | Descrição                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | assinaturas/<subscription_id>/resourceGroups/<resource_group>/provedores/Microsoft.Devices/IotHubs/<iot_hub> | <device_name <> | Alta          | Ataque de força bruta conseguiu           | Um ataque de força bruta no dispositivo foi bem sucedido        |    { "Endereço de Fonte\"Completa": "[ 10.165.12.18:\"\"\"]", "Nomes de Usuários": "[ ]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | assinaturas/<subscription_id>/resourceGroups/<resource_group>/provedores/Microsoft.Devices/IotHubs/<iot_hub> | <device_name <> | Alta          | Login local bem-sucedido no dispositivo      | Um login local bem-sucedido no dispositivo foi detectado     | { "Endereço Remoto": "Porta Remota": "", "Porta Local": "", "Login Shell": "/bin/su", "Login Process Id": "28207", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | assinaturas/<subscription_id>/resourceGroups/<resource_group>/provedores/Microsoft.Devices/IotHubs/<iot_hub> | <device_name <> | Alta          | Tentativa de login local falhada no dispositivo  | Uma tentativa de login local falha da falha no dispositivo foi detectada |  { "Endereço Remoto": "Porta Remota": "", "Porta Local": "", "Login Shell": "/bin/su", "Login Process Id": "22644", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>Resumo do dispositivo

Obtenha o número de alertas de segurança distintos detectados na última semana, agrupados pelo IoT Hub, dispositivo, gravidade de alerta, tipo de alerta.

```
// Get the number of distinct security alerts detected in the last week, grouped by 
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | Contagem |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| assinaturas/<subscription_id>/resourceGroups/<resource_group>/provedores/Microsoft.Devices/IotHubs/<iot_hub> | <device_name <> | Alta          | Ataque de força bruta conseguiu           | 9   |   
| assinaturas/<subscription_id>/resourceGroups/<resource_group>/provedores/Microsoft.Devices/IotHubs/<iot_hub> | <device_name <> | Médio        | Tentativa de login local falhada no dispositivo  | 242 |    
| assinaturas/<subscription_id>/resourceGroups/<resource_group>/provedores/Microsoft.Devices/IotHubs/<iot_hub> | <device_name <> | Alta          | Login local bem-sucedido no dispositivo      | 31  |
| assinaturas/<subscription_id>/resourceGroups/<resource_group>/provedores/Microsoft.Devices/IotHubs/<iot_hub> | <device_name <> | Médio        | Minerador de criptomoedas                     | 4   |

### <a name="iot-hub-summary"></a>Resumo do hub de IoT

Selecione um número de dispositivos distintos que tiveram alertas na última semana, pelo IoT Hub, severidade de alerta, tipo de alerta

```
// Select number of distinct devices which had alerts in the last week, by 
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId, 
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| assinaturas/<subscription_id>/resourceGroups/<resource_group>/provedores/Microsoft.Devices/IotHubs/<iot_hub> | Alta          | Ataque de força bruta conseguiu           | 1          |    
| assinaturas/<subscription_id>/resourceGroups/<resource_group>/provedores/Microsoft.Devices/IotHubs/<iot_hub> | Médio        | Tentativa de login local falhada no dispositivo  | 1          | 
| assinaturas/<subscription_id>/resourceGroups/<resource_group>/provedores/Microsoft.Devices/IotHubs/<iot_hub> | Alta          | Login local bem-sucedido no dispositivo      | 1          |
| assinaturas/<subscription_id>/resourceGroups/<resource_group>/provedores/Microsoft.Devices/IotHubs/<iot_hub> | Médio        | Minerador de criptomoedas                     | 1          |

## <a name="security-recommendations"></a>Recomendações de segurança

As recomendações de segurança são armazenadas na tabela _AzureSecurityOfThings.SecurityRecommendation_ no espaço de trabalho log analytics configurado para a solução Azure Security Center for IoT.

Nós fornecemos uma série de consultas úteis para ajudá-lo a começar a explorar recomendações de segurança.

### <a name="sample-records"></a>Registros de amostras

Selecione alguns registros aleatórios

```
// Select a few random records
//
SecurityRecommendation
| project 
    TimeGenerated, 
    IoTHubId=AssessedResourceId, 
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```
    
| TimeGenerated | IoTHubId | deviceId | Severidade de Recomendações | RecomendaçãoEstado | RecomendaçãoDisplayNome | Descrição | RecomendaçãoAdicionalDados |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | assinaturas/<subscription_id>/resourceGroups/<resource_group>/provedores/Microsoft.Devices/IotHubs/<iot_hub> | <device_name <> | Médio | Ativo | A regra de firewall permissivo na cadeia de entrada foi encontrada | Uma regra no firewall foi encontrada que contém um padrão permissivo para uma ampla gama de endereços IP ou Portas | {"Rules":"[{\"\"SourceAddress\"\"\":\",\"\"\"SourcePort\"\"\":\",\"\"DestinationAddress :, DestinationPort : 1337\"}]"} |
| 2019-03-22T10:50:27.237 | assinaturas/<subscription_id>/resourceGroups/<resource_group>/provedores/Microsoft.Devices/IotHubs/<iot_hub> | <device_name <> | Médio | Ativo | A regra de firewall permissivo na cadeia de entrada foi encontrada | Uma regra no firewall foi encontrada que contém um padrão permissivo para uma ampla gama de endereços IP ou Portas | {"Rules":"[{\"\"SourceAddress\"\"\":\",\"\"\"SourcePort\"\"\":\",\"\"DestinationAddress :, DestinationPort : 1337\"}]"} |

### <a name="device-summary"></a>Resumo do dispositivo

Obtenha o número de recomendações de segurança ativa distintas, agrupadas pelo IoT Hub, dispositivo, severidade de recomendação e tipo.

```
// Get the number of distinct active security recommendations, grouped by by 
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | deviceId      | Severidade de Recomendações | Contagem |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| assinaturas/<subscription_id>/resourceGroups/<resource_group>/provedores/Microsoft.Devices/IotHubs/<iot_hub> | <device_name <> | Alta          | 2   |    
| assinaturas/<subscription_id>/resourceGroups/<resource_group>/provedores/Microsoft.Devices/IotHubs/<iot_hub> | <device_name <> | Médio        | 1 |  
| assinaturas/<subscription_id>/resourceGroups/<resource_group>/provedores/Microsoft.Devices/IotHubs/<iot_hub> | <device_name <> | Alta          | 1  |
| assinaturas/<subscription_id>/resourceGroups/<resource_group>/provedores/Microsoft.Devices/IotHubs/<iot_hub> | <device_name <> | Médio        | 4   |


## <a name="next-steps"></a>Próximas etapas

- Leia o Centro de Segurança do Azure para [visão geral de IoT](overview.md)
- Conheça o Azure Security Center for IoT [Architecture](architecture.md)
- Entenda e explore [o Azure Security Center para alertas de IoT](concept-security-alerts.md)
- Entenda e explore [o Azure Security Center para recomendação de IoT](concept-recommendations.md)
