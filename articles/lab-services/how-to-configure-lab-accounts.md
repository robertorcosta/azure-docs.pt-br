---
title: Configurar o desligamento automático de VMs no Azure Lab Services
description: Este artigo descreve como configurar o desligamento automático de VMs na conta do laboratório.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 8647aed0e66993b8a7b8e5c0a42c8ceabbb1fb9e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798441"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>Configurar o desligamento automático de VMs para uma conta de laboratório

Você pode habilitar vários recursos de controle de custos de desligamento automático para prevenir de forma proativa custos adicionais quando as máquinas virtuais não estiverem sendo usadas ativamente. A combinação dos seguintes três recursos automáticos de desligamento e desconexão captura a maioria dos casos em que os usuários deixam acidentalmente suas máquinas virtuais em execução:
 
- Desconectar automaticamente os usuários de máquinas virtuais que o sistema operacional considera ocioso (somente Windows).
- Desligar automaticamente as máquinas virtuais quando os usuários se desconectarem (Windows & Linux).
- Desligar automaticamente as máquinas virtuais que são iniciadas, mas os usuários não se conectam.

Examine mais detalhes sobre os recursos de desligamento automático na seção [maximizar controle de custo com configurações de desligamento automático](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) .

## <a name="enable-automatic-shutdown"></a>Habilitar desligamento automático

1. Na [portal do Azure](https://portal.azure.com/) navegue até a página da **conta do laboratório** .
1. Selecione **configurações de laboratórios** no menu à esquerda.
1. Selecione as configurações de desligamento automático apropriadas para seu cenário.  

    > [!div class="mx-imgBorder"]
    > ![Configuração de desligamento automático na conta de laboratório](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)
    
    As configurações se aplicam a todos os laboratórios criados na conta do laboratório. Um criador de laboratório (professor) pode substituir essa configuração no nível de laboratório. A alteração nessa configuração na conta do laboratório afetará apenas os laboratórios criados depois que a alteração for feita.

    Para desabilitar a (s) configuração (ões), desmarque a caixa de seleção (s) nesta página. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como um proprietário de laboratório pode configurar ou substituir essa configuração no nível de laboratório, consulte [Configurar o desligamento automático de VMs para um laboratório](how-to-enable-shutdown-disconnect.md)
