---
title: Operações de Microsoft Graph com suporte
titleSuffix: Azure AD B2C
description: Um índice das operações de Microsoft Graph com suporte para o gerenciamento de recursos de Azure AD B2C, incluindo usuários, fluxos de usuário, provedores de identidade, políticas personalizadas, chaves de política e muito mais.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9db46d13c9a798204958a7c295df9cca169fc08f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954028"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Microsoft Graph operações disponíveis para Azure AD B2C

As operações de API de Microsoft Graph a seguir têm suporte para o gerenciamento de recursos de Azure AD B2C, incluindo usuários, provedores de identidade, fluxos de usuário, políticas personalizadas e chaves de política.

Cada link nas seções a seguir visa a página correspondente dentro do Microsoft Graph referência de API para essa operação.

## <a name="user-management"></a>Gerenciamento de Usuários

- [Listar usuários](/graph/api/user-list)
- [Criar um usuário do consumidor](/graph/api/user-post-users)
- [Obter um usuário](/graph/api/user-get)
- [Atualizar um usuário](/graph/api/user-update)
- [Excluir um usuário](/graph/api/user-delete)

Para obter mais informações sobre como gerenciar contas de usuário do Azure AD B2C com a API do Microsoft Graph, consulte [gerenciar contas de usuário do Azure ad B2C com o Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="user-phone-number-management"></a>Gerenciamento de número de telefone do usuário

- [Adicionar](https://docs.microsoft.com/graph/api/authentication-post-phonemethods)
- [Obter](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-get)
- [Atualização](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-update)
- [Delete (excluir)](https://docs.microsoft.com/graph/api/phoneauthenticationmethod-delete)

Para obter mais informações sobre como gerenciar o número de telefone de entrada do usuário com a API do Microsoft Graph, consulte [métodos de autenticação do B2C](https://docs.microsoft.com/graph/api/resources/b2cauthenticationmethodspolicy).

## <a name="identity-providers-user-flow"></a>Provedores de identidade (fluxo do usuário)

Gerencie os provedores de identidade disponíveis para seus fluxos de usuário em seu locatário Azure AD B2C.

- [Listar provedores de identidade registrados no locatário do Azure AD B2C](/graph/api/identityprovider-list)
- [Criar um provedor de identidade](/graph/api/identityprovider-post-identityproviders)
- [Obter um provedor de identidade](/graph/api/identityprovider-get)
- [Atualizar provedor de identidade](/graph/api/identityprovider-update)
- [Excluir um provedor de identidade](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Fluxo de usuário

Configure políticas predefinidas para inscrição, entrada, inscrição combinada e entrada, redefinição de senha e atualização de perfil.

- [Listar fluxos de usuário](/graph/api/identityuserflow-list)
- [Criar um fluxo de usuário](/graph/api/identityuserflow-post-userflows)
- [Obter um fluxo de usuário](/graph/api/identityuserflow-get)
- [Excluir um fluxo de usuário](/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Políticas personalizadas

As operações a seguir permitem que você gerencie suas políticas de estrutura de confiança Azure AD B2C, conhecidas como [políticas personalizadas](custom-policy-overview.md).

- [Listar todas as políticas de estrutura de confiança configuradas em um locatário](/graph/api/trustframework-list-trustframeworkpolicies)
- [Criar política de estrutura confiável](/graph/api/trustframework-post-trustframeworkpolicy)
- [Ler propriedades de uma política de estrutura de confiança existente](/graph/api/trustframeworkpolicy-get)
- [Atualize ou crie a política de estrutura confiável.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Excluir uma política de estrutura de confiança existente](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Chaves de política

A estrutura de experiência de identidade armazena os segredos referenciados em uma política personalizada para estabelecer a confiança entre os componentes. Esses segredos podem ser chaves/valores simétricos ou assimétricos. No portal do Azure, essas entidades são mostradas como **chaves de política**.

O recurso de nível superior para chaves de política na API de Microsoft Graph é o conjunto de [chaves de estrutura confiável](/graph/api/resources/trustframeworkkeyset). Cada conjunto de **chaves** contém pelo menos uma **chave**. Para criar uma chave, primeiro crie um conjunto de chaves vazio e, em seguida, gere uma chave no conjunto de chaves. Você pode criar um segredo manual, carregar um certificado ou uma chave PKCS12. A chave pode ser um segredo gerado, uma cadeia de caracteres que você define (como o segredo do aplicativo do Facebook) ou um certificado que você carrega. Se um conjunto de chaves tiver várias chaves, somente uma das chaves estará ativa.

### <a name="trust-framework-policy-keyset"></a>Conjunto de chaves de política de estrutura confiável

- [Listar os conjuntos de keyframework de confiança](/graph/api/trustframework-list-keysets)
- [Criar um conjunto de conjuntos de estrutura de confiança](/graph/api/trustframework-post-keysets)
- [Obter um conjunto de chaves](/graph/api/trustframeworkkeyset-get)
- [Atualizar conjuntos de keyframework de confiança](/graph/api/trustframeworkkeyset-update)
- [Excluir um conjunto de conjuntos de estrutura de confiança](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Chave de política de estrutura confiável

- [Obter a chave atualmente ativa no conjunto de chaves](/graph/api/trustframeworkkeyset-getactivekey)
- [Gerar uma chave no conjunto de chaves](/graph/api/trustframeworkkeyset-generatekey)
- [Carregar um segredo baseado em cadeia de caracteres](/graph/api/trustframeworkkeyset-uploadsecret)
- [Carregar um certificado X. 509](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Carregar um certificado de formato PKCS12](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Aplicativos

- [Listar aplicativos](/graph/api/application-list)
- [Criar um aplicativo](/graph/api/resources/application)
- [Atualizar o aplicativo](/graph/api/application-update)
- [Criar servicePrincipalName](/graph/api/resources/serviceprincipal)
- [Criar concessão de oauth2Permission](/graph/api/resources/oauth2permissiongrant)
- [Excluir aplicativo](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Propriedades de extensão do aplicativo

- [Listar Propriedades de extensão](/graph/api/application-list-extensionproperty)

O Azure AD B2C fornece um diretório que pode conter 100 atributos personalizados por usuário. Para fluxos de usuário, essas propriedades de extensão são [gerenciadas usando o portal do Azure](custom-policy-custom-attributes.md). Para políticas personalizadas, Azure AD B2C cria a propriedade para você, na primeira vez em que a política grava um valor na propriedade de extensão.

## <a name="audit-logs"></a>Logs de auditoria

- [Listar logs de auditoria](/graph/api/directoryaudit-list)

Para obter mais informações sobre como acessar Azure AD B2C logs de auditoria com a API do Microsoft Graph, consulte [Acessando Azure ad B2C logs de auditoria](view-audit-logs.md).