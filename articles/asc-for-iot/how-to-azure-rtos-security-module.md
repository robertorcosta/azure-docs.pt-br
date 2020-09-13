---
title: Configurar e personalizar o módulo de segurança para os RTOS do Azure
description: Saiba mais sobre como configurar e personalizar seu módulo de segurança para os RTOS do Azure.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: eed0422f574e54ff6d7df486b4929850a26418fa
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514785"
---
# <a name="configure-and-customize-security-module-for-azure-rtos-preview"></a>Configurar e personalizar o módulo de segurança para RTOS do Azure (versão prévia)

Use este arquivo a seguir para configurar o comportamento do dispositivo.

## <a name="azure_iot_security_moduleincasc_porth"></a>azure_iot_security_module/Inc/asc_port. h

 O comportamento padrão de cada configuração é fornecido nas seguintes tabelas: 

### <a name="general"></a>Geral

| Nome | Type | Padrão | Detalhes |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | String | --- | Identificador exclusivo do dispositivo  |
| ASC_SECURITY_MODULE_PENDING_TIME  | Número | 300 | O módulo de segurança está com tempo pendente em segundos. Se o tempo exceder a alteração de estado para suspender. |

#### <a name="collection"></a>Coleção

| Nome | Type | Padrão | Detalhes |
| - | - | - | - |
| ASC_HIGH_PRIORITY_INTERVAL | Número | 10 | Intervalo do grupo de alta prioridade dos coletores em segundos. |
| ASC_MEDIUM_PRIORITY_INTERVAL | Número | 30 | Intervalo do grupo de prioridade média de coletores em segundos. |
| ASC_LOW_PRIORITY_INTERVAL | Número | 145.440  | Intervalo do grupo de prioridade baixa dos coletores em segundos. |

#### <a name="collector-network-activity"></a>Atividade de rede do coletor

Para personalizar a configuração de atividade de rede do coletor, use o seguinte:

| Nome | Type | Padrão | Detalhes |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Booliano | false | Filtrar `TCP` atividade de rede |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Booliano | false | Filtrar `UDP` eventos de atividade de rede |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Booliano | false | Filtrar `ICMP` eventos de atividade de rede |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Booliano | true | Capturar somente pacotes de entrada unicast, quando definido como falso captura também difusão e multicast |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Número | 64 | Número máximo de eventos de rede IPv4 a serem armazenados na memória |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Número | 64  | Número máximo de eventos de rede IPv6 a serem armazenados na memória |


## <a name="compile-flags"></a>Sinalizadores de compilação
Os sinalizadores de compilação permitem que você substitua as configurações predefinidas.

### <a name="collectors"></a>Coletores
| Nome | Type | Padrão | Detalhes |
| - | - | - | - |
| collector_heartbeat_enabled | Booliano | ATIVADO | Habilitar o coletor de pulsação |
| collector_network_activity_enabled | Booliano | ATIVADO | Habilitar o coletor de atividades de rede |
| collector_system_information_enabled | Booliano | ATIVADO | Habilitar o coletor de informações do sistema |

## <a name="supported-security-alerts-and-recommendations"></a>Recomendações e alertas de segurança com suporte

O módulo de segurança para os RTOS do Azure dá suporte a alertas e recomendações de segurança específicos. Certifique-se de [examinar e personalizar os valores relevantes de alerta e recomendação](concept-rtos-security-alerts-recommendations.md) para seu serviço.

## <a name="log-analytics-optional"></a>Log Analytics (opcional)

Embora opcional e não seja necessário, a habilitação e a configuração de Log Analytics podem ser úteis quando você deseja investigar ainda mais eventos e atividades do dispositivo. Leia sobre como configurar e usar [log Analytics com a central de segurança do Azure para o serviço de IOT](how-to-security-data-access.md#log-analytics) para saber mais. 

## <a name="next-steps"></a>Próximas etapas

- Examinar e personalizar o módulo de segurança para [alertas e recomendações de segurança](concept-rtos-security-alerts-recommendations.md) dos RTOs do Azure
- Consulte o [módulo de segurança para a API dos RTOs do Azure](azure-rtos-security-module-api.md) , conforme necessário.

