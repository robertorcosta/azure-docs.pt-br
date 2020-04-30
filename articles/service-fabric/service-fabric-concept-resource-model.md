---
title: Modelo de recurso do aplicativo Service Fabric do Azure
description: Este artigo fornece uma visão geral do gerenciamento de um aplicativo de Service Fabric do Azure usando Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 7a9f59e3e44d3302ac19c7a9e7e77beb51947ce4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682636"
---
# <a name="service-fabric-application-resource-model"></a>Modelo de recurso de aplicativo Service Fabric

Você tem várias opções para implantar aplicativos de Service Fabric do Azure no Cluster Service Fabric. É recomendável usar Azure Resource Manager. Se você usar o Resource Manager, poderá descrever aplicativos e serviços em JSON e, em seguida, implantá-los no mesmo modelo do Resource Manager que o cluster. Ao contrário do uso do PowerShell ou CLI do Azure para implantar e gerenciar aplicativos, se você usar o Resource Manager, não precisará aguardar até que o cluster esteja pronto; o registro, o provisionamento e a implantação do aplicativo podem acontecer em uma única etapa. Usar o Gerenciador de recursos é a melhor maneira de gerenciar o ciclo de vida do aplicativo em seu cluster. Para obter mais informações, consulte [práticas recomendadas: Infraestrutura como código](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources).

Gerenciar seus aplicativos como recursos no Resource Manager pode ajudá-lo a obter melhorias nessas áreas:

* Trilha de auditoria: o Resource Manager audita cada operação e mantém um log de atividades detalhado. Um log de atividades pode ajudá-lo a rastrear as alterações feitas nos aplicativos e no cluster.
* Controle de acesso baseado em função: você pode gerenciar o acesso a clusters e a aplicativos implantados no cluster usando o mesmo modelo do Resource Manager.
* Eficiência de gerenciamento: usar o Gerenciador de recursos fornece um único local (o portal do Azure) para gerenciar o cluster e implantações de aplicativos críticos.

Neste documento, você aprenderá a:

> [!div class="checklist"]
>
> * Implante recursos de aplicativo usando o Gerenciador de recursos.
> * Atualize os recursos do aplicativo usando o Gerenciador de recursos.
> * Excluir recursos do aplicativo.

## <a name="deploy-application-resources"></a>Implantar recursos do aplicativo

As etapas de alto nível executadas para implantar um aplicativo e seus serviços usando o modelo de recurso de aplicativo do Resource Manager são:
1. Empacote o código do aplicativo.
1. Carregue o pacote.
1. Referencie o local do pacote em um modelo do Resource Manager como um recurso de aplicativo. 

