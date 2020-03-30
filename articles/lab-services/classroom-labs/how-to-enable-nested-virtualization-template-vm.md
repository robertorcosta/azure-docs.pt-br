---
title: Habilite a virtualização aninhada em um modelo VM no Azure Lab Services | Microsoft Docs
description: Aprenda a criar um modelo vm com várias VMs dentro.  Em outras palavras, habilite a virtualização aninhada em um modelo VM no Azure Lab Services.
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
ms.openlocfilehash: 59b32834369f76d39bb4a253dad4ec541e7ef999
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502016"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Habilite a virtualização aninhada em uma máquina virtual de modelo no Azure Lab Services

Atualmente, o Azure Lab Services permite configurar uma máquina virtual de modelo em um laboratório e disponibilizar uma única cópia para cada um de seus usuários. Se você é um professor que ensina networking, segurança ou aulas de TI, você pode precisar fornecer a cada um de seus alunos um ambiente no qual várias máquinas virtuais podem conversar entre si em uma rede.

A virtualização aninhada permite criar um ambiente multi-VM dentro da máquina virtual de modelo de um laboratório. A publicação do modelo fornecerá a cada usuário no laboratório uma máquina virtual configurada com várias VMs dentro dele.  Este artigo abrange como configurar a virtualização aninhada em uma máquina de modelos no Azure Lab Services.

## <a name="what-is-nested-virtualization"></a>O que é virtualização aninhada?

A virtualização aninhada permite criar máquinas virtuais dentro de uma máquina virtual. A virtualização aninhada é feita através do Hyper-V, e só está disponível em VMs do Windows.

Para obter mais informações sobre virtualização aninhada, consulte os seguintes artigos:

- [Virtualização aninhada no Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Como habilitar a virtualização aninhada em uma VM do Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Considerações

Antes de montar um laboratório com virtualização aninhada, aqui estão algumas coisas a levar em consideração.

- Ao criar um novo laboratório, selecione tamanhos **Médio (aninhado)** ou **Grande (virtualização aninhada)** para o tamanho da máquina virtual. Esses tamanhos de máquinas virtuais suportam a virtualização aninhada.
- Escolha um tamanho que proporcione um bom desempenho tanto para as máquinas virtuais host quanto para o cliente.  Lembre-se, ao usar a virtualização, o tamanho escolhido deve ser adequado não apenas para uma máquina, mas para o host, bem como para qualquer máquina cliente que deve ser executada simultaneamente.
- As máquinas virtuais clientes não terão acesso aos recursos do Azure, como servidores DNS na rede virtual do Azure.
- A máquina virtual host requer configuração para permitir que a máquina cliente tenha conectividade com a internet.
- As máquinas virtuais clientes são licenciadas como máquinas independentes. Consulte [o Microsoft Licensing](https://www.microsoft.com/licensing/default) para obter informações sobre licenciamento para sistemas e produtos de operação da Microsoft. Verifique os contratos de licenciamento de qualquer outro software que está sendo usado antes de configurar a máquina de modelo.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Habilitar a virtualização aninhada em uma VM modelo

Este artigo pressupõe que você criou uma conta de laboratório e laboratório.  Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte [o tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md). Para obter mais informações sobre como criar laboratório, consulte [configurar um tutorial de laboratório em sala de aula](tutorial-setup-classroom-lab.md).

>[!IMPORTANT]
>Selecione **Grande (virtualização aninhada)** ou **Média (virtualização aninhada)** para o tamanho da máquina virtual ao criar o laboratório.  A virtualização aninhada não funcionará de outra forma.  

Para conectar-se à máquina de modelos, consulte [criar e gerenciar um modelo de sala de aula](how-to-create-manage-template.md).

### <a name="using-script-to-enable-nested-virtualization"></a>Usando script para permitir virtualização aninhada

Para usar a configuração automatizada para virtualização aninhada com o Windows Server 2016 ou o Windows Server 2019, consulte [Habilitar a virtualização aninhada em uma máquina virtual de modelo no Azure Lab Services usando um script](how-to-enable-nested-virtualization-template-vm-using-script.md). Você usará scripts de [scripts Hyper-V](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) do Lab Services para instalar a função Hyper-V.  Os scripts também configurarão a rede para que as máquinas virtuais Hyper-V possam ter acesso à internet.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Usando ferramentas do Windows para permitir a virtualização aninhada

A virtualização aninhada de configuração para o Windows Server 2016 ou o Windows Server 2019 usando funções do Windows e ferramentas administrativas, consulte [Ativar a virtualização aninhada em uma máquina virtual de modelo no Azure Lab Services manualmente](how-to-enable-nested-virtualization-template-vm-ui.md).  As instruções também cobrirão como configurar a rede para que as máquinas virtuais Hyper-V possam ter acesso à internet.
