---
title: Configure contas de laboratório no Azure Lab Services | Microsoft Docs
description: Este artigo descreve como criar uma conta de laboratório, visualizar todas as contas de laboratório ou excluir uma conta de laboratório no Azure Lab Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284298"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Configure contas de laboratório no Azure Lab Services 
No Azure Lab Services, uma conta de laboratório é um contêiner para tipos de laboratório gerenciados, como laboratórios de sala de aula. Um administrador configura uma conta de laboratório no Azure Lab Services e fornece acesso aos proprietários de laboratório que podem criar laboratórios na conta. 

Este artigo mostra como executar as seguintes tarefas: 

- Especifique um intervalo de endereços para VMs no laboratório
- Configure o desligamento automático das VMs na desconexão

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Especifique um intervalo de endereços para VMs no laboratório
O procedimento a seguir tem etapas para especificar um intervalo de endereços para VMs no laboratório. Se você atualizar o intervalo que você especificou anteriormente, o intervalo de endereços modificado se aplicará apenas às VMs criadas após a alteração ser feita. 

Aqui estão algumas restrições ao especificar o intervalo de endereços que você deve ter em mente. 

- O prefixo deve ser menor ou igual a 23. 
- Se uma rede virtual for acompanhada na conta de laboratório, o intervalo de endereços fornecido não poderá se sobrepor à faixa de endereço da rede virtual peered.

1. Na página Conta do **Laboratório,** selecione **configurações de Laboratórios** no menu esquerdo.
2. Para o campo **''Intervalo de** endereços', especifique o intervalo de endereços para VMs que serão criados no laboratório. O intervalo de endereços deve estar na notação de roteamento interdomínio (CIDR) sem classe (CIDR) (exemplo: 10.20.0.0/23). As máquinas virtuais do laboratório serão criadas nesse intervalo de endereços.
3. Selecione **Salvar** na barra de ferramentas. 

    ![Configurar o intervalo de endereços](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Desligamento automático de VMs na desconexão
Você pode ativar ou desativar o desligamento automático das VMs do Windows lab (modelo ou aluno) depois que uma conexão remota da área de trabalho for desconectada. Você também pode especificar quanto tempo as VMs devem esperar para que o usuário se reconecte antes de ser automaticamente desligado.

![Configuração automática de desligamento na conta do laboratório](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Esta configuração se aplica a todos os laboratórios criados na conta do laboratório. Um dono de laboratório pode substituir essa configuração no nível do laboratório. A alteração dessa configuração na conta do laboratório só afetará os laboratórios que são criados após a mudança ser feita.

Para saber como um proprietário de laboratório pode configurar essa configuração no nível do laboratório, consulte [este artigo](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Permitir que o criador do laboratório escolha a localização do laboratório](allow-lab-creator-pick-lab-location.md)
- [Conecte a rede do seu laboratório com uma rede virtual de pares](how-to-connect-peer-virtual-network.md)
- [Anexar uma galeria de imagens compartilhadas a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicione um usuário como proprietário de laboratório](how-to-add-user-lab-owner.md)
- [Exibir configurações de firewall para um laboratório](how-to-configure-firewall-settings.md)
