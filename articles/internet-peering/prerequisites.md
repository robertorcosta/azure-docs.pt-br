---
title: Pré-requisitos de configuração de emparelhamento com a Microsoft
titleSuffix: Azure
description: Pré-requisitos de configuração de emparelhamento com a Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3c820a7be561aeef9b7e50fd0ac0cf4dee721af8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775401"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Pré-requisitos de configuração de emparelhamento com a Microsoft

Certifique-se de que os pré-requisitos abaixo sejam atendidos antes de solicitar um novo peering ou converter um peering legado para o recurso DoZure.

## <a name="azure-related-prerequisites"></a>Pré-requisitos relacionados ao Azure
* **Conta do Microsoft Azure:** Se você não tiver uma conta do Microsoft Azure, crie uma [conta do Microsoft Azure](https://azure.microsoft.com/free). Uma assinatura válida e ativa do Microsoft Azure é necessária para configurar peering, já que os peerings são modelados como recursos dentro de assinaturas do Azure. É importante observar que:
    * Os tipos de recursos do Azure usados para configurar peering são produtos Azure sempre livres, ou seja, você não é cobrado por criar uma conta do Azure ou criar uma assinatura ou acessar os recursos do Azure **PeerAsn** e **Peering** para configurar peering. Isso não deve ser confundido com o acordo de peering para peering direto entre você e a Microsoft, os termos para os quais são explicitamente discutidos com nossa equipe de peering. Entre em contato com [a Microsoft se](mailto:peering@microsoft.com) houver alguma dúvida a esse respeito.
    * Você pode usar a mesma assinatura do Azure para acessar outros produtos do Azure ou serviços em nuvem que podem ser gratuitos ou pagos. Quando você acessar um produto pago, você incorrerá em encargos.
    * Se você estiver criando uma nova conta e/ou assinatura do Azure, você pode ser elegível para crédito gratuito do Azure durante um período de teste que você pode utilizar para experimentar os serviços do Azure Cloud. Se estiver interessado, visite [a conta do Microsoft Azure](https://azure.microsoft.com/free) para obter mais informações.

* **Associado peer ASN:** Antes de solicitar o peering, primeiro associe sua ASN e entre em contato com sua assinatura. Siga as instruções em [Associate Peer ASN para Azure Subscription](howto-subscription-association-powershell.md).

## <a name="other-prerequisites"></a>Outros pré-requisitos
* **Perfil do PeeringDB:** Espera-se que os pares tenham um perfil completo e atualizado no [PeeringDB](https://www.peeringdb.com). Usamos essas informações em nosso sistema de registro para validar os detalhes do peer, como informações de NOC, informações de contato técnico e sua presença nas instalações de peering etc.

## <a name="next-steps"></a>Próximas etapas

* [Crie ou modifique um peering direto usando o portal](howto-direct-portal.md).
* [Converter um Emparelhamento direto herdado para o recurso do Azure usando o portal](howto-legacy-direct-portal.md)
* [Criar ou modificar o peering do Exchange usando o portal](howto-exchange-portal.md)
* [Converter um emparelhamento do Exchange herdado para o recurso do Azure usando o portal](howto-legacy-exchange-portal.md)