---
title: 'Início Rápido: Biblioteca de clientes do QnA Maker para Python'
description: Este início rápido oferece uma introdução à biblioteca de clientes do QnA Maker para Python.
ms.topic: include
ms.date: 06/18/2020
ms.openlocfilehash: 61c67b66e85e1d8d03cbe07b0c8d2053151e3513
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97096454"
---
Use a biblioteca de clientes do QnA Maker para python para:

* Criar uma base de dados de conhecimento
* Atualizar uma base de dados de conhecimento
* Publicar uma base de dados de conhecimento
* Obter a chave de ponto de extremidade do runtime de previsão
* Aguardar tarefa de execução longa
* Baixar uma base de dados de conhecimento
* Receber resposta
* Excluir base de dados de conhecimento

[Documentação de referência](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [Pacote (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Exemplos do Python](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Quando você tiver sua assinatura do Azure, crie um [recurso do QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) no portal do Azure para obter a chave de criação e o ponto de extremidade. Após a implantação, selecione **Ir para recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso que você criar para conectar seu aplicativo à API do QnA Maker. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Depois de instalar o Python, você pode instalar a biblioteca de clientes com:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

Crie um arquivo do Python chamado `quickstart-file.py` e importe as bibliotecas a seguir.

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Dependencies&highlight=4,5)]

Crie variáveis para a chave e o ponto de extremidade do Azure do recurso.

> [!IMPORTANT]
> Acesse o portal do Azure e localize a chave e o ponto de extremidade do recurso do QnA Maker criados nos pré-requisitos. Eles estarão localizados na página de **chave e ponto de extremidade** do recurso, em **gerenciamento de recursos**.
> Você precisa da chave inteira para criar a base de dados de conhecimento. Você precisa apenas do nome do recurso do ponto de extremidade. O formato é `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Lembre-se de remover a chave do seu código quando terminar e nunca poste-a publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Por exemplo, o [Azure Key Vault](../../../key-vault/general/overview.md) fornece armazenamento de chaves seguro.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Resourcevariables)]

## <a name="object-models"></a>Modelos de objeto

O [QnA Maker](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) usa dois modelos de objeto diferentes:
* **[QnAMakerClient](#qnamakerclient-object-model)** é o objeto usado para criar, gerenciar, publicar e baixar a base de dados de conhecimento.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** é o objeto usado para consultar a base de dados de conhecimento com a API GenerateAnswer e para enviar novas perguntas sugeridas usando a API de Treinamento (como parte do [aprendizado ativo](../concepts/active-learning-suggestions.md)).

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Modelo de objeto do QnAMakerClient

O cliente de criação do QnA Maker é um objeto [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) que é autenticado no Azure usando Microsoft.Rest.ServiceClientCredentials, que contém sua chave.

Após a criação do cliente, use a propriedade da [Base de dados de conhecimento](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) para criar, gerenciar e publicar sua base de dados de conhecimento.

Gerencie sua base de dados de conhecimento enviando um objeto JSON. Para operações imediatas, um método geralmente retorna um objeto JSON que indica o status. Para operações de longa execução, a resposta é a ID da operação. Chame o método [operations.get_details](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)?view=azure-python#get-details-operation-id--custom-headers-none--raw-false----operation-config-) com a ID da operação para determinar o [status da solicitação](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)?view=azure-python).

### <a name="qnamakerruntimeclient-object-model"></a>Modelo de objeto do QnAMakerRuntimeClient

O cliente de previsão do QnA Maker é um objeto [QnAMakerRuntimeClient](/javascript/api/@azure/cognitiveservices-qnamaker-runtime/qnamakerruntimeclient?view=azure-node-latest) que é autenticado no Azure usando Microsoft.Rest.ServiceClientCredentials, que contém sua chave do runtime de previsão, retornada pela chamada do cliente de criação, [client.EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpointkeysoperations?view=azure-python), após a publicação da base de dados de conhecimento.

Use o método [generate_answer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Runtime__ctor_Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerRuntimeClient_#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) para obter uma resposta do runtime da consulta.

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticar o cliente para criar a base de dados de conhecimento

Instancie um cliente com o ponto de extremidade e a chave. Crie um objeto CognitiveServicesCredentials com a sua chave e use-o com o ponto de extremidade para criar um objeto [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-python).

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Como criar uma base de dados de conhecimento

 Use o objeto de cliente para obter um objeto de [operações da base de dados de conhecimento](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python).

Uma base de dados de conhecimento armazena pares de perguntas e respostas para o objeto [CreateKbDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.create_kb_dto) de três fontes:

* Para **conteúdo editorial**, use o objeto [QnADTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-python).
    * Para usar os metadados e as solicitações de acompanhamento, use o contexto editorial, pois esses dados são adicionados no nível do par individual de P e R.
* Para **arquivos**, use o objeto [FileDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.file_dto). O FileDTO inclui o nome de arquivo, bem como a URL pública para acessar o arquivo.
* Em **URLs**, use uma lista de cadeias de caracteres para representar URLs disponíveis publicamente.

Chame o método [create](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) e, em seguida, passe a ID da operação retornada para o método [Operations.getDetails](#get-status-of-an-operation) para sondar o status.

A linha final do código a seguir retorna a ID da base de dados de conhecimento da resposta de MonitorOperation.

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=CreateKBMethod&highlight=36,38)]

Inclua a função [`_monitor_operation`](#get-status-of-an-operation), referenciada no código acima, para criar uma base de dados de conhecimento com êxito.

## <a name="update-a-knowledge-base"></a>Como atualizar uma base de dados de conhecimento

É possível atualizar uma base de dados de conhecimento passando sua ID e um [UpdateKbOperationDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-python) contendo os objetos [add](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-python), [update](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-python) e [delete](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-python) DTO para o método [update](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python). Use o método [Operation.getDetail](#get-status-of-an-operation) para determinar se a atualização foi bem-sucedida.

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=UpdateKBMethod&highlight=68,69)]

Inclua a função [`_monitor_operation`](#get-status-of-an-operation), referenciada no código acima, para atualizar uma base de dados de conhecimento com êxito.

## <a name="download-a-knowledge-base"></a>Baixar uma base de dados de conhecimento

Use o método [download](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) para baixar o banco de dados como uma lista de [QnADocumentsDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-python). Isso _não_ é equivalente à exportação do portal do QnA Maker da página **Configurações** do portal, porque o resultado deste método não é um arquivo TSV.

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Como publicar uma base de dados de conhecimento

Publique a base de dados de conhecimento usando o método [publish](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python). Ele usa o modelo salvo e treinado atual, referenciado pela ID da base de dados de conhecimento, e o publica em um ponto de extremidade.

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=PublishKB&highlight=2)]

## <a name="get-query-runtime-key"></a>Obter a chave do runtime de consulta

Após uma base de dados de conhecimento ser publicada, você precisa da chave do runtime de consulta para consultar o runtime. Ela não é a mesma chave usada para criar o objeto cliente original.

Use o método [EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpointkeysoperations?view=azure-python) para obter a classe [EndpointKeysDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-python).

Use qualquer uma das propriedades da chave retornadas no objeto para consultar a base de dados de conhecimento.

[!code-python[Get query runtime key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GetQueryEndpointKey&highlight=2)]


## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticar o runtime para gerar uma resposta

Crie um [QnAMakerRuntimeClient](/javascript/api/@azure/cognitiveservices-qnamaker-runtime/qnamakerruntimeclient?view=azure-node-latest) para consultar a base de dados de conhecimento para gerar uma resposta ou treinar com base em um aprendizado ativo.

[!code-python[Authenticate the runtime](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationQuery)]

Use o QnAMakerRuntimeClient para obter uma resposta de conhecimento ou para enviar novas perguntas sugeridas para a base de dados de conhecimento, para o [aprendizado ativo](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Gerar uma resposta da base de dados de conhecimento

Gere uma resposta de uma base de dados de conhecimento publicada usando o método RuntimeClient.runtime.generateAnswer. Esse método aceita a ID da base de dados de conhecimento e o QueryDTO. Acesse as propriedades adicionais do QueryDTO, como Superior e Contexto, para usar em seu chatbot.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GenerateAnswer&highlight=5)]

Este é um exemplo simples de consulta à base de dados de conhecimento. Para entender cenários de consulta avançada, confira [outros exemplos de consulta](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Excluir uma base de dados de conhecimento

Exclua a base de dados de conhecimento usando o método [delete](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python) com um parâmetro da ID da base de dados de conhecimento.

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Obter o status de uma operação

Alguns métodos, como criar e atualizar, podem levar tempo suficiente para que, em vez de aguardar a conclusão do processo, uma [operação](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)?view=azure-python) seja retornada. Use a ID da operação para fazer uma sondagem (com lógica de repetição) para determinar o status do método original.

A chamada _setTimeout_ no bloco de código a seguir é usada para simular código assíncrono. Substitua isso pela lógica de repetição.

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=MonitorOperation&highlight=7)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando python no seu arquivo de início rápido.

```console
python quickstart-file.py
```

O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py).