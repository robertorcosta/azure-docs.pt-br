---
title: Guia de início rápido de soluções VMware do Azure (AVS)-criar VMs VMware no Azure
description: Saiba como criar e configurar VMs VMware de portal do Azure usando as soluções VMware do Azure (AVS)
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 575d59d60ebfcfdbe4d234d608e8d988006773c2
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019546"
---
# <a name="quickstart---create-vmware-vms-on-azure"></a>Início rápido-criar VMs VMware no Azure

Para criar uma máquina virtual no portal do Azure, use modelos de máquina virtual que o administrador da AVS habilitou para sua assinatura. Os modelos de VM são encontrados na infra-estrutura do VMware.

## <a name="avs-vm-creation-on-azure-requires-a-vm-template"></a>A criação de VM de AVS no Azure requer um modelo de VM

Crie uma máquina virtual em sua nuvem privada da AVS na interface do usuário do vCenter. Para criar um modelo, siga as instruções em [clonar uma máquina virtual para um modelo no cliente Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Armazene o modelo de VM em seu vCenter de nuvem privada da AVS.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Criar uma máquina virtual no portal do Azure

1. Selecione **Todos os serviços**.

2. Pesquise **máquinas virtuais de AVS**.

3. Clique em **Adicionar**.

    ![Criar máquina virtual AVS](media/create-cloudsimple-virtual-machine.png)

4. Insira informações básicas e clique em **Avançar: tamanho**.

    ![Criar máquina virtual AVS-noções básicas](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | Description |
    | ------------ | ------------- |
    | Subscription | Assinatura do Azure associada à sua nuvem privada de AVS. |
    | Grupo de recursos | Grupo de recursos ao qual a VM será atribuída. Selecione um grupo existente ou crie um novo. |
    | Nome | Nome para identificar a VM.  |
    | Local | Região do Azure na qual essa VM está hospedada.  |
    | Nuvem privada de AVS | A nuvem privada de AVS em que você deseja criar a máquina virtual. |
    | Pool de recursos | Pool de recursos mapeados para a VM. Selecione entre os pools de recursos disponíveis. |
    | Modelo vSphere | modelo de vSphere para a VM.  |
    | Nome de usuário | Nome de usuário do administrador da VM (para modelos do Windows).|
    | Senha |  Senha para o administrador da VM (para modelos do Windows). |
    | Confirmar senha | Confirme a senha. |

5. Selecione o número de núcleos e a capacidade de memória para a VM e clique em **Avançar: configurações**. Marque a caixa de seleção se desejar expor a virtualização completa da CPU para o sistema operacional convidado. Os aplicativos que exigem a virtualização de hardware podem ser executados em máquinas virtuais sem conversão binária ou de paravirtualização. Saiba mais no artigo da VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">Expor virtualização assistida de hardware do VMware</a>.

    ![Criar máquina virtual AVS-tamanho](media/create-cloudsimple-virtual-machine-size.png)

6. Configure os discos e os adaptadores de rede conforme descrito nas tabelas a seguir e clique em **revisar + criar**.

    ![Criar máquina virtual AVS-configurações](media/create-cloudsimple-virtual-machine-configurations.png)

    Para interfaces de rede, clique em **Adicionar interface de rede** e defina as configurações a seguir.

    | Control | Description |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar o adaptador.  |
    | Rede | Selecione na lista de grupos de portas distribuídas configuradas na sua vSphere de nuvem privada de AVS. |
    | Placas | Selecione um adaptador vSphere na lista de tipos disponíveis configurados para a VM. Para obter mais informações, consulte o artigo da base de dados de conhecimento do VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">escolhendo um adaptador de rede para sua máquina virtual</a>. |
    | Ligar na inicialização | Escolha se quer habilitar o hardware da NIC quando a VM for inicializada. O padrão é **Habilitar**. |

    Para discos, clique em **adicionar disco** e defina as configurações a seguir.

    | Item | Description |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar o disco. |
    | Tamanho | Selecione um dos tamanhos disponíveis. |
    | Controlador SCSI | Selecione um controlador SCSI para o disco. |
    | Mode | Determina como o disco participa em instantâneos. Escolha uma destas opções: <br> – Persistente independente: todos os dados gravados no disco são gravados permanentemente.<br> – Não persistente independente: as alterações gravadas no disco são descartadas quando você desliga ou redefine a máquina virtual. O modo independente não persistente permite que você sempre reinicie a máquina virtual no mesmo estado. Saiba mais na <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">documentação da VMware</a>.

7. Quando a validação for concluída, examine as configurações e clique em **criar**. Para fazer alterações, clique nas guias na parte superior ou clique em.

    ![Criar máquina virtual AVS-revisão](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Próximos passos

* [Exibir lista de máquinas virtuais de AVS](azure-create-vm.md#view-list-of-avs-virtual-machines)
* [Gerenciar a máquina virtual AVS do Azure](azure-manage-vm.md)
