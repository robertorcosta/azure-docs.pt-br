---
title: Mapeamentos do espaço de trabalho do Azure Automation e log analytics
description: Este artigo descreve os mapeamentos permitidos entre uma conta de automação e um Espaço de Trabalho do Log Analytics para suportar a solução
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7f6508648be1e857a29f46b57e8309a7ec797291
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681846"
---
# <a name="workspace-mappings"></a>Mapeamentos de workspace

Ao habilitar soluções como gerenciamento de atualizações, rastreamento de alterações e inventário ou VMs start/stop durante as horas de folga, apenas certas regiões são suportadas para vincular um espaço de trabalho do Log Analytics e uma conta de Automação. Esse mapeamento só se aplica à conta automação e ao espaço de trabalho log analytics. Os recursos reportados à sua conta de Automação ou ao espaço de trabalho Log Analytics podem residir em outras regiões.

## <a name="supported-mappings"></a>Mapeamentos suportados

A tabela abaixo mostra os mapeamentos com suporte:

|**Região do Espaço de Trabalho do Log Analytics**|**Região da Automação do Azure**|
|---|---|
|**Nos**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Canada**||
|Canadá Central|Canadá Central|
|**Pacífico Asiático**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**Europa**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**Gov dos EUA**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> O mapeamento do EastUS para espaços de trabalho do Log Analytics para contas de automação não é uma região exata para mapeamento de região, mas é o mapeamento correto.

<sup>2</sup> Devido às restrições de capacidade, a região não está disponível na criação de novos recursos. Isso inclui contas de automação e espaços de trabalho do Log Analytics. No entanto, os recursos vinculados pré-existentes na região devem continuar funcionando.

## <a name="unlink-workspace"></a>Desvincular o workspace

Se você decidir que não quer mais integrar sua conta de Automação com um espaço de trabalho do Log Analytics, você pode desvincular sua conta diretamente do portal Azure. Antes de prosseguir, primeiro você precisa remover o Gerenciamento de atualizações, o rastreamento de alterações e o inventário e as VMs start/stop durante as horas de folga, se você estiver usando-as. Se você não removê-los, você não pode completar a operação de desvinculação. Revise o artigo para a solução específica que você importou para entender as etapas necessárias para removê-lo.

Depois de remover essas soluções, você poderá executar as etapas a seguir para desvincular sua conta de Automação.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitoramento SQL do Azure, podem ter criado ativos de automação e podem precisar ser removidas antes de desvincular o espaço de trabalho.

1. A partir do portal Azure, abra sua conta de Automação. Na página da conta Automação, selecione **o espaço de trabalho Vinculado** em Recursos **Relacionados**.

2. Na página Desvincular o workspace, clique em **Desvincular o workspace**. Você recebe uma verificação imediata se deseja continuar.

3. Enquanto a Automação do Azure tenta desvincular a conta do seu espaço de trabalho do Log Analytics, você pode acompanhar o progresso no menu **Notificações**.

4. Se você usou a solução Update Management, opcionalmente, você pode querer remover os seguintes itens que não são mais necessários depois de remover a solução.

    * Horários de atualização - Cada um tem um nome que corresponde a uma implantação de atualização que você criou.
    * Grupos de trabalhadores híbridos criados para `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`a solução - Cada um tem um nome semelhante ao .

5. Se você usou as VMs Start/Stop durante a solução de horas de folga, poderá remover opcionalmente os seguintes itens que não são necessários após a remoção da solução.

    * Iniciar e parar agendas de runbook da VM
    * Iniciar e parar runbooks da VM
    * Variáveis

Alternativamente, você pode desvincular seu espaço de trabalho de sua conta de Automação dentro do espaço de trabalho. 

1. No espaço de trabalho, selecione **Conta de Automação** em Recursos **Relacionados**. 
2. Na página Da conta de automação, selecione **Desvincular conta**.

## <a name="next-steps"></a>Próximas etapas

* Saiba como abordo as soluções de gerenciamento e controle de alterações de atualização e de rastreamento de alterações e inventário:

    * De uma [máquina virtual](../automation-onboard-solutions-from-vm.md)
    * A partir de sua [conta de Automação](../automation-onboard-solutions-from-automation-account.md)
    * Ao [navegar em várias máquinas](../automation-onboard-solutions-from-browse.md)
    * De um [runbook](../automation-onboard-solutions.md)

* Saiba como embarcar nas VMs Start/Stop durante a solução de horas de folga:

    * [Implantar VMs start/stop durante o horário de folga](../automation-solution-vm-management.md)
