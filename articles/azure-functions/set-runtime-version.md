---
title: Como direcionar para versões do Azure Functions runtime
description: O Azure Functions é compatível com várias versões do runtime. Saiba como especificar a versão de runtime de um aplicativo de funções hospedado no Azure.
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 6f93ac7bcbd25c1b120cfeecae9dd4353524855f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230316"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Como direcionar para versões do Azure Functions runtime

Uma execução do aplicativo de funções em uma versão específica do Azure Functions runtime. There are two major versions: [1.x and 2.x](functions-versions.md), with version 3.x in preview. Por padrão, os aplicativos de funções criados na versão 2.x do runtime. Este artigo explica como configurar um aplicativo de funções no Azure para executar a versão que você escolher. Para obter informações sobre como configurar um ambiente de desenvolvimento local para uma versão específica, consulte [Código e teste local do Azure Functions](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Atualizações de versão automática e manual

Azure Functions lets you target a specific version of the runtime by using the `FUNCTIONS_EXTENSION_VERSION` application setting in a function app. O aplicativo de funções é mantido na versão principal especificada até que você escolha explicitamente mudar para uma nova versão.

If you specify only the major version, the function app is automatically updated to new minor versions of the runtime when they become available. Novas versões secundárias não introduzem alterações consideráveis. Se você especificar uma versão secundária (por exemplo, "2.0.12345"), o aplicativo de funções será fixado nessa versão específica até que seja explicitamente alterado.

> [!NOTE]
> If you pin to a specific version of Azure Functions, and then try to publish to Azure using Visual Studio, a dialog window will pop up prompting you to update to the latest version or cancel the publish. To avoid this, add the `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` property in your `.csproj` file.

Quando uma nova versão está disponível publicamente, um prompt no portal oferece a possibilidade de atualizar para essa versão. Depois de mudar para uma nova versão, você sempre poderá usar a configuração de aplicativo `FUNCTIONS_EXTENSION_VERSION` para voltar para uma versão anterior do tempo de execução.

The following table shows the `FUNCTIONS_EXTENSION_VERSION` values for each major version to enable automatic updates:

| Versão principal | `FUNCTIONS_EXTENSION_VERSION` value |
| ------------- | ----------------------------------- |
| 3.x (preview) | `~3` |
| 2. x  | `~2` |
| 1.x | `~1` |

Uma alteração na versão do runtime faz com que seu aplicativo de função reinicie.

## <a name="view-and-update-the-current-runtime-version"></a>Exibir e atualizar a versão de runtime atual

You can change the runtime version used by your function app. Because of the potential of breaking changes, you can only change the runtime version before you have created any functions in your function app. 

> [!IMPORTANT]
> Embora a versão de runtime é determinada pelo `FUNCTIONS_EXTENSION_VERSION` configuração, você deve fazer essa alteração no portal do Azure e não, alterando a configuração diretamente. Isso ocorre porque o portal valida as alterações e faz outras alterações relacionadas, conforme necessário.

### <a name="from-the-azure-portal"></a>No portal do Azure

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Using the Azure portal, you can't change the runtime version for a function app that already contains functions.

### <a name="view-and-update-the-runtime-version-using-azure-cli"></a>Na CLI do Azure

Também é possível exibir e definir o `FUNCTIONS_EXTENSION_VERSION` da CLI do Azure.

>[!NOTE]
>Como outras configurações podem ser afetadas pela versão de runtime, você deverá alterar a versão no portal. Quando você altera as versões de runtime, o portal automaticamente executa outras atualizações necessárias como a versão do Node.js e a pilha de runtime.  

Usando a CLI do Azure, exiba a versão de runtime atual com o comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings).

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Nesse código, substitua `<function_app>` pelo nome de seu aplicativo de funções. Substitua também `<my_resource_group>` pelo nome do grupo de recursos para seu aplicativo de funções. 

Você verá `FUNCTIONS_EXTENSION_VERSION` na seguinte saída, que foi truncada para maior clareza:

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

É possível atualizar a configuração `FUNCTIONS_EXTENSION_VERSION` o aplicativo de funções com o comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings).

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Substitua `<function_app>` pelo nome do aplicativo de funções. Substitua também `<my_resource_group>` pelo nome do grupo de recursos para seu aplicativo de funções. Substitua também `<version>` por uma versão de runtime 1.x válida ou `~2` para a versão 2.x.

Execute esse comando a partir do [Azure Cloud Shell](../cloud-shell/overview.md) escolhendo **Experimentar** no exemplo de código anterior. Você também pode usar a [CLI do Azure localmente](/cli/azure/install-azure-cli) para executar esse comando após a execução de [az login](/cli/azure/reference-index#az-login) para entrar.



## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Direcionar para o runtime 2.0 em seu ambiente de desenvolvimento local](functions-run-local.md)

> [!div class="nextstepaction"]
> [Consulte as notas de versão para versões de runtime](https://github.com/Azure/azure-webjobs-sdk-script/releases)
