---
title: Atualizar a configuração de um cluster de Service Fabric do Azure
description: Saiba como atualizar a configuração que executa um cluster do Service Fabric no Azure usando um modelo do Resource Manager.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 476a2d910b916ea29132b108478d06f756454813
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463295"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Atualizar a configuração de um cluster no Azure 

Este artigo descreve como personalizar as várias configurações de malha e a política de atualização para o cluster do Service Fabric. Para clusters hospedados no Azure, você pode personalizá-los através do [portal do Azure](https://portal.azure.com) ou utilizando um modelo do Azure Resource Manager.

> [!NOTE]
> Nem todas as configurações estão disponíveis no portal, e é uma [prática recomendada personalizá-la usando um modelo de Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code); O portal é apenas para Service Fabric cenário de Dev\Test.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Personalize as configurações do cluster, utilizando os modelos do Resource Manager
Clusters do Azure podem ser configurados por meio do modelo do Resource Manager do JSON. Para saber mais sobre as diferentes configurações, consulte [Definições de configuração para clusters](service-fabric-cluster-fabric-settings.md). Por exemplo, as etapas a seguir mostram como adicionar uma nova configuração *MaxDiskQuotaInMB* à seção *diagnóstico* usando o Gerenciador de recursos do Azure.

1. Acesse https://resources.azure.com
2. Navegue até sua assinatura expandindo **assinaturas** ->  **\<Sua assinatura >**  -> **resourceGroups** ->  **\<Seu grupo de recursos>**  -> **provedores** -> **Microsoft.ServiceFabric** -> **clusters** ->  **\<Nome do seu cluster>**
3. No canto superior direito, selecione **Leitura/Gravação.**
4. Selecione **Editar** e atualize o elemento `fabricSettings` JSON e adicione um novo elemento:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Você também pode personalizar as configurações de cluster em uma das maneiras a seguir com o Azure Resource Manager:

- Use o [portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) para exportar e atualizar o modelo do Gerenciador de Recursos.
- Use o [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) para exportar e atualizar o modelo do Gerenciador de Recursos.
- Use a [CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) para exportar e atualizar o modelo do Gerenciador de Recursos.
- Use os comandos Azure PowerShell [set-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) e [Remove-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting) para modificar a configuração diretamente.
- Usar os comandos de configuração do cluster[az sf da CLI do Azure](https://docs.microsoft.com/cli/azure/sf/cluster/setting) para modificar a configuração diretamente.

## <a name="next-steps"></a>Próximos passos
* Saiba mais sobre [configurações de cluster do Service Fabric](service-fabric-cluster-fabric-settings.md).
* Saiba como [reduzir e escalar horizontalmente seu cluster](service-fabric-cluster-scale-up-down.md).
