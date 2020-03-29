---
title: Implantar em host dedicado
description: Use um host dedicado para obter um verdadeiro isolamento em nível de host para as cargas de trabalho do Azure Container Instances
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: adad0ddfc78530b3a3a7c139d9a95ec4790c8053
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934140"
---
# <a name="deploy-on-dedicated-hosts"></a>Implantar em hosts dedicados

"Dedicated" é um sku Azure Container Instances (ACI) que fornece um ambiente de computação isolado e dedicado para executar contêineres com segurança. O uso do sku dedicado resulta em cada grupo de contêineres ter um servidor físico dedicado em um data center do Azure, garantindo o isolamento total da carga de trabalho para ajudar a atender aos requisitos de segurança e conformidade da sua organização. 

O sku dedicado é apropriado para cargas de trabalho de contêiner que requerem isolamento da carga de trabalho de uma perspectiva física do servidor.

## <a name="prerequisites"></a>Pré-requisitos

* O limite padrão para qualquer assinatura usar o sku dedicado é 0. Se você quiser usar este sku para implantações de contêineres de produção, crie uma solicitação de suporte do [Azure][azure-support] para aumentar o limite.

## <a name="use-the-dedicated-sku"></a>Use o sku dedicado

> [!IMPORTANT]
> O uso do sku dedicado só está disponível na versão mais recente da API (2019-12-01) que está sendo lançado no momento. Especifique esta versão da API no modelo de implantação.
>

Começando com a versão 2019-12-01 `sku` da API, há uma propriedade a seção de propriedades do grupo de contêineres de um modelo de implantação, que é necessária para uma implantação de ACI. Atualmente, você pode usar essa propriedade como parte de um modelo de implantação do Azure Resource Manager para ACI. Saiba mais sobre a implantação de recursos ACI com um modelo no [Tutorial: Implante um grupo de vários contêineres usando um modelo de Gerenciador de recursos](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

A `sku` propriedade pode ter um dos seguintes valores:
* `Standard`- a escolha padrão de implantação da ACI, que ainda garante segurança em nível de hipervisor 
* `Dedicated`- usado para isolamento do nível de carga de trabalho com hosts físicos dedicados para o grupo de contêineres

## <a name="modify-your-json-deployment-template"></a>Modifique seu modelo de implantação JSON

No modelo de implantação, modifique ou adicione as seguintes propriedades:
* Em `resources`, `apiVersion` `2012-12-01`definido para .
* as propriedades do `sku` grupo de `Dedicated`contêineres, adicione uma propriedade com valor.

Aqui está um exemplo de trecho para a seção de recursos de um modelo de implantação de grupo de contêiner que usa o sku dedicado:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

A seguir está um modelo completo que implanta um grupo de contêiner de amostra executando uma única instância de contêiner:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>Implantar seu grupo de contêineres

Se você criou e editou o arquivo de modelo de implantação em sua área de trabalho, você pode carregá-lo para o diretório Cloud Shell arrastando o arquivo para ele. 

Crie um grupo de recursos com o comando [az group create.][az-group-create]

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implante o modelo com o comando [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Em alguns segundos, você deverá receber uma resposta inicial do Azure. Uma implantação bem-sucedida ocorre em um host dedicado.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
