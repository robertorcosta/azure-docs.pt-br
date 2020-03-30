---
title: Métodos de autenticação | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre o Azure Active Directory (Azure AD) e a autenticação de Chaves Compartilhadas. Ambos são usados para serviços do Microsoft Azure Maps. Saiba como obter a chave de assinatura do Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 21d29cba85adfc147ec9deb6ab362a5da943bf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335695"
---
# <a name="authentication-with-azure-maps"></a>Autenticação com Azure Mapas

O Azure Maps suporta duas maneiras de autenticar solicitações: autenticação de tecla compartilhada e autenticação do Diretório Ativo do Azure. Este artigo explica esses métodos de autenticação para ajudar a orientar sua implementação dos serviços do Azure Maps.

> [!NOTE]
> Para melhorar a comunicação segura com o Azure Maps, agora suportamos o TLS (Transport Layer Security, segurança de camada de transporte) 1.2, e estamos aposentando o suporte para O TLS 1.0 e 1.1. Para evitar interrupções de serviço, **atualize seus servidores e aplicativos para usar o TLS 1.2 antes de 2 de abril de 2020**.  Se você usar o TLS 1.x atualmente, avalie sua prontidão tls 1.2 e desenvolva um plano de migração com o teste descrito no [Problema TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).

## <a name="shared-key-authentication"></a>Autenticação de Chave Compartilhada

 As chaves primárias e secundárias são geradas após a criação da conta Do Azure Maps. Você é encorajado a usar a chave principal como chave de assinatura ao ligar para o Azure Maps usando autenticação de chave compartilhada. A autenticação de chave compartilhada passa uma chave gerada por uma conta do Azure Maps para um serviço do Azure Maps. Para cada solicitação aos serviços do Azure Maps, adicione a *chave de assinatura* como parâmetro à URL. A tecla secundária pode ser usada em cenários como mudanças de tecla de rolamento.  

Para obter informações sobre como visualizar suas chaves no portal Azure, consulte [Gerenciar autenticação](https://aka.ms/amauthdetails).

> [!Tip]
> Recomendamos a regeneração frequente das chaves. Você tem duas chaves, para que você possa manter conexões com uma chave enquanto regenera a outra. Quando você regenera suas chaves, você precisa atualizar quaisquer aplicativos, que acessem sua conta, com as novas chaves.

## <a name="authentication-with-azure-active-directory-preview"></a>Autenticação com o Azure Active Directory (versão prévia)

O Azure Maps agora oferece autenticação de solicitações para serviços do Azure Maps usando [o Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). O Azure AD fornece autenticação baseada em identidade, incluindo [o RBAC (Role-Based Access Control, controle de acesso baseado em função).](https://docs.microsoft.com/azure/role-based-access-control/overview) O RBAC é usado para conceder acesso ao nível do usuário, nível de grupo ou nível de aplicativo aos recursos do Azure Maps. As próximas seções discutem conceitos e componentes da integração do Azure Maps com o Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Autenticação com tokens de acesso OAuth

Os Azure Mapas aceitam tokens de acesso **OAuth 2.0** para locatários do Azure AD associados à assinatura do Azure que contêm uma conta dos Azure Mapas. O Azure Maps também aceita tokens para:

* Usuários de Ad do Azure
* Aplicativos parceiros que usam permissões delegadas pelos usuários
* Identidades gerenciadas dos recursos do Azure

Azure Mapas gera um *identificador exclusivo (ID do cliente)* para cada conta do Azure Mapas. Você pode solicitar tokens do Azure AD quando combinar esse ID do cliente com parâmetros adicionais. Para solicitar um token, especifique os valores na tabela a seguir com base no ambiente Azure.

| Azure Environment   | Ponto final do token Azure AD |
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

No Azure AD, use o RBAC para controlar o acesso a recursos protegidos. Configure sua conta do Azure Maps e registre seu inquilino Azure Maps Azure AD. O Azure Maps suporta controle de acesso de leitura para usuários individuais do Azure AD, grupos, aplicativos, recursos do Azure e serviços do Azure através de identidades gerenciadas para recursos do Azure. Na página do portal Azure Maps, você pode configurar o RBAC para as funções escolhidas.

![Leitor de dados do Azure Mapas (versão prévia)](./media/azure-maps-authentication/concept.png)

Para obter informações sobre como exibir as configurações do RBAC, confira [Como configurar o RBAC para os Azure Mapas](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Identidades gerenciadas para recursos do Azure e os Azure Mapas

[As identidades gerenciadas para os recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) fornecem aos serviços do Azure uma identidade gerenciada automaticamente, que pode ser autorizada a acessar os serviços do Azure Maps. Alguns exemplos de identidades gerenciadas incluem: Azure App Service, Azure Functions e Azure Virtual Machines.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como autenticar um aplicativo com o Azure AD e os Azure Mapas, confira [Como gerenciar a autenticação no Azure Mapas](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Para saber mais sobre como autenticar os Azure Mapas, o Controle de Mapeamento e o Azure AD, confira [Use o Controle de Mapeamento dos Azure Mapas](https://aka.ms/amaadmc).
