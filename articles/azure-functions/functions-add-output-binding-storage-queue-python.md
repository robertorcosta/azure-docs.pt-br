---
title: Adicionar uma associação de fila do Armazenamento do Azure à sua função do Python
description: Saiba como adicionar uma associação de saída da fila do Armazenamento do Azure à sua função do Python usando a CLI do Azure e o Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 92ee9b0a8a0906bca31d7dcb1730c3464d0d6cbc
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839186"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Adicionar uma associação de fila do Armazenamento do Azure à sua função do Python

O Azure Functions lhe permite conectar os serviços do Azure e outros recursos às funções sem precisar escrever seu próprio código de integração. Essas *associações*, que representam a entrada e a saída, são declaradas na definição de função. Dados de associações são fornecidos à função como parâmetros. Um *gatilho* é um tipo especial de associação de entrada. Embora uma função tenha apenas um gatilho, ela pode ter várias associações de entrada e de saída. Para saber mais, confira [Conceitos de gatilhos e de associações do Azure Functions](functions-triggers-bindings.md).

Este artigo mostra como integrar a função criada no [artigo de início rápido anterior](functions-create-first-function-python.md) com uma fila de Armazenamento do Azure. A associação de saída que você adiciona a essa função escreve dados de uma solicitação HTTP em uma mensagem na fila.

A maioria das associações requer uma cadeia de conexão armazenada que o Functions usa para acessar o serviço vinculado. Para facilitar essa conexão, use a conta de armazenamento que você criou com o seu aplicativo de funções. A conexão com essa conta já está armazenada em uma configuração de aplicativo chamada `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este artigo, conclua as etapas na [parte 1 do início rápido do Python](functions-create-first-function-python.md).

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>Baixar as configurações do aplicativo de funções

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Habilitar pacotes de extensão

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Agora, você pode adicionar a associação de saída do Armazenamento ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar uma associação de saída

No Functions, cada tipo de associação requer que um `direction`, `type` e um `name` exclusivo seja definido no arquivo functions.json. A maneira como você define esses atributos depende do idioma do seu aplicativo de funções.

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Adicionar o código que usa a associação de saída

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

Ao usar uma associação de saída, não é necessário usar o código do SDK do Armazenamento do Azure para se autenticar, para obter uma referência de fila ou para escrever dados. O tempo de execução do Functions e a associação de saída da fila fazem essas tarefas para você.

## <a name="run-the-function-locally"></a>Executar a função localmente

Como anteriormente, use o seguinte comando para iniciar o tempo de execução do Functions localmente:

```bash
func host start
```

> [!NOTE]  
> Já que no Início Rápido anterior você habilitou os pacotes de extensões no host.json, a [extensão de associação do armazenamento](functions-bindings-storage-blob.md#packages---functions-2x) foi baixada e instalada para você durante a inicialização, juntamente com outras extensões de associação da Microsoft.

Copie a URL da função `HttpTrigger` da saída do tempo de execução de função e cole-a na barra de endereços do navegador. Acrescente o valor de cadeia de consulta `?name=<yourname>` a essa URL e execute a solicitação. Você deve ver a mesma resposta no navegador como você viu no artigo anterior.

Dessa vez, a associação de saída também cria uma fila denominada `outqueue` em sua Conta de armazenamento e adiciona uma mensagem com essa mesma cadeia de caracteres.

Em seguida, use a CLI do Azure para exibir a nova fila e verifique se uma mensagem foi adicionada. Também é possível exibir sua fila usando o [Gerenciador de Armazenamento do Microsoft Azure][Azure Storage Explorer] ou no [portal do Azure](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Definir a conexão da Conta de armazenamento

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

### <a name="query-the-storage-queue"></a>Consultar a Fila de armazenamento

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

Agora, chegou a hora de republicar o aplicativo de funções atualizado no Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Novamente, é possível usar cURL ou um navegador para testar a função implantada. Como antes, acrescente a cadeia de caracteres de consulta `&name=<yourname>` à URL, como mostrado no exemplo a seguir:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

É possível [examinar a Mensagem da fila de armazenamento](#query-the-storage-queue) para verificar se a associação de saída gera novamente uma nova mensagem na fila.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Você atualizou a função disparada por HTTP para gravar dados em uma fila de armazenamento. Para saber mais sobre como desenvolver o Azure Functions usando o Python, confira o [Guia do desenvolvedor de Python para o Azure Functions](functions-reference-python.md) e [Gatilhos e associações do Azure Functions](functions-triggers-bindings.md). Para obter exemplos de projetos de funções completos no Python, confira os [Exemplos de funções de Python](/samples/browse/?products=azure-functions&languages=python). 

Em seguida, você deve habilitar o monitoramento do Application Insights para seu aplicativo de funções:

> [!div class="nextstepaction"]
> [Habilitar a integração do Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/