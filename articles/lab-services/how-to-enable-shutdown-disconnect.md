---
title: Configurar o desligamento automático de VMs para um laboratório no Azure Lab Services
description: Saiba como habilitar ou desabilitar o desligamento automático de VMs quando uma conexão de área de trabalho remota for desconectada.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 3188117ac651f31057b4db88e32dfb42c45abb60
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88604890"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>Configurar o desligamento automático de VMs para um laboratório

Este artigo mostra como você pode configurar o desligamento automático de VMs para um laboratório.

Você pode habilitar vários recursos de controle de custos de desligamento automático para prevenir de forma proativa custos adicionais quando as máquinas virtuais não estiverem sendo usadas ativamente. A combinação dos seguintes três recursos automáticos de desligamento e desconexão captura a maioria dos casos em que os usuários deixam acidentalmente suas máquinas virtuais em execução:
 
* Desconectar automaticamente os usuários de máquinas virtuais que o sistema operacional considera ocioso (somente Windows).
* Desligar automaticamente as máquinas virtuais quando os usuários se desconectarem (Windows & Linux).
* Desligar automaticamente as máquinas virtuais que são iniciadas, mas os usuários não se conectam.

Examine mais detalhes sobre os recursos de desligamento automático na seção [maximizar controle de custo com configurações de desligamento automático](cost-management-guide.md#maximize-cost-control-with-auto-shutdown-settings) .

Um administrador da conta de laboratório pode definir essa configuração para a conta de laboratório na qual você cria laboratórios. Para obter mais informações, consulte [Configurar o desligamento automático de VMs para uma conta de laboratório](how-to-configure-lab-accounts.md). Como proprietário de um laboratório, você poderá substituir a configuração ao criar um laboratório ou depois que o laboratório for criado. 

## <a name="configure-for-the-lab-level"></a>Configurar para o nível de laboratório

Você pode definir a configuração de desligamento automático no [Azure Lab Services](https://labs.azure.com/).

* ao criar um laboratório (em **políticas de laboratório**) ou
* após a criação do laboratório (em **configurações**)

> [!div class="mx-imgBorder"]
> ![Configurar no momento da criação do laboratório](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

Certifique-se de examinar os detalhes sobre o desligamento automático na seção [maximizar controle de custo com as configurações de desligamento automático](cost-management-guide.md#maximize-cost-control-with-auto-shutdown-settings) .

> [!WARNING]
> Se você desligar o SO (sistema operacional) Windows em uma VM antes de desconectar uma sessão RDP com a VM, o recurso de desligamento automático não funcionará corretamente.  

## <a name="next-steps"></a>Próximas etapas

[Painel para laboratórios de sala de aula](use-dashboard.md)
