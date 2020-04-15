---
title: Configurar agentes de segurança
description: Saiba como configurar o Azure Security Center para agentes de segurança IoT para uso no Azure Security Center para serviço de segurança IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: e41b7222ca89976674973e1eb700d62765b306f0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311321"
---
# <a name="tutorial-configure-security-agents"></a>Tutorial: Configure agentes de segurança

Este artigo explica o Azure Security Center para agentes de segurança de IoT e detalha como alterá-los e configurá-los.

> [!div class="checklist"]
> * Configurar agentes de segurança
> * Alterar o comportamento do agente editando propriedades gêmeas
> * Descubra a configuração padrão

## <a name="agents"></a>Agentes

O Azure Security Center para agentes de segurança de IoT coleta dados de dispositivos IoT e executa ações de segurança para mitigar as vulnerabilidades detectadas. A configuração do agente de segurança é controlável usando um conjunto de propriedades gêmeas do módulo que você pode personalizar. Em geral, as atualizações secundárias dessas propriedades são pouco frequentes.

O Azure Security Center para o objeto de configuração dupla do agente de segurança da IoT é um objeto de formato JSON. O objeto de configuração é um conjunto de propriedades controláveis que você pode definir para controlar o comportamento do agente.

Essas configurações ajudam você a personalizar o agente para cada cenário necessário. Por exemplo, excluir automaticamente alguns eventos ou manter o consumo de energia a um nível mínimo são possíveis ao configurar essas propriedades.

