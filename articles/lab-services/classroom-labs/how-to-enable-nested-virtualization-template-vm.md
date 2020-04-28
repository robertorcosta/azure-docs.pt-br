---
title: Habilitar a virtualização aninhada em uma VM de modelo no Azure Lab Services | Microsoft Docs
description: Saiba como criar uma VM de modelo com várias VMs dentro do.  Em outras palavras, habilite a virtualização aninhada em uma VM de modelo no Azure Lab Services.
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
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 3c954c4689281838ea8c61c932cdcc3b74bac442
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184666"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Habilitar a virtualização aninhada em uma máquina virtual de modelo no Azure Lab Services

Atualmente, Azure Lab Services permite que você configure uma máquina virtual de modelo em um laboratório e disponibilize uma única cópia para cada um de seus usuários. Se você for um professor ensinando a rede, segurança ou classes de ti, talvez seja necessário fornecer a cada um de seus alunos um ambiente no qual várias máquinas virtuais possam se comunicar entre si em uma rede.

A virtualização aninhada permite que você crie um ambiente de várias VMs dentro da máquina virtual de modelo de um laboratório. A publicação do modelo fornecerá a cada usuário do laboratório uma máquina virtual configurada com várias VMs dentro dela.  Este artigo aborda como configurar a virtualização aninhada em um computador de modelo no Azure Lab Services.

## <a name="what-is-nested-virtualization"></a>O que é virtualização aninhada?

A virtualização aninhada permite que você crie máquinas virtuais em uma máquina virtual. A virtualização aninhada é feita por meio do Hyper-V e só está disponível em VMs do Windows.

Para obter mais informações sobre a virtualização aninhada, consulte os seguintes artigos:

- [Virtualização aninhada no Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Como habilitar a virtualização aninhada em uma VM do Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Considerações

Antes de configurar um laboratório com virtualização aninhada, aqui estão algumas coisas a serem levadas em consideração.

- Ao criar um novo laboratório, selecione tamanhos **médios (virtualização aninhada)** ou **grandes (virtualização aninhada)** para o tamanho da máquina virtual. Esses tamanhos de máquina virtual dão suporte à virtualização aninhada.
- Escolha um tamanho que fornecerá um bom desempenho para as máquinas virtuais do host e do cliente.  Lembre-se de que, ao usar a virtualização, o tamanho escolhido deve ser adequado para não apenas um computador, mas o host, bem como quaisquer computadores Hyper-V em execução simultânea.
- As máquinas virtuais do cliente não terão acesso aos recursos do Azure, como servidores DNS, na rede virtual do Azure.
- A máquina virtual do host requer a instalação para permitir que o computador cliente tenha conectividade com a Internet.
- As máquinas virtuais do cliente são licenciadas como máquinas independentes. Consulte [Licenciamento da Microsoft](https://www.microsoft.com/licensing/default) para obter informações sobre licenciamento para produtos e sistemas de operações da Microsoft. Verifique os contratos de licenciamento para qualquer outro software que esteja sendo usado antes de configurar o computador de modelo.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Habilitar a virtualização aninhada em uma VM modelo

Este artigo pressupõe que você criou uma conta de laboratório e um laboratório.  Para obter mais informações sobre como criar uma nova conta de laboratório, consulte [tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md). Para obter mais informações sobre como criar um laboratório, consulte [configurar um tutorial de laboratório de sala de aula](tutorial-setup-classroom-lab.md).

>[!IMPORTANT]
>Selecione **grande (virtualização aninhada)** ou **média (virtualização aninhada)** para o tamanho da máquina virtual ao criar o laboratório.  Caso contrário, a virtualização aninhada não funcionará.  

Para se conectar ao computador de modelo, consulte [criar e gerenciar um modelo de sala de aula](how-to-create-manage-template.md).

Para habilitar a virtualização aninhada, há algumas tarefas a serem realizadas.  

- **Habilite a função Hyper-V**. A função Hyper-V deve ser habilitada para a criação e execução de máquinas virtuais do Hyper-V na máquina virtual dos serviços de laboratório.
- **Habilite o DHCP**.  Quando a máquina virtual dos serviços de laboratório tiver a função DHCP habilitada, as máquinas virtuais do Hyper-V poderão ser automaticamente atribuídas a um endereço IP.
- **Criar rede NAT para VMs do Hyper-V**.  A rede NAT é configurada para permitir que as máquinas virtuais do Hyper-V tenham acesso à Internet.  As máquinas virtuais do Hyper-V podem se comunicar entre si.

>[!NOTE]
>A rede NAT criada na VM do Lab Services permitirá que uma VM do Hyper-V acesse a Internet e outras VMs do Hyper-V na mesma VM dos serviços de laboratório.  A VM do Hyper-V não poderá acessar recursos do Azure, como servidores DNS, na rede virtual do Azure.

A realização das tarefas listadas acima pode ser feita usando um script ou usando as ferramentas do Windows.  Leia as seções abaixo para obter mais detalhes.

### <a name="using-script-to-enable-nested-virtualization"></a>Usando o script para habilitar a virtualização aninhada

Para usar a configuração automatizada para a virtualização aninhada com o Windows Server 2016 ou o Windows Server 2019, consulte [habilitar a virtualização aninhada em uma máquina virtual de modelo no Azure Lab Services usando um script](how-to-enable-nested-virtualization-template-vm-using-script.md). Você usará scripts dos [scripts do Hyper-v dos serviços de laboratório](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) para instalar a função do Hyper-v.  Os scripts também irão configurar a rede para que as máquinas virtuais do Hyper-V possam ter acesso à Internet.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Usando as ferramentas do Windows para habilitar a virtualização aninhada

A configuração de virtualização aninhada para Windows Server 2016 ou Windows Server 2019 usando funções do Windows e ferramentas administrativas, consulte [habilitar a virtualização aninhada em uma máquina virtual de modelo em Azure Lab Services manualmente](how-to-enable-nested-virtualization-template-vm-ui.md).  As instruções também abordarão como configurar a rede para que as máquinas virtuais do Hyper-V possam ter acesso à Internet.
