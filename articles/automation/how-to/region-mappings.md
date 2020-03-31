---
title: Mapeamentos do espaço de trabalho do Azure Automation e log analytics
description: Este artigo descreve os mapeamentos permitidos entre uma conta de automação e um espaço de trabalho do Log Analytics para suportar a solução
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 965d6b206bb64e90fe59798ce0c37ccf029117f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849506"
---
# <a name="workspace-mappings"></a>Mapeamentos de workspace

Ao habilitar soluções como gerenciamento de atualizações, rastreamento de alterações e inventário ou vMs start/stop durante a solução off-hours, apenas certas regiões são suportadas para vincular um espaço de trabalho do Log Analytics e uma conta de automação. Esse mapeamento só se aplica à conta de automação e ao espaço de trabalho log analytics. Os recursos reportados à sua conta de automação ou espaço de trabalho do Log Analytics podem residir em outras regiões.

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

Se você decidir que não deseja mais integrar sua conta de Automação com um espaço de trabalho do Log Analytics, você pode desvincular sua conta diretamente do portal Azure. Antes de prosseguir, primeiro você precisa remover o Gerenciamento de atualizações, o rastreamento de alterações e o inventário ou as VMs Start/Stop durante as soluções off-hours, se você estiver usando-as. Se você não removê-los, este processo será impedido de prosseguir. Examine o artigo sobre a solução específica que você importou para entender as etapas necessárias para removê-la.

Depois de remover essas soluções, você poderá executar as etapas a seguir para desvincular sua conta de Automação.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitoramento do Azure SQL, podem ter criado ativos de automação e também podem precisar ser removidas antes de desvincular o workspace.

1. No portal do Azure, abra sua conta da Automação e, na página da conta da Automação, selecione **Workspace vinculado** na seção **Recursos Relacionados** à esquerda.

2. Na página Desvincular o workspace, clique em **Desvincular o workspace**. Você receberá um aviso verificando se deseja continuar.

3. Enquanto a Automação do Azure tenta desvincular a conta do seu espaço de trabalho do Log Analytics, você pode acompanhar o progresso no menu **Notificações**.

Se você tiver usado a solução Gerenciamento de Atualizações, como opção, convém remover os itens a seguir que não serão mais necessários após a remoção da solução.

* Atualizar agendas - Cada uma terá nomes que correspondam às implantações de atualizações que você criou)

* Grupos de trabalhadores híbridos criados para a `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`solução - Cada um será nomeado de forma semelhante a ).

Se você tiver usado a solução Iniciar/parar VMs durante os horários fora de pico, como opção, convém remover os itens a seguir que não serão mais necessários após a remoção da solução.

* Iniciar e parar agendas de runbook da VM
* Iniciar e parar runbooks da VM
* Variáveis

Alternativamente, você também pode desvincular seu espaço de trabalho de sua conta de automação do seu espaço de trabalho log analytics. Em seu espaço de trabalho, selecione **Conta de Automação** em Recursos **Relacionados**. Na página Da conta de automação, selecione **Desvincular conta**.

## <a name="next-steps"></a>Próximas etapas

Saiba como embarcar nas seguintes soluções:

Gerenciamento de atualizações e rastreamento de alterações e inventário:

* De uma [máquina virtual](../automation-onboard-solutions-from-vm.md)
* A partir de sua [conta de Automação](../automation-onboard-solutions-from-automation-account.md)
* Ao [navegar em várias máquinas](../automation-onboard-solutions-from-browse.md)
* De um [runbook](../automation-onboard-solutions.md)

Iniciar/Parar VMs durante os horários inativos

* [Implantar VMs start/stop durante as horas de folga](../automation-solution-vm-management.md)
