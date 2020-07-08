---
title: Criar um espaço de trabalho do Log Analytics no portal do Azure | Microsoft Docs
description: Saiba como criar um espaço de trabalho do Log Analytics para habilitar as soluções de gerenciamento e a coleta de dados da sua nuvem e dos ambientes locais no portal do Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: e6ef8fb7dfd98a509b0b89e049c70169dafbe134
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860555"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Criar um espaço de trabalho do Log Analytics no Portal do Azure
Use o menu **Espaços de trabalho do Log Analytics** para criar um espaço de trabalho do Log Analytics usando o portal do Azure. Um espaço de trabalho do Log Analytics é um ambiente exclusivo para dados de log do Azure Monitor. Cada workspace tem seu próprio repositório de dados e configuração. As fontes de dados e as soluções são configuradas para armazenar seus dados em um determinado workspace. Você precisa de um espaço de trabalho do Log Analytics se pretender coletar dados das seguintes fontes:

* Recursos do Azure em sua assinatura
* Computadores locais monitorados pelo System Center Operations Manager
* Coleções de dispositivos do Configuration Manager 
* Dados de diagnóstico ou de log do armazenamento do Azure

Para outras fontes, como as VMs do Azure e as VMs do Windows ou do Linux em seu ambiente, veja os tópicos a seguir:

*  [Coletar dados de máquinas virtuais do Azure](../learn/quick-collect-azurevm.md) 
*  [Coletar dados de um computador Linux híbrido](../learn/quick-collect-linux-computer.md)
*  [Coletar dados de um computador Windows híbrido](quick-collect-windows-computer.md)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure-portal"></a>Entre no portal do Azure
Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Criar um workspace
1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Escolha **workspaces do Log Analytics**.

    ![Portal do Azure](media/quick-create-workspace/azure-portal-01.png)
  
2. Clique em **Adicionar**e selecione opções para os seguintes itens:

   * Forneça um nome para o novo **Espaço de Trabalho do Log Analytics**, como *DefaultLAWorkspace*. Esse nome deve ser globalmente exclusivo em todas as assinaturas do Azure Monitor.
   * Selecione uma **Assinatura** a vincular escolhendo uma na lista suspensa, se a selecionada por padrão não é adequada.
   * Para o **Grupo de Recursos**, escolha entre usar um grupo de recursos existente já configurado ou criar um novo.  
   * Selecione um **local** disponível.  Para obter mais informações, confira em quais [regiões em que o Log Analytics está disponível](https://azure.microsoft.com/regions/services/) e pesquise Azure Monitor no campo **Pesquisar por um produto**.  
   * Se você estiver criando um workspace em uma nova assinatura feita depois de 2 de abril de 2018, ele usará o plano de preços *por GB* e a opção de selecionar um tipo de preço não estará disponível.  Se você estiver criando um workspace para uma assinatura existente feita antes de 2 de abril ou uma assinatura associada a um registro do Enterprise Agreement existente, selecione seu tipo de preço preferido.  Para obter mais informações sobre os tipos de preço específicos, consulte [Detalhes de preço do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Criar folha de recursos do Log Analytics](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Depois de fornecer as informações necessárias no painel **Espaço de Trabalho do Log Analytics**, clique em **OK**.  

Enquanto as informações são verificadas e o workspace é criado, você pode acompanhar seu progresso no menu **Notificações**. 

## <a name="troubleshooting"></a>Solução de problemas
Quando você cria um workspace que foi excluído nos últimos 14 dias e no [estado de exclusão reversível](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#soft-delete-behavior), a operação pode ter um resultado diferente, dependendo da configuração do seu workspace:
1. Se você fornecer o mesmo nome do workspace, grupo de recursos, assinatura e região como no workspace excluído, seu workspace será recuperado, incluindo seus dados, configuração e agentes conectados.
2. Se você usar o mesmo nome de workspace, mas outro grupo de recursos, assinatura ou região, receberá o erro *Esse nome de workspace já está em uso. Tente escolher outro*. Para substituir a exclusão reversível e excluir permanentemente o workspace e criar um novo workspace com o mesmo nome, siga estas etapas para recuperar o workspace primeiro e executar a exclusão permanente:
   - [Recuperar](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace) seu workspace
   - [Excluir permanentemente](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete) seu espaço de trabalho
   - Criar um novo workspace usando o mesmo nome

## <a name="next-steps"></a>Próximas etapas
Agora que já tem um workspace disponível, você pode configurar a coleta de monitoramento de telemetria, executar pesquisas de logs para analisar os dados e adicionar uma solução de gerenciamento para fornecer dados adicionais e informações analíticas. 

* Para habilitar a coleta de dados de recursos do Azure com o Diagnóstico do Azure ou com o armazenamento do Azure, consulte [Coletar logs e as métricas do serviço do Azure para uso no Log Analytics](../platform/collect-azure-metrics-logs.md).  
* Adicione o [System Center Operations Manager como uma fonte de dados](../platform/om-agents.md) para coletar dados de agentes que se reportam ao seu grupo de gerenciamento do Operations Manager e armazene-o em seu repositório de espaço de trabalho do Log Analytics. 
* Conecte-se ao [Configuration Manager](../platform/collect-sccm.md) para importar computadores que são membros de coleções na hierarquia.  
* Examine as [soluções de monitoramento](../insights/solutions.md) disponíveis e como adicionar ou remover uma solução do workspace.
