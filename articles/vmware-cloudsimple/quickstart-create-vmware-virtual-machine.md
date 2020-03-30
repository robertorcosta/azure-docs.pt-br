---
title: 'Quickstart: Consuma VMs VMware no Azure'
titleSuffix: Azure VMware Solution by CloudSimple
description: Saiba como configurar e consumir VMs VMware do portal Azure usando a solução Azure VMware pela CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ab613c251bc43a025e0381046805ec998a04227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019546"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Quickstart - Consumir VMs VMware no Azure

Para criar uma máquina virtual no portal Azure, use modelos de máquinas virtuais que o administrador do CloudSimple habilitou para sua assinatura. Os modelos de VM são encontrados na infra-estrutura VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Criação de VM cloudSimple no Azure requer um modelo de VM

Crie uma máquina virtual em sua Nuvem Privada a partir da ui vCenter. Para criar um modelo, siga as instruções em [Clonar uma máquina virtual para um modelo no vSphere Web Client](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Armazene o modelo vM em seu vCenter private cloud.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Criar uma máquina virtual no portal do Azure

1. Selecione **Todos os serviços**.

2. Pesquise **Máquinas Virtuais do CloudSimple**.

3. Clique em **Adicionar**.

    ![Criar máquina virtual CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Digite as informações básicas e clique **em Next:Size**.

    ![Criar máquina virtual CloudSimple - noções básicas](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | Descrição |
    | ------------ | ------------- |
    | Subscription | Assinatura do Azure associada ao seu Private Cloud.  |
    | Grupo de recursos | Grupo de recursos ao qual a VM será atribuída. Selecione um grupo existente ou crie um novo. |
    | Nome | Nome para identificar a VM.  |
    | Location | Região azure em que esta VM está hospedada.  |
    | Nuvem privada | CloudSimple Private Cloud onde você deseja criar a máquina virtual. |
    | Pool de recursos | Pool de recursos mapeado para a VM. Selecione entre os pools de recursos disponíveis. |
    | Modelo do vSphere | modelo vSphere para o VM.  |
    | Nome de usuário | Nome de usuário do administrador vm (para modelos do Windows).|
    | Senha |  Senha para o administrador de VM (para modelos do Windows). |
    | Confirmar senha | Confirme a senha. |

5. Selecione o número de núcleos e capacidade de memória da VM e clique **em Next:Configurations**. Selecione a caixa de seleção se quiser expor a virtualização completa da CPU ao sistema operacional convidado. Os aplicativos que exigem a virtualização de hardware podem ser executados em máquinas virtuais sem conversão binária ou de paravirtualização. Saiba mais no artigo da VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">Expor virtualização assistida de hardware do VMware</a>.

    ![Criar máquina virtual CloudSimple - tamanho](media/create-cloudsimple-virtual-machine-size.png)

6. Configure interfaces de rede e discos conforme descrito nas tabelas a seguir e clique em **Revisar + criar**.

    ![Criar máquina virtual CloudSimple - configurações](media/create-cloudsimple-virtual-machine-configurations.png)

    Para interfaces de rede, clique **em Adicionar interface de rede** e configure as seguintes configurações.

    | Control | Descrição |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar o adaptador.  |
    | Rede | Selecione na lista do grupo de portas distribuídas configurados em seu private cloud vSphere.  |
    | Adaptador | Selecione um adaptador vSphere na lista de tipos disponíveis configurados para a VM. Para obter mais informações, consulte o artigo base de conhecimento da VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">Escolhendo um adaptador de rede para sua máquina virtual</a>. |
    | Ligar na inicialização | Escolha se quer habilitar o hardware da NIC quando a VM for inicializada. O padrão é **Habilitar**. |

    Para discos, clique **em Adicionar disco** e configure as seguintes configurações.

    | Item | Descrição |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar o disco.  |
    | Tamanho | Selecione um dos tamanhos disponíveis.  |
    | Controlador SCSI | Selecione um controlador SCSI para o disco.  |
    | Mode | Determina como o disco participa de instantâneos. Escolha uma destas opções: <br> - Persistência independente: Todos os dados gravados no disco são gravados permanentemente.<br> - Independente não persistente: As alterações escritas no disco são descartadas quando você desliga ou reinicia a máquina virtual.  O modo independente não persistente permite que você sempre reinicie a máquina virtual no mesmo estado. Saiba mais na <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">documentação da VMware</a>.

7. Quando a validação for concluída, revise as configurações e clique **em Criar**. Para fazer qualquer alteração, clique nas guias na parte superior ou clique.

    ![Criar máquina virtual CloudSimple - revisão](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Próximas etapas

* [Exibir lista de máquinas virtuais do CloudSimple](azure-create-vm.md#view-list-of-cloudsimple-virtual-machines)
* [Gerencie a máquina virtual CloudSimple do Azure](azure-manage-vm.md)
