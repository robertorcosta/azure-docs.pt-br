---
title: Modelo de recurso de aplicativo de malha de serviço do Azure
description: Este artigo fornece uma visão geral do gerenciamento de um aplicativo de malha de serviço do Azure usando o Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 7a9f59e3e44d3302ac19c7a9e7e77beb51947ce4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682636"
---
# <a name="service-fabric-application-resource-model"></a>Modelo de recurso de aplicativo de malha de serviço

Você tem várias opções para implantar aplicativos de malha de serviço do Azure no cluster Service Fabric. Recomendamos o uso do Azure Resource Manager. Se você usar o Gerenciador de recursos, poderá descrever aplicativos e serviços no JSON e implantá-los no mesmo modelo de Gerenciador de recursos do seu cluster. Ao contrário de usar o PowerShell ou o Azure CLI para implantar e gerenciar aplicativos, se você usar o Gerenciador de Recursos, você não precisa esperar que o cluster esteja pronto; registro de aplicativos, provisionamento e implantação podem acontecer em uma etapa. Usar o Gerenciador de recursos é a melhor maneira de gerenciar o ciclo de vida do aplicativo em seu cluster. Para obter mais informações, consulte [As melhores práticas: Infra-estrutura como código](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources).

Gerenciar seus aplicativos como recursos no Gerenciador de Recursos pode ajudá-lo a obter melhorias nessas áreas:

* Trilha de auditoria: o Gerenciador de Recursos audita todas as operações e mantém um registro de atividade detalhado. Um registro de atividadepode ajudá-lo a rastrear quaisquer alterações feitas nos aplicativos e no cluster.
* Controle de acesso baseado em função: Você pode gerenciar o acesso a clusters e a aplicativos implantados no cluster usando o mesmo modelo do Gerenciador de recursos.
* Eficiência de gerenciamento: O uso do Gerenciador de Recursos oferece um único local (o portal Azure) para gerenciar suas implantações de cluster e aplicativos críticos.

Neste documento, você aprenderá como:

> [!div class="checklist"]
>
> * Implante recursos de aplicativos usando o Resource Manager.
> * Atualize os recursos do aplicativo usando o Resource Manager.
> * Exclua os recursos do aplicativo.

## <a name="deploy-application-resources"></a>Implantar recursos de aplicativos

As etapas de alto nível que você toma para implantar um aplicativo e seus serviços usando o modelo de recurso de aplicativo do Resource Manager são:
1. Empacote o código do aplicativo.
1. Faça o upload do pacote.
1. Consulte a localização do pacote em um modelo do Gerenciador de recursos como um recurso de aplicativo. 

