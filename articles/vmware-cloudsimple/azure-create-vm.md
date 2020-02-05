---
title: Soluções VMware do Azure (AVS) – criar uma máquina virtual no Azure com modelos de VM
description: Descreve como criar máquinas virtuais no Azure usando modelos de VM na infraestrutura do VMware para sua nuvem privada de AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 533aaab13f1b957e709f66b23b511fc199ee0285
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015194"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Criar uma máquina virtual no Azure usando modelos de VM na infra-estrutura do VMware

Você pode criar uma máquina virtual no portal do Azure usando modelos de VM na infra-estrutura do VMware que o administrador da AVS tem habilitado para sua assinatura.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-avs-virtual-machine"></a>Criar máquina virtual AVS

1. Selecione **Todos os serviços**.

2. Pesquise **máquinas virtuais de AVS**.

3. Clique em **Adicionar**.

    ![Criar máquina virtual AVS](media/create-cloudsimple-virtual-machine.png)

4. Inserir informações básicas clique em **Avançar: tamanho**.

    > [!NOTE]
    > A criação de máquina virtual AVS no Azure requer um modelo de VM. Esse modelo de VM deve existir em seu vCenter de nuvem privada. Crie uma máquina virtual em sua nuvem privada da interface do usuário do vCenter com o sistema operacional e as configurações desejadas. Usando as instruções em [clonar uma máquina virtual para um modelo no cliente Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html), crie um modelo.

    ![Criar máquina virtual AVS-noções básicas](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | Description |
    | ------------ | ------------- |
    | Subscription | Assinatura do Azure associada à sua nuvem privada.  |
    | Grupo de recursos | Grupo de recursos ao qual a VM será atribuída. Selecione um grupo existente ou crie um novo. |
    | Nome | Nome para identificar a VM.  |
    | Local | Região do Azure na qual essa VM está hospedada.  |
    | Nuvem privada | A nuvem privada de AVS em que você deseja criar a máquina virtual. |
    | Pool de recursos | Pool de recursos mapeados para a VM. Selecione entre os pools de recursos disponíveis. |
    | Modelo vSphere | modelo de vSphere para a VM.  |
    | Nome de usuário | Nome de usuário do administrador da VM (para modelos do Windows)|
    | Senha <br>Confirmar senha | Senha para o administrador da VM (para modelos do Windows).  |

5. Selecione o número de núcleos e a capacidade de memória para a VM e clique em **Avançar: configurações**. Marque a caixa de seleção se você quiser expor a virtualização de CPU completa para o sistema operacional convidado para que os aplicativos que exigem a virtualização de hardware possam ser executados em máquinas virtuais sem conversão binária ou paravirtualização. Saiba mais no artigo da VMware [Expor virtualização assistida de hardware do VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

    ![Criar máquina virtual AVS-tamanho](media/create-cloudsimple-virtual-machine-size.png)

6. Configure os discos e os adaptadores de rede conforme descrito nas tabelas a seguir e clique em **revisar + criar**.

    ![Criar máquina virtual AVS-configurações](media/create-cloudsimple-virtual-machine-configurations.png)

    Para interfaces de rede, clique em **Adicionar interface de rede** e defina as configurações a seguir.

    | Control | Description |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar o adaptador.  |
    | Rede | Selecione na lista de grupos de portas distribuídas configuradas em sua nuvem privada vSphere.  |
    | Placas | Selecione um adaptador vSphere na lista de tipos disponíveis configurados para a VM. Para obter mais informações, consulte o artigo da base de dados de conhecimento do VMware [escolhendo um adaptador de rede para sua máquina virtual](https://kb.vmware.com/s/article/1001805). |
    | Ligar na inicialização | Escolha se quer habilitar o hardware da NIC quando a VM for inicializada. O padrão é **Habilitar**. |

    Para discos, clique em **adicionar disco** e defina as configurações a seguir.

    | Item | Description |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar o disco.  |
    | Tamanho | Selecione um dos tamanhos disponíveis.  |
    | Controlador SCSI | Selecione um controlador SCSI para o disco.  |
    | Mode | Determina como o disco participa em instantâneos. Escolha uma destas opções: <br> – Persistente independente: todos os dados gravados no disco são gravados permanentemente.<br> – Não persistente independente: as alterações gravadas no disco são descartadas quando você desliga ou redefine a máquina virtual. O modo independente não persistente permite que você sempre reinicie a máquina virtual no mesmo estado. Saiba mais na [documentação da VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. Após a conclusão da validação, examine as configurações e clique em **criar**. Para fazer alterações, clique nas guias na parte superior ou clique em.

    ![Criar máquina virtual AVS-revisão](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-avs-virtual-machines"></a>Exibir lista de máquinas virtuais de AVS

1. Selecione **Todos os serviços**.

2. Pesquise **máquinas virtuais de AVS**.

3. Selecione o em que sua nuvem privada foi criada.

    ![Lista de máquinas virtuais de AVS](media/list-cloudsimple-virtual-machines.png)

A lista de máquinas virtuais de AVS inclui máquinas virtuais criadas a partir de portal do Azure.  As máquinas virtuais criadas no vCenter de nuvem privada no pool de recursos do vCenter mapeado serão mostradas na lista.  
