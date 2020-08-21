---
title: 'Determinar sua opção de publicação: marketplace comercial da Microsoft'
description: Este artigo descreve os critérios de qualificação e os requisitos para a publicação de ofertas no Microsoft AppSource e no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/30/2020
ms.openlocfilehash: 4a27ace4e45d00a2fbb35b9e0e3e9b7c03383681
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705870"
---
# <a name="determine-your-publishing-option"></a>Determinar a opção de publicação

A opção de publicação que você escolher para a oferta estará relacionada diretamente aos requisitos de qualificação e benefícios de GTM do marketplace comercial. Mais importante, a seleção da opção de publicação e do tipo de oferta define como será a interação dos usuários com sua oferta do marketplace comercial.

Para configurar sua oferta, você precisará compreender os seguintes conceitos principais do Marketplace comercial: as opções de publicação, tipos de oferta e configuração e chamadas para ação que irão controlar como e onde sua oferta será apresentada nas lojas online do Marketplace comercial.

Neste artigo, você aprenderá:

- Como determinar a loja online apropriada para sua solução.
- Quais opções de publicação e chamadas para ação estão disponíveis em cada loja online.
- Quais tipos de oferta estão disponíveis para cada opção de publicação.

## <a name="commercial-marketplace-publishing-options"></a>Opções de publicação do marketplace comercial

A tabela a seguir mostra as opções de publicação para tipos de oferta no Microsoft AppSource e no Azure Marketplace.

|   | **Lista (contato)**  | **Lista (avaliação)**  | **Gratuito** | **BYOL** | **Transação**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Máquina Virtual** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Aplicativos Azure (várias VMs)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **Imagem de contêiner** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Módulo do IoT Edge** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Serviços gerenciados** |  |  |  | Azure Marketplace |   |
| **Serviços de consultoria** | Ambas as lojas online |  |  |  |   |
| **Aplicativo SaaS** | Ambas as lojas online | Ambas as lojas online | Ambas as lojas online |  | Ambos os repositórios online * |
| **Aplicativo do Microsoft 365** | AppSource | AppSource |  |  | AppSource**  |
| **Suplemento Dynamics 365** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; As ofertas transacionais do aplicativo SaaS em Microsoft AppSource atualmente são apenas para cartões de crédito.

&#42;&#42; As ofertas de Microsoft 365 são gratuitas para instalação e podem ser monetizadas por meio de uma oferta de SaaS como um serviço de licenciamento. Para obter mais informações, consulte [Monetizar seu suplemento do Office 365 por meio do marketplace comercial da Microsoft](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="choose-a-listing-option"></a>Escolha uma opção de listagem

As opções de listagem disponíveis oferecem o envolvimento do cliente diferenciado, oferecendo acesso a benefícios de compartilhamento de leads e do [Marketplace comercial](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits). Observe as opções de listagem que correspondem à opção de publicação:

| **Opção de publicação**    | **Descrição**  |
| :------------------- | :-------------------|
| **Lista** | Listagem simples de seu aplicativo ou serviço que permite que um usuário do marketplace comercial solicite a conexão com o cliente por meio da CTA **Entre em Contato Comigo**. |
| **Avaliação** | Use o marketplace comercial para melhorar a capacidade de descoberta e automatizar o provisionamento da experiência de avaliação da sua solução, permitindo que os usuários em potencial usem sua experiência no aplicativo SaaS, IaaS ou da Microsoft sem nenhum custo, por tempo limitado, antes de comprar. A CTA usada para a opção de publicação de avaliação é uma entre: **Avaliação Gratuita** e **Test Drive**. |
| **BYOL** | Use o marketplace comercial para aprimorar a capacidade de descoberta, automatizar o provisionamento da sua solução e concluir a transação financeira separadamente. Os tipos de oferta BYOL são ideais para migrações do local para a nuvem. A CTA é **Experimente agora**.
| **Transação** | As ofertas transacionais são vendidas por meio do marketplace comercial. A Microsoft é responsável pelas cobranças. A CTA é **Experimente Agora**.|

