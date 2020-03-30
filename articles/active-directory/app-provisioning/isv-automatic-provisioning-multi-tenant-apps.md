---
title: Habilite o provisionamento automático do usuário para aplicativos multilocatários - Azure AD
description: Um guia para fornecedores independentes de software para habilitar o provisionamento automatizado
services: active-directory
documentationcenter: azure
author: BarbaraSelden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: baselden
ms.reviewer: zhchia
ms.collection: active-directory
ms.openlocfilehash: 93e1d879f69a95fe7472ce530e0e9f38f3480f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522386"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>Habilite o provisionamento automático do usuário para o seu aplicativo multi-inquilino

O provisionamento automático do usuário é o processo de automatização da criação, manutenção e remoção de identidades de usuários em sistemas de destino, como seus aplicativos de software como serviço.

## <a name="why-enable-automatic-user-provisioning"></a>Por que ativar o provisionamento automático do usuário?

Aplicativos que requerem que um registro de usuário esteja presente no aplicativo antes do primeiro login do usuário exigem o provisionamento do usuário. Existem benefícios para você como prestador de serviços e benefícios para seus clientes.

### <a name="benefits-to-you-as-the-service-provider"></a>Benefícios para você como prestador de serviços

* Aumente a segurança do seu aplicativo usando a plataforma de identidade microsoft.

* Reduza o esforço real e percebido do cliente para adotar sua aplicação.

* Reduza seus custos na integração com vários provedores de identidade (IdPs) para provisionamento automático do usuário usando o provisionamento baseado em System for Cross-Domain Identity Management (SCIM).

* Reduza os custos de suporte fornecendo logs ricos para ajudar os clientes a solucionar problemas de provisionamento de usuários.

* Aumente a visibilidade do seu aplicativo na [galeria de aplicativos Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps).

* Obtenha uma listagem priorizada na página Tutoriais do aplicativo.

### <a name="benefits-to-your-customers"></a>Benefícios para seus clientes

* Aumente a segurança removendo automaticamente o acesso ao seu aplicativo para usuários que mudam de função ou deixam a organização para o seu aplicativo.

* Simplifique o gerenciamento do usuário para o seu aplicativo, evitando erros humanos e trabalhos repetitivos associados ao provisionamento manual.

* Reduza os custos de hospedagem e manutenção de soluções de provisionamento personalizadas.

## <a name="choose-a-provisioning-method"></a>Escolher um método de provisionamento

O Azure AD fornece vários caminhos de integração para permitir o provisionamento automático do usuário para o seu aplicativo.

* O [Azure AD Provisioning Service](../app-provisioning/user-provisioning.md) gerencia o provisionamento e o desprovisionamento de usuários do Azure AD para seu aplicativo (provisionamento de saída) e do seu aplicativo para o Azure AD (provisionamento de entrada). O serviço se conecta aos pontos finais de Gerenciamento de Identidade (SCIM) do Sistema para Gerenciamento de Identidade Entre Domínios (SCIM) fornecidos pelo seu aplicativo.

