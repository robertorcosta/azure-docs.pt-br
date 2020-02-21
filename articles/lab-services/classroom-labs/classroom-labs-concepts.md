---
title: Conceitos de laboratórios de sala de aula-Azure Lab Services | Microsoft Docs
description: Aprenda os conceitos básicos dos serviços de laboratório e como ele pode facilitar a criação e o gerenciamento de laboratórios.
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
ms.date: 02/26/2019
ms.author: spelluru
ms.openlocfilehash: 15fd3c18d059466c2b2bd5e2431013f393092b4b
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526222"
---
# <a name="classroom-labs-concepts"></a>Conceitos dos laboratórios de sala de aula

A lista a seguir contém os principais conceitos e definições dos serviços de laboratório:

## <a name="quota"></a>Quota

Cota é o limite de tempo (em horas) que um professor pode definir para que um aluno use uma VM de laboratório. Ele pode ser definido como 0 ou um número específico de horas. Se a cota for definida como 0, um aluno só poderá usar a máquina virtual quando uma agenda estiver em execução ou quando um professor ligar manualmente a máquina virtual do aluno.  

As horas de cota são contadas quando o aluno inicia a própria VM do laboratório.  Se um professor iniciar manualmente a VM do laboratório para um aluno, as horas de cota não serão usadas para esse aluno.

## <a name="schedules"></a>Agendas

Agendas são os slots de tempo que um professor pode criar para a classe para que as VMs dos alunos estejam disponíveis para o tempo de aula.  As agendas podem ser uma única vez ou recorrentes.  As horas de cota não são usadas quando uma agenda está em execução.

Há três tipos de agendas: Standard, somente iniciar e somente parar.

- **Standard**.  Essa agenda iniciará todas as VMs de aluno na hora de início especificada e desligará todas as VMs de aluno na hora de parada especificada.
- **Somente iniciar**.   Essa agenda iniciará todas as VMs de aluno na hora especificada.  As VMs dos alunos não serão interrompidas até que um aluno interrompa a VM por meio do portal de Azure Lab Services ou que ocorra uma agenda somente de interrupção.
- **Somente parar**.  Essa agenda interromperá todas as VMs de aluno na hora especificada.  

## <a name="template-virtual-machine"></a>Máquina virtual de modelo

Uma máquina virtual de modelo em um laboratório é uma imagem de máquina virtual base da qual as máquinas virtuais de todos os usuários são criadas. Os criadores/criador de laboratório configuram a máquina virtual de modelo e as configuram com o software que eles desejam fornecer para treinar os participantes para fazer laboratórios. Quando você publica uma VM de modelo, o Azure Lab Services cria ou atualiza VMs de laboratório com base na VM de modelo.

## <a name="user-profiles"></a>Perfis do usuário

Este artigo descreve os diferentes perfis de usuário no Azure Lab Services.

### <a name="lab-account-owner"></a>Proprietário da conta do laboratório

Normalmente, e o administrador de TI dos recursos de nuvem da organização, que tem a assinatura do Azure atua como um proprietário de conta de laboratório e realiza as seguintes tarefas:

- Define uma conta de laboratório para sua organização.
- Gerencia e configura as políticas em todos os laboratórios.
- Concede permissões para as pessoas na organização para criarem um laboratório usando a conta do laboratório.

### <a name="professor"></a>Professor

Normalmente, os usuários como um professor ou um instrutor online criam laboratórios de sala de aula em uma conta de laboratório. Um educador executa as seguintes tarefas:

- Cria um laboratório de sala de aula.
- Cria máquinas virtuais no laboratório.
- Instala o software apropriado em máquinas virtuais.
- Especifica quem pode acessar o laboratório.
- Fornece um link de registro para o laboratório para alunos.

### <a name="student"></a>Aluno

Um estudante executa as seguintes tarefas:

- Usa o link de registro que o usuário do laboratório recebe de um criador do laboratório para registrar com o laboratório.
- Conecta-se a uma máquina virtual no laboratório e usa-a para fazer trabalhos, tarefas e projetos em sala de aula.

## <a name="next-steps"></a>Próximas etapas

Introdução à configuração de uma conta de laboratório necessária para criar um laboratório de sala de aula usando o Azure Lab Services:

- [Configurar uma conta de laboratório](tutorial-setup-lab-account.md)
