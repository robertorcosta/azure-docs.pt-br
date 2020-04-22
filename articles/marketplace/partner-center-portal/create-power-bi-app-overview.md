---
title: Visão geral da criação de aplicativos power BI - Azure Marketplace
description: Este artigo descreve as etapas de alto nível para a publicação de um aplicativo Power BI para o Microsoft AppSource. Os requisitos técnicos e comerciais que seu aplicativo Power BI deve atender para serem publicados no mercado comercial também são fornecidos.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 8f050a43cbdf4ab29df55cd5526eb231c301b271
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732670"
---
# <a name="power-bi-app-creation-overview"></a>Visão geral da criação de aplicativos do Power BI

> [!IMPORTANT]
> Estamos mudando o gerenciamento das ofertas do seu aplicativo Power BI do Portal de Parceiros da Nuvem para o Partner Center. Até que suas ofertas sejam migradas, siga as instruções na [oferta de aplicativo Power BI](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-power-bi-offer) para o Cloud Partner Portal para gerenciar suas ofertas.

Este artigo explica como publicar um aplicativo Power BI para o Microsoft [AppSource](https://appsource.microsoft.com/). Um aplicativo Power BI embala conteúdo personalizável, incluindo conjuntos de dados, relatórios e dashboards. Em seguida, você pode usar o aplicativo com outras plataformas de Power BI usando o AppSource, realizar os ajustes e personalizações permitidos pelo desenvolvedor e conectá-lo aos seus próprios dados.

## <a name="publishing-benefits"></a>Benefícios da publicação

Benefícios da publicação no mercado comercial:

- Promova sua empresa usando a marca Microsoft.
- Potencialmente atingir mais de 100 milhões de usuários do Office 365 e Dynamics 365 no AppSource e mais de 200.000 organizações através do Azure Marketplace.
- Receba leads de alta qualidade desses marketplaces.
- Tenha seus serviços promovidos pelas equipes de campo e televendas da Microsoft.

## <a name="overview"></a>Visão geral

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="visão geral das etapas para publicar um aplicativo Power BI" border="false":::

Estas são as principais etapas de publicação:

1. Crie seu aplicativo no Power BI. Você receberá um link de instalação de pacote, que é o principal ativo técnico para a oferta. Envie o pacote de teste para a pré-produção antes de criar a oferta no Partner Center. Para obter detalhes, veja [quais são os aplicativos Power BI?](https://docs.microsoft.com/power-bi/service-template-apps-overview).
2. Adicione os materiais de marketing, como nome oficial, descrição e logotipos.
3. Inclua os documentos legais e de suporte da oferta, como termos de uso, política de privacidade, política de suporte e ajuda do usuário.
4. Crie a oferta: Use o Partner Center para editar os detalhes, incluindo a descrição da oferta, materiais de marketing, informações legais, informações de suporte e especificações de ativos.
5. Submeta-o para publicação.
6. Monitore o processo no Partner Center, onde a equipe de onboarding AppSource testa, valida e certifica seu aplicativo.
7. Depois de certificado, revise o aplicativo em seu ambiente de teste e libere-o. Isso irá listá-lo no AppSource (ele "vai ao ar").
8. Em Power BI, envie o pacote para a produção. Para obter detalhes, consulte [Gerenciar a versão do aplicativo Power BI](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release).

## <a name="before-you-begin"></a>Antes de começar

Revise os links abaixo, que fornecem modelos, dicas e amostras.

- [Criar um aplicativo do Power BI](https://docs.microsoft.com/power-bi/service-template-apps-create)
- [Dicas para criar um aplicativo Power BI](https://docs.microsoft.com/power-bi/service-template-apps-tips)
- [Amostras](https://docs.microsoft.com/power-bi/service-template-apps-samples)

## <a name="requirements"></a>Requisitos

Para ser publicado no mercado comercial, sua oferta de aplicativo Power BI deve atender aos seguintes requisitos técnicos e de negócios.

### <a name="technical-requirements"></a>Requisitos técnicos

O principal ativo técnico que você vai precisar é um [aplicativo Power BI](https://go.microsoft.com/fwlink/?linkid=2028636). Esta é uma coleção de conjuntos de dados primários, relatórios ou dashboards. Ele também inclui serviços conectados opcionais e conjuntos de dados incorporados, anteriormente conhecidos como um [pacote de conteúdo](https://docs.microsoft.com/power-bi/service-organizational-content-pack-introduction). Para obter mais informações sobre o desenvolvimento desse tipo de aplicativo, consulte [Quais são os aplicativos Power BI?](https://go.microsoft.com/fwlink/?linkid=2028636)

#### <a name="get-an-installation-web-address"></a>Obtenha um endereço web de instalação

Você só pode construir um aplicativo Power BI dentro do ambiente [Power BI.](https://powerbi.microsoft.com/)

1. Faça login com uma [licença Power BI Pro](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro).
2. Crie e teste seu aplicativo no Power BI.
3. Quando você receber o endereço web de instalação do aplicativo, adicione-o à página **Configuração Técnica** na Central de Parceiros.

Depois que seu aplicativo for criado e testado no Power BI, salve o endereço web de instalação do aplicativo, pois você precisará dele para [criar uma oferta de aplicativo Power BI](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer).

### <a name="business-requirements"></a>Requisitos de negócios

Os requisitos do negócio incluem obrigações processuais, contratuais e legais. Você deve:

- Seja um editor de mercado comercial registrado. Se você não estiver registrado, siga os passos em [Become a Commercial Marketplace Publisher](https://docs.microsoft.com/azure/marketplace/become-publisher).
- Forneça conteúdo que atenda aos critérios para sua oferta ser listada no AppSource. Para obter mais informações, consulte [Ter um aplicativo para listar no AppSource? Aqui está como.](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how)
- Concorde e siga a [Declaração de Privacidade](https://privacy.microsoft.com/privacystatement)da Microsoft .

## <a name="next-steps"></a>Próximas etapas

- [Crie uma oferta de aplicativo Power BI no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer)