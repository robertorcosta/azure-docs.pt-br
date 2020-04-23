---
title: Gerencie a solução Azure VMware por CloudSimple Private Cloud
description: Descreve os recursos disponíveis para gerenciar seus recursos e atividades da CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 13496a18f4c99b69a5b8095caf5b74a04d1bba88
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869294"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Gerencie recursos e atividades da Nuvem Privada

Nuvens privadas são gerenciadas a partir do portal CloudSimple.  Verifique o status, recursos disponíveis, atividade na nuvem privada e outras configurações do portal CloudSimple.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Acessar o portal da CloudSimple

Acesse o [portal CloudSimple](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Ver a lista de nuvens privadas

A guia **Nuvens Privadas** na página **Recursos** lista todas as Nuvens Privadas em sua assinatura. As informações incluem o nome, o número de clusters vSphere, a localização, o estado atual da nuvem privada e, informações de recursos.

![Página da Nuvem Privada](media/manage-private-cloud.png)

Selecione uma Nuvem Privada para obter informações e ações adicionais.

## <a name="private-cloud-summary"></a>Resumo da Nuvem Privada

Veja um resumo completo da Nuvem Privada selecionada.  A página resumo inclui os servidores DNS implantados na Nuvem Privada.  Você pode configurar o encaminhamento de DNS a partir de servidores DNS no local para seus servidores DNS privados em nuvem.  Para obter mais informações sobre o encaminhamento de DNS, consulte [Configurar dNS para resolução de nomes para private cloud vCenter a partir de locais](https://docs.microsoft.com/azure/vmware-cloudsimple/on-premises-dns-setup/).

![Resumo privado da nuvem](media/private-cloud-summary.png)

### <a name="available-actions"></a>Ações disponíveis

* [Inicie o cliente vSphere](https://docs.microsoft.com/azure/vmware-cloudsimple/vcenter-access). Acesse o vCenter para esta Nuvem Privada.
* [Comprar nós](create-nodes.md). Adicione nomes a esta Nuvem Privada.
* [Expandir](expand-private-cloud.md). Adicione nomes a esta Nuvem Privada.
* **Atualizar**. Atualize as informações nesta página.
* **Excluir**. Você pode excluir a Nuvem Privada a qualquer momento. **Antes de excluir, certifique-se de que você fez backup de todos os sistemas e dados.** Excluir uma nuvem privada exclui todas as VMs, configuração do vCenter e dados. Clique em **Excluir** na seção resumo da nuvem privada selecionada. Após a exclusão, todos os dados da Nuvem Privada são apagados em um processo de eliminação seguro e altamente compatível.
* [Alterar privilégios vSphere](escalate-private-cloud-privileges.md).  Aumente seus privilégios nesta Nuvem Privada.

## <a name="private-cloud-vlanssubnets"></a>VLANS/sub-redes de nuvem privada

Veja a lista de VLANs/sub-redes definidas para a Nuvem Privada selecionada.  A lista inclui os VLANs/sub-redes de gerenciamento criados quando a nuvem privada foi criada.

![Nuvem Privada - VLANs/Subnets](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Ações disponíveis

* [Adicionar VLANS/Subnets](https://docs.microsoft.com/azure/vmware-cloudsimple/create-vlan-subnet/). Adicione um VLAN/subconjunto a esta nuvem privada.

Selecione uma VLAN/Sub-rede para seguir as ações
* [Anexar tabela de firewall](https://docs.microsoft.com/azure/vmware-cloudsimple/firewall/). Anexar uma tabela de firewall a esta Nuvem Privada.
* **Editar**
* **Excluir** (apenas VLANs/Subnets definidos pelo usuário)

## <a name="private-cloud-activity"></a>Atividade de Nuvem Privada

Veja as seguintes informações para a Nuvem Privada selecionada.  As informações de atividade são uma lista filtrada de todas as atividades para a Nuvem Privada selecionada.  Esta página mostra até 25 atividades recentes.

* Alertas recentes
* Eventos recentes
* Tarefas recentes
* Auditoria recente

![Nuvem Privada - Atividade](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Racks de nuvem

Racks de nuvem são os blocos de construção da sua Nuvem Privada. Cada rack fornece uma unidade de capacidade. O CloudSimple configura automaticamente racks de nuvem com base em suas seleções ao criar ou expandir uma Nuvem Privada.  Veja a lista completa de racks de nuvem, incluindo o Private Cloud ao que cada um é atribuído.

![Nuvem Privada - Racks de Nuvem](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere Management Network

Lista de recursos de gerenciamento vMware e máquinas virtuais que estão atualmente configuradas na Nuvem Privada. As informações incluem a versão do software, o nome de domínio totalmente qualificado (FQDN) e o endereço IP dos recursos.

![Private Cloud - vSphere Management Network](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Próximas etapas

* [Consumir VMs do VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)