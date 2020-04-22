---
title: Use Java e Gradle para publicar uma função no Azure
description: Crie e publique uma função acionada por HTTP para o Azure com Java e Gradle.
author: KarlErickson
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: 34aab24bf39e387715cfa5783b801d45ed488750
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732721"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Use Java e Gradle para criar e publicar uma função no Azure

Este artigo mostra como construir e publicar um projeto de função Java para funções do Azure com a ferramenta de linha de comando Gradle. Quando você terminar, o código de função será executado no Azure em um [plano de hospedagem sem servidor](functions-scale.md#consumption-plan) e será disparado por uma solicitação HTTP. 

> [!NOTE]
> Se gradle não é sua ferramenta de desenvolvimento preferida, confira nossos tutoriais semelhantes para desenvolvedores Java usando [Maven,](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java) [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) e [VS Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java).

## <a name="prerequisites"></a>Pré-requisitos

Para desenvolver funções usando Java, você deve ter o seguinte instalado:

- [Java Developer Kit](https://aka.ms/azure-jdks), versão 8
- [CLI do Azure]
- [Azure Functions Core Tools](./functions-run-local.md#v2) versão 2.6.666 ou posterior
- [Gradle](https://gradle.org/), versão 4.10 e acima

Você também precisará de uma assinatura ativa do Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> A variável de ambiente JAVA_HOME deve ser definida como o local de instalação do JDK para concluir este guia de início rápido.

## <a name="prepare-a-functions-project"></a>Prepare um projeto de Funções

Use o seguinte comando para clonar o projeto de amostra:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Abra `build.gradle` e `appName` altere a seção a seguir para um nome único para evitar o conflito de nomes de domínio ao ser implantado no Azure. 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```

Abra o novo arquivo Function.java do caminho *src/main/java* em um editor de texto e examine o código gerado. Esse código é uma função [disparada por HTTP](functions-bindings-http-webhook.md) que ecoa o corpo da solicitação. 

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Executar a função localmente

Execute o seguinte comando para construir e execute o projeto de função:

```bash
gradle jar --info
gradle azureFunctionsRun
```
Você verá uma saída semelhante à seguinte no Azure Functions Core Tools quando executar o projeto localmente:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Acione a função a partir da linha de comando usando o seguinte comando cURL em uma nova janela de terminal:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

A saída esperada é a seguinte:

<pre>
Hello AzureFunctions!
</pre>

A [chave de função](functions-bindings-http-webhook-trigger.md#authorization-keys) não é necessária na execução local.  
Use `Ctrl+C` no terminal para interromper o código da função.

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Implantar a função no Azure

Um aplicativo de funções e os recursos relacionados são criados no Azure quando você implanta o aplicativo de funções pela primeira vez. Para fazer a implantação, use o comando [az login](/cli/azure/authenticate-azure-cli) da CLI do Azure para entrar em sua assinatura do Azure. 

```azurecli
az login
```

> [!TIP]
> Caso a sua conta possa acessar várias assinaturas, use [az account set](/cli/azure/account#az-account-set) para definir a assinatura padrão para essa sessão. 

Use o comando a seguir para implantar o projeto em um novo aplicativo de funções. 

```bash
gradle azureFunctionsDeploy
```

Isso cria os seguintes recursos no Azure, com base nos valores do arquivo build.gradle:

+ Grupo de recursos. Nomeado com o _resourceGroup_ fornecido.
+ Conta de armazenamento. Necessária para o Functions. O nome é gerado aleatoriamente de acordo com os requisitos de nome da conta de armazenamento.
+ Plano do Serviço de Aplicativo. Plano de consumo sem servidor hospedagem para seu aplicativo de função no _aplicativo especificadoRegião_. O nome é gerado aleatoriamente.
+ Aplicativo de funções. Um aplicativo de funções é a unidade de implantação e execução para suas funções. O nome é o _appName_, acrescentado a um número gerado aleatoriamente. 

A implantação também empacota os arquivos de projeto e implanta-os no novo aplicativo de funções usando [zip deployment](functions-deployment-technologies.md#zip-deploy), com o modo run-from-package habilitado.

Como o gatilho HTTP que publicamos usa `authLevel = AuthorizationLevel.FUNCTION`, você precisa obter a chave de função para chamar o ponto de extremidade da função via HTTP. A maneira mais fácil de obter a chave de função é no [portal do Azure].

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>Obter a URL do gatilho HTTP

Obtenha a URL necessária para disparar a função, com a chave de função, no portal do Azure. 

1. Navegue até o [portal do Azure], entre nele, digite o _appName_ do aplicativo de funções em **Pesquisar** na parte superior da página e pressione Enter.
 
1. No aplicativo de funções, expanda **Funções (Somente Leitura)**, selecione a função e, em seguida, selecione **</> Obter URL da função** no canto superior direito. 

    ![Copiar a URL da função do Portal do Azure](./media/functions-create-java-maven/get-function-url-portal.png)

1. Escolha **padrão (Chave de função)** e selecione **Copiar**. 

Agora você pode usar a URL copiada para acessar a função.

## <a name="verify-the-function-in-azure"></a>Verificar a função no Azure

Para verificar o aplicativo de funções em execução no Azure usando `cURL`, substitua a URL da amostra abaixo pela URL copiada do portal.

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpExample?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Isso envia uma solicitação POST ao ponto de extremidade da função com `AzureFunctions` no corpo da solicitação. Você verá a resposta a seguir.

<pre>
Hello AzureFunctions!
</pre>

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Próximas etapas

Você criou um projeto de funções Java com uma função disparada por HTTP, executou-o no computador local e implantou-o no Azure. Agora, estenda sua função por meio da...

> [!div class="nextstepaction"]
> [Adição de uma associação de saída de fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-java.md)


[CLI do Azure]: /cli/azure
[Azure portal]: https://portal.azure.com
