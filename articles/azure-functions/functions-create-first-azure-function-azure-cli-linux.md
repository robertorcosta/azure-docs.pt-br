---
title: Criar sua primeira função Linux no Azure
description: Saiba como criar sua primeira função hospedada no Linux no Azure usando as ferramentas de linha de comando, o Azure Functions Core Tools e a CLI do Azure.
author: ggailey777
ms.author: glenga
ms.date: 03/12/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc, fasttrack-edit
manager: gwallace
ms.openlocfilehash: 34f4dc4b36f4efd75c5a7cd14b3214886955a403
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329688"
---
# <a name="quickstart-create-your-first-function-hosted-on-linux-using-command-line-tools"></a>Início Rápido: Criar sua primeira função hospedada no Linux usando ferramentas de linha de comando

O Azure Functions permite executar seu código em um ambiente Linux [sem servidor](https://azure.com/serverless) sem que seja preciso primeiro criar uma VM ou publicar um aplicativo Web. A hospedagem no Linux exige [o Functions 2.x Runtime](functions-versions.md). As funções sem servidor são executadas no [plano de Consumo](functions-scale.md#consumption-plan).

Este artigo de início rápido explica como usar a CLI do Azure para criar seu primeiro aplicativo de funções em execução no Linux. O código da função é criado localmente e implantado no Azure usando o [Azure Functions Core Tools](functions-run-local.md).

As etapas a seguir têm suporte em um computador Mac, Windows ou Linux. Este artigo mostra como criar funções em JavaScript ou C#. Para saber como criar funções do Python, confira [Criar sua primeira função do Python usando o Core Tools e a CLI do Azure](functions-create-first-function-python.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar este exemplo, você deve ter o seguinte:

+ Instale o [Azure Functions Core Tools](./functions-run-local.md#v2) versão 2.6.666 ou posterior.

+ Instale a [CLI do Azure]( /cli/azure/install-azure-cli). Este artigo requer a CLI do Azure versão 2.0 ou posterior. Execute `az --version` descobrir a versão que você tem. Você também pode usar o [Azure Cloud Shell](https://shell.azure.com/bash).

+ Uma assinatura ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>Habilitar pacotes de extensão

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Criar um aplicativo de funções Linux no Azure

Você deve ter um aplicativo de funções para hospedar a execução de suas funções no Linux. O aplicativo de funções fornece um ambiente sem servidor para a execução do código da função. Ele permite que você agrupe funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos. Crie um aplicativo de funções em execução no Linux usando o comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

No comando a seguir, use um nome de aplicativo de funções exclusivo quando vir o espaço reservado `<app_name>` e o nome da conta de armazenamento de `<storage_name>`. O `<app_name>` também é o domínio do DNS padrão para o aplicativo de funções. O nome precisa ser exclusivo em todos os aplicativos no Azure. É possível definir o tempo de execução de `<language>` para seu aplicativo de funções, de `dotnet` (C#), `node` (JavaScript/TypeScript) ou `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westus --os-type Linux \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

Depois que o aplicativo de função for criado, você verá a seguinte mensagem:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Agora é possível publicar seu projeto no novo aplicativo de funções no Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]