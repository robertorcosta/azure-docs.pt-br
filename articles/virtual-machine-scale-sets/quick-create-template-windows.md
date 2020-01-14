---
title: Início Rápido – Criar um conjunto de dimensionamento de máquinas virtuais do Windows com um modelo do Azure
description: Saiba como criar rapidamente um dimensionamento de máquinas virtuais do Windows com um modelo do Azure Resource Manager que implante um aplicativo de exemplo e configure regras de dimensionamento automático
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: cynthn
ms.openlocfilehash: f3dd2fd092e49a0e526856f1e55211ec1837cfa4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351088"
---
# <a name="quickstart-create-a-windows-virtual-machine-scale-set-with-an-azure-template"></a>Início Rápido: Criar um conjunto de dimensionamento de máquinas virtuais do Windows com um modelo do Azure

Um conjunto de dimensionamento de máquinas virtuais permite implantar e gerenciar um conjunto de máquinas virtuais idênticas de dimensionamento automático. É possível dimensionar o número de VMs manualmente no conjunto de dimensionamento ou definir as regras para o dimensionamento automático com base no uso de recursos, como CPU, demanda de memória ou tráfego de rede. Um balanceador de carga do Azure então distribui o tráfego para as instâncias de VM no conjunto de dimensionamento. Neste guia de início rápido, você cria um conjunto de dimensionamento de máquinas virtuais e implanta um aplicativo de exemplo com um modelo do Azure Resource Manager.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="define-a-scale-set-in-a-template"></a>Definir um conjunto de dimensionamento em um modelo
Os modelos do Azure Resource Manager permitem a implantação de grupos de recursos relacionados. Os modelos são escritos em JSON (JavaScript Object Notation) e definem todo o ambiente de infraestrutura do Azure para seu aplicativo. Em um único modelo, é possível criar o conjunto de dimensionamento de máquinas virtuais, instalar aplicativos e configurar regras de dimensionamento automático. Com o uso de variáveis e parâmetros, esse modelo pode ser reutilizado para atualizar conjuntos de dimensionamento existentes ou criar conjuntos adicionais. Você pode implantar modelos por meio do portal do Azure, do Azure CLI ou do Azure PowerShell ou de pipelines de integração contínua / entrega contínua (CI / CD).

