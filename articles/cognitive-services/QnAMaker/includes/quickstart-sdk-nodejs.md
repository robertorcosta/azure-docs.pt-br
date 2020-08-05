---
title: 'Início Rápido: Biblioteca de clientes do QnA Maker para Node.js'
description: Este início rápido oferece uma introdução à biblioteca de clientes do QnA Maker para Node.js.
ms.topic: quickstart
ms.date: 06/18/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 76d0587c68ac46253dbd937c661b06bdb8c495c8
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87404066"
---
Use a biblioteca de clientes do QnA Maker para Node.js para:

* Criar uma base de dados de conhecimento
* Atualizar uma base de dados de conhecimento
* Publicar uma base de dados de conhecimento
* Obter a chave de ponto de extremidade do runtime de previsão
* Aguardar tarefa de execução longa
* Baixar uma base de dados de conhecimento
* Receber resposta
* Excluir base de dados de conhecimento

[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [Pacote (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Exemplos de Node.js](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [Node.js](https://nodejs.org).
* Quando você tiver sua assinatura do Azure, crie um [recurso do QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal do Azure para obter a chave de criação e o recurso. Após a implantação, selecione **Ir para recurso**.
    * Você precisará do nome do recurso e da chave do recurso que você criar para conectar o aplicativo à API do QnA Maker. Cole a chave e o nome do recurso no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-nodejs-application"></a>Criar um novo aplicativo do Node.js

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Execute o comando `npm init -y` para criar um aplicativo do Node com um arquivo `package.json`.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Instale os seguintes pacotes NPM:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/cognitiveservices-qnamaker-runtime
npm install @azure/ms-rest-js
```

O arquivo `package.json` do seu aplicativo é atualizado com as dependências.

Crie um arquivo chamado index.js e importe as seguintes bibliotecas:

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Dependencies)]

Crie uma variável para o nome de recurso e a chave do Azure do seu recurso. As URLs de criação e de previsão usam o nome do recurso como o subdomínio.

> [!IMPORTANT]
> Acesse o portal do Azure e localize a chave e o ponto de extremidade do recurso do QnA Maker criados nos pré-requisitos. Eles estarão localizados na página de **chave e ponto de extremidade** do recurso, em **gerenciamento de recursos**.
> Você precisa da chave inteira para criar a base de dados de conhecimento. Você precisa apenas do nome do recurso do ponto de extremidade. O formato é `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Lembre-se de remover a chave do seu código quando terminar e nunca poste-a publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Por exemplo, o [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) fornece armazenamento de chaves seguro.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Resourcevariables)]

## <a name="object-models"></a>Modelos de objeto

O QnA Maker usa dois modelos de objeto diferentes:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto usado para criar, gerenciar, publicar e baixar a base de dados de conhecimento.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** é o objeto usado para consultar a base de dados de conhecimento com a API GenerateAnswer e para enviar novas perguntas sugeridas usando a API de Treinamento (como parte do [aprendizado ativo](../concepts/active-learning-suggestions.md)).


### <a name="qnamakerclient-object-model"></a>Modelo de objeto do QnAMakerClient

O cliente de criação do QnA Maker é um objeto [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) que se autentica no Azure usando suas credenciais, que contém sua chave.

Após o cliente ser criado, use a [base de dados de conhecimento](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) para criar, gerenciar e publicar sua base de dados de conhecimento.

Gerencie sua base de dados de conhecimento enviando um objeto JSON. Para operações imediatas, um método geralmente retorna um objeto JSON que indica o status. Para operações de longa execução, a resposta é a ID da operação. Chame o método [client.operations.getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--msrest-requestoptionsbase-) com a ID da operação para determinar o [status da solicitação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest).

### <a name="qnamakerruntimeclient-object-model"></a>Modelo de objeto do QnAMakerRuntimeClient

O cliente de previsão do QnA Maker é um objeto QnAMakerRuntimeClient que é autenticado no Azure usando Microsoft.Rest.ServiceClientCredentials, que contém sua chave do runtime de previsão, retornada pela chamada do cliente de criação, [client.EndpointKeys.getKeys](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest#getkeys-msrest-requestoptionsbase-), após a publicação da base de dados de conhecimento.


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



## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticar o cliente para criar a base de dados de conhecimento

Instancie um cliente com o ponto de extremidade e a chave. Crie um objeto ServiceClientCredentials com a sua chave e use-o com o ponto de extremidade para criar um objeto [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest).

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Como criar uma base de dados de conhecimento

Uma base de dados de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) de três fontes:

* Para **conteúdo editorial**, use o objeto [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest).
    * Para usar os metadados e as solicitações de acompanhamento, use o contexto editorial, pois esses dados são adicionados no nível do par individual de P e R.
* Para **arquivos**, use o objeto [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest). O FileDTO inclui o nome de arquivo, bem como a URL pública para acessar o arquivo.
* Em **URLs**, use uma lista de cadeias de caracteres para representar URLs disponíveis publicamente.

A etapa de criação também inclui propriedades para a base de conhecimento:
* `defaultAnswerUsedForExtraction` – o que é retornado quando nenhuma resposta é encontrada
* `enableHierarchicalExtraction` – criar automaticamente relações de prompt entre pares extraídos do QnA
* `language` – ao criar a primeira base de dados de conhecimento de um recurso, defina o idioma a ser usado no índice do Azure Search.

Chame o método [create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) com as informações da base de dados de conhecimento. As informações da base de dados de conhecimento são, basicamente um objeto JSON.

Quando o método create for retornado, passe a ID da operação retornada para o método [wait_for_operation](#get-status-of-an-operation) para sondar o status. O método wait_for_operation é retornado quando a operação é concluída. Analise o valor do cabeçalho de `resourceLocation` da operação retornada para obter a nova ID da base de dados de conhecimento.

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=CreateKBMethod&highlight=39,46)]

Inclua a função [`wait_for_operation`](#get-status-of-an-operation), referenciada no código acima, para criar uma base de dados de conhecimento com êxito.

## <a name="update-a-knowledge-base"></a>Como atualizar uma base de dados de conhecimento

É possível atualizar uma base de dados de conhecimento passando sua ID e um [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) contendo os objetos [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update) e [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO para o método [update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-). Os DTOs também são, basicamente, objetos JSON. Use o método [wait_for_operation](#get-status-of-an-operation) para determinar se a atualização foi bem-sucedida.

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=UpdateKBMethod&highlight=74,81)]

Inclua a função [`wait_for_operation`](#get-status-of-an-operation), referenciada no código acima, para atualizar uma base de dados de conhecimento com êxito.

## <a name="download-a-knowledge-base"></a>Baixar uma base de dados de conhecimento

Use o método [download](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#download-string--models-environmenttype--msrest-requestoptionsbase-) para baixar o banco de dados como uma lista de [QnADocumentsDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadocumentsdto?view=azure-node-latest). Isso _não_ é equivalente à exportação do portal do QnA Maker da página **Configurações** do portal, porque o resultado deste método não é um arquivo TSV.

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DownloadKB&highlight=2)]


## <a name="publish-a-knowledge-base"></a>Como publicar uma base de dados de conhecimento

Publique a base de dados de conhecimento usando o método [publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-). Ele usa o modelo salvo e treinado atual, referenciado pela ID da base de dados de conhecimento, e o publica em um ponto de extremidade. Verifique o código de resposta HTTP para validar que a publicação foi bem-sucedida.

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=PublishKB&highlight=3)]


## <a name="get-query-runtime-key"></a>Obter a chave do runtime de consulta

Após uma base de dados de conhecimento ser publicada, você precisa da chave do runtime de consulta para consultar o runtime. Ela não é a mesma chave usada para criar o objeto cliente original.

Use o método [EndpointKeys.getKeys](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest) para obter a classe [EndpointKeysDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeysdto?view=azure-node-latest).

Use qualquer uma das propriedades da chave retornadas no objeto para consultar a base de dados de conhecimento.

[!code-javascript[Get query runtime key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GetQueryEndpointKey&highlight=4)]

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticar o runtime para gerar uma resposta

Crie um QnAMakerRuntimeClient para consultar a base de dados de conhecimento para gerar uma resposta ou treinar com base em um aprendizado ativo.

[!code-javascript[Authenticate the runtime](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationQuery)]

Use o QnAMakerRuntimeClient para obter uma resposta de conhecimento ou para enviar novas perguntas sugeridas para a base de dados de conhecimento, para o [aprendizado ativo](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta da base de dados de conhecimento

Gere uma resposta de uma base de dados de conhecimento publicada usando o método RuntimeClient.runtime.generateAnswer. Esse método aceita a ID da base de dados de conhecimento e o QueryDTO. Acesse as propriedades adicionais do QueryDTO, como Superior e Contexto, para usar em seu chatbot.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GenerateAnswer&highlight=3)]

Este é um exemplo simples de consulta à base de dados de conhecimento. Para entender cenários de consulta avançada, confira [outros exemplos de consulta](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Excluir uma base de dados de conhecimento

Exclua a base de dados de conhecimento usando o método [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) com um parâmetro da ID da base de dados de conhecimento.

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>Obter o status de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente para que, em vez de aguardar a conclusão do processo, uma [operação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) seja retornada. Use a [ID da operação](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) para fazer uma sondagem (com lógica de repetição) para determinar o status do método original.

A chamada _delayTimer_ no bloco de código a seguir é usada para simular a lógica de repetição. Substitua isso por sua pela lógica de repetição.

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=MonitorOperation&highlight=8)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `node index.js` do seu próprio diretório de aplicativo.

```console
node index.js
```

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js).