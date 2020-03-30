---
title: Crie & publique documentação de login único para sua aplicação
description: Orientação para fornecedores independentes de software sobre a integração com o Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb223ec8ab7b5c053136c78d3b4ca30ad4da4e18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232277"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>Crie e publique documentação de login único para sua aplicação   

## <a name="documentation-on-your-site"></a>Documentação em seu site

A facilidade de adoção é um fator significativo nas decisões de software corporativo. A documentação clara e fácil de seguir suporta seus clientes em sua jornada de adoção e reduz os custos de suporte. Trabalhando com milhares de fornecedores de software, a Microsoft viu o que funciona.

Recomendamos que sua documentação em seu site inclua no mínimo os seguintes itens.

* Introdução à sua funcionalidade SSO

  * Protocolos compatíveis

  * Versão e SKU

  * Lista de provedores de identidade suportados com links de documentação

* Informações de licenciamento para sua aplicação

* Controle de acesso baseado em função para configurar OSS

* Etapas de configuração do SSO

  * Elementos de configuração de IU para SAML com valores esperados do provedor

  * Informações do provedor de serviços a serem passadas aos provedores de identidade

* Se OIDC/OAuth

  * Lista de permissões necessárias para consentimento com justificativas comerciais

* Passos de teste para usuários piloto

* Solução de problemas de informações, incluindo códigos de erro e mensagens

* Mecanismos de suporte para clientes

## <a name="documentation-on-the-microsoft-site"></a>Documentação no Site da Microsoft

Quando você listar seu aplicativo com a Azure Active Directory Application Gallery, que também publica seu aplicativo no Azure Marketplace, a Microsoft gerará documentação para nossos clientes mútuos explicando o processo passo a passo. Você pode ver um exemplo [aqui.](https://aka.ms/appstutorial) Essa documentação é criada com base no seu envio à galeria, e você pode atualizá-la facilmente se fizer alterações no seu aplicativo usando sua conta do GitHub.

## <a name="next-steps"></a>Próximas etapas

[Liste seu aplicativo na Galeria de Aplicativos AD do Azure](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)