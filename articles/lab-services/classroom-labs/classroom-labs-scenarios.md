---
title: Use laboratórios de sala de aula para treinamentos - Azure Lab Services
description: Este artigo descreve como usar o Azure DevTest Labs para criar laboratórios no Azure para cenários de treinamento.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 155806222f9e11fec177487b7147d81054ac06ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717990"
---
# <a name="use-classroom-labs-for-trainings"></a>Use laboratórios de sala de aula para treinamentos
Você pode montar um laboratório para treinamentos. Os laboratórios de sala de aula do Azure Lab Services permitem que você crie um laboratório para o seu treinamento onde cada estagiário usa ambientes idênticos e isolados para treinamento. Você pode aplicar políticas para garantir que os ambientes de treinamento estejam disponíveis para cada estagiário apenas quando necessário e contém recursos suficientes, como máquinas virtuais, para o treinamento. 

![Laboratório de sala de aula](../media/classroom-labs-scenarios/classroom.png)

O Classroom Labs atende aos seguintes requisitos necessários para realizar o treinamento em qualquer ambiente virtual: 

- Os estagiários podem provisionar seus ambientes de treinamento rapidamente
- Todas as máquinas de treinamento devem ser idênticas
- Os estagiários não conseguem ver VMs criadas por outros estagiários
- Controle os custos fazendo com que os estagiários não possam obter mais VMs do que o necessário para o treinamento e desligando VMs quando não estiverem em uso
- Compartilhe facilmente o laboratório de treinamento com os estagiários
- Reutilize o laboratório de treinamento várias vezes

Neste artigo, você aprende sobre vários recursos do Azure Lab Services que podem ser usados para atender aos requisitos de treinamento descritos anteriormente e passos detalhados que você pode seguir para configurar um laboratório para treinamento.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Crie a conta de laboratório como administradora de contas de laboratório
O primeiro passo para usar o Azure Lab Services é criar uma conta de laboratório no portal Azure. Depois que um administrador de conta de laboratório cria a conta do laboratório, o administrador adiciona usuários que desejam criar laboratórios para a função **de Criador do Laboratório.** Os treinadores criam laboratórios com máquinas virtuais para que os alunos façam exercícios para o curso que estão ensinando. Para obter detalhes, consulte [Criar e gerenciar a conta de laboratório](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Criar e gerenciar laboratórios de sala de aula
Um treinador, que é membro do papel de Criador do Laboratório em uma conta de laboratório, pode criar um ou mais laboratórios na conta do laboratório. Você cria e configura um modelo VM com todos os softwares necessários para fazer exercícios em seu curso. Você escolhe uma imagem pronta das imagens disponíveis para criar um laboratório de sala de aula e, em seguida, personalizá-la instalando o software necessário para o laboratório. Para obter detalhes, consulte [Criar e gerenciar laboratórios de sala de aula](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Configurar políticas e configurações de uso
O criador do laboratório pode adicionar ou remover usuários ao laboratório, obter link de registro para enviar aos usuários do laboratório, configurar políticas como definir cotas individuais por usuário, atualizar o número de VMs disponíveis no laboratório e muito mais. Para obter detalhes, consulte [Configurar configurações de uso e políticas](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Criar e gerenciar agendas
Agendamentos permitem que você configure um laboratório de sala de aula, de modo que as VMs no laboratório iniciam e desligam automaticamente em um horário especificado. Você pode definir um agendamento único ou recorrente. Para obter detalhes, consulte [Criar e gerenciar horários para laboratórios de sala de aula](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Configurar e publicar um modelo VM
Um modelo em um laboratório é uma imagem básica da máquina virtual a partir do qual todas as máquinas virtuais de todos os usuários são criadas. Configure o modelo VM para que ele seja configurado exatamente com o que você deseja fornecer aos participantes do treinamento. Você pode fornecer um nome e uma descrição do modelo exibido para os usuários do laboratório. Em seguida, publique o modelo para tornar as instâncias do modelo de VM disponíveis para os usuários do laboratório. Quando você publicar um modelo, o Azure Lab Services criará VMs no laboratório usando o modelo. O número de VMs criadas neste processo é o mesmo que o número máximo de usuários permitidos em um laboratório, que você pode definir na política de uso do laboratório. Todas as máquinas virtuais têm a mesma configuração que o modelo. Para obter detalhes, [consulte Configurar e publicar máquinas virtuais de modelo](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Use VMs no laboratório de sala de aula
Um aluno ou participante do treinamento se inscreve no laboratório e se conecta à VM para fazer exercícios para o curso. Para obter detalhes, [consulte Como acessar um laboratório de sala de aula](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Próximas etapas
Comece com a criação de uma conta de laboratório no Classroom Labs seguindo as seguintes instruções no artigo: [Tutorial: Configure uma conta de laboratório com o Azure Lab Services](tutorial-setup-lab-account.md).