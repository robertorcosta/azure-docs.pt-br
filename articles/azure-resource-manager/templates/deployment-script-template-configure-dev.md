---
title: Configurar o ambiente de desenvolvimento para scripts de implantação em modelos | Microsoft Docs
description: Configure o ambiente de desenvolvimento para scripts de implantação em modelos de Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: jgao
ms.openlocfilehash: 4a7f21410bb97db0a7974870efb812c9954ac241
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503549"
---
# <a name="configure-development-environment-for-deployment-scripts-in-templates"></a>Configurar o ambiente de desenvolvimento para scripts de implantação em modelos

Saiba como criar um ambiente de desenvolvimento para desenvolver e testar scripts de implantação com uma imagem de script de implantação. Você pode criar a [instância de contêiner do Azure](../../container-instances/container-instances-overview.md) ou usar o [Docker](https://docs.docker.com/get-docker/). Ambos são abordados neste artigo.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver um script de implantação, poderá criar um arquivo de **hello.ps1** com o seguinte conteúdo:

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

## <a name="use-azure-container-instance"></a>Usar instância de contêiner do Azure

Para criar seus scripts em seu computador, é necessário que você crie uma conta de armazenamento e monte a conta de armazenamento na instância do contêiner. Para que você possa carregar o script na conta de armazenamento e executar o script na instância do contêiner.

> [!NOTE]
> A conta de armazenamento que você cria para testar seu script não é a mesma conta de armazenamento que o serviço de script de implantação usa para executar o script. O serviço de script de implantação cria um nome exclusivo como um compartilhamento de arquivos em cada execução.

### <a name="create-an-azure-container-instance"></a>Criar uma instância de contêiner do Azure

O modelo ARM a seguir cria uma instância de contêiner e um compartilhamento de arquivos e, em seguida, monta o compartilhamento de arquivos para a imagem de contêiner.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "deploymentScript",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('projectName'), 'store')]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/sh",
                "-c",
                "pwsh -c 'Start-Sleep -Seconds 1800'"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```
O valor padrão para o caminho de montagem é **deploymentScript**.  Esse é o caminho na instância de contêiner onde ele é montado no compartilhamento de arquivos.

A imagem de contêiner padrão especificada no modelo é **MCR.Microsoft.com/azuredeploymentscripts-PowerShell:az4.3 "**.  Para obter uma lista de versões de Azure PowerShell com suporte e versões de CLI do Azure, consulte [Azure PowerShell ou CLI do Azure](./deployment-script-template.md#prerequisites).

O modelo suspende a instância de contêiner de 1800 segundos. Você tem 30 minutos antes de a instância do contêiner entrar no estado do terminal e a sessão terminar.

Para implantar o modelo:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-azResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-deployment-script"></a>Carregar script de implantação

Carregue o script de implantação na conta de armazenamento. Este é um exemplo do PowerShell:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

Você também pode carregar o arquivo usando o portal do Azure e CLI do Azure.

### <a name="test-the-deployment-script"></a>Testar o script de implantação

1. No portal do Azure, abra o grupo de recursos em que você implantou a instância de contêiner e a conta de armazenamento.
1. Abra o grupo de contêineres. O nome do grupo de contêineres padrão é o nome do projeto com **CG** anexado. Você deve ver que a instância de contêiner está em estado de **execução** .
1. Selecione **contêineres** no menu à esquerda. Você deverá ver uma instância de contêiner.  O nome da instância de contêiner é o nome do projeto com o **contêiner** anexado.

    ![script de implantação conectar instância de contêiner](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. Selecione **conectar** e, em seguida, selecione **conectar**. Se você não conseguir se conectar à instância de contêiner, reinicie o grupo de contêineres e tente novamente.
1. No painel de console, execute os seguintes comandos:

    ```
    cd deploymentScript
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    A saída é **Hello John giros**.

    ![teste de instância de contêiner de script de implantação](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

## <a name="use-docker"></a>Usar o Docker

Você pode usar uma imagem de contêiner pré-configurada do Docker como seu ambiente de desenvolvimento de script de implantação. Para instalar o Docker, consulte [obter Docker](https://docs.docker.com/get-docker/).
Você também precisa configurar o compartilhamento de arquivos para montar o diretório, que contém os scripts de implantação no contêiner do Docker.

1. Efetuar pull da imagem de contêiner do script de implantação para o computador local:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    O exemplo usa o cmdlet do PowerShell de versão.

    Para efetuar pull de uma imagem da CLI de um Registro de Contêiner da Microsoft (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    Este exemplo usa a versão 2.0.80 da CLI. O script de implantação usa as imagens de contêineres da CLI padrão encontradas [aqui](https://hub.docker.com/_/microsoft-azure-cli).

1. Execute a imagem do Docker localmente.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    Substitua a **&lt;letra da unidade do host>** e o **&lt;nome do diretório de host>** com uma pasta existente na unidade compartilhada.  Ele mapeia a pasta para a pasta **/data** no contêiner. Por exemplo, para mapear D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **—** significa manter a imagem de contêiner ativa.

    Um exemplo de CLI:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. A captura de tela a seguir mostra como executar um script do PowerShell, Considerando que você tem um arquivo de helloworld.ps1 na unidade compartilhada.

    ![Cmd do Docker do script de implantação do modelo do Resource Manager](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Depois que o script for testado com êxito, você poderá usá-lo como um script de implantação em seus modelos.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como usar os scripts de implantação. Para percorrer um tutorial de script de implantação:

> [!div class="nextstepaction"]
> [Tutorial: Usar scripts de implantação em modelos do Azure Resource Manager](./template-tutorial-deployment-script.md)
