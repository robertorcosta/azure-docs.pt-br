---
title: Perguntas frequentes sobre o DNS privado do Azure
description: Neste artigo, aprenda perguntas frequentes sobre o Azure Private DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 4de585a965cfefa6399b0c0929a8f732d0712617
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939416"
---
# <a name="azure-private-dns-faq"></a>Perguntas frequentes sobre o DNS privado do Azure

As perguntas a seguir são frequentemente feitas sobre o DNS privado do Azure.

## <a name="does-azure-dns-support-private-domains"></a>O DNS do Azure oferece suporte a domínios particulares?

Domínios privados são suportados usando o recurso Dezonas Privadas Do Zure. As zonas DeD privadas são solucionáveis somente a partir de redes virtuais especificadas. Para mais informações, consulte a [visão geral](private-dns-overview.md).

Para obter informações sobre outras opções internas de DNS no Azure, consulte [A resolução Nome para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>As zonas de DNS privadas do Azure funcionarão em todas as regiões do Azure?

Sim. As zonas privadas são suportadas para resolução de DNS entre redes virtuais nas regiões do Azure. As zonas privadas funcionam mesmo sem espiar explicitamente as redes virtuais. Todas as redes virtuais devem ser vinculadas à zona de DNS privada.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>A conectividade com a Internet de redes virtuais é necessária para zonas privadas?

Não. As zonas privadas funcionam junto com as redes virtuais. Você os usa para gerenciar domínios para máquinas virtuais ou outros recursos dentro e em redes virtuais. A conectividade com a Internet não é necessária para a resolução de nomes.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>A mesma zona privada pode ser usada para várias redes virtuais para resolução?

Sim. Você pode vincular uma zona de DNS privada com milhares de redes virtuais. Para obter mais informações, consulte [Azure DNS Limits](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Uma rede virtual que pertence a uma assinatura diferente pode ser vinculada a uma zona privada?

Sim. Você precisa ter permissão de operação de gravação nas redes virtuais e na zona DNS privado. A permissão de gravação pode ser concedida para várias funções do RBAC. Por exemplo, a função RBAC do Contribuinte de Rede Clássica tem permissões de gravação para redes virtuais e zonas de DNS privadas A função contribuinte tem permissões de gravação nas regiões Privadas de DNS. Para obter mais informações sobre funções do RBAC, consulte [Controle de acesso baseado em função](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Os registros DNS da máquina virtual automaticamente registrados em uma zona privada serão automaticamente excluídos quando você excluir a máquina virtual?

Sim. Se você excluir uma máquina virtual dentro de uma rede virtual vinculada com o registro automático ativado, os registros registrados serão excluídos automaticamente.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Um registro de máquina virtual registrado automaticamente em uma zona privada de uma rede virtual vinculada pode ser excluído manualmente?

Sim. Você pode regravar os registros DNS registrados automaticamente com um registro DNS criado manualmente na zona. A pergunta e resposta a seguir abordam este tópico.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>O que acontece quando tento criar manualmente um novo registro de DNS em uma região privada que tenha o mesmo nome de host de uma máquina virtual existente registrada automaticamente em uma rede virtual vinculada?

Você tenta criar manualmente um novo registro de DNS em uma região privada que tenha o mesmo nome de host de uma máquina virtual existente e registrada automaticamente em uma rede virtual vinculada. Quando você faz isso, o novo registro DNS sobrescreve o registro de máquina virtual registrado automaticamente. Se você tentar excluir esse registro DNS criado manualmente da zona novamente, a exclusão será bem-sucedida. O registro automático acontece novamente, contanto que a máquina virtual ainda exista e tenha um IP privado anexado a ela. O registro DNS é recriado automaticamente na zona.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>O que acontece quando desvinculamos uma rede virtual vinculada de uma região privada? Os registros da máquina virtual registrados automaticamente da rede virtual também serão removidos da zona?

Sim. Para desvincular uma rede virtual vinculada de uma região privada, atualize a zona DNS para remover o link de rede virtual associado. Nesse processo, os registros da máquina virtual que foram registrados automaticamente são removidos da zona.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>O que acontece quando excluímos uma rede virtual vinculada que está vinculada a uma zona privada? Temos que atualizar manualmente a região privada para desvincular a rede virtual como uma rede virtual vinculada da região?

Não. Quando você exclui uma rede virtual vinculada sem desvinculá-la de uma região privada primeiro, sua operação de exclusão é bem sucedida e os links para a região DNS são automaticamente apagados.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>A resolução dns usando o FQDN padrão (internal.cloudapp.net) ainda funcionará mesmo quando uma zona privada (por exemplo, private.contoso.com) estiver vinculada a uma rede virtual?

Sim. As Regiões Privadas não substituem a zona de internal.cloudapp.net fornecida pelo Azure padrão. Independentemente de você confiar no internal.cloudapp.net fornecido pelo Azure ou em sua própria zona privada, use o FQDN da zona que deseja resolver.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>O sufixo DNS em máquinas virtuais em uma rede virtual vinculada será alterado para o da zona privada?

Não. O sufixo DNS nas máquinas virtuais em sua rede virtual vinculada permanece como o sufixo fornecido pelo Azure padrão ("*.internal.cloudapp.net"). Você pode alterar manualmente esse sufixo DNS em suas máquinas virtuais para aquela da zona privada.
Para obter orientações sobre como alterar esse sufixo, consulte [Usar DNS dinâmico para registrar nomes de host em seu próprio servidor DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Quais são os limites de uso para zonas privadas do Azure DNS?

Consulte [os limites do Azure DNS](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) para obter detalhes sobre os limites de uso para zonas privadas do Azure DNS.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Por que minhas zonas privadas de DNS não aparecem em novas experiências de portal?

Se a região de DNS privada existente foi criada usando a API de visualização, você deve migrar essas regiões para um novo modelo de recurso. As zonas de DNS privadas criadas usando a API de visualização não aparecerão em novas experiências de portal. Veja abaixo as instruções sobre como migrar para o novo modelo de recursos.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Como migrar minhas regiões de DNS privadas existentes para o novo modelo?

Recomendamos fortemente que você migre para o novo modelo de recursos o mais rápido possível. O modelo de recursos legado será suportado, no entanto, outros recursos não serão desenvolvidos em cima deste modelo. No futuro, pretendemos depreenlá-lo em favor de um novo modelo de recursos. Para obter orientações sobre como migrar suas regiões dns privadas existentes para um novo modelo de recursos, consulte[o guia de migração para zonas privadas do Azure DNS](private-dns-migration-guide.md).

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre o Azure Private DNS](private-dns-overview.md)
