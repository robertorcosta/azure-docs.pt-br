---
title: Conceitos de Laboratórios de Sala de Aula - Azure Lab Services | Microsoft Docs
description: Aprenda os conceitos básicos de Serviços de Laboratório e como ele pode facilitar a criação e o gerenciamento de laboratórios.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526222"
---
# <a name="classroom-labs-concepts"></a>Conceitos dos laboratórios de sala de aula

A lista a seguir contém conceitos e definições importantes do Lab Services:

## <a name="quota"></a>Quota

Cota é o limite de tempo (em horas) que um professor pode definir para um aluno usar uma VM de laboratório. Pode ser definido como 0, ou um número específico de horas. Se a cota for definida como 0, o aluno só poderá usar a máquina virtual quando um horário estiver em execução ou quando um professor liga manualmente a máquina virtual para o aluno.  

As horas de cotas são contadas quando o aluno inicia o laboratório VM.  Se um professor inicia manualmente o VM do laboratório para um aluno, as horas de cotas não são usadas para esse aluno.

## <a name="schedules"></a>Agendas

Os horários são as faixas horárias que um professor pode criar para a aula para que as VMs do aluno estejam disponíveis para o horário de aula.  Os horários podem ser únicos ou recorrentes.  As horas de cota não são usadas quando um horário está em execução.

Existem três tipos de horários: Padrão, Somente Start e Stop.

- **Padrão**.  Este cronograma iniciará todas as VMs dos alunos na hora de início especificada e desligará todas as VMs dos alunos na hora de parada especificada.
- **Comece apenas**.   Este cronograma iniciará todas as VMs dos alunos no horário especificado.  As VMs dos alunos não serão paradas até que um aluno pare a VM através do portal azure Lab Services ou ocorra um cronograma de parada.
- **Pare apenas.**  Este cronograma irá parar todas as VMs dos alunos no horário especificado.  

## <a name="template-virtual-machine"></a>Modelo de máquina virtual

Uma máquina virtual de modelo em um laboratório é uma imagem de máquina virtual base a partir da qual todas as máquinas virtuais dos usuários são criadas. Os criadores de treinadores/laboratórios configuram a máquina virtual do modelo e a configuram com o software que eles querem fornecer aos participantes de treinamento para fazer laboratórios. Quando você publica um modelo VM, o Azure Lab Services cria ou atualiza vMs de laboratório com base no modelo VM.

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