Use o esquema de [configuração](https://aka.ms/iot-security-github-module-schema) do agente de segurança Azure para ioT para fazer alterações.

## <a name="configuration-objects"></a>Objetos de configuração

As propriedades relacionadas a cada centro de segurança do Azure para agente de segurança IoT estão localizadas no objeto de configuração do agente, dentro da seção de propriedades desejada, do módulo **de segurança azureiotsecurity.**

Para modificar a configuração, crie e modifique este objeto dentro da identidade dupla do módulo **de segurança azureiotsecurity.**

Se o objeto de configuração do agente não existir no módulo **de segurança azureiotsecurity** twin, todos os valores de propriedade do agente de segurança serão definidos como padrão.

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  }
}
```

## <a name="configuration-schema-and-validation"></a>Esquema de configuração e validação

Certifique-se de validar a configuração do seu agente contra este [esquema](https://aka.ms/iot-security-github-module-schema). Um agente não será lançado se o objeto de configuração não corresponder ao esquema.

Se, enquanto o agente estiver em execução, o objeto de configuração for alterado para uma configuração não válida (a configuração não corresponde ao esquema), o agente ignorará a configuração inválida e continuará usando a configuração atual.

### <a name="configuration-validation"></a>Validação de configuração

O Azure Security Center para agente de segurança IoT informa sua configuração atual dentro da seção de propriedades relatadas do módulo **de segurança azureiotsecurity** identidade dupla.
O agente relata todas as propriedades disponíveis, se uma propriedade não foi definida pelo usuário, o agente relata a configuração padrão.

Para validar sua configuração, compare os valores definidos na seção desejada com os valores relatados na seção relatada.

Se houver uma incompatibilidade entre as propriedades desejadas e as relatadas, então o agente não foi capaz de analisar a configuração.

Valide suas propriedades desejadas contra o [esquema,](https://aka.ms/iot-security-github-module-schema)corrija os erros e defina suas propriedades desejadas novamente!

> [!NOTE]
> Um alerta de erro de configuração será acionado do agente caso o agente não tenha sido capaz de analisar a configuração desejada.
> Compare a seção relatada e desejada para entender se o alerta ainda se aplica

## <a name="editing-a-property"></a>Edição de uma propriedade

Todas as propriedades personalizadas devem ser definidas dentro do objeto de configuração do agente dentro do módulo **de segurança azureiotsecurity** twin.
Para usar um valor de propriedade padrão, remova a propriedade do objeto de configuração.

### <a name="setting-a-property"></a>Definindo uma propriedade

1. No seu IoT Hub, localize e selecione o dispositivo que deseja alterar.

1. Clique no seu dispositivo e, em seguida, no módulo **de segurança azureiot.**

1. Clique em **Module Identity Twin**.

1. Edite as propriedades que deseja alterar no módulo de segurança.

   Por exemplo, para configurar eventos de conexão como de alta prioridade e coletar eventos de alta prioridade a cada 7 minutos, use a seguinte configuração.

    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. Clique em **Save** (Salvar).

### <a name="using-a-default-value"></a>Usando um valor padrão

Para usar um valor de propriedade padrão, remova a propriedade do objeto de configuração.

## <a name="default-properties"></a>Propriedades padrão

A tabela a seguir contém as propriedades controláveis do Azure Security Center para agentes de segurança IoT.

Os valores padrão estão disponíveis no esquema apropriado no [GitHub](https\://aka.ms/iot-security-module-default).

| Nome| Status | Valores válidos| Valores padrão| Descrição |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Necessário: falso |Valores válidos: Duração no formato ISO 8601 |Valor padrão: PT7M |Intervalo de tempo máximo antes que mensagens de alta prioridade sejam enviadas.|
|lowPriorityMessageFrequency |Necessário: falso|Valores válidos: Duração no formato ISO 8601 |Valor padrão: PT5H |Tempo máximo antes que mensagens de baixa prioridade sejam enviadas.|
|snapshotFrequency |Exigir: falso|Valores válidos: Duração no formato ISO 8601 |Valor padrão PT13H |Intervalo de tempo para a criação de instantâneos de status do dispositivo.|
|maxLocalCacheSizeInBytes |Necessário: falso |Valores válidos:  |Valor padrão: 2560000, maior que 8192 | O armazenamento máximo (em bytes) permitiu o cache de mensagem de um agente. Quantidade máxima de espaço permitida para armazenar mensagens no dispositivo, antes que as mensagens sejam enviadas.|
|maxMessageSizeInBytes |Necessário: falso |Valores válidos: Um número positivo, maior que 8192, menor que 262144 |Valor padrão: 204800 |Tamanho máximo permitido de um agente para a mensagem na nuvem. Esta configuração controla a quantidade de dados máximos enviados em cada mensagem. |
|eventPriority${EventName} |Necessário: falso |Valores válidos: Alto, Baixo, Desligado |Valores padrão: |Prioridade de cada evento gerado por agentes |

### <a name="supported-security-events"></a>Eventos de segurança suportados

|Nome do evento| PropertyName | Valor Padrão| Evento instantâneo| Status dos detalhes  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Evento de diagnóstico|eventPriorityDiagnostic| Desativado| Falso| Eventos diagnósticos relacionados com agentes. Use este evento para registrar verbose.|
|Erro de configuração |eventPriorityConfigurationConfigurationError |Baixo |Falso |O agente não analisou a configuração. Verifique a configuração contra o esquema.|
|Estatísticas de eventos descartados |eventPriorityDroppedEventsStatistics |Baixo |True|Estatísticas de eventos relacionados com agentes. |
|Hardware conectado|eventoPrioridadeConectadoHardware |Baixo |True |Instantâneo de todo o hardware conectado ao dispositivo.|
|Portas de escuta|eventPriorListeningPortas de escuta |Alta |True |Instantâneo de todas as portas de escuta abertas no dispositivo.|
|Criação de processos |eventPriorityProcessCreate |Baixo |Falso |Auditorias de criação de processos no dispositivo.|
|Processo encerrado|eventPriorityProcessTerminate |Baixo |Falso |Auditorias processam o término do dispositivo.|
|Informações do sistema |eventPrioritySystemInformation |Baixo |True |Um instantâneo de informações do sistema (por exemplo: Sistema operacional ou CPU).|
|Usuários locais| eventPriorityLocalUsers |Alta |True|Um instantâneo dos usuários locais registrados dentro do sistema. |
|Logon|  eventPriorityLogin |Alta|Falso|Audite os eventos de login no dispositivo (logins locais e remotos).|
|Criação de conexão |eventPriorityConnectionCreate|Baixo|Falso|Audita conexões TCP criadas de e para o dispositivo. |
|Configuração do firewall| eventPriorityFirewallConfiguração|Baixo|True|Instantâneo da configuração de firewall do dispositivo (regras de firewall). |
|Linha de base do SISTEMA OPERACIONAL| eventPriorityOSBaseline| Baixo|True|Instantâneo da verificação da linha de base do sistema operacional do dispositivo.|
|

## <a name="next-steps"></a>Próximas etapas

- [Entenda o Azure Security Center para recomendações de IoT](concept-recommendations.md)
- [Explore o Azure Security Center para alertas de IoT](concept-security-alerts.md)
- [Acessar dados de segurança brutos](how-to-security-data-access.md)
