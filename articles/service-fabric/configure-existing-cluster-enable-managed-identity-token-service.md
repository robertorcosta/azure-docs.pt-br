---
title: Configure o suporte de identidade gerenciado em um cluster de malha de serviço existente
description: Veja como ativar o suporte a identidades gerenciadas em um cluster de malha de serviço azure existente
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: cb6e4ab00afd80cba41881e46296f7046a905919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934957"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster-preview"></a>Configure o suporte de identidade gerenciado em um cluster de malha de serviço existente (visualização)

Para usar [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) em seus aplicativos de malha de serviço, primeiro habilite o *Serviço de Token de Identidade Gerenciado* no cluster. Este serviço é responsável pela autenticação de aplicativos de Malha de Serviço usando suas identidades gerenciadas e pela obtenção de tokens de acesso em seu nome. Uma vez ativado o serviço, você pode vê-lo no Service Fabric Explorer na seção **Sistema** no painel esquerdo, executando o nome **de tecido:/System/ManagedIdentityTokenService**.

> [!NOTE]
> A versão em tempo de execução do Service Fabric 6.5.658.9590 ou superior é necessária para habilitar o **Serviço de Token de Identidade Gerenciado**.  
>
> Você pode encontrar a versão Service Fabric de um cluster do portal Azure abrindo o recurso de cluster e verificando a propriedade **de versão Service Fabric** na seção **Essenciais.**
>
> Se o cluster estiver no modo de atualização **manual,** você precisará primeiro atualizá-lo para 6.5.658.9590 ou posterior.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>Habilitar *serviço de token de identidade gerenciado* em um cluster existente

Para habilitar o Serviço de Token de Identidade Gerenciado em um cluster existente, você precisará iniciar uma atualização de cluster especificando duas alterações: (1) Habilitando o Serviço de Token de Identidade Gerenciada e (2) solicitando uma reinicialização de cada nó. Primeiro, adicione o seguinte trecho do modelo do cluster Azure Resource Manager:

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

Para que as alterações entrem em vigor, você também precisará alterar a política de upgrade para especificar uma reinicialização vigorosa do tempo de execução do Service Fabric em cada nó à medida que a atualização progride através do cluster. Essa reinicialização garante que o serviço do sistema recém-habilitado seja iniciado e executado em cada nó. No trecho abaixo, `forceRestart` está a configuração essencial; use seus valores existentes para o restante das configurações.  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> Após a conclusão bem-sucedida da atualização, `forceRestart` não se esqueça de reverter a configuração, para minimizar o impacto das atualizações subsequentes. 

## <a name="errors-and-troubleshooting"></a>Erros e solução de problemas

Se a implantação falhar com a seguinte mensagem, significa que o cluster não está sendo executado em uma versão de malha de serviço alta o suficiente:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Próximas etapas
* [Implante um aplicativo de malha de serviço do Azure com uma identidade gerenciada atribuída ao sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implante um aplicativo de malha de serviço do Azure com uma identidade gerenciada atribuída pelo usuário](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Aproveite a identidade gerenciada de um aplicativo service fabric a partir do código de serviço](./how-to-managed-identity-service-fabric-app-code.md)
* [Conceda acesso a um aplicativo de malha de serviço do Azure a outros recursos do Azure](./how-to-grant-access-other-resources.md)