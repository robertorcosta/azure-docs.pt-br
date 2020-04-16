---
title: Implantar aplicativo com uma identidade gerenciada atribuída pelo usuário
description: Este artigo mostra como implantar o aplicativo Service Fabric com uma identidade gerenciada atribuída pelo usuário
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 9aef81db7a455b72c83cf96898a0c228f1c382fd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415636"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>Implantar aplicativo de malha de serviço com uma identidade gerenciada atribuída pelo usuário

Para implantar um aplicativo de malha de serviço com identidade gerenciada, o aplicativo precisa ser implantado através do Azure Resource Manager, normalmente com um modelo do Azure Resource Manager. Para obter mais informações sobre como implantar o aplicativo Service Fabric através do Azure Resource Manager, consulte [Gerenciar aplicativos e serviços como recursos do Azure Resource Manager](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Aplicativos que não são implantados como um recurso do Azure **não podem** ter identidades gerenciadas. 
>
> A implantação do aplicativo Service Fabric com identidade `"2019-06-01-preview"`gerenciada é suportada com versão aPi . Você também pode usar a mesma versão de API para tipo de aplicativo, versão do tipo de aplicativo e recursos de serviço.
>

## <a name="user-assigned-identity"></a>Identidade atribuída pelo usuário

Para habilitar o aplicativo com identidade atribuída ao usuário, adicione primeiro a propriedade **de identidade** ao recurso do aplicativo com o usuário do tipo **Atribuído** e as identidades atribuídas pelo usuário referenciadas. Em seguida, adicione uma seção **'Identidades gerenciadas'** dentro da seção **propriedades** para o recurso do **aplicativo** que contém uma lista de nomes amigáveis ao mapeamento principalId para cada uma das identidades atribuídas pelo usuário. Para obter mais informações sobre identidades atribuídas ao usuário, consulte [Criar, listar ou excluir uma identidade gerenciada atribuída pelo usuário](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell).

### <a name="application-template"></a>Modelo de aplicativo

Para habilitar o aplicativo com a identidade atribuída ao usuário, adicione primeiro a propriedade **de identidade** ao recurso do aplicativo com usuário de **tipoAtribuído** e as identidades atribuídas pelo usuário referenciado e adicione um objeto **de identidade gerenciado** dentro da seção **propriedades** que contém uma lista de nome amigável ao mapeamento principalId para cada uma das identidades atribuídas pelo usuário.

    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
      ],
      "identity": {
        "type" : "userAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
        }
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        },
        "managedIdentities": [
          {
            "name" : "[parameters('userAssignedIdentityName')]",
            "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
          }
        ]
      }
    }

No exemplo acima, o nome do recurso da identidade atribuída pelo usuário está sendo usado como o nome amigável da identidade gerenciada para o aplicativo. Os exemplos a seguir supõem que o nome amigo real é "AdminUser".

### <a name="application-package"></a>Pacote de aplicativos

1. Para cada identidade `managedIdentities` definida na seção no modelo Azure Resource Manager, adicione uma `<ManagedIdentity>` tag no manifesto do aplicativo na seção **Principais.** O `Name` atributo precisa `name` corresponder à `managedIdentities` propriedade definida na seção.

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. Na seção **ServiceManifestImport,** adicione uma **IdentityBindingPolicy** para o serviço que usa a Identidade Gerenciada. Essa política `AdminUser` mapeia a identidade para um nome de identidade específico do serviço que precisa ser adicionado ao manifesto de serviço mais tarde.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Atualize o manifesto de serviço para adicionar uma **ManagedIdentity** `IdentityBindingPolicy` dentro da seção **Recursos** com o nome correspondente ao `ServiceIdentityRef` do manifesto do aplicativo:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Próximas etapas

* [Como usar o código de aplicativo de configuração de identidade gerenciada no service fabric](how-to-managed-identity-service-fabric-app-code.md)
* [Como conceder acesso a aplicativos de malha de serviço a outros recursos do Azure](how-to-grant-access-other-resources.md)
