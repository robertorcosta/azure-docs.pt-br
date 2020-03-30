---
title: APIs de cumprimento do SaaS - FAQ | Mercado Azure
description: Experiências de descoberta e compra pelos clientes de uma oferta SaaS no Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6d3a84341d5221950da20f39456461dafc5d2e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275689"
---
# <a name="saas-fulfillment-apis---faq"></a>APIs de cumprimento de SaaS – perguntas frequentes

Os requisitos de integração com o Azure Marketplace para permitir que os clientes do Azure assinem a oferta SaaS estão listados.

## <a name="discovery-experience"></a>Experiência de descoberta

Uma vez que a oferta é publicada, os usuários do Azure podem descobrir a oferta do SaaS no Azure Marketplace. Seus clientes poderão filtrar ofertas com base no tipo de produto (SaaS) e descobrir os serviços SaaS em que estão interessados.

## <a name="purchase-experience"></a>Experiência de compra

Uma vez que um usuário esteja interessado em um serviço SaaS específico, o usuário pode assiná-lo no Azure Marketplace.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>O que significa para um usuário do Azure assinar uma oferta SaaS no Azure Marketplace?

Isso significa que um usuário pode visualizar os termos de uso e declaração de privacidade associados ao serviço SaaS e concordar em pagá-lo de acordo com os termos de cobrança estabelecidos por você, o editor da oferta SaaS, na fatura da Microsoft. Os usuários podem usar seu perfil de pagamento existente no Azure para pagar pelo consumo do serviço SaaS.

Isso é benéfico por muitas razões. Os clientes agora podem descobrir e se inscrever em um só lugar usando a Microsoft Cloud Platform como uma fonte confiável, sem ter que examinar todos os softwares ISV que pretende usar. Além disso, os clientes podem usar seu perfil de pagamento existente sem ter que pagar explicitamente cada software ISV de forma independente.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>O usuário é cobrado automaticamente quando a oferta é subscrita?

Ao assinar a oferta SaaS, o usuário concordou em pagar pelo consumo do serviço SaaS através da plataforma Microsoft. No entanto, as taxas só começarão quando a oferta for consumida. O usuário deve ir à sua oferta SaaS e confirmar a criação da conta para começar a consumir a oferta. Em seguida, você notificará a Microsoft para começar a faturar por essa assinatura SaaS do cliente.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Como você é notificado quando um usuário assina sua oferta SaaS?

Após assinar uma oferta, o usuário do Azure pode descobrir e gerenciar todas as suas ofertas no Azure. Por padrão, o estado de uma oferta SaaS recém-subscrita é **'Provisionamento, cumprimento pendente'**. Neste estado, o usuário do Azure será solicitado com uma ação para **'Configurar conta',** a fim de navegar para sua experiência de gerenciamento de assinatura SaaS no portal Azure.

Quando o usuário clicar em **'Configurar conta',** ele será redirecionado para o site do serviço SaaS. A URL para onde eles são navegados é fornecida pelo editor no momento da publicação da oferta. Esta página é referida como a página de aterrissagem do editor. Os usuários do Azure devem ser capazes de fazer login na página de aterrissagem do SaaS com base em suas credenciais AAD existentes no Azure.

Quando o usuário do Azure é redirecionado para a página de inicialidade, um token é adicionado à URL da consulta. Este token tem vida curta e é válido por um tempo de duração de 24 horas. Em seguida, você pode detectar a presença deste token e ligar para a API da Microsoft para obter mais contexto associado ao token.

![Fluxo de assinatura do cliente](media/saas-metering-service-integration-flow-a.png)

Consulte o documento de API de cumprimento do [SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) para obter mais informações sobre o contrato de API para lidar com cenários de transações no ciclo de vida de uma oferta SaaS.

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Como você conhece a oferta SaaS à qual o usuário assina no Azure?

A resposta `Resolve` à API inclui as informações de oferta e plano associadas à assinatura SaaS.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Como o usuário do Azure pode alterar o plano associado a esta assinatura do Azure?

* O usuário do Azure pode alterar o plano associado à assinatura SaaS diretamente na experiência SaaS, ou através da plataforma Microsoft.

* As conversões podem ser feitas a qualquer momento no ciclo de faturamento. Você deve reconhecer qualquer conversão, que se tornará eficaz uma vez reconhecida.

* As taxas de plano pré-pago **(mensal ou** **anual)** são rateadas. Qualquer excesso emitido até o momento da conversão será cobrado na próxima fatura. Novos excessos serão emitidos com base no novo plano.

>[!Note]
>Você pode bloquear downgrades se não quiser suportar caminhos de conversão específicos.

A seqüência abaixo captura o fluxo quando um cliente do Azure altera o plano na experiência SaaS:

![Fluxo de mudança do plano do cliente](media/saas-metering-service-integration-flow-b.png)

A seqüência abaixo captura o fluxo quando um cliente do Azure muda o plano na vitrine da Microsoft

![Fluxo de mudança do plano da loja do cliente](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Como o usuário do Azure pode cancelar a assinatura do plano associado à assinatura do Azure?

Um usuário do Azure pode cancelar a assinatura de uma oferta SaaS comprada diretamente na experiência SaaS, ou através da plataforma Microsoft. Uma vez que o usuário cancela a inscrição, ele não será mais cobrado a partir do próximo ciclo de cobrança.

A seqüência abaixo captura o fluxo quando um cliente do Azure cancela a oferta do SaaS na experiência SaaS:

![Desinscritos do cliente na experiência SaaS](media/saas-metering-service-integration-flow-d.png)

A seqüência abaixo captura o fluxo quando o usuário do Azure não faz cancelamento de assinatura na vitrine da Microsoft:

![Desinscrições do cliente na vitrine da Microsoft](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Próximas etapas

- Consulte [APIs de serviço de medição do Marketplace](./marketplace-metering-service-apis.md) para obter mais informações.
