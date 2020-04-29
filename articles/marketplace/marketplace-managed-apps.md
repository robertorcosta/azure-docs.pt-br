---
title: Guia de publicação de oferta de aplicativo gerenciado por aplicativos do Azure | Azure Marketplace
description: Este artigo descreve os requisitos para publicar um aplicativo gerenciado no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084865"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Aplicativos do Azure: requisitos de publicação de oferta de aplicativo gerenciado

Este artigo explica os requisitos para o tipo de oferta de aplicativo gerenciado, que é uma maneira de publicar uma oferta de aplicativo do Azure no Azure Marketplace. Os aplicativos gerenciados são ofertas do Transact que são implantadas e cobradas por meio do Azure Marketplace. A chamada à ação que um usuário vê é "Obtenha agora".

Use o tipo de oferta de aplicativo gerenciado quando as seguintes condições forem necessárias:

- Você implanta uma solução baseada em assinatura para seu cliente usando uma VM ou uma solução inteira baseada em IaaS.
- Você ou seu cliente exigem que a solução seja gerenciada por um parceiro.

>[!NOTE]
>Por exemplo, um parceiro pode ser um SI ou MSP (provedor de serviços gerenciados).  

## <a name="managed-application-offer"></a>Oferta de aplicativo gerenciado

|Requisitos |Detalhes  |
|---------|---------|
|Implantado na assinatura do Azure de um cliente | Os aplicativos gerenciados devem ser implantados na assinatura do cliente e podem ser gerenciados por terceiros. |
|Cobrança e medição    |  Os recursos serão provisionados na assinatura do Azure do cliente. As máquinas virtuais PAYGO (pré-pagas) serão transacionadas com o cliente pela Microsoft, cobradas por meio da assinatura do Azure do cliente (PAYGO). <br> No caso do traga sua própria licença, enquanto a Microsoft cobrará os custos de infraestrutura incorridos na assinatura do cliente, você receberá suas tarifas de licenciamento de software diretamente para o cliente.        |
|VHD (disco rígido virtual) compatível com Azure    |   As VMs devem ser criadas em Windows ou Linux.<ul> <ul> <li>Para obter mais informações sobre como criar um VHD Linux, consulte [Distribuições do Linux endossadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Para obter mais informações sobre como criar um VHD do Windows, consulte [criar uma oferta de aplicativo do Azure](./partner-center-portal/create-new-azure-apps-offer.md).</li> </ul> |

>[!NOTE]
> Aplicativos gerenciados devem poder ser implantados por meio do Marketplace. Se a comunicação do cliente é relevante, você deve falar com clientes interessados depois de habilitar o compartilhamento de leads.  

>[!Note]
>A aceitação do canal de parceiros do CSP (provedores de soluções na nuvem) já está disponível. Para obter mais informações sobre como comercializar sua oferta por meio dos canais de parceiros do Microsoft CSP, consulte [provedores de soluções de nuvem](./cloud-solution-providers.md).

## <a name="next-steps"></a>Próximas etapas

- Se você ainda não fez isso, [saiba mais](https://azuremarketplace.microsoft.com/sell) sobre o Azure Marketplace.
- [Entre no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou concluir sua oferta.
- [Crie uma oferta de aplicativo do Azure](./partner-center-portal/create-new-azure-apps-offer.md) para obter mais informações.
