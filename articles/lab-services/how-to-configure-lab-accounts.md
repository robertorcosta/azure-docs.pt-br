---
title: Configurar o desligamento automático de VMs no Azure Lab Services
description: Este artigo descreve como configurar o desligamento automático de VMs na conta do laboratório.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 783e3b310b3ad06f637453f0e1b11f6a78beec3a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445807"
---
# <a name="configure-automatic-shutdown-of-vms-on-disconnect-setting-for-a-lab-account"></a>Configurar o desligamento automático de VMs na configuração de desconexão para uma conta de laboratório
Você poderá habilitar ou desabilitar o desligamento automático de VMs de laboratório do Windows (modelo ou aluno) depois que uma conexão de área de trabalho remota for desconectada. Você também poderá especificar quanto tempo o Lab Services deve esperar até o usuário se reconectar antes do desligamento automático.

## <a name="enable-automatic-shutdown"></a>Habilitar desligamento automático

1. Na página **Conta de Laboratório**, selecione **Configurações dos laboratórios** no menu à esquerda.
2. Selecione a opção **Desligar automaticamente as máquinas virtuais quando os usuários se desconectarem**.
3. Especifique quanto tempo o Lab Services deve esperar até o usuário se reconectar antes do desligamento automático das VMs.

    ![Configuração de desligamento automático na conta de laboratório](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

    Essa configuração se aplica a todos os laboratórios criados na conta de laboratório. Um criador de laboratório (professor) pode substituir essa configuração no nível de laboratório. A alteração nessa configuração na conta do laboratório afetará apenas os laboratórios criados depois que a alteração for feita.

    Para desabilitar essa configuração, desmarque a opção **Desligar automaticamente as máquinas virtuais quando os usuários se desconectarem** nesta página. 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como um proprietário de laboratório pode configurar ou substituir essa configuração no nível de laboratório, consulte [este artigo](how-to-enable-shutdown-disconnect.md)
