---
title: Configure o suporte de identidade gerenciado para um novo cluster de malha de serviço
description: Veja como ativar o suporte a identidades gerenciadas em um novo cluster de malha de serviço do Azure
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 0e35d2192fdcdb294b349105f3f0158564cec86b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76930473"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster-preview"></a>Configure o suporte de identidade gerenciado para um novo cluster de malha de serviço (visualização)

Para usar [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) em seus aplicativos de malha de serviço, primeiro habilite o *Serviço de Token de Identidade Gerenciado* no cluster. Este serviço é responsável pela autenticação de aplicativos de Malha de Serviço usando suas identidades gerenciadas e pela obtenção de tokens de acesso em seu nome. Uma vez que o serviço esteja ativado, você pode vê-lo no Service Fabric Explorer na seção **Sistema** no painel esquerdo, executando o nome **de malha:/System/ManagedIdentityTokenService** ao lado de outros serviços do sistema.

> [!NOTE]
> A versão em tempo de execução do Service Fabric 6.5.658.9590 ou superior é necessária para habilitar o **Serviço de Token de Identidade Gerenciado**.  

## <a name="enable-the-managed-identity-token-service"></a>Habilite o serviço de token de identidade gerenciado

Para habilitar o Serviço de Token de Identidade Gerenciado no tempo de criação do cluster, adicione o seguinte trecho ao modelo do Cluster Azure Resource Manager:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

## <a name="errors"></a>Errors

Se a implantação falhar com esta mensagem, significa que o cluster não está na versão de malha de serviço necessária (o tempo mínimo de execução suportado é 6.5 CU2):


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>Artigos relacionados

* Revisar [o suporte de identidade gerenciado](./concepts-managed-identity.md) no Azure Service Fabric

* [Habilite o suporte de identidade gerenciado em um cluster azure Service Fabric existente](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Próximas etapas

* [Implante um aplicativo de malha de serviço do Azure com uma identidade gerenciada atribuída ao sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implante um aplicativo de malha de serviço do Azure com uma identidade gerenciada atribuída pelo usuário](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Aproveite a identidade gerenciada de um aplicativo service fabric a partir do código de serviço](./how-to-managed-identity-service-fabric-app-code.md)
* [Conceda acesso a um aplicativo de malha de serviço do Azure a outros recursos do Azure](./how-to-grant-access-other-resources.md)