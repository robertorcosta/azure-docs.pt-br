---
title: Configurar o diagnóstico
titleSuffix: Azure Digital Twins
description: Consulte Como habilitar o registro em log com as configurações de diagnóstico.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 1a349883c8144aaff4aed70ba46ad0eaab04fe69
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388351"
---
# <a name="troubleshooting-azure-digital-twins-with-diagnostics-logging"></a>Solução de problemas do Azure digital gêmeos com log de diagnóstico

O Azure digital gêmeos coleta [métricas](how-to-view-metrics.md) para sua instância de serviço que fornecem informações sobre o estado de seus recursos. Você pode usar essas métricas para avaliar a integridade geral do serviço de gêmeos digital do Azure e os recursos conectados a ele. Essas estatísticas voltadas para o usuário ajudam a ver o que está acontecendo com o gêmeos digital do Azure e ajudam a executar a análise da causa raiz sobre problemas sem a necessidade de contatar o suporte do Azure.

Este artigo mostra como ativar o log de **diagnóstico** para seus dados de métricas de sua instância de gêmeos digital do Azure. Você pode usar esses logs para ajudá-lo a solucionar problemas de serviço.

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

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Captura de tela mostrando a página de configurações de diagnóstico e o botão para adicionar":::

As novas configurações terão efeito em aproximadamente 10 minutos. Depois disso, os logs aparecem no destino configurado de volta na página **configurações de diagnóstico** da instância do. 

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como configurar o diagnóstico, consulte [*coletar e consumir dados de log dos recursos do Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Para obter informações sobre as métricas do gêmeos digital do Azure, consulte [*como: Exibir métricas com Azure monitor*](how-to-view-metrics.md).
