---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4075b8d05c179e8115ff46c9f82751817372491
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842075"
---
## <a name="run-the-function-locally"></a>Executar a função localmente

O Visual Studio Code integra-se ao [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) para permitir que você execute esse projeto em seu computador de desenvolvimento local antes de publicar no Azure.

1. Para chamar sua função, pressione F5 para iniciar o projeto de aplicativo de funções. A saída do Core Tools é exibida no painel **Terminal**.

1. Se ainda não tiver instalado o Azure Functions Core Tools, selecione **Instalar** no prompt. Quando o Core Tools for instalado, seu aplicativo será iniciado no painel **Terminal**.

1. No painel **Terminal**, copie o ponto de extremidade de URL da sua função disparada por HTTP. 

    ![Saída local do Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Cole a URL para a solicitação HTTP na barra de endereços do navegador. Acrescente a cadeia de caracteres de consulta `?name=<yourname>` a essa URL e execute a solicitação GET. 

1. Uma resposta é retornada, semelhante ao seguinte em um navegador:

    ![Resposta da função localhost no navegador](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Pressione Shift + F5 para parar o Core Tools e desconectar o depurador.
