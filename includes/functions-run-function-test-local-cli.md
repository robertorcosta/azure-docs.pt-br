---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: d53c41752d57a27ebea9bd60f7e723dab1e7308a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "78190820"
---
## <a name="run-the-function-locally"></a>Executar a função localmente

Execute sua função iniciando o host de runtime do Azure Functions local da pasta *LocalFunctionProj*:

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-javascript,programming-language-python"
```
func start
```
::: zone-end

::: zone pivot="programming-language-typescript"
```
npm install
npm start
```
::: zone-end

Perto do fim da saída, devem aparecer as seguintes linhas: 

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...

</pre>

>[!NOTE]  
> Se HttpExample não aparece conforme mostrado abaixo, é provável que você tenha iniciado o host de dentro da pasta *HttpExample*. Nesse caso, use **Ctrl**+**C** para parar o host, navegue até a pasta *LocalFunctionProj* pai e execute o comando anterior novamente.

Copie a URL da função `HttpExample` dessa saída para um navegador e acrescente a cadeia de caracteres de consulta `?name=<your-name>`, fazendo com que a URL completa seja `http://localhost:7071/api/HttpExample?name=Functions`. O navegador deve exibir uma mensagem como `Hello Functions`:

![Resultado da execução local da função no navegador](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

O terminal em que você executou `func start` também mostra a saída do log conforme você faz solicitações.

Quando você estiver pronto, use **Ctrl**+**C** e escolha `y` para interromper o host de funções.