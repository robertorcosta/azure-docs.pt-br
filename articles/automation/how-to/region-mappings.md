---
title: Mapeamentos de espaço de trabalho de automação e Log Analytics do Azure
description: Este artigo descreve os mapeamentos permitidos entre uma conta de automação e um Log Analytics espaço de trabalho.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6b00b25dc4eaea5bc8a3f5fbd42389aff501f14a
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901018"
---
# <a name="workspace-mappings"></a>Mapeamentos de workspace

Na automação do Azure, você pode habilitar as seguintes soluções: "Gerenciamento de Atualizações", "Controle de Alterações e inventário" e "iniciar/parar VMs fora do horário de expediente". No entanto, quando você fizer isso, lembre-se de que apenas determinadas regiões têm suporte para vincular um Log Analytics espaço de trabalho e uma conta de automação em sua assinatura. Esse mapeamento só se aplica à conta de automação e ao espaço de trabalho Log Analytics. O espaço de trabalho Log Analytics e a conta de automação devem estar na mesma assinatura, mas podem estar em grupos de recursos diferentes implantados na mesma região.

Para obter mais informações, consulte [log Analytics espaço de trabalho e conta de automação](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Mapeamentos com suporte

A tabela abaixo mostra os mapeamentos com suporte:

|**Região do espaço de trabalho Log Analytics**|**Região de automação do Azure**|
|---|---|
|**EUA**||
|Lesteus<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Canada**||
|Canadá Central|Canadá Central|
|**Pacífico Asiático**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**Ocidental**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**Gov dos EUA**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> o mapeamento de lesteus para log Analytics espaços de trabalho a contas de automação não é um mapeamento de região para região exato, mas é o mapeamento correto.

<sup>2</sup> devido a restrições de capacidade, a região não está disponível quando você está criando novos recursos. Isso inclui contas de automação e espaços de trabalho do Log Analytics. No entanto, os recursos vinculados preexistentes na região devem continuar funcionando.

## <a name="unlink-a-workspace"></a>Desvincular um espaço de trabalho

Se você decidir que não deseja mais integrar sua conta de automação a um espaço de trabalho Log Analytics, você poderá desvincular sua conta diretamente da portal do Azure. Antes de continuar, primeiro você precisa remover "Gerenciamento de Atualizações", "Controle de Alterações e inventário" e "iniciar/parar VMs fora do horário de expediente" se estiver usando-as. Se você não removê-los, não poderá concluir a operação de desvinculação. Examine o artigo para cada solução que você está habilitando para entender as etapas necessárias para removê-lo.

Depois de removê-los, você pode executar as etapas a seguir para desvincular sua conta de automação.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitoramento do SQL Azure, podem ter criado ativos de automação e talvez precisem ser removidas antes de desvincular o espaço de trabalho.

1. Na portal do Azure, abra sua conta de automação. Na página **conta de automação** , selecione **espaço de trabalho vinculado** em **recursos relacionados**.

2. Na página **desvincular espaço de trabalho** , selecione **desvincular espaço de trabalho**. Você receberá uma solicitação verificando se deseja continuar.

3. Enquanto a Automação do Azure tenta desvincular a conta do seu espaço de trabalho do Log Analytics, você pode acompanhar o progresso no menu **Notificações**.

4. Se você usou "Gerenciamento de Atualizações", opcionalmente, talvez queira remover os itens a seguir que não são mais necessários depois de removê-lo.

    * Agendas de atualização: cada um tem um nome que corresponde a uma implantação de atualização que você criou.
    * Grupos de Hybrid Worker criados para a solução: cada um tem um nome `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`semelhante a.

5. Se você usou "iniciar/parar VMs fora do horário de expediente", você pode opcionalmente remover os itens a seguir que não são necessários depois de removê-lo.

    * Iniciar e parar agendas de runbook da VM
    * Iniciar e parar runbooks da VM
    * Variáveis

Como alternativa, você pode desvincular seu espaço de trabalho de sua conta de automação no espaço de trabalho.

1. No espaço de trabalho, selecione **conta de automação** em **recursos relacionados**. 
2. Na página **conta de automação** , selecione **desvincular conta**.

## <a name="next-steps"></a>Próximas etapas

* Saiba como começar a usar "Gerenciamento de Atualizações" e "Controle de Alterações e inventário":

    * De uma [máquina virtual](../automation-onboard-solutions-from-vm.md).
    * Da sua [conta de automação](../automation-onboard-solutions-from-automation-account.md).
    * Ao [procurar vários computadores](../automation-onboard-solutions-from-browse.md).
    * De um [runbook](../automation-onboard-solutions.md).

* Saiba como começar a usar "iniciar/parar VMs fora do horário de pico":

    * [Visão geral iniciar/parar VMs fora do horário de expediente](../automation-solution-vm-management.md).
