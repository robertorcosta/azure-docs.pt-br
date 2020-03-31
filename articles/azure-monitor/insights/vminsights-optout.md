---
title: Desativar o monitoramento no Monitor Azure para VMs
description: Este artigo descreve como parar de monitorar suas máquinas virtuais no Monitor Azure para VMs.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 80473aa494b8fbcea5e43870b7717cd3472dd7d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480514"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms"></a>Desativar o monitoramento de suas VMs no Monitor Azure para VMs

Depois de habilitar o monitoramento de suas máquinas virtuais (VMs), você pode optar por desativar o monitoramento no Monitor Azure para VMs. Este artigo mostra como desativar o monitoramento de uma ou mais VMs.  

Atualmente, o Monitor Azure para VMs não suporta desabilitação seletiva do monitoramento de VM. Seu espaço de trabalho do Log Analytics pode suportar o Azure Monitor para VMs e outras soluções. Também pode coletar outros dados de monitoramento. Se o espaço de trabalho do Log Analytics fornecer esses serviços, você precisa entender o efeito e os métodos de desativação do monitoramento antes de iniciar.

O Azure Monitor para VMs depende dos seguintes componentes para fornecer sua experiência:

* Um espaço de trabalho do Log Analytics, que armazena dados de monitoramento de VMs e outras fontes.
* Uma coleção de contadores de desempenho configurados no espaço de trabalho. A coleção atualiza a configuração de monitoramento em todas as VMs conectadas ao espaço de trabalho.
* `VMInsights`, que é uma solução de monitoramento configurada no espaço de trabalho. Esta solução atualiza a configuração de monitoramento em todas as VMs conectadas ao espaço de trabalho.
* `MicrosoftMonitoringAgent`e `DependencyAgent`, que são extensões Azure VM. Essas extensões coletam e enviam dados para o espaço de trabalho.

Enquanto você se prepara para desativar o monitoramento de suas VMs, tenha essas considerações em mente:

* Se você avaliou com uma única VM e usou o espaço de trabalho padrão do Log Analytics pré-selecionado, poderá desativar o monitoramento desinstalando o agente dependency da VM e desconectando o agente log analytics deste espaço de trabalho. Esta abordagem é apropriada se você pretende usar a VM para outros fins e decidir depois reconectá-la a um espaço de trabalho diferente.
* Se você selecionou um espaço de trabalho pré-existente do Log Analytics que suporta outras soluções de monitoramento e coleta de dados de outras fontes, você pode remover componentes da solução do espaço de trabalho sem interromper ou afetar seu espaço de trabalho.  

>[!NOTE]
> Depois de remover os componentes da solução do seu espaço de trabalho, você pode continuar a ver dados de desempenho e mapa para suas VMs do Azure. Os dados eventualmente pararão de aparecer nas exibições **Desempenho** e **Mapa.** A opção **Ativar** estará disponível na VM Azure selecionada para que você possa reativar o monitoramento no futuro.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Remova completamente o Monitor Azure para VMs

Se você ainda precisar do espaço de trabalho do Log Analytics, siga essas etapas para remover completamente o Monitor Azure para VMs. Você removerá `VMInsights` a solução do espaço de trabalho.  

1. Faça login no [portal Azure](https://portal.azure.com).
2. No portal Azure, selecione **Todos os serviços**. Na lista de recursos, **digite Log Analytics**. À medida que você começa a digitar, a lista filtra sugestões com base na sua entrada. Selecione **Análise de log**.
3. Na sua lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho escolhido quando habilitou o Monitor Azure para VMs.
4. À esquerda, selecione **Soluções**.  
5. Na lista de soluções, selecione **VMInsights (nome do espaço de trabalho).** Na **página Visão geral** da solução, **selecione Excluir**. Quando solicitado para confirmar, selecione **Sim**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Desabilite o monitoramento e mantenha o espaço de trabalho  

Se o espaço de trabalho do Log Analytics ainda precisar suportar o monitoramento de outras fontes, seguindo essas etapas para desativar o monitoramento na VM que você usou para avaliar o Monitor Azure para VMs. Para As VMs do Azure, você removerá a extensão VM do agente de dependência e a extensão VM do agente Log Analytics para Windows ou Linux diretamente da VM. 

>[!NOTE]
>Não remova o agente do Log Analytics se: 
>
> * A Azure Automation gerencia a VM para orquestrar processos ou gerenciar configurações ou atualizações. 
> * O Azure Security Center gerencia a VM para detecção de segurança e ameaças. 
>
> Se você remover o agente Log Analytics, você impedirá que esses serviços e soluções gerenciem proativamente sua VM. 

1. Faça login no [portal Azure](https://portal.azure.com). 
2. No Portal do Azure, selecione **Máquinas Virtuais**. 
3. Na lista, selecione uma VM. 
4. À esquerda, **selecione Extensões**. Na página **Extensões,** selecione **DependencyAgent**.
5. Na página propriedades de extensão, **selecione Desinstalar**.
6. Na página **Extensões,** selecione **MicrosoftMonitoringAgent**. Na página propriedades de extensão, **selecione Desinstalar**.  
