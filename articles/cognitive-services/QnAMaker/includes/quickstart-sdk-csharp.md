---
title: 'Início Rápido: Biblioteca de clientes do QnA Maker para .NET'
description: Este início rápido oferece uma introdução à biblioteca de clientes do QnA Maker para .NET. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas.  O QnA Maker permite ter um serviço avançado de perguntas e respostas a partir de um conteúdo semiestruturado, como documentos de perguntas frequentes ou URLs e manuais de produtos.
ms.topic: quickstart
ms.date: 06/11/2020
ms.openlocfilehash: e487783e506d16006231b07b9a86f93864f51903
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2020
ms.locfileid: "84765125"
---
Use a biblioteca de clientes do QnA Maker para .NET para:

 * Criar uma base de dados de conhecimento
 * Atualizar uma base de dados de conhecimento
 * Publicar uma base de dados de conhecimento, aguardar a conclusão da publicação
 * Obter a chave de ponto de extremidade do runtime de previsão
 * Baixar uma base de dados de conhecimento
 * Excluir uma base de dados de conhecimento

[Documentação de referência](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [Exemplos de C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* O [IDE do Visual Studio](https://visualstudio.microsoft.com/vs/) ou a versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Quando você tiver sua assinatura do Azure, crie um [recurso do QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal do Azure para obter a chave de criação e o ponto de extremidade. Após a implantação, selecione **Ir para recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso que você criar para conectar seu aplicativo à API do QnA Maker. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando


#### <a name="visual-studio-ide"></a>[IDE do Visual Studio](#tab/visual-studio)

Usando o Visual Studio, crie um aplicativo .NET Core e instale a biblioteca de clientes clicando com o botão direito do mouse na solução no **Gerenciador de Soluções** e selecionando **Gerenciar Pacotes NuGet**. No gerenciador de pacotes aberto, selecione **Procurar**, marque **Incluir pré-lançamento** e pesquise `(package-name)`. Selecione a versão `(version)` e, em seguida, **Instalar**.

#### <a name="cli"></a>[CLI](#tab/cli)

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `qna-maker-quickstart`. Esse comando cria um projeto simples em C# do tipo "Olá, Mundo" com um arquivo de origem único: *program.cs*.

```console
dotnet new console -n qna-maker-quickstart
```

Altere o diretório para a pasta do aplicativo recém-criado. É possível criar o aplicativo com:

```console
dotnet build
```

A saída de compilação não deve conter nenhum aviso ou erro.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Dentro do diretório do aplicativo, instale a biblioteca de clientes do QnA Maker para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.1.0
```


---

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs), que contém os exemplos de código neste início rápido.

No diretório do projeto, abra o arquivo *program.cs* e adicione as seguintes diretivas `using`:

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies&highlight=1-2)]

No método `Main` do aplicativo, adicione as variáveis e o código mostrados nas seções a seguir para usar as tarefas comuns neste guia de início rápido.

## <a name="object-models"></a>Modelos de objeto

O QnA Maker usa dois modelos de objeto diferentes:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto usado para criar, gerenciar, publicar e baixar a base de dados de conhecimento.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** é o objeto usado para consultar a base de dados de conhecimento com a API GenerateAnswer e para enviar novas perguntas sugeridas usando a API de Treinamento (como parte do [aprendizado ativo](../concepts/active-learning-suggestions.md)).


### <a name="qnamakerclient-object-model"></a>Modelo de objeto do QnAMakerClient

O cliente de criação do QnA Maker é um objeto [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) que é autenticado no Azure usando Microsoft.Rest.ServiceClientCredentials, que contém sua chave.

Após a criação do cliente, use a propriedade da [Base de dados de conhecimento](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) para criar, gerenciar e publicar sua base de dados de conhecimento.

Gerencie sua base de dados de conhecimento enviando um objeto JSON. Para operações imediatas, um método geralmente retorna um objeto JSON que indica o status. Para operações de longa execução, a resposta é a ID da operação. Chame o método [client.Operations.GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) com a ID da operação para determinar o [status da solicitação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet).

### <a name="qnamakerruntimeclient-object-model"></a>Modelo de objeto do QnAMakerRuntimeClient

O cliente de previsão do QnA Maker é um objeto [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) que é autenticado no Azure usando Microsoft.Rest.ServiceClientCredentials, que contém sua chave do runtime de previsão, retornada pela chamada do cliente de criação, `client.EndpointKeys.GetKeys`, após a publicação da base de dados de conhecimento.

## <a name="code-examples"></a>Exemplos de código

Estes snippets de códigos mostram como fazer o seguinte com a biblioteca de clientes do QnA Maker para .NET:

* [Autenticar o cliente de criação](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Criar uma base de dados de conhecimento](#create-a-knowledge-base)
* [Atualizar uma base de dados de conhecimento](#update-a-knowledge-base)
* [Baixar uma base de dados de conhecimento](#download-a-knowledge-base)
* [Publicar uma base de dados de conhecimento](#publish-a-knowledge-base)
* [Excluir uma base de dados de conhecimento](#delete-a-knowledge-base)
* [Obter a chave do runtime de consulta](#get-query-runtime-key)
* [Obter o status de uma operação](#get-status-of-an-operation)
* [Autenticar o cliente do runtime de consulta](#authenticate-the-runtime-for-generating-an-answer)
* [Gerar uma resposta da base de dados de conhecimento](#generate-an-answer-from-the-knowledge-base)

## <a name="using-this-example-knowledge-base"></a>Usando esta base de dados de conhecimento de exemplo

A base de dados de conhecimento neste guia de início rápido começa com 2 pares de conversa de P e R. Isso é feito de propósito para simplificar o exemplo e ter IDs altamente previsíveis para usar no método de atualização, associando solicitações de acompanhamento com perguntas a novos pares. Isso foi planejado e implementado em uma ordem específica para este guia de início rápido.

Se você planeja desenvolver sua base de dados de conhecimento ao longo do tempo, com solicitações de acompanhamento que dependem de pares de P e R existentes, você pode optar por:
* Para bases de dados de conhecimento maiores, gerenciar a base de dados de conhecimento em um editor de texto ou ferramenta TSV com suporte para automação e substituir completamente a base de dados de conhecimento de uma só vez com uma atualização.
* Para bases de dados de conhecimento menores, gerenciar as solicitações de acompanhamento inteiramente no portal do QnA Maker.

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticar o cliente para criar a base de dados de conhecimento

No método **main**, crie uma variável para o nome de recurso e a chave do Azure do seu recurso. As URLs de criação e de previsão usam o nome do recurso como o subdomínio.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]


> [!IMPORTANT]
> Acesse o portal do Azure e localize a chave e o ponto de extremidade do recurso do QnA Maker criados nos pré-requisitos. Eles estarão localizados na página de **chave e ponto de extremidade** do recurso, em **gerenciamento de recursos**.
> Você precisa da chave inteira para criar a base de dados de conhecimento. Você precisa apenas do nome do recurso do ponto de extremidade. O formato é ``.
> Lembre-se de remover a chave do seu código quando terminar e nunca poste-a publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Por exemplo, o [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) fornece armazenamento de chaves seguro.

Em seguida, crie um objeto [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) com a sua chave e use-o com o ponto de extremidade para criar um objeto [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet).

|variável|Exemplo|
|--|--|
|`authoringKey`|A chave é uma cadeia de 32 caracteres e está disponível no portal do Azure, no recurso do QnA Maker, na página **Chaves e pontos de extremidade**. Ela não é a mesma que a chave do runtime de previsão.|
|`resourceName`| O nome do recurso é usado no formato `https://{resourceName}.cognitiveservices.azure.com`. Essa não é a mesma URL usada para consultar o runtime de previsão.|
||||

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

Uma base de dados de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) de três fontes:

* Para **conteúdo editorial**, use o objeto [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet).
    * Para usar os metadados e as solicitações de acompanhamento, use o contexto editorial, pois esses dados são adicionados no nível do par individual de P e R.
* Para **arquivos**, use o objeto [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet).
* Para **URLs**, use uma lista de cadeias de caracteres.

Chame o método [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) e, em seguida, passe a ID da operação retornada para o método [MonitorOperation](#get-status-of-an-operation) para sondar o status.

A linha final do código a seguir retorna a ID da base de dados de conhecimento da resposta de MonitorOperation.

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod&highlight=37-38)]

Inclua a função [`MonitorOperation`](#get-status-of-an-operation), referenciada no código acima, para criar uma base de dados de conhecimento com êxito.

## <a name="update-a-knowledge-base"></a>Atualizar uma base de dados de conhecimento

É possível atualizar uma base de dados de conhecimento passando sua ID e um [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) contendo os objetos [add](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [update](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet) e [delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) DTO para o método [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet). Use o método [MonitorOperation](#get-status-of-an-operation) para determinar se a atualização foi bem-sucedida.

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod&highlight=8)]

Inclua a função [`MonitorOperation`](#get-status-of-an-operation), referenciada no código acima, para atualizar uma base de dados de conhecimento com êxito.

## <a name="download-a-knowledge-base"></a>Baixar uma base de dados de conhecimento

Use o método [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) para baixar o banco de dados como uma lista de [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Isso _não_ é equivalente à exportação do portal do QnA Maker da página **Configurações** do portal, porque o resultado deste método não é um arquivo TSV.

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB&highlight=3,4)]

## <a name="publish-a-knowledge-base"></a>Publicar uma base de dados de conhecimento

Publique a base de dados de conhecimento usando o método [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet). Ele usa o modelo salvo e treinado atual, referenciado pela ID da base de dados de conhecimento, e o publica em seu ponto de extremidade.

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB&highlight=3)]

