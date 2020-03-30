---
title: Controle de acesso baseado em função para contas de Serviços de Mídia - Azure | Microsoft Docs
description: Este artigo discute o RBAC (Role-based Access Control, controle de acesso baseado em função) para contas do Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 93b2cd3a2565b14ea07d6db6b14dd146e4223528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66236910"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>RBAC (Role-based Access Control, controle de acesso baseado em função) para contas de Serviços de Mídia

Atualmente, o Azure Media Services não define nenhuma função personalizada específica para o serviço. Para ter acesso total à conta de Serviços de Mídia, os clientes podem usar as funções incorporadas de **Proprietário** ou **Contribuinte**. A principal diferença entre essas funções é: o **Proprietário** pode controlar quem tem acesso a um recurso e o **Contribuinte** não pode. A função **Leitor** incorporado também pode ser usada, mas o usuário ou aplicativo só terá acesso à leitura das APIs dos Serviços de Mídia. 

## <a name="design-principles"></a>Princípios de design

Um dos princípios de design de chave da API v3 é tornar a API mais segura. as APIs v3 não retornam segredos ou credenciais nas operações **Get** ou **List.** As chaves são sempre nulas, vazias ou corrigidas na resposta. O usuário precisa chamar um método de ação separado para obter segredos ou credenciais. A função **Reader** não pode chamar operações como Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Ter ações separadas permite definir permissões de segurança RBAC mais granulares em uma função personalizada, se desejar.

Para listar os suportes dos Serviços de Mídia de operações, faça:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

O artigo [de definições de função incorporada](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) diz exatamente o que o papel concede. 

Veja os seguintes artigos para obter mais informações:

- [Funções de administrador da assinatura clássica, funções do Azure RBAC e funções de administrador do Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [O que é RBAC para recursos do Azure?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Use o RBAC para gerenciar o acesso](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Operações de provedores de recursos de serviços de mídia](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>Próximas etapas

- [Desenvolvimento com ApIs de Serviços de Mídia v3](media-services-apis-overview.md)
- [Obtenha a política-chave de conteúdo usando os Serviços de Mídia .NET](get-content-key-policy-dotnet-howto.md)
