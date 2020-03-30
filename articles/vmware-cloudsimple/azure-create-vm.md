---
title: Solução Azure VMware by CloudSimple - Crie uma máquina virtual no Azure com modelos de VM
description: Descreve como criar máquinas virtuais no Azure usando modelos de VM na infra-estrutura VMware para sua CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee3029de9826aee17dc76d0e69f08b3c1068423b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244687"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Crie uma máquina virtual no Azure usando modelos de VM na infra-estrutura VMware

Você pode criar uma máquina virtual no portal Azure usando modelos de VM na infra-estrutura VMware que o administrador do CloudSimple habilitou para sua assinatura.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça login no [portal Azure](https://portal.azure.com).

## <a name="create-cloudsimple-virtual-machine"></a>Criar máquina virtual CloudSimple

1. Selecione **Todos os serviços**.

2. Pesquise **Máquinas Virtuais do CloudSimple**.

3. Clique em **Adicionar**.

    ![Criar máquina virtual CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Digite informações básicas clique **em Next:Size**.

    > [!NOTE]
    > CloudA Criação de máquina virtual no Azure requer um modelo de VM.  Este modelo vM deve existir no seu vCenter private cloud.  Crie uma máquina virtual em sua Nuvem Privada a partir da ui vCenter com o sistema operacional e as configurações desejadas.  Usando instruções em [Clonar uma máquina virtual para um modelo no vSphere Web Client,](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html)crie um modelo.

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
    | Nome de usuário | Nome de usuário do administrador de VM (para modelos do Windows)|
    | Senha <br>Confirmar senha | Senha para o administrador de VM (para modelos do Windows).  |

5. Selecione o número de núcleos e capacidade de memória da VM e clique **em Next:Configurations**. Selecione a caixa de seleção se quiser expor a virtualização completa da CPU ao sistema operacional convidado para que aplicativos que requerem virtualização de hardware possam ser executados em máquinas virtuais sem tradução binária ou paravirtualização. Saiba mais no artigo da VMware [Expor virtualização assistida de hardware do VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

    ![Criar máquina virtual CloudSimple - tamanho](media/create-cloudsimple-virtual-machine-size.png)

6. Configure interfaces de rede e discos conforme descrito nas tabelas a seguir e clique em **Revisar + criar**.

    ![Criar máquina virtual CloudSimple - configurações](media/create-cloudsimple-virtual-machine-configurations.png)

    Para interfaces de rede, clique **em Adicionar interface de rede** e configure as seguintes configurações.

    | Control | Descrição |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar o adaptador.  |
    | Rede | Selecione na lista do grupo de portas distribuídas configurados em seu private cloud vSphere.  |
    | Adaptador | Selecione um adaptador vSphere na lista de tipos disponíveis configurados para a VM. Para obter mais informações, consulte o artigo base de conhecimento da VMware [Escolhendo um adaptador de rede para sua máquina virtual](https://kb.vmware.com/s/article/1001805). |
    | Ligar na inicialização | Escolha se quer habilitar o hardware da NIC quando a VM for inicializada. O padrão é **Habilitar**. |

    Para discos, clique **em Adicionar disco** e configure as seguintes configurações.

    | Item | Descrição |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar o disco.  |
    | Tamanho | Selecione um dos tamanhos disponíveis.  |
    | Controlador SCSI | Selecione um controlador SCSI para o disco.  |
    | Mode | Determina como o disco participa de instantâneos. Escolha uma destas opções: <br> - Persistência independente: Todos os dados gravados no disco são gravados permanentemente.<br> - Independente não persistente: As alterações escritas no disco são descartadas quando você desliga ou reinicia a máquina virtual.  O modo independente não persistente permite que você sempre reinicie a máquina virtual no mesmo estado. Saiba mais na [documentação da VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. Uma vez concluída a validação, revise as configurações e clique **em Criar**. Para fazer qualquer alteração, clique nas guias na parte superior ou clique.

    ![Criar máquina virtual CloudSimple - revisão](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>Exibir lista de máquinas virtuais do CloudSimple

1. Selecione **Todos os serviços**.

2. Pesquise **Máquinas Virtuais do CloudSimple**.

3. Selecione o em que sua Nuvem Privada foi criada.

    ![Lista de máquinas virtuais CloudSimple](media/list-cloudsimple-virtual-machines.png)

Lista de máquinas virtuais CloudSimple inclui máquinas virtuais criadas a partir do portal Azure.  As máquinas virtuais criadas no Private Cloud vCenter no pool de recursos vCenter mapeado serão mostrados na lista.  
