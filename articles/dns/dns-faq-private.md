---
title: Perguntas frequentes sobre o DNS privado do Azure
description: In this article, learn frequently asked questions about Azure Private DNS
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: de63799400a10afc1930cd373df0c8dd86320f78
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212006"
---
# <a name="azure-private-dns-faq"></a>Perguntas frequentes sobre o DNS privado do Azure

The following are frequently asked questions about Azure private DNS.

## <a name="does-azure-dns-support-private-domains"></a>O DNS do Azure oferece suporte a domínios particulares?

Private domains are supported using the Azure Private DNS zones feature. Private DNS zones are resolvable only from within specified virtual networks. Para mais informações, consulte a [visão geral](private-dns-overview.md).

Para obter informações sobre outras opções de DNS interno no Azure, consulte [Resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Will Azure Private DNS zones work across Azure regions?

Sim. As zonas privadas são suportadas para resolução de DNS entre redes virtuais nas regiões do Azure. As zonas privadas funcionam mesmo sem espiar explicitamente as redes virtuais. All the virtual networks must be linked to the private DNS zone.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>A conectividade com a Internet de redes virtuais é necessária para zonas privadas?

Não. As zonas privadas funcionam junto com as redes virtuais. You use them to manage domains for virtual machines or other resources within and across virtual networks. A conectividade com a Internet não é necessária para a resolução de nomes.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>A mesma zona privada pode ser usada para várias redes virtuais para resolução?

Sim. You can link a private DNS zone with thousands of virtual networks. For more information, see [Azure DNS Limits](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Can a virtual network that belongs to a different subscription be linked to a private zone?

Sim. Você precisa ter permissão de operação de gravação nas redes virtuais e na zona DNS privado. A permissão de gravação pode ser concedida para várias funções do RBAC. For example, the Classic Network Contributor RBAC role has write permissions to virtual networks and Private DNS zones Contributor role has write permissions on the private DNS zones. Para obter mais informações sobre funções do RBAC, consulte [Controle de acesso baseado em função](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Will the automatically registered virtual machine DNS records in a private zone be automatically deleted when you delete the virtual machine?

Sim. If you delete a virtual machine within a linked virtual network with autoregistration enabled, the registered records are automatically deleted.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Can an automatically registered virtual machine record in a private zone from a linked virtual network be deleted manually?

Sim. Você pode regravar os registros DNS registrados automaticamente com um registro DNS criado manualmente na zona. A pergunta e resposta a seguir abordam este tópico.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>What happens when I try to manually create a new DNS record into a private zone that has the same hostname as an automatically registered existing virtual machine in a linked virtual network?

You try to manually create a new DNS record into a private zone that has the same hostname as an existing, automatically registered virtual machine in a linked virtual network. Quando você faz isso, o novo registro DNS sobrescreve o registro de máquina virtual registrado automaticamente. Se você tentar excluir esse registro DNS criado manualmente da zona novamente, a exclusão será bem-sucedida. O registro automático acontece novamente, contanto que a máquina virtual ainda exista e tenha um IP privado anexado a ela. O registro DNS é recriado automaticamente na zona.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>What happens when we unlink a linked virtual network from a private zone? Os registros da máquina virtual registrados automaticamente da rede virtual também serão removidos da zona?

Sim. To unlink a linked virtual network from a private zone, you update the DNS zone to remove the associated virtual network link. Nesse processo, os registros da máquina virtual que foram registrados automaticamente são removidos da zona.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>What happens when we delete a linked virtual network that's linked to a private zone? Do we have to manually update the private zone to unlink the virtual network as a linked virtual network from the zone?

Não. When you delete a linked virtual network without unlinking it from a private zone first, your deletion operation succeeds and the links to the DNS zone are automatically cleared.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Will DNS resolution by using the default FQDN (internal.cloudapp.net) still work even when a private zone (for example, private.contoso.com) is linked to a virtual network?

Sim. Private Zones don't replace the default Azure-provided internal.cloudapp.net zone. Independentemente de você confiar no internal.cloudapp.net fornecido pelo Azure ou em sua própria zona privada, use o FQDN da zona que deseja resolver.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>O sufixo DNS em máquinas virtuais em uma rede virtual vinculada será alterado para o da zona privada?

Não. O sufixo DNS nas máquinas virtuais em sua rede virtual vinculada permanece como o sufixo fornecido pelo Azure padrão ("*.internal.cloudapp.net"). Você pode alterar manualmente esse sufixo DNS em suas máquinas virtuais para aquela da zona privada.
For guidance on how to change this suffix refer to [Use dynamic DNS to register hostnames in your own DNS server](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>What are the usage limits for Azure DNS Private zones?

Refer to [Azure DNS limits](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits) for details on the usage limits for Azure DNS private zones.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Why don’t my existing private DNS zones show up in new portal experience?

If your existing private DNS zone were created using preview API, you must migrate these zones to new resource model. Private DNS zones created using preview API will not show up in new portal experience. See below for instructions on how to migrate to new resource model.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>How do I migrate my existing private DNS zones to the new model?

We strongly recommend that you migrate to the new resource model as soon as possible. Legacy resource model will be supported, however, further features will not be developed on top of this model. In future, we intend to deprecate it in favor of new resource model. For guidance on how to migrate your existing private DNS zones to new resource model see[migration guide for Azure DNS private zones](private-dns-migration-guide.md).

## <a name="next-steps"></a>Próximos passos

- [Learn more about Azure Private DNS](private-dns-overview.md)
