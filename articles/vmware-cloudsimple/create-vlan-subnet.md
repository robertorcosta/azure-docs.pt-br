---
title: Criar VLANs/sub-redes
description: Soluções VMware do Azure (AVS) – descreve como criar e gerenciar VLANs/sub-redes para suas nuvens de AVS privado e aplicar regras de firewall.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 249c48500dbcd75f62f856b3345b3a2c02502d1a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024765"
---
# <a name="create-and-manage-vlanssubnets-for-your-avs-private-clouds"></a>Criar e gerenciar VLANs/sub-redes para suas nuvens privadas da AVS

Abra a guia VLANs/sub-redes na página rede para criar e gerenciar VLANs/sub-redes para suas nuvens privadas da AVS. Depois de criar uma VLAN/sub-rede, você pode aplicar regras de firewall.

## <a name="create-a-vlansubnet"></a>Criar uma VLAN/sub-rede

1. [Acesse o portal da AVS](access-cloudsimple-portal.md) e selecione **rede** no menu lateral.
2. Selecione **VLANs/sub-redes**.
3. Clique em **criar VLAN/sub-rede**.

    ![Página VLAN/sub-rede](media/vlan-subnet-page.png)

4. Selecione a nuvem privada da AVS para a nova VLAN/sub-rede.
5. Insira uma ID de VLAN.
6. Insira o nome da sub-rede.
7. Para habilitar o roteamento na VLAN (sub-rede), especifique o intervalo de CIDR da sub-rede. Certifique-se de que o intervalo CIDR não se sobreponha a nenhuma de suas sub-redes locais, sub-redes do Azure ou sub-rede de gateway.
8. Clique em **Enviar**.

    ![Criar VLAN/sub-rede](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> Há uma cota de 30 VLANs por nuvem privada. Esses limites podem ser aumentados [entrando em contato com o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>Usar informações de VLAN para configurar um grupo de portas distribuídas no vSphere

Para criar um grupo de portas distribuídas no vSphere, siga as instruções no tópico "adicionar um grupo de portas distribuídas" do VMware no <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">Guia de rede do vSphere</a>. Ao configurar o grupo de portas distribuídas, forneça as informações de VLAN da configuração de AVS.

![Grupo de portas distribuídas](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Selecionar uma tabela de firewall

As tabelas de firewall e as regras associadas são definidas na página **tabelas de firewall > de rede** . Para selecionar a tabela de firewall a ser aplicada à VLAN/sub-rede para uma nuvem privada de AVS, selecione a VLAN/sub-rede clique em **anexo de tabela de firewall** na página **VLANs/sub-redes** . Consulte [tabelas de firewall](firewall.md) para obter instruções sobre como configurar tabelas de firewall e definir regras.

![Link de tabela de firewall](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Uma sub-rede pode ser associada a uma tabela de firewall. Uma tabela de firewall pode ser associada a várias sub-redes.

## <a name="edit-a-vlansubnet"></a>Editar uma VLAN/sub-rede

Para editar as configurações de uma VLAN/sub-rede, selecione-a na página **VLANs/sub-redes** e clique no ícone **Editar** . Faça alterações e clique em **submet**.

## <a name="delete-a-vlansubnet"></a>Excluir uma VLAN/sub-rede

Para excluir uma VLAN/sub-rede, selecione-a na página **VLANs/sub-redes** e clique no ícone **excluir** . Clique em **excluir** para confirmar.
