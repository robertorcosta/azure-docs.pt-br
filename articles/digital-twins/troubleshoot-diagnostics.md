---
title: Configurar o diagnóstico
titleSuffix: Azure Digital Twins
description: Consulte Como habilitar o registro em log com as configurações de diagnóstico.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 5091edbf9138cb8ff03df193dcbeed692aaf13e3
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612394"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Solução de problemas do Azure digital gêmeos: log de diagnóstico

O Azure digital gêmeos coleta [métricas](troubleshoot-metrics.md) para sua instância de serviço que fornecem informações sobre o estado de seus recursos. Você pode usar essas métricas para avaliar a integridade geral do serviço de gêmeos digital do Azure e os recursos conectados a ele. Essas estatísticas voltadas para o usuário ajudam a ver o que está acontecendo com o gêmeos digital do Azure e ajudam a executar a análise da causa raiz sobre problemas sem a necessidade de contatar o suporte do Azure.

Este artigo mostra como ativar o log de **diagnóstico** para seus dados de métricas de sua instância de gêmeos digital do Azure. Você pode usar esses logs para ajudá-lo a solucionar problemas de serviço e definir configurações de diagnóstico para enviar métricas de gêmeos digitais do Azure para destinos diferentes. Você pode ler mais sobre essas configurações em [*criar configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes*](../azure-monitor/platform/diagnostic-settings.md).

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Ativar as configurações de diagnóstico com o portal do Azure

Aqui está como habilitar as configurações de diagnóstico para sua instância do gêmeos digital do Azure:

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até sua instância do gêmeos digital do Azure. Você pode encontrá-lo digitando seu nome na barra de pesquisa do Portal. 

2. Selecione **configurações de diagnóstico** no menu e, em seguida, **Adicionar configuração de diagnóstico**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Captura de tela mostrando a página de configurações de diagnóstico e o botão para adicionar":::

3. Na página a seguir, preencha os seguintes valores:
     * **Nome da configuração de diagnóstico**: dê um nome às configurações de diagnóstico.
     * **Detalhes da categoria**: escolha as operações que você deseja monitorar e marque as caixas para habilitar o diagnóstico para essas operações. As operações que podem ser informadas em configurações de diagnóstico são:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Para obter mais detalhes sobre essas opções, consulte a seção [*detalhes da categoria*](#category-details) abaixo.
     * **Detalhes de destino**: escolha onde deseja enviar os logs. Você pode selecionar qualquer combinação das três opções abaixo:
        - Enviar para o Log Analytics
        - Arquivar em uma conta de armazenamento
        - Transmitir para um hub de eventos

        Você pode ser solicitado a preencher detalhes adicionais se eles forem necessários para a seleção de destino.  
    
4. Salve as novas configurações. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Captura de tela mostrando a página de configurações de diagnóstico e o botão para adicionar":::

As novas configurações terão efeito em aproximadamente 10 minutos. Depois disso, os logs aparecem no destino configurado de volta na página **configurações de diagnóstico** da instância do. 

## <a name="category-details"></a>Detalhes da categoria

Aqui estão mais detalhes sobre as categorias de log que podem ser selecionadas em **detalhes da categoria** ao definir as configurações de diagnóstico.

| Categoria do log | Descrição |
| --- | --- |
| ADTModelsOperation | Registrar em log todas as chamadas de API pertencentes a modelos |
| ADTQueryOperation | Registrar em log todas as chamadas de API pertencentes a consultas |
| ADTEventRoutesOperation | Registrar em log todas as chamadas de API pertencentes a rotas de eventos, bem como saída de eventos do Azure digital gêmeos para um serviço de ponto de extremidade como grade de eventos, hubs de eventos e barramento de serviço |
| ADTDigitalTwinsOperation | Registrar em log todas as chamadas de API pertencentes ao Azure digital gêmeos |

Cada categoria de log consiste em operações de gravação, leitura, exclusão e ação.  Eles são mapeados para chamadas à API REST da seguinte maneira:

| Tipo de evento | Operações da API REST |
| --- | --- |
| Gravar | PUT e PATCH |
| Ler | GET |
| Excluir | Delete (excluir) |
| Ação | POST |

Aqui está uma lista abrangente das operações e das [chamadas da API REST do Azure digital gêmeos](https://docs.microsoft.com/rest/api/azure-digitaltwins/) que são registradas em log em cada categoria. 

>[!NOTE]
> Cada categoria de log contém várias operações/chamadas à API REST. Na tabela a seguir, cada categoria de log é mapeada para todas as operações/chamadas à API REST abaixo dela até que a próxima categoria de log seja listada. 

| Categoria do log | Operação | Chamadas à API REST e outros eventos |
| --- | --- | --- |
| ADTModelsOperation | Microsoft. DigitalTwins/Models/Write | API de atualização de modelos de entrelaçamento digital |
|  | Microsoft. DigitalTwins/Models/Read | Modelos de entrelaçamento digital obter por ID e APIs de lista |
|  | Microsoft. DigitalTwins/Models/Delete | API de exclusão de modelos de entrelaçamento digital |
|  | Microsoft. DigitalTwins/modelos/ação | Modelos de entrelaçamento digital adicionar API |
| ADTQueryOperation | Microsoft. DigitalTwins/consulta/ação | API do gêmeos de consulta |
| ADTEventRoutesOperation | Microsoft. DigitalTwins/eventroutes/Write | Adicionar API às rotas de eventos |
|  | Microsoft. DigitalTwins/eventroutes/Read | Rotas de eventos Get por ID e listar APIs |
|  | Microsoft. DigitalTwins/eventroutes/Delete | API de exclusão de rotas de eventos |
|  | Microsoft. DigitalTwins/eventroutes/Action | Falha ao tentar publicar eventos em um serviço de ponto de extremidade (não uma chamada à API) |
| ADTDigitalTwinsOperation | Microsoft. DigitalTwins/DigitalTwins/Write | Gêmeos digital-adicionar, adicionar relação, atualizar, atualizar componente |
|  | Microsoft. DigitalTwins/DigitalTwins/Read | Digital gêmeos Get por ID, obter componente, obter relação por ID, listar relações de entrada, relações de lista |
|  | Microsoft. DigitalTwins/DigitalTwins/Delete | Gêmeos digital excluir, excluir relação |
|  | Microsoft. DigitalTwins/DigitalTwins/Action | Telemetria do componente de envio digital gêmeos, enviar telemetria |

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como configurar o diagnóstico, consulte [*coletar e consumir dados de log dos recursos do Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Para obter informações sobre as métricas do gêmeos digital do Azure, consulte [*solução de problemas: Exibir métricas com Azure monitor*](troubleshoot-metrics.md).
* Para ver como habilitar alertas para suas métricas, consulte [*solução de problemas: configurar alertas*](troubleshoot-alerts.md).
