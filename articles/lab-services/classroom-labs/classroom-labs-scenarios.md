---
title: Use os laboratórios da sala de aula para treinamentos-Azure Lab Services
description: Este artigo descreve como usar Azure DevTest Labs para criar laboratórios no Azure para cenários de treinamento.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717990"
---
# <a name="use-classroom-labs-for-trainings"></a>Usar laboratórios de sala de aula para treinamentos
Você pode configurar um laboratório para treinamentos. Os laboratórios de sala de aula do Azure Lab Services permitem que você crie um laboratório para seu treinamento, onde cada treinador usa ambientes idênticos e isolados para treinamento. Você pode aplicar políticas para garantir que os ambientes de treinamento estejam disponíveis para cada estagiário apenas quando necessário e contém recursos suficientes, como máquinas virtuais, para o treinamento. 

![Laboratório de sala de aula](../media/classroom-labs-scenarios/classroom.png)

Os laboratórios de sala de aula atendem aos seguintes requisitos necessários para realizar o treinamento em qualquer ambiente virtual: 

- Os estagiários podem provisionar seus ambientes de treinamento rapidamente
- Todas as máquinas de treinamento devem ser idênticas
- Os estagiários não conseguem ver VMs criadas por outros estagiários
- Controle os custos fazendo com que os estagiários não possam obter mais VMs do que o necessário para o treinamento e desligando VMs quando não estiverem em uso
- Compartilhe facilmente o laboratório de treinamento com os estagiários
- Reutilize o laboratório de treinamento várias vezes

Neste artigo, você aprenderá sobre vários recursos Azure Lab Services que podem ser usados para atender aos requisitos de treinamento descritos anteriormente e etapas detalhadas que você pode seguir para configurar um laboratório para treinamento.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Criar a conta de laboratório como um administrador de conta de laboratório
A primeira etapa no uso do Azure Lab Services é criar uma conta de laboratório no portal do Azure. Depois que um administrador de conta de laboratório cria a conta de laboratório, o administrador adiciona os usuários que desejam criar laboratórios para a função de **criador de laboratório** . Os treinadores criam laboratórios com máquinas virtuais para que os alunos façam exercícios para o curso que estão ensinando. Para obter detalhes, consulte [criar e gerenciar conta de laboratório](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Criar e gerenciar laboratórios de sala de aula
Um instrutor, que é membro da função de criador de laboratório em uma conta de laboratório, pode criar um ou mais laboratórios na conta de laboratório. Você cria e configura uma VM de modelo com todo o software necessário para fazer exercícios no seu curso. Você escolhe uma imagem pronta das imagens disponíveis para criar um laboratório de sala de aula e, em seguida, personalizá-lo instalando o software necessário para o laboratório. Para obter detalhes, consulte [criar e gerenciar laboratórios de sala de aula](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Configurar políticas e configurações de uso
O criador do laboratório pode adicionar ou remover usuários do laboratório, obter o link de registro para enviar aos usuários do laboratório, configurar políticas como definir cotas individuais por usuário, atualizar o número de VMs disponíveis no laboratório e muito mais. Para obter detalhes, consulte [definir configurações e políticas de uso](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Criar e gerenciar agendas
Agendamentos permitem que você configure um laboratório de sala de aula, de modo que as VMs no laboratório iniciam e desligam automaticamente em um horário especificado. Você pode definir um agendamento único ou recorrente. Para obter detalhes, consulte [criar e gerenciar agendas para laboratórios de sala de aula](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Configurar e publicar uma VM de modelo
Um modelo em um laboratório é uma imagem básica da máquina virtual a partir do qual todas as máquinas virtuais de todos os usuários são criadas. Configure a VM do modelo para que ela seja configurada com exatamente o que você deseja fornecer aos participantes do treinamento. Você pode fornecer um nome e uma descrição do modelo exibido para os usuários do laboratório. Em seguida, publique o modelo para tornar as instâncias do modelo de VM disponíveis para os usuários do laboratório. Quando você publicar um modelo, o Azure Lab Services criará VMs no laboratório usando o modelo. O número de VMs criadas neste processo é o mesmo que o número máximo de usuários permitidos em um laboratório, que você pode definir na política de uso do laboratório. Todas as máquinas virtuais têm a mesma configuração que o modelo. Para obter detalhes, consulte [configurar e publicar máquinas virtuais de modelo](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Usar VMs no laboratório da sala de aula
Um aluno ou um participante de treinamento registra-se no laboratório e se conecta à VM para fazer exercícios para o curso. Para obter detalhes, consulte [como acessar um laboratório de sala de aula](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Próximos passos
Comece com a criação de uma conta de laboratório em laboratórios de sala de aula seguindo as instruções no artigo: [tutorial: configurar uma conta de laboratório com Azure Lab Services](tutorial-setup-lab-account.md).