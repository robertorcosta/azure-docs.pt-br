---
title: Configure o suporte de identidade gerenciado em um cluster de malha de serviço existente
description: Veja como ativar o suporte a identidades gerenciadas em um cluster de malha de serviço azure existente
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 396978546b301884087c4ea51e242258d64a6b0b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983799"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster-preview"></a>Configure o suporte de identidade gerenciado em um cluster de malha de serviço existente (visualização)

Para usar [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) em seus aplicativos de malha de serviço, primeiro habilite o *Serviço de Token de Identidade Gerenciado* no cluster. Este serviço é responsável pela autenticação de aplicativos de Malha de Serviço usando suas identidades gerenciadas e pela obtenção de tokens de acesso em seu nome. Uma vez ativado o serviço, você pode vê-lo no Service Fabric Explorer na seção **Sistema** no painel esquerdo, executando sob o nome **de tecido:/System/ManagedIdentityTokenService**.

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

Para que as alterações entrem em vigor, você também precisará alterar a política de upgrade para especificar uma reinicialização vigorosa do tempo de execução do Service Fabric em cada nó à medida que a atualização progride através do cluster. Essa reinicialização garante que o serviço do sistema recém-habilitado seja iniciado e executado em cada nó. No trecho abaixo, `forceRestart` está a configuração essencial para ativar a reinicialização. Para os demais parâmetros, use os valores descritos abaixo ou use valores personalizados já especificados para o recurso de cluster. As configurações personalizadas da Política de Atualização de Malha ('upgradeDescription') podem ser visualizadas no Portal Azure selecionando a opção 'Upgrades de malha' no recurso 'Malha de serviço' ou resources.azure.com. As opções padrão para a política de upgrade ('upgradeDescription') não podem ser visualizadas no powershell ou resources.azure.com. Consulte [ClusterUpgradePolicy](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy?view=azure-dotnet) para obter informações adicionais.  

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
