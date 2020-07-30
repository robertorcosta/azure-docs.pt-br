---
title: Configurar o diagnóstico
titleSuffix: Azure Digital Twins
description: Consulte Como habilitar o registro em log com as configurações de diagnóstico.
author: baanders
ms.author: baanders
ms.date: 7/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 60ee2102a2b725c68e29afec9a44de03e52e9467
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87379609"
---
# <a name="enable-logging-with-diagnostics-settings"></a>Habilitar registro em log com as configurações de diagnóstico

Você pode optar por enviar seus dados de métricas para [log Analytics](../azure-monitor/log-query/get-started-portal.md), um ponto de extremidade de [hubs de eventos](../event-hubs/event-hubs-about.md) ou o [armazenamento do Azure](../storage/blobs/storage-blobs-overview.md) habilitando o log com as configurações de diagnóstico para sua instância.

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Ativar as configurações de diagnóstico com o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até sua instância do gêmeos digital do Azure. Você pode encontrá-lo digitando seu nome na barra de pesquisa do Portal. 

2. Selecione **configurações de diagnóstico** no menu e, em seguida, **Adicionar configuração de diagnóstico**.

    :::image type="content" source="media/how-to-view-metrics/diagnostic-settings.png" alt-text="Captura de tela mostrando a página de configurações de diagnóstico e o botão para adicionar":::

3. Na página a seguir, preencha os seguintes valores:
 * **Nome da configuração de diagnóstico**: dê um nome às configurações de diagnóstico.
 * **Detalhes da categoria**: escolha as operações que você deseja monitorar e marque as caixas para habilitar o diagnóstico para essas operações. As operações que podem ser informadas em configurações de diagnóstico são:
    - DigitalTwinsOperation
    - EventRoutesOperation
    - ModelsOperation
    - QueryOperation
    - AllMetrics
 * **Detalhes de destino**: escolha onde deseja enviar os logs. Você pode selecionar qualquer combinação das três opções abaixo:
    - Enviar para o Log Analytics
    - Arquivar em uma conta de armazenamento
    - Transmitir para um hub de eventos

    Você pode ser solicitado a preencher detalhes adicionais se eles forem necessários para a seleção de destino.  
    
4. Salve as novas configurações. 

    :::image type="content" source="media/how-to-set-up-diagnostics/diagnostic-settings-details.png" alt-text="Captura de tela mostrando a página de configurações de diagnóstico e o botão para adicionar":::

As novas configurações terão efeito em aproximadamente 10 minutos. Depois disso, os logs aparecem no destino configurado de volta na página **configurações de diagnóstico** da instância do. 

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como configurar o diagnóstico, consulte [*coletar e consumir dados de log dos recursos do Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Para obter informações sobre as métricas do gêmeos digital do Azure, consulte [ *como: Exibir métricas com Azure monitor*](how-to-view-metrics.md)