Para obter mais informações sobre modelos, confira [Visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview#template-deployment-process). Para obter a sintaxe JSON e as propriedades, confira a referência de modelo [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets).

Um modelo define a configuração de cada tipo de recurso. Um tipo de recurso de conjunto de dimensionamento de máquinas virtuais é semelhante a uma VM individual. As partes principais do tipo de recurso de conjunto de dimensionamento de máquinas virtuais são:

| Propriedade                     | Descrição da propriedade                                  | Exemplo de valor do modelo                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | Tipo de recurso do Azure a ser criado                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | O nome do conjunto de dimensionamento                                       | myScaleSet                                |
| local                     | O local para criar o conjunto de dimensionamento                     | Leste dos EUA                                   |
| sku.name                     | O tamanho da VM para cada instância do conjunto de dimensionamento                  | Standard_A1                               |
| sku.capacity                 | O número de instâncias de VM a serem criadas inicialmente           | 2                                         |
| upgradePolicy.mode           | Modo de atualização de instância de VM quando ocorrem alterações              | Automático                                 |
| imageReference               | A plataforma ou a imagem personalizada a ser usada nas instâncias de VM | Datacenter do Microsoft Windows Server 2016  |
| osProfile.computerNamePrefix | O prefixo do nome de cada instância de VM                     | myvmss                                    |
| osProfile.adminUsername      | O nome de usuário de cada instância de VM                        | azureuser                                 |
| osProfile.adminPassword      | A senha de cada instância de VM                        | P@ssw0rd!                                 |

 O exemplo a seguir mostra a definição de recurso do conjunto de dimensionamento principal. Para personalizar um modelo de conjunto de dimensionamento, altere o tamanho da VM ou a capacidade inicial, ou use uma plataforma diferente ou uma imagem personalizada.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

 Para manter o exemplo curto, a configuração da NIC (placa de interface de rede) virtual não é mostrada. Os componentes adicionais, como um balanceador de carga, também não são mostrados. Um modelo completo de conjunto de dimensionamento é mostrado [no fim deste artigo](#deploy-the-template).


## <a name="add-a-sample-application"></a>Adicionar um aplicativo de exemplo
Para testar seu conjunto de dimensionamento, instale um aplicativo Web básico. Quando você implanta um conjunto de dimensionamento, as extensões da VM podem fornecer tarefas de configuração e de automação pós-implantação, tais como instalar um aplicativo. Os scripts podem ser baixados do armazenamento do Azure ou do GitHub, ou fornecidos ao Portal do Azure no tempo de execução da extensão. Para aplicar uma extensão ao conjunto de dimensionamento, adicione a seção *extensionProfile* ao exemplo anterior do recurso. O perfil da extensão normalmente define as propriedades a seguir:

- Tipo de extensão
- Editor de extensão
- Versão da extensão
- Local dos scripts de configuração ou de instalação
- Comandos a serem executados nas instâncias da VM

O modelo de exemplo do [aplicativo ASP.NET no Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) usa a extensão de DSC do PowerShell para instalar um aplicativo ASP.NET MVC, que é executado no IIS. 

Um script de instalação é baixado do GitHub, conforme definido na *URL*. A extensão executa *InstallIIS* do script *IISInstall.ps1*, conforme definido em *função* e *Script*. O aplicativo ASP.NET é fornecido como um pacote de Implantação da Web, e também pode ser baixado do GitHub, conforme definido em *WebDeployPackagePath*:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "Microsoft.Powershell.DSC",
      "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.9",
        "autoUpgradeMinorVersion": true,
        "forceUpdateTag": "1.0",
        "settings": {
          "configuration": {
            "url": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/DSC/IISInstall.ps1.zip",
            "script": "IISInstall.ps1",
            "function": "InstallIIS"
          },
          "configurationArguments": {
            "nodeName": "localhost",
            "WebDeployPackagePath": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/WebDeploy/DefaultASPWebApp.v1.0.zip"
          }
        }
      }
    }
  ]
}
```

## <a name="deploy-the-template"></a>Implantar o modelo
É possível implantar o modelo [Aplicativo ASP.NET MVC no Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) com o botão **Implantar no Azure** a seguir. Esse botão abre o Portal do Azure, carrega o modelo completo e solicita alguns parâmetros, como o nome de um conjunto de dimensionamento, a contagem de instâncias e as credenciais de administrador.

[![Implantar o modelo no Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

Você também pode usar o Azure PowerShell para instalar o aplicativo ASP.NET no Windows com [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) da seguinte maneira:

```azurepowershell-interactive
# Create a resource group
New-AzResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateURI https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

Responda os prompts para fornecer nome do conjunto de dimensionamento e credenciais de administrador para as instâncias de VM. Pode levar 10 a 15 minutos para o conjunto de dimensionamento ser criado e aplicar a extensão para configurar o aplicativo.


## <a name="test-your-scale-set"></a>Testar seu conjunto de dimensionamento
Para ver seu conjunto de dimensionamento em ação, acesse o aplicativo Web de exemplo em um navegador da Web. Obtenha o endereço IP público do balanceador de carga com [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) da seguinte maneira:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Insira o endereço IP público do balanceador de carga em um navegador da Web no formato *http:\//publicIpAddress/MyApp*. O balanceador de carga distribui o tráfego para uma de suas instâncias de VM, conforme mostrado no exemplo a seguir:

![Execução do site do IIS](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Limpar os recursos
Quando não for mais necessário, você poderá usar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, conjunto de dimensionamento. O parâmetro `-Force` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso. O parâmetro `-AsJob` retorna o controle ao prompt sem aguardar a conclusão da operação.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você criou um conjunto de dimensionamento do Windows com um modelo do Azure e usou a Extensão de DSC do PowerShell para instalar um aplicativo ASP.NET básico nas instâncias de VM. Para saber mais, siga para o tutorial sobre como criar e gerenciar um conjunto de dimensionamento de máquinas virtuais do Azure.

> [!div class="nextstepaction"]
> [Criar e gerenciar um conjunto de dimensionamento de máquinas virtuais do Azure](tutorial-create-and-manage-powershell.md)
