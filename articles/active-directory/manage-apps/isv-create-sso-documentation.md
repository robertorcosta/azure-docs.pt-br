---
title: Criar & publicar a documentação de logon único para seu aplicativo
description: Diretrizes para fornecedores independentes de software na integração com o Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c758e90548dd22b5abfb731f674f83dfbde9819
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85955974"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>Criar e publicar a documentação de logon único para seu aplicativo   

## <a name="documentation-on-your-site"></a>Documentação no seu site

A facilidade de adoção é um fator significativo nas decisões de software empresarial. A documentação clara e fácil de seguir dá suporte a seus clientes em sua jornada de adoção e reduz os custos de suporte. Trabalhando com milhares de fornecedores de software, a Microsoft observou o que funciona.

É recomendável que sua documentação em seu site, no mínimo, inclua os itens a seguir.

* Introdução à sua funcionalidade de SSO

  * Protocolos compatíveis

  * Versão e SKU

  * Lista de provedores de identidade com suporte com links de documentação

* Informações de licenciamento para seu aplicativo

* Controle de acesso baseado em função para configurar o SSO

* Etapas de configuração de SSO

  * Elementos de configuração da interface do usuário para SAML com valores esperados do provedor

  * Informações do provedor de serviços a serem passadas para provedores de identidade

* Se OIDC/OAuth

  * Lista de permissões necessárias para o consentimento com justificativas de negócios

* Etapas de teste para usuários piloto

* Informações de solução de problemas, incluindo códigos de erro e mensagens

* Mecanismos de suporte para clientes

## <a name="documentation-on-the-microsoft-site"></a>Documentação no site da Microsoft

Quando você listar seu aplicativo com a Galeria de aplicativos Azure Active Directory, que também publica seu aplicativo no Azure Marketplace, a Microsoft gerará documentação para nossos clientes mútuos explicando o processo passo a passo. Você pode ver um exemplo [aqui](https://aka.ms/appstutorial). Esta documentação é criada com base no seu envio para a galeria e você pode atualizá-la facilmente se fizer alterações em seu aplicativo usando sua conta do GitHub.

## <a name="next-steps"></a>Próximas etapas

[Listar seu aplicativo na Galeria de aplicativos do Azure AD](https://docs.microsoft.com/Azure/active-directory/develop/howto-app-gallery-listing)
