---
title: Implantar um aplicativo de malha de serviço com MI atribuído ao sistema
description: Este artigo mostra como atribuir uma identidade gerenciada atribuída ao sistema a um aplicativo de malha de serviço do Azure
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: c5c7a17c51eee18d9b7276f2c57289a5de5c8181
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415649"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity"></a>Implantar aplicativo de malha de serviço com identidade gerenciada atribuída ao sistema

Para acessar o recurso de identidade gerenciada para aplicativos azure Service Fabric, você deve primeiro ativar o Serviço de Token de Identidade Gerenciado no cluster. Este serviço é responsável pela autenticação de aplicativos de Malha de Serviço usando suas identidades gerenciadas e pela obtenção de tokens de acesso em seu nome. Uma vez que o serviço esteja ativado, você pode vê-lo no Service Fabric Explorer na seção **Sistema** no painel esquerdo, executando sob o nome **de malha:/System/ManagedIdentityTokenService** ao lado de outros serviços do sistema.

> [!NOTE] 
> A implantação de aplicativos de malha de serviço com identidades gerenciadas é suportada a partir da versão `"2019-06-01-preview"`aPi . Você também pode usar a mesma versão de API para tipo de aplicativo, versão do tipo de aplicativo e recursos de serviço. O tempo mínimo de execução do Service Fabric suportado é de 6,5 CU2. Em additoin, o ambiente de construção /pacote também deve ter o SF .Net SDK em CU2 ou superior

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

### <a name="application-template"></a>Modelo de aplicativo

Para habilitar o aplicativo com uma identidade gerenciada atribuída ao sistema, adicione a propriedade **de identidade** ao recurso do aplicativo, com o sistema de **tipoAtribuído** conforme mostrado no exemplo abaixo:

```json
    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
Essa propriedade declara (ao Azure Resource Manager, e aos Provedores gerenciados de recursos de`system assigned`identidade e malha de serviço, respectivamente, que esse recurso deve ter uma identidade gerenciada implícita ( ) .

### <a name="application-and-service-package"></a>Pacote de aplicativos e serviços

1. Atualize o manifesto do aplicativo para adicionar um elemento **ManagedIdentity** na seção **Principais,** contendo uma única entrada conforme mostrado abaixo:

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Isso mapeia a identidade atribuída ao aplicativo como um recurso para um nome amigável, para posterior atribuição aos serviços que compõem o aplicativo. 

2. Na seção **ServiceManifestImport** correspondente ao serviço que está sendo atribuído à identidade gerenciada, adicione um elemento **IdentityBindingPolicy,** conforme indicado abaixo:

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Esse elemento atribui a identidade do aplicativo ao serviço; sem essa atribuição, o serviço não poderá acessar a identidade do aplicativo. No trecho acima, a `SystemAssigned` identidade (que é uma palavra-chave reservada) é mapeada para a definição do serviço sob o nome `WebAdmin`amigável .

3. Atualize o manifesto de serviço para adicionar um elemento **ManagedIdentity** `ServiceIdentityRef` dentro da `IdentityBindingPolicy` seção **Recursos** com o nome correspondente ao valor da configuração a partir da definição no manifesto do aplicativo:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Este é o mapeamento equivalente de uma identidade para um serviço como descrito acima, mas na perspectiva da definição do serviço. A identidade é referenciada aqui`WebAdmin`pelo seu nome amigável ( ), como declarado no manifesto de aplicação.

## <a name="next-steps"></a>Próximas etapas
* Revisar [o suporte de identidade gerenciado](./concepts-managed-identity.md) no Azure Service Fabric
* [Implantar um novo](./configure-new-azure-service-fabric-enable-managed-identity.md) Cluster de malha de serviço do Azure com suporte gerenciado de identidade 
* [Habilitar a identidade gerenciada](./configure-existing-cluster-enable-managed-identity-token-service.md) em um cluster de malha de serviço azure existente
* Aproveite a identidade gerenciada de um aplicativo de malha de serviço [a partir do código-fonte](./how-to-managed-identity-service-fabric-app-code.md)
* [Implante um aplicativo de malha de serviço do Azure com uma identidade gerenciada atribuída pelo usuário](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Conceda acesso a um aplicativo de malha de serviço do Azure a outros recursos do Azure](./how-to-grant-access-other-resources.md)