## <a name="get-query-runtime-key"></a>Obter a chave do runtime de consulta

Após uma base de dados de conhecimento ser publicada, você precisa da chave do runtime de consulta para consultar o runtime. Ela não é a mesma chave usada para criar o objeto de cliente original.

Use o método [EndpointKeys](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) para obter a classe [EndpointKeysDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-dotnet).

Use qualquer uma das propriedades da chave retornadas no objeto para consultar a base de dados de conhecimento.

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey&highlight=3)]

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticar o runtime para gerar uma resposta

Crie um [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) para consultar a base de dados de conhecimento para gerar uma resposta ou treinar com base em um aprendizado ativo.

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

Use o QnAMakerRuntimeClient para obter uma resposta de conhecimento ou para enviar novas perguntas sugeridas para a base de dados de conhecimento, para o [aprendizado ativo](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta da base de dados de conhecimento

Gere uma resposta de uma base de dados de conhecimento publicada usando o método [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase).[GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet). Esse método aceita a ID da base de dados de conhecimento e o [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Acesse as propriedades adicionais do QueryDTO, como [Superior](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) e [Contexto](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) para usar em seu chatbot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer&highlight=3)]

Este é um exemplo simples de consulta à base de dados de conhecimento. Para entender cenários de consulta avançada, confira [outros exemplos de consulta](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Excluir uma base de dados de conhecimento

Exclua a base de dados de conhecimento usando o método [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) com um parâmetro da ID da base de dados de conhecimento.

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>Obter o status de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente para que, em vez de aguardar a conclusão do processo, uma [operação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) seja retornada. Use a [ID da operação](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) para fazer uma sondagem (com lógica de repetição) para determinar o status do método original.

Os elementos _loop_ e _Task.Delay_ no bloco de código a seguir são usados para simular a lógica de repetição. Eles devem ser substituídos por sua própria lógica de repetição.

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `dotnet run` do seu próprio diretório de aplicativo.

Todos os snippets de código neste artigo estão [disponíveis](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) e podem ser executados como um único arquivo.

```dotnetcli
dotnet run
```

* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart).