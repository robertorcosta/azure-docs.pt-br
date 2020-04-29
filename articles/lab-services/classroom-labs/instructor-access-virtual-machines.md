---
title: Instrutor acessando VMs no Azure Lab Services
description: Este artigo mostra como os instrutores podem acessar suas VMs de aluno na exibição do instrutor. Por exemplo, um assistente de ensino pode ser um instrutor para uma classe, mas um aluno para outras classes.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641934"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>Acesse máquinas virtuais como um aluno da exibição do instrutor
Este artigo mostra como os instrutores podem acessar suas VMs para classes que participam como alunos. 

Aqui está um cenário em que esse recurso ajudará. Um assistente de ensino é um instrutor de uma classe, mas um aluno em outras classes. E o instrutor de ensino quer exibir e acessar as VMs dos alunos na exibição do instrutor que mostra os laboratórios que eles possuem. 

## <a name="access-vms-from-instructor-view"></a>Acessar VMs do modo de exibição do instrutor

1. Entre no site do [Azure Lab Services](https://labs.azure.com). Você vê os laboratórios que possui. Esses laboratórios podem ser laboratórios que você mesmo criou ou os laboratórios que o administrador atribuiu a você como proprietário. Para obter mais informações, consulte [como adicionar proprietários adicionais a um laboratório existente](how-to-add-user-lab-owner.md)
2. Para acessar as VMs de classes que você participa como aluno, selecione o ícone de computador no canto superior direito. Confirme que você vê as VMs que você pode acessar como um aluno. No exemplo a seguir, o usuário é um assistente de ensino para o laboratório do Python, mas um aluno do laboratório do Java. Portanto, o usuário vê a VM do laboratório Java na lista suspensa. O usuário pode iniciar a VM e conectar-se a ela. 
    
    ![Acessar VMs de alunos](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como conectar-se a uma VM](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Conectar-se a uma VM usando RDP em um Mac](connect-virtual-machine-mac-rdp.md)
- [Usar a área de trabalho remota para máquinas virtuais do Linux](how-to-use-remote-desktop-linux-student.md)
