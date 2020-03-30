---
title: Gerenciar instâncias de alerta no Azure Monitor
description: Gerenciar instâncias de alerta no Azure
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1910b3b474012365e8117f584379b2b29f8ce3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667611"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>Gerenciar instâncias de alerta com alertas unificados

Com a [experiência de alertas unificados](https://aka.ms/azure-alerts-overview) no Azure Monitor, você pode ver todos os seus diferentes tipos de alertas através do Azure. Isso abrange várias assinaturas, em um único painel. Este artigo mostra como você pode visualizar suas instâncias de alerta e como encontrar instâncias de alerta específicas para solução de problemas.

> [!NOTE]
> Você só pode acessar os alertas gerados nos últimos 30 dias.

## <a name="go-to-the-alerts-page"></a>Vá para a página de alertas

Você pode ir para a página de alertas de qualquer uma das seguintes maneiras:

- No [portal Azure,](https://portal.azure.com/)selecione**Alertas de** **Monitor** > .  

     ![Captura de tela de alertas do monitor](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- Use o contexto de um recurso específico. Abra um recurso, vá até a seção **Monitoramento** e escolha **Alertas**. A página de inicialidade é pré-filtrada para alertas sobre esse recurso específico.

     ![Captura de tela de alertas de monitoramento de recursos](media/alerts-managing-alert-instances/alert-resource.JPG)

- Use o contexto de um grupo de recursos específico. Abra um grupo de recursos, vá até a seção **Monitoramento** e escolha **Alertas**. A página de inicialidade é pré-filtrada para alertas sobre esse grupo de recursos específico.    

     ![Captura de tela de alertas de monitoramento do grupo de recursos](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>Encontre instâncias de alerta

A página **Resumo de alertas** fornece uma visão geral de todas as suas instâncias de alerta em todo o Azure. Você pode modificar a exibição de resumo selecionando **várias assinaturas** (até um máximo de 5), ou filtrando entre grupos de **recursos,** **recursos**específicos ou **intervalos de tempo.** Selecione **Alertas Totais,** ou qualquer uma das bandas de gravidade, para ir à exibição da lista para seus alertas.     

![Captura de tela da página Resumo de Alertas](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
Na página **Todos os Alertas,** todas as instâncias de alerta em todo o Azure estão listadas. Se estiver indo ao portal de uma notificação de alerta, você poderá usar os filtros disponíveis para restringir essa instância de alerta específica.

> [!NOTE]
> Se você veio para a página selecionando qualquer uma das faixas de gravidade, a lista será pré-filtrada para essa gravidade.

Além dos filtros disponíveis na página anterior, você também pode filtrar com base no serviço de monitor (por exemplo, plataforma para métricas), condição do monitor (acionado ou resolvido), gravidade, estado de alerta (novo/reconhecido/fechado) ou iD do grupo inteligente.

![Captura de tela da página Todos os Alertas](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> Se você veio para a página selecionando qualquer uma das faixas de gravidade, a lista será pré-filtrada para essa gravidade.

A seleção de qualquer instância de alerta abre a página **Detalhes de alerta,** permitindo que você veja mais detalhes sobre essa instância específica de alerta.   

![Captura de tela da página Detalhes do Alerta](media/alerts-managing-alert-instances/alert-details.jpg)  

