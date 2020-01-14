---
title: Início Rápido – Criar um conjunto de dimensionamento de máquinas virtuais do Linux com um modelo do Azure
description: Saiba como criar rapidamente um dimensionamento de máquinas virtuais do Linux com um modelo do Azure Resource Manager que implante um aplicativo de exemplo e configure regras de dimensionamento automático
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
ms.openlocfilehash: 34cc269bc9355fa6744c7d31fff7e986baf1e049
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351102"
---
# <a name="quickstart-create-a-linux-virtual-machine-scale-set-with-an-azure-template"></a>Início Rápido: Criar um conjunto de dimensionamento de máquinas virtuais Linux com um modelo do Azure
Um conjunto de dimensionamento de máquinas virtuais permite implantar e gerenciar um conjunto de máquinas virtuais idênticas de dimensionamento automático. É possível dimensionar o número de VMs manualmente no conjunto de dimensionamento ou definir as regras para o dimensionamento automático com base no uso de recursos, como CPU, demanda de memória ou tráfego de rede. Um balanceador de carga do Azure então distribui o tráfego para as instâncias de VM no conjunto de dimensionamento. Neste guia de início rápido, você cria um conjunto de dimensionamento de máquinas virtuais e implanta um aplicativo de exemplo com um modelo do Azure Resource Manager.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando a CLI do Azure versão 2.0.29 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).


## <a name="define-a-scale-set-in-a-template"></a>Definir um conjunto de dimensionamento em um modelo
Os modelos do Azure Resource Manager permitem a implantação de grupos de recursos relacionados. Os modelos são escritos em JSON (JavaScript Object Notation) e definem todo o ambiente de infraestrutura do Azure para seu aplicativo. Em um único modelo, é possível criar o conjunto de dimensionamento de máquinas virtuais, instalar aplicativos e configurar regras de dimensionamento automático. Com o uso de variáveis e parâmetros, esse modelo pode ser reutilizado para atualizar conjuntos de dimensionamento existentes ou criar conjuntos adicionais. Você pode implantar modelos por meio do portal do Azure, do Azure CLI ou do Azure PowerShell ou de pipelines de integração contínua / entrega contínua (CI / CD).

Para obter mais informações sobre modelos, confira [Visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview#template-deployment-process). Para obter a sintaxe JSON e as propriedades, confira a referência de modelo [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets).

Para criar uma escala com um modelo, defina os recursos apropriados. As partes principais do tipo de recurso de conjunto de dimensionamento de máquinas virtuais são:

| Propriedade                     | Descrição da propriedade                                  | Exemplo de valor do modelo                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | Tipo de recurso do Azure a ser criado                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | O nome do conjunto de dimensionamento                                       | myScaleSet                                |
| local                     | O local para criar o conjunto de dimensionamento                     | Leste dos EUA                                   |
| sku.name                     | O tamanho da VM para cada instância do conjunto de dimensionamento                  | Standard_A1                               |
| sku.capacity                 | O número de instâncias de VM a serem criadas inicialmente           | 2                                         |
| upgradePolicy.mode           | Modo de atualização de instância de VM quando ocorrem alterações              | Automático                                 |
| imageReference               | A plataforma ou a imagem personalizada a ser usada nas instâncias de VM | Canonical Ubuntu Server 16.04-LTS         |
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
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
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

O modelo [servidor HTTP Python no Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) usa a extensão de Script personalizado para instalar o [Bottle](https://bottlepy.org/docs/dev/), uma estrutura de Web Python e um servidor HTTP simples. 

Dois scripts são definidos em **fileUris** - *installserver.sh* e *workserver.py*. Esses arquivos são baixados do GitHub e o *commandToExecute* executa `bash installserver.sh` para instalar e configurar o aplicativo:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
          ],
          "commandToExecute": "bash installserver.sh"
        }
      }
    }
  ]
}
```


## <a name="deploy-the-template"></a>Implantar o modelo
É possível implantar o modelo [servidor HTTP Python no Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) com o seguinte botão **Implantar no Azure**. Esse botão abre o Portal do Azure, carrega o modelo completo e solicita alguns parâmetros, como o nome de um conjunto de dimensionamento, a contagem de instâncias e as credenciais de administrador.

[![Implantar o modelo no Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

Também é possível usar a CLI do Azure para instalar o servidor HTTP Python no Linux com [az group deployment create](/cli/azure/group/deployment) da seguinte maneira:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Responda os prompts para fornecer nome do conjunto de dimensionamento, uma contagem de instâncias e credenciais de administrador para as instâncias de VM. Levará alguns minutos para o conjunto de dimensionamento e os recursos de suporte serem criados.


## <a name="test-your-scale-set"></a>Testar seu conjunto de dimensionamento
Para ver seu conjunto de dimensionamento em ação, acesse o aplicativo Web de exemplo em um navegador da Web. Obtenha o endereço IP público do balanceador de carga com a [az network public-ip list](/cli/azure/network/public-ip) da seguinte maneira:

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Insira o endereço IP público do balanceador de carga em um navegador da Web no formato *http:\//publicIpAddress:9000/do_work*. O balanceador de carga distribui o tráfego para uma de suas instâncias de VM, conforme mostrado no exemplo a seguir:

![Página da Web padrão em NGINX](media/virtual-machine-scale-sets-create-template/running-python-app.png)


## <a name="clean-up-resources"></a>Limpar os recursos
Quando não for mais necessário, use [az group delete](/cli/azure/group) para remover o grupo de recursos, o conjunto de dimensionamento e todos os recursos relacionados, como demonstrado a seguir. O parâmetro `--no-wait` retorna o controle ao prompt sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você criou um conjunto de dimensionamento do Linux com um modelo do Azure e usou uma Extensão de Script Personalizado para instalar um servidor Web Python básico nas instâncias de VM. Para saber mais, siga para o tutorial sobre como criar e gerenciar um conjunto de dimensionamento de máquinas virtuais do Azure.

> [!div class="nextstepaction"]
> [Criar e gerenciar um conjunto de dimensionamento de máquinas virtuais do Azure](tutorial-create-and-manage-cli.md)
