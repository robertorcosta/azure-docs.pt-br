---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: 43da0ea4ddfc5410425465d436522523739218fe
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91408519"
---
## <a name="run-the-function-locally"></a>Executar a função localmente

O Visual Studio Code integra-se ao [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) para permitir que você execute esse projeto em seu computador de desenvolvimento local antes da publicação no Azure.

1. Para chamar sua função, pressione <kbd>F5</kbd> para iniciar o projeto de aplicativo de funções. A saída do Core Tools é exibida no painel **Terminal**.

1. Se ainda não tiver instalado o Azure Functions Core Tools, selecione **Instalar** no prompt. Quando o Core Tools for instalado, seu aplicativo será iniciado no painel **Terminal**. Você pode ver o ponto de extremidade de URL de sua função disparada por HTTP localmente.

    ![Saída do VS Code de função local](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Com o Core Tools em execução, navegue até a URL a seguir para executar uma solicitação GET, que inclui a cadeia de caracteres de consulta `?name=Functions`.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Uma resposta é retornada, semelhante ao seguinte em um navegador:

    ![Navegador – saída de exemplo de localhost](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. As informações sobre a solicitação são mostradas no painel **Terminal**.

    ![Início do host da tarefa – saída do terminal do VS Code](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Pressione <kbd>Ctrl + C</kbd> para parar o Core Tools e desconectar o depurador.
