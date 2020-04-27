---
title: Controle de acesso baseado em função para contas de serviços de mídia – Azure | Microsoft Docs
description: Este artigo aborda o RBAC (controle de acesso baseado em função) para contas dos serviços de mídia do Azure.
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2020
ms.locfileid: "66236910"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>RBAC (controle de acesso baseado em função) para contas de serviços de mídia

Atualmente, os serviços de mídia do Azure não definem nenhuma função personalizada específica para o serviço. Para obter acesso completo à conta dos serviços de mídia, os clientes podem usar as funções internas de **proprietário** ou **colaborador**. A principal diferença entre essas funções é: o **proprietário** pode controlar quem tem acesso a um recurso e o **colaborador** não pode. A função **leitor** interna também pode ser usada, mas o usuário ou aplicativo terá acesso de leitura somente para as APIs dos serviços de mídia. 

## <a name="design-principles"></a>Princípios de design

Um dos princípios de design de chave da API v3 é tornar a API mais segura. as APIs v3 não retornam segredos ou credenciais em operações de **obtenção** ou de **lista** . As chaves são sempre nulas, vazias ou corrigidas na resposta. O usuário precisa chamar um método de ação separado para obter segredos ou credenciais. A função **leitor** não pode chamar operações como Asset. ListContainerSas, StreamingLocator. ListContentKeys, ContentKeyPolicies. GetPolicyPropertiesWithSecrets. Ter ações separadas permite definir permissões de segurança RBAC mais granulares em uma função personalizada, se desejado.

Para listar as operações que os serviços de mídia dão suporte, faça:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

O artigo [definições de função internas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) informa exatamente o que a função concede. 

Veja os seguintes artigos para obter mais informações:

- [Funções de administrador da assinatura clássica, funções do Azure RBAC e funções de administrador do Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [O que é o RBAC para recursos do Azure?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Usar o RBAC para gerenciar o acesso](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Operações do provedor de recursos dos serviços de mídia](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>Próximas etapas

- [Desenvolvendo com APIs dos serviços de mídia v3](media-services-apis-overview.md)
- [Obter a política de chave de conteúdo usando o .NET dos serviços de mídia](get-content-key-policy-dotnet-howto.md)
