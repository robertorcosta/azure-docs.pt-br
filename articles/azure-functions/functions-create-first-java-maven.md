---
title: Usar o Java e o Maven/Gradle para publicar uma função no Azure
description: Crie e publique uma função disparada por HTTP no Azure com o Java e o Maven ou o Gradle.
author: KarlErickson
ms.author: karler
ms.topic: quickstart
ms.date: 08/10/2018
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: ad3b38a12020c56c31e03879b3fbcb9a8dda25f1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79136860"
---
# <a name="quickstart-use-java-and-mavengradle-to-create-and-publish-a-function-to-azure"></a>Início Rápido: Usar o Java e o Maven/Gradle para criar e publicar uma função no Azure

Este artigo mostra como criar e publicar uma função Java no Azure Functions com a ferramenta de linha de comando Maven/Gradle. Quando você terminar, o código de função será executado no Azure em um [plano de hospedagem sem servidor](functions-scale.md#consumption-plan) e será disparado por uma solicitação HTTP.

<!--
> [!NOTE] 
> You can also create a Kotlin-based Azure Functions project by using the azure-functions-kotlin-archetype instead. Visit the [GitHub repository](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) for more information.
-->

## <a name="prerequisites"></a>Pré-requisitos

Para desenvolver funções usando Java, você deve ter o seguinte instalado:

- [Java Developer Kit](https://aka.ms/azure-jdks), versão 8
- [CLI do Azure]
- [Azure Functions Core Tools](./functions-run-local.md#v2) versão 2.6.666 ou posterior
::: zone pivot="java-build-tools-maven" 
- [Apache Maven](https://maven.apache.org), versão 3.0 ou posterior
::: zone-end

::: zone pivot="java-build-tools-gradle"  
- [Gradle](https://gradle.org/), versão 4.10 e superior
::: zone-end 

Você também precisará de uma assinatura ativa do Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


> [!IMPORTANT]
> A variável de ambiente JAVA_HOME deve ser definida como o local de instalação do JDK para concluir este guia de início rápido.

## <a name="prepare-a-functions-project"></a>Preparar um projeto do Functions

::: zone pivot="java-build-tools-maven" 
Em uma pasta vazia, execute o seguinte comando para gerar o projeto do Functions a partir de um [arquétipo Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Se estiver usando o PowerShell, lembre-se de adicionar "" em torno dos parâmetros.

> [!NOTE]
> Se você estiver com problemas com a execução do comando, verifique qual versão do `maven-archetype-plugin` é usada. Como você está executando o comando em um diretório vazio sem nenhum arquivo `.pom`, ele pode estar tentando usar um plug-in da versão mais antiga do `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin`, se você atualizou o Maven de uma versão anterior. Nesse caso, tente excluir o diretório `maven-archetype-plugin` e executar novamente o comando.

O Maven solicita os valores necessários para concluir a geração do projeto na implantação. Forneça os seguintes valores quando solicitado:

| Valor | Descrição |
| ----- | ----------- |
| **groupId** | Um valor que identifica exclusivamente o projeto em todos os projetos, seguindo as [regras de nomenclatura do pacote](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) para Java. Os exemplos deste início rápido usam `com.fabrikam.functions`. |
| **artifactId** | Um valor que é o nome do jar, sem um número de versão. Os exemplos deste início rápido usam `fabrikam-functions`. |
| **version** | Escolha o valor padrão `1.0-SNAPSHOT`. |
| **package** | Um valor que é o pacote Java para o código de função gerado. Use o padrão. Os exemplos deste início rápido usam `com.fabrikam.functions`. |
| **appName** | Nome global exclusivo que identifica seu novo aplicativo de funções no Azure. Use o padrão, que é a _artifactId_ acrescentada a um número aleatório. Anote esse valor; você precisará dele mais tarde. |
| **appRegion** | Escolha uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços que suas funções acessam. O padrão é `westus`. Execute este comando da [CLI do Azure] para obter uma lista de todas as regiões:<br/>`az account list-locations --query '[].{Name:name}' -o tsv` |
| **resourceGroup** | Nome do novo [grupo de recursos](../azure-resource-manager/management/overview.md) no qual o aplicativo de funções será criado. Use `myResourceGroup`, que é usado pelos exemplos neste início rápido. Um grupo de recursos precisa ser exclusivo em sua assinatura do Azure.|

Digite `Y` ou pressione Enter para confirmar.

O Maven cria os arquivos de projeto em uma nova pasta com o nome _artifactId_, que, neste exemplo, é `fabrikam-functions`. Execute o comando a seguir para alterar o diretório para a pasta de projeto criada.
```bash
cd fabrikam-function
```

::: zone-end 
::: zone pivot="java-build-tools-gradle"
Use o seguinte comando para clonar o projeto de exemplo:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Abra `build.gradle` e altere o `appName` na seção a seguir para um nome exclusivo, a fim de evitar conflito de nome de domínio durante a implantação no Azure. 

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
::: zone-end

Abra o novo arquivo Function.java do caminho *src/main/java* em um editor de texto e examine o código gerado. Esse código é uma função [disparada por HTTP](functions-bindings-http-webhook.md) que ecoa o corpo da solicitação. 

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Executar a função localmente

Execute o seguinte comando para compilar e, em seguida, executar o projeto de função:

::: zone pivot="java-build-tools-maven" 
```bash
mvn clean package 
mvn azure-functions:run
```
::: zone-end 

::: zone pivot="java-build-tools-gradle"  
```bash
gradle jar --info
gradle azureFunctionsRun
```
::: zone-end 

Você verá um resultado semelhante ao seguinte no Azure Functions Core Tools quando executar o projeto localmente:

```output
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
```

Dispare a função na linha de comando usando o cURL em uma nova janela de terminal:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

```output
Hello AzureFunctions!
```
A [chave de função](functions-bindings-http-webhook-trigger.md#authorization-keys) não é necessária na execução local. Use `Ctrl+C` no terminal para interromper o código da função.

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


::: zone pivot="java-build-tools-maven" 
```bash
mvn azure-functions:deploy
```
::: zone-end 

::: zone pivot="java-build-tools-gradle"  
```bash
gradle azureFunctionsDeploy
```
::: zone-end

Isso criará os seguintes recursos no Azure:

+ Grupo de recursos. Nomeado com o _resourceGroup_ fornecido.
+ Conta de armazenamento. Necessária para o Functions. O nome é gerado aleatoriamente de acordo com os requisitos de nome da conta de armazenamento.
+ Plano do Serviço de Aplicativo. Hospedagem sem servidor para o aplicativo de funções na _appRegion_ especificada. O nome é gerado aleatoriamente.
+ Aplicativo de funções. Um aplicativo de funções é a unidade de implantação e execução para suas funções. O nome é o _appName_, acrescentado a um número gerado aleatoriamente. 

A implantação também empacota os arquivos de projeto e implanta-os no novo aplicativo de funções usando [zip deployment](functions-deployment-technologies.md#zip-deploy), com o modo run-from-package habilitado.

Após a conclusão da implantação, você verá a URL que poderá usar para acessar os pontos de extremidade do aplicativo de funções. Como o gatilho HTTP que publicamos usa `authLevel = AuthorizationLevel.FUNCTION`, você precisa obter a chave de função para chamar o ponto de extremidade da função via HTTP. A maneira mais fácil de obter a chave de função é no [Azure portal].

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>Obter a URL do gatilho HTTP

<!--- We can updates this to remove portal dependency after the Maven archetype returns the full URLs with keys on publish (https://github.com/microsoft/azure-maven-plugins/issues/571). -->

Obtenha a URL necessária para disparar a função, com a chave de função, no portal do Azure. 

1. Navegue até o [Azure portal], entre nele, digite o _appName_ do aplicativo de funções em **Pesquisar** na parte superior da página e pressione Enter.
 
1. No aplicativo de funções, expanda **Funções (Somente Leitura)** , selecione a função e, em seguida, selecione **</> Obter URL da função** no canto superior direito. 

    ![Copiar a URL da função do Portal do Azure](./media/functions-create-java-maven/get-function-url-portal.png)

1. Escolha **padrão (Chave de função)** e selecione **Copiar**. 

Agora você pode usar a URL copiada para acessar a função.

## <a name="verify-the-function-in-azure"></a>Verificar a função no Azure

Para verificar o aplicativo de funções em execução no Azure usando `cURL`, substitua a URL da amostra abaixo pela URL copiada do portal.

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpExample?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Isso envia uma solicitação POST ao ponto de extremidade da função com `AzureFunctions` no corpo da solicitação. Você verá a resposta a seguir.

```output
Hello AzureFunctions!
```

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Próximas etapas

Você criou um projeto de funções Java com uma função disparada por HTTP, executou-o no computador local e implantou-o no Azure. Agora, estenda sua função por meio da...

> [!div class="nextstepaction"]
> [Adição de uma associação de saída de fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-java.md)


[CLI do Azure]: /cli/azure
[Azure portal]: https://portal.azure.com
