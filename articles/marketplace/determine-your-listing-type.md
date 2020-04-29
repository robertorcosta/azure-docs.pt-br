---
title: Determine sua opção de publicação nas vitrines do Microsoft Commercial Marketplace
description: Este artigo descreve os critérios de qualificação e os requisitos de publicação para parceiros que tentam entender como publicar aplicativos no Microsoft AppSource e no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: e91ca0334276dbe9118eb0e40d042f598e8b3c0f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415733"
---
# <a name="determine-your-publishing-option"></a>Determinar a opção de publicação

A opção de publicação que você escolhe para sua oferta está relacionada diretamente aos requisitos de qualificação e aos benefícios do GTM do Marketplace comercial. O mais importante é que a seleção de opção de publicação e tipo de oferta define como os usuários irão interagir com sua oferta de Marketplace comercial.

Para configurar sua oferta, você precisará compreender os seguintes conceitos principais do Marketplace comercial: as opções de publicação, tipos de oferta e configuração e chamadas para ação que governarão como e onde sua oferta será apresentada nas vitrines do Marketplace comercial.

Neste artigo, você aprenderá:

- Como determinar a vitrine apropriada para sua solução
- Quais opções de publicação e chamadas de ação estão disponíveis em cada vitrine
- Quais tipos de oferta estão disponíveis para cada opção de publicação

## <a name="commercial-marketplace-publishing-options"></a>Opções de publicação do Marketplace comercial

A tabela a seguir mostra as opções de publicação para tipos de oferta no Microsoft AppSource e no Azure Marketplace.

|   | **Lista (contato)**  | **Lista (avaliação)**  | **Gratuito** | **BYOL** | **Comercializa**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Máquina Virtual** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Aplicativos do Azure (várias VMS)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **Imagem de contêiner** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Módulo do IoT Edge** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Serviços gerenciados** |  |  |  | Azure Marketplace |   |
| **Serviços de consultoria** | Ambas as vitrines |  |  |  |   |
| **Aplicativo SaaS** | Ambas as vitrines | Ambas as vitrines | Ambas as vitrines |  | Ambas as vitrines * |
| **Microsoft 365 aplicativo** | AppSource | AppSource |  |  | AppSource * *  |
| **Suplemento do Dynamics 365** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; as ofertas de aplicativo SaaS em Microsoft AppSource são atualmente apenas o cartão de crédito.

&#42;&#42; as ofertas de Microsoft 365 são gratuitas para instalação e podem ser monetizadas por meio de uma oferta de SaaS como um serviço de licenciamento. Para obter mais informações, consulte [monetizar Your seu suplemento do Office 365 por meio do Microsoft Commercial Marketplace](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="selecting-a-storefront"></a>Selecionando uma vitrine

Antes de selecionar uma opção de publicação, é importante entender os requisitos de qualificação de vitrine para soluções, aplicativos e serviços do Marketplace comercial. Cada vitrine atende aos requisitos exclusivos do cliente e destina-se a públicos específicos. Seu tipo de oferta, os recursos de transação e a categoria ou setor determinarão onde publicar sua oferta.

**Microsoft AppSource** aplicativos são soluções de linha de negócios que podem ser internas do Azure ou internas: Dynamics 365, Office 365, Power bi ou Power apps. Serviços de consultoria do AppSource são ofertas de serviços profissionais que ajudam os clientes a começarem a usar o Dynamics 365 e o Power BI.

Os aplicativos **do Azure Marketplace** são soluções técnicas de "desenvolvimento-bloco" internas ou internas para o Azure e destinadas a um público de ti ou desenvolvedor. Serviços de consultoria do Azure Marketplace são ofertas de serviços profissionais que ajudam os clientes a começarem a usar o Azure, ou a aprenderem a usá-lo mais rápido.

>[!Note]
>Listagem cruzada (somente para aplicativos SaaS): quando uma oferta de lista ou baseada em avaliação atende aos critérios para um público de usuário comercial e técnico, sua oferta será listada em ambas as vitrines. Saiba mais sobre as opções de publicação abaixo.

## <a name="choose-a-publishing-option"></a>Selecione uma opção de publicação

As opções de publicação disponíveis oferecem o envolvimento do cliente diferenciado, oferecendo acesso a benefícios de compartilhamento de leads e do [Marketplace comercial](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits). Observe as chamadas de ação que correspondem à opção de publicação:

| **Opção de publicação**    | **Descrição**  |
| :------------------- | :-------------------|
| **Lista** | Listagem simples de seu aplicativo ou serviço que permite que um usuário do Marketplace comercial solicite que você se conecte ao cliente por meio da chamada a ação **entre em contato comigo** . |
| **Avaliação** | Use o Marketplace comercial para aprimorar a capacidade de descoberta e automatizar o provisionamento da experiência de avaliação da solução, permitindo que os usuários potenciais usem sua experiência no aplicativo SaaS, IaaS ou Microsoft sem custos por um tempo limitado antes de comprarem. As chamadas para a ação usadas para a opção de publicação de avaliação são a **avaliação gratuita** ou o **Test Drive**. |
| **BYOL** | Use o Marketplace comercial para aprimorar a capacidade de descoberta e automatizar o provisionamento de sua solução e concluir a transação financeira separadamente. Os tipos de oferta BYOL são ideais para migrações de local para nuvem. O plano de ação é obtê- **lo agora**.
| **Comercializa** | As ofertas do Transact são vendidas por meio do Marketplace comercial. A Microsoft é responsável por cobranças e coleções. O plano de ação é obtê- **lo agora**.|

> [!Note]
> Ao usar a opção de publicação de Transação, é importante entender as considerações de preços, cobrança, faturamento e pagamento antes de selecionar um tipo de oferta e criação de sua oferta. Para saber mais, examine o artigo [recursos de transação do Marketplace comercial](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="next-steps"></a>Próximas etapas

- Depois de decidir sobre uma opção de publicação, você estará pronto para [selecionar o tipo de oferta](./publisher-guide-by-offer-type.md) que será usado para apresentar sua oferta.
- Examine os requisitos de qualificação nas opções de publicação pela seção de tipo de oferta para finalizar a seleção e a configuração de sua oferta.
- Examine os padrões de publicação por vitrine eletrônica para obter exemplos de como sua solução é mapeada para uma configuração e tipo de oferta.
