---
title: Determine sua opção de publicação nas vitrines do mercado comercial da Microsoft
description: Este artigo descreve os critérios de elegibilidade e os requisitos de publicação para parceiros que tentam entender como publicar aplicativos no Microsoft AppSource e no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: e91ca0334276dbe9118eb0e40d042f598e8b3c0f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415733"
---
# <a name="determine-your-publishing-option"></a>Determinar a opção de publicação

A opção de publicação que você escolher para sua oferta diz respeito diretamente aos requisitos de elegibilidade e aos benefícios gtm do mercado comercial. Mais importante, a seleção da opção de publicação e do tipo de oferta define como os usuários irão interagir com sua oferta de marketplace comercial.

Para configurar sua oferta, você precisará entender os seguintes conceitos principais de mercado comercial: as opções de publicação, tipos de oferta e configuração, e chamadas para ação que irão reger como e onde sua oferta é apresentada nas vitrines do mercado comercial.

Neste artigo, você aprenderá:

- Como determinar a vitrine apropriada para sua solução
- Quais opções de publicação e chamadas de ação estão disponíveis em cada vitrine
- Quais tipos de oferta estão disponíveis para cada opção de publicação

## <a name="commercial-marketplace-publishing-options"></a>Opções de publicação de mercado comercial

A tabela a seguir mostra as opções de publicação para tipos de oferta no Microsoft AppSource e no Azure Marketplace.

|   | **Lista (Contato)**  | **Lista (Teste)**  | **Gratuito** | **BYOL** | **Transact**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Máquina Virtual** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Aplicativos Azure (multi-VM)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **Imagem do contêiner** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Módulo do IoT Edge** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Serviços gerenciados** |  |  |  | Azure Marketplace |   |
| **Serviços de consultoria** | Ambas as vitrines |  |  |  |   |
| **Aplicativo SaaS** | Ambas as vitrines | Ambas as vitrines | Ambas as vitrines |  | Ambas as vitrines* |
| **Aplicativo Microsoft 365** | AppSource | AppSource |  |  | Fonte de aplicativo**  |
| **Dinâmica 365 complemento** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; aplicativo SaaS Que a Transact oferece no Microsoft AppSource são atualmente apenas cartão de crédito.

&#42;&#42; ofertas do Microsoft 365 são gratuitas e podem ser monetizadas via oferta SaaS como um serviço de licenciamento. Para obter mais informações, consulte [Monetize seu Office 365 através do mercado comercial da Microsoft](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="selecting-a-storefront"></a>Selecionando uma vitrine

Antes de selecionar uma opção de publicação, é importante entender os requisitos de elegibilidade da vitrine para soluções, aplicativos e serviços de mercado comercial. Cada vitrine atende aos requisitos exclusivos dos clientes e tem como alvo públicos específicos. Seu tipo de oferta, recursos de transação e categoria ou indústria determinarão onde publicar sua oferta.

Os aplicativos **Microsoft AppSource** são soluções de linha de negócios que podem ser incorporadas ao Azure ou incorporadas para: Dynamics 365, Office 365, Power BI ou Power Apps. Serviços de consultoria do AppSource são ofertas de serviços profissionais que ajudam os clientes a começarem a usar o Dynamics 365 e o Power BI.

Os aplicativos **do Azure Marketplace** são soluções técnicas de "bloco de construção" incorporadas ou incorporadas para o Azure e destinadas a um público de TI ou desenvolvedor. Serviços de consultoria do Azure Marketplace são ofertas de serviços profissionais que ajudam os clientes a começarem a usar o Azure, ou a aprenderem a usá-lo mais rápido.

>[!Note]
>Cross-listing (somente para aplicativos SaaS): quando uma oferta baseada em lista ou teste atender aos critérios tanto para um público técnico quanto para usuários de negócios, sua oferta será listada em ambas as vitrines. Saiba mais sobre as opções de publicação abaixo.

## <a name="choose-a-publishing-option"></a>Selecione uma opção de publicação

As opções de publicação disponíveis oferecem engajamento diferenciado do cliente, ao mesmo tempo em que lhe dão acesso a benefícios de compartilhamento de chumbo e [mercado comercial.](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits) Observe as chamadas de ação que correspondem à opção de publicação:

| **Opção de publicação**    | **Descrição**  |
| :------------------- | :-------------------|
| **Lista** | Listagem simples do seu aplicativo ou serviço que permite que um usuário de marketplace comercial solicite que você se conecte com o cliente através do call-to-action **do Contact Me.** |
| **Julgamento** | Use o mercado comercial para melhorar a capacidade de descoberta e automatizar o provisionamento da experiência de teste da sua solução, permitindo que os usuários em potencial usem sua experiência no aplicativo SaaS, IaaS ou Microsoft sem nenhum custo por um tempo limitado antes de comprarem. As chamadas à ação usadas para a opção de publicação de teste são **free trial** ou **test drive**. |
| **BYOL** | Use o mercado comercial para melhorar a capacidade de descoberta e automatizar o provisionamento de sua solução e concluir a transação financeira separadamente. Os tipos de oferta BYOL são ideais para migrações em tempo integral para migrações em nuvem. O call-to-action é **Get it Now**.
| **Transact** | As ofertas da Transact são vendidas através do mercado comercial. A Microsoft é responsável pelo faturamento e cobranças. O call-to-action é **Get it Now**.|

> [!Note]
> Ao usar a opção de publicação de Transação, é importante entender as considerações de preços, cobrança, faturamento e pagamento antes de selecionar um tipo de oferta e criação de sua oferta. Para saber mais, revise o artigo [Recursos de transação de mercado comercial.](./marketplace-commercial-transaction-capabilities-and-considerations.md)

## <a name="next-steps"></a>Próximas etapas

- Depois de decidir sobre uma opção de publicação, você está pronto para [selecionar o tipo de oferta](./publisher-guide-by-offer-type.md) que será usado para apresentar sua oferta.
- Examine os requisitos de qualificação nas opções de publicação pela seção de tipo de oferta para finalizar a seleção e a configuração de sua oferta.
- Examine os padrões de publicação por vitrine eletrônica para obter exemplos de como sua solução é mapeada para uma configuração e tipo de oferta.