Para obter mais informações, veja [empacotar um aplicativo](service-fabric-package-apps.md#create-an-sfpkg).

Em seguida, você cria um modelo do Resource Manager, atualiza o arquivo de parâmetros com os detalhes do aplicativo e implanta o modelo no Cluster Service Fabric. [Explore exemplos](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM).

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para implantar um aplicativo de um modelo do Resource Manager, você deve ter uma conta de armazenamento. A conta de armazenamento é usada para preparar a imagem do aplicativo. 

Você pode reutilizar uma conta de armazenamento existente ou pode criar uma nova conta de armazenamento para preparar seus aplicativos. Se você usar uma conta de armazenamento existente, poderá ignorar esta etapa. 

![Criar uma conta de armazenamento][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Configurar sua conta de armazenamento

Depois que a conta de armazenamento é criada, você cria um contêiner de BLOBs onde os aplicativos podem ser preparados. Na portal do Azure, vá para a conta de armazenamento do Azure na qual você deseja armazenar seus aplicativos. Selecione **BLOBs** > **Adicionar contêiner**. 

Os recursos no cluster podem ser protegidos definindo o nível de acesso público como **privado**. Você pode conceder acesso de várias maneiras:

* Autorize o acesso a BLOBs e filas usando [Azure Active Directory](../storage/common/storage-auth-aad-app.md).
* Conceda acesso ao blob do Azure e aos dados [da fila usando o RBAC no portal do Azure](../storage/common/storage-auth-aad-rbac-portal.md).
* Delegar acesso usando uma [assinatura de acesso compartilhado](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature).

O exemplo na captura de tela a seguir usa acesso de leitura anônimo para BLOBs.

![Criar blob][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Preparar o aplicativo em sua conta de armazenamento

Para poder implantar um aplicativo, você deve preparar o aplicativo no armazenamento de BLOBs. Neste tutorial, criamos o pacote de aplicativos manualmente. Tenha em mente que essa etapa pode ser automatizada. Para obter mais informações, consulte [empacotar um aplicativo](service-fabric-package-apps.md#create-an-sfpkg). 

Neste tutorial, usamos o aplicativo de [exemplo de votação](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart).

1. No Visual Studio, clique com o botão direito do mouse no projeto de **votação** e selecione **pacote**.

   ![Aplicativo de pacote][PackageApplication]  
1. Vá para o diretório *.\Service-Fabric-dotnet-quickstart\Voting\pkg\Debug* Compacte o conteúdo em um arquivo chamado *votação. zip*. O arquivo *ApplicationManifest. xml* deve estar na raiz no arquivo zip.

   ![Aplicativo zip][ZipApplication]  
1. Renomeie o arquivo para alterar a extensão de. zip para *. sfpkg*.

1. No portal do Azure, no contêiner **aplicativos** para sua conta de armazenamento, selecione **carregar**e, em seguida, carregar **votação. sfpkg**. 

   ![Carregar pacote do aplicativo][UploadAppPkg]

Agora, o aplicativo está preparado e você pode criar o modelo do Resource Manager para implantar o aplicativo.

### <a name="create-the-resource-manager-template"></a>Criar o modelo do Gerenciador de Recursos

O aplicativo de exemplo contém [modelos de Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) que você pode usar para implantar o aplicativo. Os nomes de arquivo de modelo são *UserApp. JSON* e *UserApp. Parameters. JSON*.

> [!NOTE]
> O arquivo *UserApp. Parameters. JSON* deve ser atualizado com o nome do cluster.
>
>

| Parâmetro              | Descrição                                 | Exemplo                                                      | Comentários                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | O nome do cluster no qual você está implantando | It-cluster123                                                |                                                              |
| aplicativo            | O nome do aplicativo                 | Dica                                                       |
| applicationTypeName    | O nome do tipo do aplicativo           | VotingType                                                   | Deve corresponder a ApplicationManifest. xml                 |
| applicationTypeVersion | A versão do tipo de aplicativo         | 1.0.0                                                        | Deve corresponder a ApplicationManifest. xml                 |
| serviceName            | O nome do serviço         | Votação ~ VotingWeb                                             | Deve estar no formato ApplicationName ~ ServiceType            |
| serviceTypeName        | O nome do tipo do serviço                | VotingWeb                                                    | Deve corresponder ao inmanifest. xml                 |
| appPackageUrl          | A URL de armazenamento de BLOBs do aplicativo     | https:\//servicefabricapps.blob.Core.Windows.net/apps/Voting.sfpkg | A URL do pacote de aplicativos no armazenamento de BLOBs (o procedimento para definir a URL é descrito posteriormente no artigo) |

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>Implantar o aplicativo

Execute o cmdlet **New-AzResourceGroupDeployment** para implantar o aplicativo no grupo de recursos que contém o cluster:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Atualizar o aplicativo Service Fabric usando o Gerenciador de recursos

Você pode atualizar um aplicativo que já está implantado em um Cluster Service Fabric por um destes motivos:

* Um novo serviço é adicionado ao aplicativo. Uma definição de serviço deve ser adicionada aos arquivos *Service-manifest. xml* e *Application-manifest. xml* quando um serviço é adicionado ao aplicativo. Para refletir uma nova versão de um aplicativo, você também deve alterar a versão do tipo de aplicativo de 1.0.0 para 1.0.1 em [UserApp. Parameters. JSON](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json):

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* Uma nova versão de um serviço existente é adicionada ao aplicativo. Os exemplos incluem alterações de código do aplicativo e atualizações para a versão e o nome do tipo de aplicativo. Para essa atualização, atualize UserApp. Parameters. JSON da seguinte maneira:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Excluir recursos do aplicativo

Para excluir um aplicativo que foi implantado usando o modelo de recurso de aplicativo no Gerenciador de recursos:

1. Use o cmdlet [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0) para obter a ID de recurso para o aplicativo:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Use o cmdlet [Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) para excluir os recursos do aplicativo:

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Próximas etapas

Obter informações sobre o modelo de recurso de aplicativo:

* [Modelar um aplicativo na Malha do Serviço](service-fabric-application-model.md)
* [Manifestos de serviço e aplicativo do Service Fabric](service-fabric-application-and-service-manifests.md)
* [Práticas recomendadas: Infraestrutura como código](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Gerenciar aplicativos e serviços como recursos do Azure](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
