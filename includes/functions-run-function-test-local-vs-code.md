---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4af3c202d4f00c4ac3041921175c92226f0db7c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964050"
---
## <a name="run-the-function-locally"></a>Executar a função localmente

O Visual Studio Code integra-se ao [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) para permitir que você execute esse projeto em seu computador de desenvolvimento local antes de publicar no Azure.

1. Para chamar sua função, pressione F5 para iniciar o projeto de aplicativo de funções. A saída do Core Tools é exibida no painel **Terminal**.

1. Se ainda não tiver instalado o Azure Functions Core Tools, selecione **Instalar** no prompt. Quando o Core Tools for instalado, seu aplicativo será iniciado no painel **Terminal**. Você pode ver o ponto de extremidade de URL de sua função disparada por HTTP localmente. 

    ![Saída local do Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Com o Core Tools em execução, navegue até a URL a seguir para executar uma solicitação GET, que inclui a cadeia de caracteres de consulta `?name=Functions`.

    <http://localhost:7071/api/HttpExample?name=Functions>

1. Uma resposta é retornada, semelhante ao seguinte em um navegador:

    ![Resposta da função localhost no navegador](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. As informações sobre a solicitação são mostradas no painel **Terminal**.

    ![Execução de função no painel Terminal](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Pressione Ctrl + C para parar o Core Tools e desconectar o depurador.
