---
title: 'Determinar sua opção de publicação: marketplace comercial da Microsoft'
description: Este artigo descreve os critérios de qualificação e os requisitos para a publicação de ofertas no Microsoft AppSource e no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/30/2020
ms.openlocfilehash: 37647a9591c0f686e4fc3f1fd858baa46e01f7ac
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498065"
---
# <a name="determine-your-publishing-option"></a>Determinar a opção de publicação

A opção de publicação que você escolher para a oferta estará relacionada diretamente aos requisitos de qualificação e benefícios de GTM do marketplace comercial. Mais importante, a seleção da opção de publicação e do tipo de oferta define como será a interação dos usuários com sua oferta do marketplace comercial.

Para configurar sua oferta, é preciso entender os seguintes conceitos principais do marketplace comercial: opções de publicação, tipos de oferta e de configuração e as CTAs, que determinarão como e onde sua oferta será apresentada nas vitrines do marketplace comercial.

Neste artigo, você aprenderá:

- Como determinar a vitrine apropriada para sua solução
- Quais opções de publicação e chamadas de ação estão disponíveis em cada vitrine
- Quais tipos de oferta estão disponíveis para cada opção de publicação

## <a name="commercial-marketplace-publishing-options"></a>Opções de publicação do marketplace comercial

A tabela a seguir mostra as opções de publicação para tipos de oferta no Microsoft AppSource e no Azure Marketplace.

|   | **Lista (contato)**  | **Lista (avaliação)**  | **Gratuito** | **BYOL** | **Transação**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Máquina Virtual** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Aplicativos Azure (várias VMs)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **Imagem de contêiner** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Módulo do IoT Edge** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Serviços gerenciados** |  |  |  | Azure Marketplace |   |
| **Serviços de consultoria** | Ambas as vitrines |  |  |  |   |
| **Aplicativo SaaS** | Ambas as vitrines | Ambas as vitrines | Ambas as vitrines |  | Ambas as vitrines* |
| **Aplicativo do Microsoft 365** | AppSource | AppSource |  |  | AppSource**  |
| **Suplemento Dynamics 365** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; As ofertas transacionais do aplicativo SaaS em Microsoft AppSource atualmente são apenas para cartões de crédito.

&#42;&#42; As ofertas de Microsoft 365 são gratuitas para instalação e podem ser monetizadas por meio de uma oferta de SaaS como um serviço de licenciamento. Para obter mais informações, consulte [Monetizar seu suplemento do Office 365 por meio do marketplace comercial da Microsoft](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="choose-a-call-to-action"></a>Escolha uma chamada para ação

As opções de publicação disponíveis oferecem envolvimento do cliente diferenciado, dando a ele acesso ao compartilhamento de leads e [benefícios do marketplace comercial](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits). Observe as chamadas de ação que correspondem à opção de publicação:

| **Opção de publicação**    | **Descrição**  |
| :------------------- | :-------------------|
| **Lista** | Listagem simples de seu aplicativo ou serviço que permite que um usuário do marketplace comercial solicite a conexão com o cliente por meio da CTA **Entre em Contato Comigo**. |
| **Avaliação** | Use o marketplace comercial para melhorar a capacidade de descoberta e automatizar o provisionamento da experiência de avaliação da sua solução, permitindo que os usuários em potencial usem sua experiência no aplicativo SaaS, IaaS ou da Microsoft sem nenhum custo, por tempo limitado, antes de comprar. A CTA usada para a opção de publicação de avaliação é uma entre: **Avaliação Gratuita** e **Test Drive**. |
| **BYOL** | Use o marketplace comercial para aprimorar a capacidade de descoberta, automatizar o provisionamento da sua solução e concluir a transação financeira separadamente. Os tipos de oferta BYOL são ideais para migrações do local para a nuvem. A CTA é **Experimente agora**.
| **Transação** | As ofertas transacionais são vendidas por meio do marketplace comercial. A Microsoft é responsável pelas cobranças. A CTA é **Experimente Agora**.|

