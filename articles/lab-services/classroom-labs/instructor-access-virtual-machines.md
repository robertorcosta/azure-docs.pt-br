---
title: Instrutor acessando VMs nos Serviços de Laboratório do Azure
description: Este artigo mostra como os instrutores podem acessar suas VMs de alunos do ponto de vista do instrutor. Por exemplo, um assistente de ensino pode ser um instrutor para uma classe, mas um aluno para outras classes.
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
ms.date: 04/17/2020
ms.author: spelluru
ms.openlocfilehash: c048a2f159784a5bd38f185af29cec314b31824f
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641934"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>Acesse máquinas virtuais como um aluno a partir da visão do instrutor
Este artigo mostra como os instrutores podem acessar suas VMs para as aulas que frequentam como alunos. 

Aqui está um cenário onde esse recurso vai ajudar. Um assistente de ensino é um instrutor para uma classe, mas um aluno em outras classes. E, o instrutor de ensino quer ver e acessar as VMs dos alunos a partir da visão do instrutor que mostra os laboratórios que eles possuem. 

## <a name="access-vms-from-instructor-view"></a>Acesse VMs da visão do instrutor

1. Faça login no site do [Azure Lab Services](https://labs.azure.com). Você vê os laboratórios que você possui. Esses laboratórios podem ser laboratórios que você criou a si mesmo ou os laboratórios que admin atribuído a você como proprietário. Para obter mais informações, consulte [Como adicionar proprietários adicionais a um laboratório existente](how-to-add-user-lab-owner.md)
2. Para acessar as VMs para as aulas que você frequenta como estudante, selecione o ícone do computador no canto superior direito. Confirme se você vê VMs que você pode acessar como um estudante. No exemplo a seguir, o usuário é um assistente de ensino para o laboratório Python, mas um aluno do laboratório Java. Então, o usuário vê o VM do laboratório Java na lista de desímparabaixo. O usuário pode iniciar a VM e se conectar a ela. 
    
    ![Acesso às VMs dos estudantes](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como conectar-se a uma VM](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Conectar-se a uma VM usando RDP em um Mac](connect-virtual-machine-mac-rdp.md)
- [Use desktop remoto para máquinas virtuais Linux](how-to-use-remote-desktop-linux-student.md)
