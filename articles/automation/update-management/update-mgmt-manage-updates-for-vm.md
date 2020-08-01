---
title: Gerenciar atualizações e patches para suas VMs na automação do Azure
description: Este artigo informa como usar Gerenciamento de Atualizações para gerenciar atualizações e patches para suas VMs do Azure e não Azure.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: e4f630931e3cd79a46a539955b45d72e5b573271
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449864"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>Gerenciar atualizações e patches para suas VMs

As atualizações de software no Azure Automation Gerenciamento de Atualizações fornecem um conjunto de ferramentas e recursos que podem ajudar a gerenciar a tarefa complexa de rastrear e aplicar atualizações de software a computadores no Azure e na nuvem híbrida. Um processo de gerenciamento de atualização de software eficaz é necessário para manter a eficiência operacional, superar problemas de segurança e reduzir os riscos de ameaças de segurança cibernéticos maiores. No entanto, devido à natureza variável da tecnologia e ao aparecimento contínuo de novas ameaças à segurança, a eficiência do gerenciamento de atualização de software exige atenção consistente e contínua.

> [!NOTE]
> O Gerenciamento de Atualizações dá suporte à implantação de atualizações de terceiros e ao pré-download delas. Esse suporte requer alterações nos sistemas que estão sendo atualizados. Confira [Definir configurações do Windows Update para o Gerenciamento de Atualizações da Automação do Azure](update-mgmt-configure-wuagent.md) para saber como definir essas configurações em seus sistemas.

Antes de tentar gerenciar atualizações para suas VMs, verifique se você habilitou o Gerenciamento de Atualizações usando um destes métodos:

* [Habilitar o Gerenciamento de Atualizações de uma conta de Automação](update-mgmt-enable-automation-account.md)
* [Habilitar o Gerenciamento de Atualizações navegando no portal do Azure](update-mgmt-enable-portal.md)
* [Habilitar o Gerenciamento de Atualizações de um runbook](update-mgmt-enable-runbook.md)
* [Habilitar o Gerenciamento de Atualizações de uma VM do Azure](update-mgmt-enable-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limitar o escopo para a implantação

O Gerenciamento de Atualizações usa uma configuração de escopo dentro do workspace para definir os computadores a receberem atualizações. Para obter mais informações, confira [Limitar o escopo de implantação do Gerenciamento de Atualizações](update-mgmt-scope-configuration.md).

## <a name="compliance-assessment"></a>Avaliação de conformidade

Antes de implantar as atualizações de software em seus computadores, examine os resultados da avaliação de conformidade da atualização para computadores habilitados. Para cada atualização de software, seu estado de conformidade é registrado e, depois que a avaliação é concluída, ele é coletado e encaminhado em massa para Azure Monitor logs.

Em um computador com Windows, a verificação de conformidade é executada a cada 12 horas por padrão. Além da verificação agendada, a verificação de conformidade de atualização é iniciada em 15 minutos do agente de Log Analytics para Windows que está sendo reiniciado, antes da instalação da atualização e após a instalação da atualização. Também é importante examinar nossas recomendações sobre como [Configurar o cliente do Windows Update](update-mgmt-configure-wuagent.md) com gerenciamento de atualizações para evitar problemas que o impeçam de ser gerenciado corretamente.

Para um computador com Linux, a verificação de conformidade é executada a cada hora por padrão. Se o agente de Log Analytics para Linux for reiniciado, uma verificação de conformidade será iniciada dentro de 15 minutos.

Os resultados de conformidade são apresentados em Gerenciamento de Atualizações para cada computador avaliado. Para um novo computador habilitado para gerenciamento, pode levar até 30 minutos para que o painel exiba dados atualizados a partir dele.

Examine [as atualizações de software do monitor](update-mgmt-view-update-assessments.md) para saber como exibir os resultados de conformidade.

## <a name="deploy-updates"></a>Implantar atualizações

Depois de examinar os resultados de conformidade, a fase de implantação de atualização de software é o processo de implantação de atualizações de software. Para instalar atualizações, agende uma implantação que alinhe-se com a agenda de liberação e período de serviço. Você pode escolher quais tipos de atualização deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

Examine [implantar atualizações de software](update-mgmt-deploy-updates.md) para saber como agendar uma implantação de atualização.

## <a name="review-update-deployments"></a>Examinar implantações de atualização

Após a conclusão da implantação, examine o processo para determinar o êxito da implantação de atualização por máquina ou grupo de destino. Consulte [examinar o status da implantação](update-mgmt-deploy-updates.md#check-deployment-status) para saber como você pode monitorar o status da implantação.

## <a name="next-steps"></a>Próximas etapas

* Para saber como criar alertas para notificá-lo sobre os resultados da implantação de atualização, consulte [criar alertas para gerenciamento de atualizações](update-mgmt-configure-alerts.md).

* Você pode [consultar logs de Azure monitor](update-mgmt-query-logs.md) para analisar avaliações de atualização, implantações e outras tarefas de gerenciamento relacionadas. Ele inclui consultas predefinidas para ajudá-lo a começar.