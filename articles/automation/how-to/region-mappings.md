---
title: Regiões compatíveis com o workspace do Log Analytics vinculado
description: Este artigo descreve os mapeamentos de região com suporte entre uma conta de Automação e um workspace do Log Analytics.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 06/12/2020
ms.topic: conceptual
manager: carmonm
ms.custom: references_regions
ms.openlocfilehash: 4e5cad25c80661f9e707f545929e6ffcb00a1e42
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87447852"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>Regiões compatíveis com o workspace do Log Analytics vinculado

Na Automação do Azure, habilite os recursos de Gerenciamento de Atualizações, Controle de Alterações e Inventário, Iniciar/Parar VMs fora do horário comercial para suas VMs. No entanto, somente determinadas regiões têm suporte para vincular um workspace do Log Analytics e uma Conta de Automação na sua assinatura. Os mapeamentos de região se aplicam somente à conta de Automação e ao workspace do Log Analytics. O workspace do Log Analytics e a conta de Automação devem estar na mesma assinatura, mas podem estar em grupos de recursos diferentes implantados na mesma região. Para saber mais informações, veja [Workspace do Log Analytics e conta de Automação](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Mapeamentos com suporte

A tabela abaixo mostra os mapeamentos com suporte:

|**Região do workspace do Log Analytics**|**Região da Automação do Azure**|
|---|---|
|**EUA**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS|WestCentralUS|
|**Canadá**||
|Canadá Central|Canadá Central|
|**Pacífico Asiático**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|ChinaEast2<sup>2</sup>|ChinaEast2|
|JapanEast|JapanEast|
|**Europa**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**US Gov**||
|USGovVirginia|USGovVirginia|
|USGovArizona<sup>2</sup>|USGovArizona|

<sup>1</sup> O mapeamento EastUS para workspaces do Log Analytics para contas de Automação não é um mapeamento exato de região para região, mas é o mapeamento correto.

<sup>2</sup> nessa região, somente gerenciamento de atualizações tem suporte e outros recursos como controle de alterações e inventário não estão disponíveis no momento.

## <a name="unlink-a-workspace"></a>Desvincular um workspace

Caso decida que não quer mais integrar sua conta de Automação ao workspace do Log Analytics, você pode desvincular a conta diretamente no portal do Azure. Antes de continuar, primeiro [remova](move-account.md#remove-features) os recursos Gerenciamento de Atualizações, Controle de Alterações e inventário, e Iniciar/Parar VMs fora do horário comercial se estiver usando-os. Se não os remover, você não poderá concluir a operação de desvinculação. 

Com os recursos removidos, siga as etapas abaixo para desvincular sua conta de Automação.

> [!NOTE]
> Alguns recursos, incluindo versões anteriores da solução de monitoramento do Azure SQL, podem ter criado ativos de Automação que precisam ser removidos antes de desvincular o workspace.

1. No portal do Azure, abra sua conta de Automação. Na página da conta de Automação, selecione **Workspace vinculado** em **Recursos relacionados**.

2. Na página Desvincular workspace, selecione **Desvincular workspace**. Você receberá um aviso perguntando se você deseja continuar.

3. Enquanto a Automação do Azure desvincula a conta do seu workspace do Log Analytics, você pode acompanhar o progresso em **Notificações** no menu.

4. Se usou o Gerenciamento de Atualizações, convém remover, opcionalmente, os seguintes itens que não são mais necessários:

    * Agendamentos de atualização: cada um tem um nome que corresponde à implantação de atualização que você criou.
    * Grupos de trabalho híbridos criados para o recurso: Cada um tem um nome semelhante a `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`.

5. Se usou o recurso Iniciar/Parar VMs fora do horário comercial, opcionalmente, você pode remover os seguintes itens que não são mais necessários:

    * Iniciar e parar agendas de runbook da VM
    * Iniciar e parar runbooks da VM
    * Variáveis

Como alternativa, desvincule o workspace da sua conta da Automação no workspace.

1. No workspace, selecione **Conta de Automação** em **Recursos relacionados**.
2. Na página Conta de Automação, selecione **Desvincular conta**.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o Gerenciamento de Atualizações, veja [Visão geral do recurso Gerenciamento de Atualizações](../update-management/update-mgmt-overview.md).
* Para obter informações sobre os recursos Controle de Alterações e Inventário, veja [Visão geral do recurso Controle de Alterações e Inventário](../change-tracking.md).
* Saiba mais sobre o recurso Iniciar/Parar VMs fora do horário comercial em [Visão geral do recurso Iniciar/Parar VMs fora do horário comercial](../automation-solution-vm-management.md).
