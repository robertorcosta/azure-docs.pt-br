---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 6944d2e6a8f762e62c14f6f3fa3f600a9b3c333e
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84195018"
---
## <a name="run-the-function-locally"></a>Executar a função localmente

Execute sua função iniciando o host de runtime do Azure Functions local da pasta *LocalFunctionProj*:

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-javascript,programming-language-python"
```
func host start
```
::: zone-end

::: zone pivot="programming-language-typescript"
```
npm install
npm start
```
::: zone-end

::: zone pivot="programming-language-java"
```
mvn clean package 
mvn azure-functions:run
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
> Se HttpExample não aparece conforme mostrado abaixo, é provável que você tenha iniciado o host de fora da pasta raiz do projeto. Nesse caso, use **Ctrl**+**C** para parar o host, navegue até a pasta raiz do projeto e execute o comando anterior novamente.

Copie a URL da função `HttpExample` dessa saída para um navegador e acrescente a cadeia de caracteres de consulta `?name=<your-name>`, fazendo com que a URL completa seja `http://localhost:7071/api/HttpExample?name=Functions`. O navegador deve exibir uma mensagem como `Hello Functions`:

![Resultado da execução local da função no navegador](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

O terminal em que você iniciou seu projeto também mostra a saída do log conforme você faz solicitações.

Quando você estiver pronto, use **Ctrl**+**C** e escolha `y` para interromper o host de funções.
