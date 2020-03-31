---
title: Operações do Microsoft Graph suportadas
titleSuffix: Azure AD B2C
description: Um índice das operações do Microsoft Graph suportado para o gerenciamento de recursos Azure AD B2C, incluindo usuários, fluxos de usuários, provedores de identidade, políticas personalizadas, chaves de políticas e muito mais.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 27fe1a41365d96a4179f8c659b63dc22c7b9fc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184241"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Operações do Microsoft Graph disponíveis para Azure AD B2C

As seguintes operações de API do Microsoft Graph são suportadas para o gerenciamento de recursos Azure AD B2C, incluindo usuários, provedores de identidade, fluxos de usuários, políticas personalizadas e chaves de diretiva.

Cada link nas seções a seguir tem como alvo a página correspondente na referência da API do Microsoft Graph para essa operação.

## <a name="user-management"></a>Gerenciamento de Usuários

- [Listar usuários](https://docs.microsoft.com/graph/api/user-list)
- [Criar um usuário consumidor](https://docs.microsoft.com/graph/api/user-post-users)
- [Obter um usuário](https://docs.microsoft.com/graph/api/user-get)
- [Atualize um usuário](https://docs.microsoft.com/graph/api/user-update)
- [Excluir um usuário](https://docs.microsoft.com/graph/api/user-delete)

Para obter mais informações sobre como gerenciar contas de usuários Azure AD B2C com a API do Microsoft Graph, consulte [Gerenciar contas de usuário Azure AD B2C com o Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="identity-providers-user-flow"></a>Provedores de identidade (fluxo de usuário)

Gerencie os provedores de identidade disponíveis para seus fluxos de usuário no seu inquilino Azure AD B2C.

- [Listar provedores de identidade registrados no inquilino Azure AD B2C](https://docs.microsoft.com/graph/api/identityprovider-list)
- [Crie um provedor de identidade](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [Obtenha um provedor de identidade](https://docs.microsoft.com/graph/api/identityprovider-get)
- [Atualizar provedor de identidade](https://docs.microsoft.com/graph/api/identityprovider-update)
- [Exclua um provedor de identidade](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Fluxo de usuário

Configure políticas pré-construídas para inscrição, login, login combinado e login, redefinição de senha e atualização de perfil.

- [Listar fluxos de usuário](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [Criar um fluxo de usuário](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [Obtenha um fluxo de usuário](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [Exclua um fluxo de usuário](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Políticas personalizadas

As operações a seguir permitem que você gerencie suas políticas azure AD B2C Trust Framework, conhecidas como [políticas personalizadas](custom-policy-overview.md).

- [Liste todas as políticas de estrutura de confiança configuradas em um inquilino](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [Criar política de estrutura de confiança](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [Leia propriedades de uma política de estrutura de confiança existente](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [Atualize ou crie uma política de estrutura de confiança.](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [Exclua uma política de estrutura de confiança existente](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Chaves de política

O Identity Experience Framework armazena os segredos mencionados em uma política personalizada para estabelecer confiança entre os componentes. Esses segredos podem ser chaves/valores simétricos ou assimétricos. No portal Azure, essas entidades são mostradas como **chaves de política**.

O recurso de alto nível para chaves de diretiva na API do Gráfico do Microsoft é o [conjunto de chaves de estrutura confiável](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset). Cada **conjunto de chaves** contém pelo menos uma **chave**. Para criar uma chave, primeiro crie um conjunto de chaves vazio e, em seguida, gere uma chave no conjunto de chaves. Você pode criar um segredo manual, carregar um certificado ou uma chave PKCS12. A chave pode ser um segredo gerado, uma seqüência que você define (como o segredo do aplicativo do Facebook) ou um certificado que você carrega. Se um conjunto de chaves tiver várias teclas, apenas uma das teclas está ativa.

### <a name="trust-framework-policy-keyset"></a>Conjunto de chaves de diretiva do Trust Framework

- [Liste os conjuntos de chaves do quadro de confiança](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [Crie um conjunto de chaves de estrutura de confiança](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [Obter um conjunto de chaves](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [Atualize um conjunto de chaves de estrutura de confiança](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [Exclua um conjunto de chaves de estrutura de confiança](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Chave da política do Trust Framework

- [Obtenha a chave ativa no conjunto de chaves](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [Gerar uma chave no conjunto de chaves](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [Carregue um segredo baseado em string](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [Carregar um certificado X.509](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [Carregar um certificado de formato PKCS12](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Aplicativos

- [Listar aplicativos](https://docs.microsoft.com/graph/api/application-list)
- [Criar um aplicativo](https://docs.microsoft.com/graph/api/resources/application)
- [Atualizar aplicativo](https://docs.microsoft.com/graph/api/application-update)
- [Criar serviçoPrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [Criar subvenção de permissão oauth2](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [Excluir aplicativo](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>Propriedades de extensão de aplicativos

- [Listar propriedades de extensão](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

O Azure AD B2C fornece um diretório que pode conter 100 atributos personalizados por usuário. Para fluxos de usuário, essas propriedades de extensão são [gerenciadas usando o portal Azure](custom-policy-custom-attributes.md). Para políticas personalizadas, o Azure AD B2C cria a propriedade para você na primeira vez que a diretiva escreve um valor para a propriedade de extensão.

## <a name="audit-logs"></a>Logs de auditoria

- [Registros de auditoria de listas](https://docs.microsoft.com/graph/api/directoryaudit-list)

Para obter mais informações sobre como acessar os logs de auditoria AD B2C do Azure com a API do Gráfico Microsoft, consulte [Acessando os registros de auditoria do Azure AD B2C](view-audit-logs.md).
