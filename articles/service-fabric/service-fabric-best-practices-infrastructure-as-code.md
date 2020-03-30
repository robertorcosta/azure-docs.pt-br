---
title: Infra-estrutura de malha de serviço do Azure como Práticas Recomendadas de Código
description: As melhores práticas e considerações de design para gerenciar o Azure Service Fabric como infra-estrutura como código.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 1c044d5fd973d3c577088a887f2fac413d2ab79d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551804"
---
# <a name="infrastructure-as-code"></a>Infraestrutura como código

Em um cenário de produção, crie clusters do Azure Service Fabric usando modelos do Resource Manager. Os modelos do Resource Manager fornecem maior controle das propriedades de recurso e garantem que você tenha um modelo de recursos consistente.

Os modelos de exemplo do Resource Manager estão disponíveis para Windows e Linux nos [Exemplos do Azure no GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Esses modelos podem ser usados como ponto de partida para o modelo de cluster. Faça o download de `azuredeploy.json` e de `azuredeploy.parameters.json` e edite-os para atender aos seus requisitos personalizados.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para implantar os modelos `azuredeploy.json` e `azuredeploy.parameters.json` baixados acima, use os seguintes comandos da CLI do Azure:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Como criar um recurso usando o Powershell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Recursos do Azure Service Fabric

É possível implantar aplicativos e serviços em seu cluster do Service Fabric por meio do Azure Resource Manager. Confira [Gerenciar aplicativos e serviços como recursos do Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) para obter detalhes. Veja a seguir os recursos específicos de aplicativo do Service Fabric considerados como prática recomendada a serem incluídos em seus recursos de modelo do Resource Manager.

```json
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
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Para implantar o seu aplicativo usando o Azure Resource Manager, primeiramente, você deve [criar um sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg), pacote de aplicativo do Service Fabric. O seguinte script python é um exemplo de como criar um sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Configuração automática de atualização automática do sistema operacional da máquina virtual do Azure 
Atualizar suas máquinas virtuais é uma operação iniciada pelo usuário, e é recomendável que você use o upgrade automático do sistema operacional do conjunto de [escala de máquina virtual](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) para o gerenciamento de patches de host de clusters azure Service Fabric; Patch Orchestration Application é uma solução alternativa que se destina quando hospedada fora do Azure, embora o POA possa ser usado no Azure, com a sobrecarga de hospedagem de POA no Azure sendo uma razão comum para preferir o Upgrade Automático do Sistema Operacional da Máquina Virtual em vez de POA. A seguir estão as propriedades do algoritmo de gerenciamento de recursos do Set de escala de máquina sumido para permitir a atualização do auto-sistema operacional:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Ao usar upgrades automáticos do sistema operacional com malha de serviço, a nova imagem do SISTEMA OPERACIONAL é lançada um Domínio de Atualização por vez para manter a alta disponibilidade dos serviços em execução no Service Fabric. Para utilizar atualizações automáticas do sistema operacional no Service Fabric, o cluster deve ser configurado para usar a camada de durabilidade Prata ou superior.

Certifique-se de que a seguinte chave de registro está definida como falsa para evitar que suas máquinas host do Windows institem atualizações descoordenadas: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

A seguir estão as propriedades do algoritmo de gerenciamento de recursos do Set de escala de máquina satisfaz as propriedades do Gerenciador de recursos da Compute Virtual Machine Para definir a chave de registro do WindowsUpdate como falsa:
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Configuração de atualização do cluster de malha de serviço do Azure
A seguir está a propriedade de modelo service fabric cluster Resource Manager para habilitar o upgrade automático:
```json
"upgradeMode": "Automatic",
```
Para atualizar manualmente seu cluster, baixe a distribuição de cabine/deb para uma máquina virtual de cluster e, em seguida, invoque o seguinte PowerShell:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Próximas etapas

* Crie um cluster em VMs ou em computadores que estejam executando o Windows Server: [Criação de um cluster do Service Fabric para o Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Criar um cluster em VMs ou em computadores que estejam executando o Linux: [Criar um cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Conheça as [opções de suporte ao Service Fabric](service-fabric-support.md)
