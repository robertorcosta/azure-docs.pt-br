---
title: Configurar contas de laboratório no Azure Lab Services | Microsoft Docs
description: Este artigo descreve como criar uma conta de laboratório, exibir todas as contas de laboratório ou excluir uma conta de laboratório no Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: fa9dba62b3b58687ec6a2bfc29e8722f7016b679
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443407"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Configurar contas de laboratório no Azure Lab Services 
No Azure Lab Services, uma conta de laboratório é um contêiner para tipos de laboratório gerenciados, como laboratórios de sala de aula. Um administrador configura uma conta de laboratório no Azure Lab Services e fornece acesso aos proprietários de laboratório que podem criar laboratórios na conta. 

Este artigo mostra como executar as seguintes tarefas: 

- Especificar um intervalo de endereços para VMs no laboratório
- Configurar o desligamento automático de VMs na desconexão

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Especificar um intervalo de endereços para VMs no laboratório
O procedimento a seguir tem etapas para especificar um intervalo de endereços para VMs no laboratório. Se você atualizar o intervalo especificado anteriormente, o intervalo de endereços modificados se aplicará somente às VMs criadas depois que a alteração tiver sido feita. 

Aqui estão algumas restrições ao especificar o intervalo de endereços que você deve ter em mente. 

- O prefixo deve ser menor ou igual a 23. 
- Se uma rede virtual estiver emparelhada com a conta de laboratório, o intervalo de endereços fornecido não poderá se sobrepor ao intervalo de endereços da rede virtual emparelhada.

1. Na página **conta do laboratório** , selecione **configurações de laboratórios** no menu à esquerda.
2. Para o campo **intervalo de endereços** , especifique o intervalo de endereços para as VMs que serão criadas no laboratório. O intervalo de endereços deve estar na notação CIDR (roteamento entre domínios sem classificação) (exemplo: 10.20.0.0/23). As máquinas virtuais do laboratório serão criadas nesse intervalo de endereços.
3. Selecione **Salvar** na barra de ferramentas. 

    ![Configurar intervalo de endereços](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Desligamento automático de VMs na desconexão
Você pode habilitar ou desabilitar o desligamento automático de VMs do laboratório do Windows (modelo ou aluno) depois que uma conexão de área de trabalho remota for desconectada. Você também pode especificar quanto tempo as VMs devem aguardar até que o usuário se reconecte antes de desligar automaticamente.

![Configuração de desligamento automático na conta do laboratório](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Essa configuração se aplica a todos os laboratórios criados na conta do laboratório. Um proprietário de laboratório pode substituir essa configuração no nível de laboratório. A alteração nessa configuração na conta do laboratório afetará apenas os laboratórios criados depois que a alteração for feita.

Para saber mais sobre como um proprietário de laboratório pode definir essa configuração no nível de laboratório, consulte [Este artigo](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Permitir que o criador de laboratório escolha o local do laboratório](allow-lab-creator-pick-lab-location.md)
- [Conectar a rede do seu laboratório a uma rede virtual de mesmo nível](how-to-connect-peer-virtual-network.md)
- [Anexar uma galeria de imagens compartilhadas a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicionar um usuário como um proprietário de laboratório](how-to-add-user-lab-owner.md)
- [Exibir configurações de firewall para um laboratório](how-to-configure-firewall-settings.md)
