---
title: 'Determinar sua opção de publicação: marketplace comercial da Microsoft'
description: Este artigo descreve os critérios de qualificação e os requisitos para a publicação de ofertas no Microsoft AppSource e no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: dsindona
ms.openlocfilehash: 457ff90c21fee2b569fa24d90fffc6ce80cdf969
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027096"
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

## <a name="selecting-a-storefront"></a>Selecionar uma vitrine

Antes de selecionar uma opção de publicação, é importante entender os requisitos de qualificação de vitrine para aplicativos, serviços e soluções do marketplace comercial. Cada vitrine atende aos requisitos exclusivos do cliente e destina-se a públicos específicos. Seu tipo de oferta, os recursos de transação e a categoria ou o setor determinarão onde publicar sua oferta.

Os aplicativos do **Microsoft AppSource** são soluções de linha de negócios que podem ser compiladas no Azure ou compiladas para: Dynamics 365, Office 365, Power BI ou Power Apps. Serviços de consultoria do AppSource são ofertas de serviços profissionais que ajudam os clientes a começarem a usar o Dynamics 365 e o Power BI.

Os aplicativos do **Azure Marketplace** são soluções técnicas de "bloco de construção", integradas ao Azure ou criadas para ele e destinadas a um público de TI ou de desenvolvedores. Serviços de consultoria do Azure Marketplace são ofertas de serviços profissionais que ajudam os clientes a começarem a usar o Azure, ou a aprenderem a usá-lo mais rápido.

>[!Note]
>"Listagem cruzada" (somente para aplicativos SaaS): se sua oferta de SaaS for destinada a um público técnico (Azure Marketplace) e a um público-alvo (AppSource), você poderá selecionar uma categoria e/ou subcategoria aplicável a qualquer uma das lojas. Lembre-se de que a intenção de "listagem cruzada" de sua oferta deve ser baseada em uma proposta de valor que se estenda para ambas as audiências. Clique [aqui](./gtm-offer-listing-best-practices.md#categories) para exibir categorias aplicáveis a cada vitrine.

## <a name="choose-a-publishing-option"></a>Selecione uma opção de publicação

As opções de publicação disponíveis oferecem envolvimento do cliente diferenciado, dando a ele acesso ao compartilhamento de leads e [benefícios do marketplace comercial](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits). Observe as chamadas de ação que correspondem à opção de publicação:

| **Opção de publicação**    | **Descrição**  |
| :------------------- | :-------------------|
| **Lista** | Listagem simples de seu aplicativo ou serviço que permite que um usuário do marketplace comercial solicite a conexão com o cliente por meio da CTA **Entre em Contato Comigo**. |
| **Avaliação** | Use o marketplace comercial para melhorar a capacidade de descoberta e automatizar o provisionamento da experiência de avaliação da sua solução, permitindo que os usuários em potencial usem sua experiência no aplicativo SaaS, IaaS ou da Microsoft sem nenhum custo, por tempo limitado, antes de comprar. A CTA usada para a opção de publicação de avaliação é uma entre: **Avaliação Gratuita** e **Test Drive**. |
| **BYOL** | Use o marketplace comercial para aprimorar a capacidade de descoberta, automatizar o provisionamento da sua solução e concluir a transação financeira separadamente. Os tipos de oferta BYOL são ideais para migrações do local para a nuvem. A CTA é **Experimente agora**.
| **Transação** | As ofertas transacionais são vendidas por meio do marketplace comercial. A Microsoft é responsável pelas cobranças. A CTA é **Experimente Agora**.|

> [!Note]
> Ao usar a opção de publicação de Transação, é importante entender as considerações de preços, cobrança, faturamento e pagamento antes de selecionar um tipo de oferta e criação de sua oferta. Para saber mais, leia o artigo [Recursos de transação do marketplace comercial](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="next-steps"></a>Próximas etapas

- Após decidir sobre uma opção de publicação, você estará pronto para [selecionar o tipo de oferta](./publisher-guide-by-offer-type.md) que será usado para apresentar sua oferta.
- Examine os requisitos de qualificação nas opções de publicação pela seção de tipo de oferta para finalizar a seleção e a configuração de sua oferta.
- Examine os padrões de publicação por vitrine eletrônica para obter exemplos de como sua solução é mapeada para uma configuração e tipo de oferta.