> [!Note]
> Ao usar a opção de publicação de Transação, é importante entender as considerações de preços, cobrança, faturamento e pagamento antes de selecionar um tipo de oferta e criação de sua oferta. Para saber mais, leia o artigo [Recursos de transação do marketplace comercial](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="selecting-a-storefront"></a>Selecionar uma vitrine

Cada vitrine atende aos requisitos exclusivos do cliente e destina-se a públicos específicos. Seu tipo de oferta, recursos de transação e categoria determinarão onde sua oferta será publicada. As categorias e subcategorias são mapeadas para cada vitrine com base no público-alvo:

**Microsoft AppSource** destina-se a usuários empresariais que procuram soluções de linha de negócios ou do setor e serviços de consultoria para Dynamics 365, Microsoft 365 e Power Platform.

O **Azure Marketplace** tem como alvo profissionais de ti e desenvolvedores procurando soluções criadas para o ou no Azure, bem como serviços de consultoria que aceleram o uso do Azure.

Selecione a categoria e a subcategoria que melhor se alinham com o público-alvo. Por exemplo, um firewall do aplicativo Web deve ser publicado no Azure Marketplace, sob a categoria segurança, pois o público-alvo é profissional de ti. Em vez disso, um aplicativo de gerenciamento de contrato deve ser publicado em AppSource sob a categoria Sales, já que o público-alvo é usuários empresariais. A seleção da categoria ou subcategoria incorreta pode resultar na publicação da sua oferta na vitrine errada.

### <a name="publishing-to-both-storefronts-saas-offers-only"></a>Publicando em ambas as vitrines (somente ofertas de SaaS)

As ofertas de SaaS podem ser publicadas no Azure Marketplace ou no AppSource. Se sua oferta de SaaS for destinada *a um público* técnico (Azure Marketplace) e a um público-alvo de negócios (AppSource), selecione uma categoria e/ou uma subcategoria aplicável a cada vitrine. As ofertas publicadas em ambas as vitrines devem ter uma proposta de valor que se estenda aos profissionais de ti *e* aos usuários empresariais.

> [!IMPORTANT]
> As ofertas de SaaS com cobrança limitada estão disponíveis por meio do Azure Marketplace e do portal do Azure. As ofertas de SaaS com apenas planos privados estão disponíveis por meio do portal do Azure.

| Cobrança limitada | Plano público | Plano particular | Disponível em: |
|---|---|---|---|
| Yes             | Sim         | No           | Azure Marketplace e portal do Azure |
| Yes             | Yes         | Yes          | Azure Marketplace e portal do Azure * |
| Sim             | Não          | Sim          | Somente portal do Azure |
| No              | Não          | Sim          | Somente portal do Azure |

&#42; o plano privado da oferta só estará disponível por meio do portal do Azure

Por exemplo, uma oferta com cobrança limitada e um plano privado somente (sem plano público) serão adquiridas pelos clientes na portal do Azure. Saiba mais sobre as [ofertas privadas no Microsoft Commercial Marketplace](private-offers.md).

### <a name="categories"></a>Categorias

As categorias e subcategorias são mapeadas para cada vitrine com base no público-alvo. Selecione as categorias e subcategorias que melhor se alinham com sua oferta e o público-alvo. Você pode selecionar:

- Pelo menos uma e até duas categorias. Você tem a opção de escolher uma categoria primária e uma secundária.
- Até duas subcategorias para cada categoria primária e/ou secundária. Se você não selecionar nenhuma subcategoria, sua oferta ainda poderá ser descoberta na categoria selecionada.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>Próximas etapas

- Após decidir sobre uma opção de publicação, você estará pronto para [selecionar o tipo de oferta](./publisher-guide-by-offer-type.md) que será usado para apresentar sua oferta.
- Examine os requisitos de qualificação nas opções de publicação pela seção de tipo de oferta para finalizar a seleção e a configuração de sua oferta.
- Examine os padrões de publicação por vitrine eletrônica para obter exemplos de como sua solução é mapeada para uma configuração e tipo de oferta.