> [!Note]
> Ao usar a opção de publicação de Transação, é importante entender as considerações de preços, cobrança, faturamento e pagamento antes de selecionar um tipo de oferta e criação de sua oferta. Para saber mais, leia o artigo [Recursos de transação do marketplace comercial](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="selecting-an-online-store"></a>Selecionando uma loja online

Cada loja online atende aos requisitos exclusivos do cliente e se destina a públicos específicos. Seu tipo de oferta, recursos de transação e categoria determinarão onde sua oferta será publicada. As categorias e subcategorias são mapeadas para cada loja online com base no público-alvo:

**Microsoft AppSource** destina-se a usuários empresariais que procuram soluções de linha de negócios ou do setor e serviços de consultoria para Dynamics 365, Microsoft 365 e Power Platform.

O **Azure Marketplace** tem como alvo profissionais de ti e desenvolvedores procurando soluções criadas para o ou no Azure, bem como serviços de consultoria que aceleram o uso do Azure.

Selecione a categoria e a subcategoria que melhor se alinham com o público-alvo. Por exemplo, um firewall do aplicativo Web deve ser publicado no Azure Marketplace, sob a categoria segurança, pois o público-alvo é profissional de ti. Em vez disso, um aplicativo de gerenciamento de contrato deve ser publicado em AppSource sob a categoria Sales, já que o público-alvo é usuários empresariais. A seleção da categoria ou subcategoria incorreta pode resultar na publicação da sua oferta no repositório online errado.

### <a name="publishing-to-both-online-stores-saas-offers-only"></a>Publicando em lojas online (somente ofertas de SaaS)

As ofertas de SaaS podem ser publicadas no Azure Marketplace ou no AppSource. Se sua oferta de SaaS for destinada *a um público* técnico (Azure Marketplace) e a um público-alvo de negócios (AppSource), selecione uma categoria e/ou uma subcategoria aplicável a cada loja online. As ofertas publicadas em lojas online devem ter uma proposta de valor que se estenda aos profissionais de ti *e* aos usuários empresariais.

> [!IMPORTANT]
> As ofertas de SaaS com cobrança limitada estão disponíveis por meio do Azure Marketplace e do portal do Azure. As ofertas de SaaS com apenas planos privados estão disponíveis por meio do portal do Azure.

| Cobrança limitada | Plano público | Plano particular | Disponível em: |
|---|---|---|---|
| Sim             | Sim         | Não           | Azure Marketplace e portal do Azure |
| Sim             | Sim         | Sim          | Azure Marketplace e portal do Azure * |
| Sim             | Não          | Sim          | Somente portal do Azure |
| Não              | Não          | Sim          | Somente portal do Azure |

&#42; o plano privado da oferta só estará disponível por meio do portal do Azure

Por exemplo, uma oferta com cobrança limitada e um plano privado somente (sem plano público) serão adquiridas pelos clientes na portal do Azure. Saiba mais sobre as [ofertas privadas no Microsoft Commercial Marketplace](private-offers.md).

### <a name="categories"></a>Categorias

As categorias e subcategorias são mapeadas para cada loja online com base no público-alvo. Selecione as categorias e subcategorias que melhor se alinham com sua oferta e o público-alvo. Você pode selecionar:

- Pelo menos uma e até duas categorias. Você tem a opção de escolher uma categoria primária e uma secundária.
- Até duas subcategorias para cada categoria primária e/ou secundária. Se você não selecionar nenhuma subcategoria, sua oferta ainda poderá ser descoberta na categoria selecionada.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>Próximas etapas

- Após decidir sobre uma opção de publicação, você estará pronto para [selecionar o tipo de oferta](./publisher-guide-by-offer-type.md) que será usado para apresentar sua oferta.
- Examine os requisitos de qualificação nas opções de publicação pela seção de tipo de oferta para finalizar a seleção e a configuração de sua oferta.
- Examine os padrões de publicação por loja online para obter exemplos de como sua solução é mapeada para um tipo de oferta e configuração.
