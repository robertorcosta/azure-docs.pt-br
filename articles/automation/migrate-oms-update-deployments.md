---
title: Migrar suas implantações de atualização do OMS para o Azure
description: Este artigo descreve como migrar as implantações de atualização do OMS para Azure
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 910f284eedbf50be5b58b6c18f02e50adda35e9a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679996"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Migre suas implantações de atualização do OMS para o Azure

O portal do OMS (Operations Management Suite) está sendo [preterido](../azure-monitor/platform/oms-portal-transition.md). Todas as funcionalidades disponíveis no portal OMS para gerenciamento de atualizações estão disponíveis no portal Azure, através de logs do Monitor Do Azure. Este artigo fornece as informações que você precisa para migrar para o portal Azure.

## <a name="key-information"></a>Principais informações

* As implantações existentes continuarão funcionando. Após recriar a implantação no Azure, será possível excluir a implantação antiga do OMS.
* Todos os recursos existentes que você tinha no OMS estão disponíveis no Azure, para saber mais sobre o Gerenciamento de Atualizações, consulte [Visão geral do Gerenciamento de Atualizações](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Acesse o portal do Azure

No workspace do OMS, clique em **Abrir no Azure**. Essa seleção navega para o espaço de trabalho do Log Analytics que o OMS vem usando.

![Abrir no Azure - portal do OMS](media/migrate-oms-update-deployments/link-to-azure-portal.png)

No portal do Azure, clique em **Conta de Automação**

![Logs do Azure Monitor](media/migrate-oms-update-deployments/log-analytics.png)

Em sua conta de Automação, clique em **Atualizar gerenciamento**.

![Gerenciamento de atualizações](media/migrate-oms-update-deployments/azure-automation.png)

No portal Azure, selecione **Contas de Automação** em Todos os **serviços**. 

Em **Ferramentas de Gerenciamento,** selecione a conta de automação apropriada e clique em **Gerenciamento de atualização**.

## <a name="recreate-existing-deployments"></a>Recriar implantações existentes

Todas as implantações de atualização criadas no portal do OMS têm uma [pesquisa salva](../azure-monitor/platform/computer-groups.md) também conhecida como grupo de computadores, com o mesmo nome da implantação de atualização existente. A pesquisa salva contém a lista de computadores que foram agendados na implantação de atualizações.

![Gerenciamento de atualizações](media/migrate-oms-update-deployments/oms-deployment.png)

Para usar essa pesquisa salva existente, siga estas etapas:

Para criar uma nova implantação de atualizações, vá para o portal do Azure, selecione a Conta de Automação usada e clique em **Gerenciamento de Atualizações**. Clique em **Agendar implantação de atualizações**.

![Agendar implantação de atualizações](media/migrate-oms-update-deployments/schedule-update-deployment.png)

O painel Nova implantação de atualizações é aberto. Insira valores para as propriedades descritas na tabela a seguir e clique em **Criar**:

Para **que as máquinas atualizem,** selecione a pesquisa salva usada pela implantação do OMS existente.

| Propriedade | Descrição |
| --- | --- |
|Nome |Nome exclusivo para identificar a Implantação de Atualizações. |
|Sistema operacional| Selecione **Linux** ou **Windows**.|
|Computadores para atualizar |Selecione uma pesquisa salva, um grupo importado ou selecione a máquina na lista suspensa e selecione máquinas individuais. Se você escolher **Machines**, a prontidão da máquina é mostrada na coluna **UPDATE AGENT READINESS**.</br> Para saber mais sobre os diferentes métodos de criação de grupos de computador nos registros do Azure Monitor, consulte [grupos de computador nos registros do Monitor do Azure](../azure-monitor/platform/computer-groups.md) |
|Classificações de origem|Selecione todas as classificações de atualização que você precisa. CentOS não oferece suporte para isso fora da caixa.|
|Atualizações para excluir|Insira as atualizações a serem excluídas. Para Windows, insira o artigo KB sem o prefixo **KB**. Para o Linux, insira o nome do pacote ou use um caractere curinga.  |
|Configurações de agendamento|Selecione o tempo para iniciar e selecione **Uma Vez** ou **Recorrente** para a recorrência. | 
| Janela de manutenção |Número de minutos definido para atualizações. O valor não pode ser inferior a 30 minutos ou superior a 6 horas. |
| Reinicialize o controle| Determina como as reinicializações devem ser tratadas.</br>As opções disponíveis são:</br>Reinicialização, se necessário (Padrão)</br>Sempre reinicializar</br>Nunca reinicializar</br>Somente reinicialização - não instalará as atualizações|

Clique em **Implantações de atualização agendadas** para exibir o status da implantação da atualização criada recentemente.

![nova implantação de atualizações](media/migrate-oms-update-deployments/new-update-deployment.png)

Conforme mencionado anteriormente, quando as novas implantações forem configuradas por meio do portal do Azure, as implantações existentes poderão ser removidas pelo portal do OMS.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Update Management no Azure, consulte [Gerenciamento de atualizações](automation-update-management.md).
