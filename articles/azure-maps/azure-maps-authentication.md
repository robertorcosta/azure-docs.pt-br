---
title: Métodos de autenticação | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre o Azure Active Directory (Azure AD) e a autenticação de chave compartilhada. Ambos são usados para serviços do Microsoft Azure Maps. Saiba como obter a chave de assinatura do Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 73c0d9f76ad92d0ef7ed0f518de5ab1f8b174c9d
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189777"
---
# <a name="authentication-with-azure-maps"></a>Autenticação com Azure Mapas

O mapas do Azure dá suporte a duas maneiras de autenticar solicitações: autenticação de chave compartilhada e autenticação de Azure Active Directory. Este artigo explica esses métodos de autenticação para ajudar a orientar sua implementação dos serviços do Azure Maps.

## <a name="shared-key-authentication"></a>Autenticação de Chave Compartilhada

 As chaves primária e secundária são geradas depois que a conta do Azure Maps é criada. Você é incentivado a usar a chave primária como a chave de assinatura ao chamar o Azure Maps usando a autenticação de chave compartilhada. A autenticação de chave compartilhada passa uma chave gerada por uma conta do Azure Maps para um serviço do Azure Maps. Para cada solicitação para os serviços do Azure Maps, adicione a *chave de assinatura* como um parâmetro à URL. A chave secundária pode ser usada em cenários como alterações de chave sem interrupção.  

Para obter informações sobre como exibir suas chaves no portal do Azure, consulte [gerenciar a autenticação](https://aka.ms/amauthdetails).

> [!Tip]
> Recomendamos a regeneração frequente das chaves. Você recebe duas chaves, para que você possa manter conexões com uma chave ao gerar novamente a outra. Ao regenerar suas chaves, você precisa atualizar todos os aplicativos que acessam sua conta, com as novas chaves.

## <a name="authentication-with-azure-active-directory-preview"></a>Autenticação com o Azure Active Directory (versão prévia)

O Azure Maps agora oferece autenticação de solicitações para os serviços do Azure Maps usando o [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). O Azure AD fornece autenticação baseada em identidade, incluindo [RBAC (controle de acesso baseado em função)](https://docs.microsoft.com/azure/role-based-access-control/overview). O RBAC é usado para conceder acesso em nível de usuário, nível de grupo ou de aplicativo aos recursos do Azure Maps. As seções a seguir discutem conceitos e componentes da integração do Azure Maps com o Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Autenticação com tokens de acesso OAuth

Os Azure Mapas aceitam tokens de acesso **OAuth 2.0** para locatários do Azure AD associados à assinatura do Azure que contêm uma conta dos Azure Mapas. O Azure Maps também aceita tokens para:

* Usuários do Azure AD
* Aplicativos de parceiros que usam permissões delegadas por usuários
* Identidades gerenciadas dos recursos do Azure

Azure Mapas gera um *identificador exclusivo (ID do cliente)* para cada conta do Azure Mapas. Você pode solicitar tokens do Azure AD ao combinar essa ID de cliente com parâmetros adicionais. Para solicitar um token, especifique os valores na tabela a seguir com base em seu ambiente do Azure.

| Azure Environment   | Ponto de extremidade de token do Azure AD |
| --------------------|-------------------------|
| Público do Azure        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Para obter mais informações sobre como configurar o Azure AD e solicitar tokens para os Azure Mapas, confira [Como gerenciar a autenticação nos Azure Mapas](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Para obter informações gerais sobre como solicitar tokens do Azure AD, consulte [O que é autenticação?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Solicite recursos dos Azure Mapas com tokens OAuth

Depois que o Azure AD recebe um token, o Azure Maps envia uma solicitação com o seguinte conjunto de cabeçalhos de solicitação necessários:

| Cabeçalho da solicitação    |    Valor    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Autorização     | Portador eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` é o GUID baseado na conta dos Azure Mapas exibido na página de autenticação dos Azure Mapas.

Aqui está um exemplo de uma solicitação de rota do Azure Mapas que usa um token OAuth:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Para obter informações sobre como exibir a ID do cliente, consulte [Exibir detalhes de autenticação](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Controlar o acesso com RBAC

No Azure AD, use o RBAC para controlar o acesso a recursos protegidos. Configure sua conta do Azure Maps e Registre seu locatário do Azure AD do Azure Maps. O Azure Maps dá suporte ao controle de acesso de leitura para usuários individuais do Azure AD, grupos, aplicativos, recursos do Azure e serviços do Azure por meio de identidades gerenciadas para recursos do Azure. Na página do portal do Azure Maps, você pode configurar o RBAC para suas funções escolhidas.

![Leitor de dados do Azure Mapas (versão prévia)](./media/azure-maps-authentication/concept.png)

Para obter informações sobre como exibir as configurações do RBAC, confira [Como configurar o RBAC para os Azure Mapas](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Identidades gerenciadas para recursos do Azure e os Azure Mapas

[Identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) fornecem serviços do Azure com uma identidade gerenciada automaticamente, que pode ser autorizada a acessar os serviços do Azure Maps. Alguns exemplos de identidades gerenciadas incluem: Azure App Service, Azure Functions e máquinas virtuais do Azure.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como autenticar um aplicativo com o Azure AD e os Azure Mapas, confira [Como gerenciar a autenticação no Azure Mapas](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Para saber mais sobre como autenticar os Azure Mapas, o Controle de Mapeamento e o Azure AD, confira [Use o Controle de Mapeamento dos Azure Mapas](https://aka.ms/amaadmc).
