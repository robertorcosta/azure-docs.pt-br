---
title: Aplicativos gerenciados do Azure oferecem guia de publicação | Mercado Azure
description: Este artigo descreve os requisitos para publicar um aplicativo gerenciado no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084865"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Aplicativos Azure: Aplicativos gerenciados oferecem requisitos de publicação

Este artigo explica os requisitos para o tipo de oferta de aplicativo gerenciado, que é uma maneira de publicar uma oferta de aplicativo Azure no Azure Marketplace. Os aplicativos gerenciados são ofertas de transação que são implantadas e cobradas através do Azure Marketplace. A chamada à ação que um usuário vê é "Obtenha agora".

Use o tipo de oferta de aplicativo gerenciado quando as seguintes condições forem necessárias:

- Você implanta uma solução baseada em assinatura para seu cliente usando uma VM ou uma solução baseada em IaaS inteira.
- Você ou seu cliente exigem que a solução seja gerenciada por um parceiro.

>[!NOTE]
>Por exemplo, um parceiro pode ser um SI ou MSP (provedor de serviços gerenciados).  

## <a name="managed-application-offer"></a>Oferta de aplicativo gerenciado

|Requisitos |Detalhes  |
|---------|---------|
|Implantado na assinatura do Azure de um cliente | Os aplicativos gerenciados devem ser implantados na assinatura do cliente e podem ser gerenciados por terceiros. |
|Cobrança e medição    |  Os recursos serão provisionados na assinatura do Azure do cliente. As máquinas virtuais Pay-as-you-go (PAYGO) serão transacionadas com o cliente via Microsoft, faturadas através da assinatura do Cliente Azure (PAYGO). <br> No caso de trazer sua própria licença, enquanto a Microsoft cobrará os custos de infra-estrutura incorridos na assinatura do cliente, você transacionará suas taxas de licenciamento de software diretamente para o cliente.        |
|VHD (disco rígido virtual) compatível com Azure    |   As VMs devem ser criadas em Windows ou Linux.<ul> <ul> <li>Para obter mais informações sobre como criar um VHD Linux, consulte [Distribuições do Linux endossadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Para obter mais informações sobre a criação de um VHD do Windows, consulte [criar uma oferta de aplicativo do Azure](./partner-center-portal/create-new-azure-apps-offer.md).</li> </ul> |

>[!NOTE]
> Aplicativos gerenciados devem poder ser implantados por meio do Marketplace. Se a comunicação do cliente é relevante, você deve falar com clientes interessados depois de habilitar o compartilhamento de leads.  

>[!Note]
>O opt-in do canal parceiro Cloud Solution Providers (CSP) já está disponível. Para obter mais informações sobre o marketing de sua oferta através dos canais parceiros Microsoft CSP, consulte [Cloud Solution Providers](./cloud-solution-providers.md).

## <a name="next-steps"></a>Próximas etapas

- Se você ainda não fez isso, [saiba](https://azuremarketplace.microsoft.com/sell) mais sobre o Azure Marketplace.
- [Faça login no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou concluir sua oferta.
- [Crie uma oferta de aplicativo do Azure](./partner-center-portal/create-new-azure-apps-offer.md) para obter mais informações.
