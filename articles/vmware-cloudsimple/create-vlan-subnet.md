---
title: Criar VLANs/sub-redes - Solução Azure VMware por CloudSimple
description: Azure VMware Solutions by CloudSimple - Descreve como criar e gerenciar VLANs/sub-redes para suas Nuvens Privadas e, em seguida, aplicar regras de firewall.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0160513eb9abca54adbc3819b982348dc202c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565988"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>Crie e gerencie VLANs/sub-redes para suas nuvens privadas

Abra a guia VLANs/Subnets na página Rede para criar e gerenciar VLANs/sub-redes para suas Nuvens Privadas. Depois de criar uma VLAN/sub-rede, você pode aplicar regras de firewall.

## <a name="create-a-vlansubnet"></a>Criar uma VLAN/sub-rede

1. [Acesse o portal CloudSimple](access-cloudsimple-portal.md) e selecione **Rede** no menu lateral.
2. Selecione **VLANs/sub-redes**.
3. Clique **em Criar VLAN/Sub-net**.

    ![Página VLAN/sub-rede](media/vlan-subnet-page.png)

4. Selecione a Nuvem Privada para a nova VLAN/sub-rede.
5. Digite um ID VLAN.
6. Digite o nome da sub-rede.
7. Para habilitar o roteamento na VLAN (sub-rede), especifique o intervalo CIDR da sub-rede. Certifique-se de que o intervalo CIDR não se sobreponha a nenhuma de suas sub-redes no local, sub-redes Azure ou sub-rede de gateway.
8. Clique em **Enviar**.

    ![Criar VLAN/sub-rede](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> Há uma cota de 30 VLANs por nuvem privada. Esses limites podem ser aumentados [entrando em contato com o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>Use informações de VLAN para configurar um grupo de portas distribuídas no vSphere

Para criar um grupo de porta distribuída no vSphere, siga as instruções no tópico VMware 'Adicionar um grupo de porta distribuída' no <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">guia de rede vSphere</a>. Ao configurar o grupo de portas distribuídas, forneça as informações da VLAN a partir da configuração CloudSimple.

![Grupo Portuário Distribuído](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Selecione uma tabela de firewall

As tabelas de firewall e as regras associadas são definidas na página **tabelas > de de firewall** de rede. Para selecionar a tabela de firewall para aplicar à VLAN/sub-rede para uma Nuvem Privada, selecione o anexo da tabela VLAN/sub-rede com base em **Firewall** na página **VLANs/Subnets.** Consulte [tabelas de firewall](firewall.md) para obter instruções sobre como configurar tabelas de firewall e definir regras.

![Link de tabela de firewall](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Uma sub-rede pode ser associada a uma tabela de firewall. Uma tabela de firewall pode ser associada a várias sub-redes.

## <a name="edit-a-vlansubnet"></a>Editar uma VLAN/sub-rede

Para editar as configurações de uma VLAN/Sub-rede, selecione-a na página **VLANs/Subnets** e clique no ícone **Editar.** Faça alterações e clique **em Submet**.

## <a name="delete-a-vlansubnet"></a>Exclua uma VLAN/sub-rede

Para excluir uma VLAN/Sub-rede, selecione-a na página **VLANs/Subnets** e clique no ícone **Excluir.** Clique **em Excluir** para confirmar.