Para obter mais informações, [visualize O pacote de um aplicativo](service-fabric-package-apps.md#create-an-sfpkg).

Em seguida, você cria um modelo de Gerenciador de recursos, atualiza o arquivo de parâmetros com detalhes do aplicativo e implanta o modelo no cluster Service Fabric. [Explorar amostras](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM).

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para implantar um aplicativo a partir de um modelo de Gerenciador de recursos, você deve ter uma conta de armazenamento. A conta de armazenamento é usada para encenar a imagem do aplicativo. 

Você pode reutilizar uma conta de armazenamento existente ou criar uma nova conta de armazenamento para a realização de seus aplicativos. Se você usar uma conta de armazenamento existente, você pode pular essa etapa. 

![Criar uma conta de armazenamento][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Configure sua conta de armazenamento

Depois que a conta de armazenamento é criada, você cria um recipiente blob onde os aplicativos podem ser encenados. No portal Azure, vá até a conta do Azure Storage onde deseja armazenar seus aplicativos. Selecione **Blobs** > **Adicionar recipiente**. 

Os recursos em seu cluster podem ser protegidos definindo o nível de acesso público ao **privado**. Você pode conceder acesso de várias maneiras:

* Autorize o acesso a bolhas e filas usando [o Azure Active Directory](../storage/common/storage-auth-aad-app.md).
* Conceda acesso aos dados de blob e fila do Azure usando [o RBAC no portal Azure](../storage/common/storage-auth-aad-rbac-portal.md).
* Delegar o acesso usando uma [assinatura de acesso compartilhado](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature).

O exemplo na captura de tela a seguir usa acesso de leitura anônima para blobs.

![Criar blob][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Estágio do aplicativo em sua conta de armazenamento

Antes de implantar um aplicativo, você deve encenar o aplicativo no armazenamento blob. Neste tutorial, criamos o pacote de aplicativos manualmente. Tenha em mente que este passo pode ser automatizado. Para obter mais informações, consulte [Pacote de um aplicativo](service-fabric-package-apps.md#create-an-sfpkg). 

Neste tutorial, usamos o [aplicativo de amostra de votação](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart).

1. No Visual Studio, clique com o botão direito do mouse no projeto **Votação** e, em seguida, selecione **Pacote**.

   ![Aplicativo do pacote][PackageApplication]  
1. Vá para o diretório *.\service-fabric-dotnet-quickstart\Voting\pkg\Debug.* Feche o conteúdo em um arquivo chamado *Voting.zip*. O arquivo *ApplicationManifest.xml* deve estar na raiz do arquivo zip.

   ![Aplicativo Zip][ZipApplication]  
1. Renomeie o arquivo para alterar a extensão de .zip para *.sfpkg*.

1. No portal Azure, no contêiner **de aplicativos** para sua conta de armazenamento, selecione **Upload**e, em seguida, carregue **Voting.sfpkg**. 

   ![Enviar pacote de aplicativos][UploadAppPkg]

Agora, o aplicativo está em etapas e você pode criar o modelo gerenciador de recursos para implantar o aplicativo.

### <a name="create-the-resource-manager-template"></a>Criar o modelo do Gerenciador de Recursos

O aplicativo de exemplo contém [modelos do Azure Resource Manager que](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) você pode usar para implantar o aplicativo. Os nomes dos arquivos do modelo são *UserApp.json* e *UserApp.Parameters.json*.

> [!NOTE]
> O arquivo *UserApp.Parameters.json* deve ser atualizado com o nome do seu cluster.
>
>

| Parâmetro              | Descrição                                 | Exemplo                                                      | Comentários                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | O nome do cluster que você está implantando para | sf-cluster123                                                |                                                              |
| aplicativo            | O nome do aplicativo                 | Votação                                                       |
| aplicativoTypeNome    | O nome do tipo do aplicativo           | Tipo de votação                                                   | Deve corresponder applicationManifest.xml                 |
| applicationTypeVersion | A versão do tipo de aplicativo         | 1.0.0                                                        | Deve corresponder applicationManifest.xml                 |
| serviceName            | O nome do serviço         | Votação~VotaçãoWeb                                             | Deve estar no formato ApplicationName~ServiceType            |
| serviceTypeName        | O nome do tipo do serviço                | VotingWeb                                                    | Deve corresponder serviceManifest.xml                 |
| appPackageUrl          | A URL de armazenamento blob do aplicativo     | https:\//servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | A URL do pacote de aplicativos no armazenamento blob (o procedimento para definir a URL é descrito mais tarde no artigo) |

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

Execute o **cmdlet New-AzResourceGroupDeployment** para implantar o aplicativo no grupo de recursos que contém o cluster:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Atualize o aplicativo Service Fabric usando o Gerenciador de recursos

Você pode atualizar um aplicativo que já está implantado em um cluster de malha de serviço por uma dessas razões:

* Um novo serviço é adicionado ao aplicativo. Uma definição de serviço deve ser adicionada aos arquivos *service-manifest.xml* e *application-manifest.xml* quando um serviço é adicionado ao aplicativo. Para refletir uma nova versão de um aplicativo, você também deve alterar a versão do tipo de aplicativo de 1.0.0 para 1.0.1 no [UserApp.Parameters.json](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json):

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

* Uma nova versão de um serviço existente é adicionada ao aplicativo. Exemplos incluem alterações no código do aplicativo e atualizações para a versão e nome do tipo do aplicativo. Para esta atualização, atualize userApp.parameters.json assim:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Excluir recursos do aplicativo

Para excluir um aplicativo que foi implantado usando o modelo de recurso do aplicativo no Gerenciador de recursos:

1. Use o [cmdlet Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0) para obter o ID de recurso para o aplicativo:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Use o [cmdlet Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) para excluir os recursos do aplicativo:

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Próximas etapas

Obter informações sobre o modelo de recurso do aplicativo:

* [Modelar um aplicativo na Malha do Serviço](service-fabric-application-model.md)
* [Manifestos de serviço e aplicativo do Service Fabric](service-fabric-application-and-service-manifests.md)
* [Melhores práticas: Infra-estrutura como código](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Gerencie aplicativos e serviços como recursos do Azure](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