* Ao usar o [Microsoft Graph,](https://docs.microsoft.com/graph/)seu aplicativo gerencia o provisionamento de entrada e saída de usuários e grupos do Azure AD para o seu aplicativo consultando a API do Microsoft Graph.

* O provisionamento do usuário SAML JIT (Security Assertion Markup Language Just in Time) (SAML JIT) pode ser ativado se o seu aplicativo estiver usando o SAML para federação. Ele usa informações de sinistros enviadas no token SAML para provisionar os usuários.

Para ajudar a determinar qual opção de integração usar para seu aplicativo, consulte a tabela de comparação de alto nível e, em seguida, consulte as informações mais detalhadas sobre cada opção.

| Recursos habilitados ou aprimorados pelo Provisionamento Automático| Serviço de Provisionamento Ad do Azure (SCIM 2.0)| API do Gráfico Microsoft (OData v4.0)| SAML JIT |
|---|---|---|---|
| Gerenciamento de usuários e grupos no Azure AD| √| √| Somente para usuários |
| Gerenciar usuários e grupos sincronizados a partir do Active Directory no local| √*| √*| Somente para usuário* |
| Acessar dados além de usuários e grupos durante o provisionamento acesso a dados O365 (Teams, SharePoint, E-mail, Calendário, Documentos, etc.)| X+| √| X |
| Criar, ler e atualizar usuários com base nas regras de negócios| √| √| √ |
| Excluir usuários com base nas regras de negócios| √| √| X |
| Gerenciar o provisionamento automático do usuário para todos os aplicativos do portal Azure| √| X| √ |
| Suporte a vários provedores de identidade| √| X| √ |
| Suporte a contas de hóspedes (B2B)| √| √| √ |
| Suporte a contas não-corporativas (B2C)| X| √| √ |

<sup>*</sup>– A configuração do Azure AD Connect é necessária para sincronizar usuários de AD para Azure AD.  
<sup>+</sup >– O uso do SCIM para provisionamento não o impede de integrar seu aplicativo com o MIcrosoft Graph para outros fins.

## <a name="azure-ad-provisioning-service-scim"></a>Serviço de Provisionamento AD do Azure (SCIM)

Os serviços de provisionamento Azure AD utilizam [o SCIM](https://aka.ms/SCIMOverview), um padrão do setor para provisionamento suportado por muitos provedores de identidade (IdPs), bem como aplicativos (por exemplo, Slack, G Suite, Dropbox). Recomendamos que você use o serviço de provisionamento AD do Azure se quiser suportar IdPs além do Azure AD, já que qualquer IdP compatível com SCIM pode se conectar ao seu ponto final SCIM. Construindo um ponto final simples /Usuário, você pode ativar o provisionamento sem ter que manter seu próprio mecanismo de sincronização. 

Para obter mais informações sobre como os usuários do Azure AD Provisioning Service SCIM, consulte: 

* [Saiba mais sobre o padrão SCIM](https://aka.ms/SCIMOverview)

* [Usar o SCIM (Sistema de Gerenciamento de Identidades entre Domínios) para provisionar automaticamente usuários e grupos do Azure Active Directory para aplicativos](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Entenda a implementação do Azure AD SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>Gráfico da Microsoft para provisionamento

Quando você usa o Microsoft Graph para provisionamento, você tem acesso a todos os dados de usuário ricos disponíveis no Graph. Além dos detalhes de usuários e grupos, você também pode obter informações adicionais como funções do usuário, relatórios gerenciadores e diretos, dispositivos de propriedade e registrados, e centenas de outras peças de dados disponíveis no [Microsoft Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0). 

Mais de 15 milhões de organizações e 90% das empresas da Fortune 500 usam o Azure AD enquanto assinam serviços de nuvem da Microsoft como Office 365, Microsoft Azure, Enterprise Mobility Suite ou Microsoft 365. Você pode usar o Microsoft Graph para integrar seu aplicativo com fluxos de trabalho administrativos, como onboarding (e rescisão de funcionários), manutenção de perfil e muito mais. 

Saiba mais sobre como usar o Microsoft Graph para provisionamento:

* [Página inicial do Microsoft Graph](https://developer.microsoft.com/graph)

* [Visão geral do Microsoft Graph](https://docs.microsoft.com/graph/overview)

* [Visão geral do Microsoft Graph Auth](https://docs.microsoft.com/graph/auth/)

* [Começando com o Microsoft Graph](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>Usando SAML JIT para provisionamento

Se você quiser provisionar os usuários apenas no primeiro login em seu aplicativo, e não precisar desprovisionar automaticamente os usuários, o SAML JIT é uma opção. Seu aplicativo deve suportar o SAML 2.0 como um protocolo de federação para usar o SAML JIT.

O SAML JIT usa as informações de sinistros no token SAML para criar e atualizar as informações do usuário no aplicativo. Os clientes podem configurar essas reivindicações necessárias no aplicativo Azure AD conforme necessário. Às vezes, o provisionamento JIT precisa ser ativado do lado do aplicativo para que o cliente possa usar esse recurso. O SAML JIT é útil para criar e atualizar usuários, mas não pode excluir ou desativar os usuários no aplicativo.

## <a name="next-steps"></a>Próximas etapas

* [Habilite o login único para sua aplicação](../manage-apps/isv-sso-content.md)

* [Envie sua lista de aplicativos](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) e faça parceria com a Microsoft para criar documentação no site da Microsoft.

* [Junte-se à Microsoft Partner Network (gratuitamente) e crie seu plano de ir para o mercado](https://partner.microsoft.com/en-us/explore/commercial).
