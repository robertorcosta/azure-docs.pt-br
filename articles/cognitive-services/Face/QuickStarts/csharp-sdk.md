---
title: 'Início Rápido: Biblioteca de clientes de Detecção Facial para .NET'
description: Introdução à biblioteca de clientes de Detecção Facial para .NET com este início rápido.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: e305a5634aa0c065342e1873c413039eb734b972
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76165881"
---
# <a name="quickstart-face-client-library-for-net"></a>Início Rápido: Biblioteca de clientes de Detecção Facial para .NET

Introdução à biblioteca de clientes de Detecção Facial para .NET. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas. O serviço de Detecção Facial fornece acesso a algoritmos avançados para detectar e reconhecer rostos humanos em imagens.

Use a biblioteca de clientes de Detecção Facial para .NET para:

* [Autenticar o cliente](#authenticate-the-client)
* [Detectar faces em uma imagem](#detect-faces-in-an-image)
* [Encontrar rostos semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identificar um rosto](#identify-a-face)
* [Tirar um instantâneo para migração de dados](#take-a-snapshot-for-data-migration)

[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1) | [Exemplos](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Prerequisites

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configurando

### <a name="create-a-face-azure-resource"></a>Criar um recurso de Detecção Facial do Azure

Os Serviços Cognitivos do Azure são representados por recursos do Azure que você assina. Crie um recurso para Detecção Facial usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou a [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também é possível:

* Obter uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias gratuitamente. Depois de se inscrever, ela estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exibir esse recurso no [portal do Azure](https://portal.azure.com/).

Após obter uma chave da assinatura ou do recurso de avaliação gratuita, [crie uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e a URL de ponto de extremidade, denominadas `FACE_SUBSCRIPTION_KEY` e `FACE_ENDPOINT`, respectivamente.

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Crie um novo aplicativo .NET Core em seu IDE ou editor preferido. 

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `face-quickstart`. Esse comando cria um projeto simples C# "Olá, Mundo" com um arquivo de origem único: *Program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Altere o diretório para a pasta do aplicativo recém-criado. É possível criar o aplicativo com:

```dotnetcli
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

No diretório do projeto, abra o arquivo *Program.cs* no IDE ou no editor de sua preferência. Adicione as seguintes diretivas `using`:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_using)]

No método `Main` do aplicativo, crie variáveis para a chave e o ponto de extremidade do Azure do recurso.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Dentro do diretório do aplicativo, instale a biblioteca de clientes de Detecção Facial para .NET com o seguinte comando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.5.0-preview.1
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de clientes estará disponível como um pacote baixável do NuGet.

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir lidam com alguns dos principais recursos do SDK do .NET de Detecção Facial:

|Nome|Descrição|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Essa classe representa sua autorização para usar o serviço de Detecção Facial e é necessária para todas as funcionalidades desse serviço. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes. |
|[FaceOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Essa classe manipula as tarefas básicas de detecção e reconhecimento que você pode realizar com rostos humanos. |
|[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Essa classe representa todos os dados detectados de um único rosto em uma imagem. Você pode usá-la para recuperar informações detalhadas sobre o rosto.|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Essa classe gerencia os constructos **FaceList** armazenados em nuvem, que armazenam um conjunto classificado de rostos. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Essa classe gerencia os constructos **Person** armazenados em nuvem, que armazenam um conjunto de rostos que pertencem a uma única pessoa.|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Essa classe gerencia os constructos **PersonGroup** armazenados em nuvem, que armazenam um conjunto classificado de objetos **Person**. |
|[ShapshotOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Essa classe gerencia a funcionalidade de instantâneo. Você pode usá-la para salvar temporariamente todos os dados de Detecção Facial baseados em nuvem e migrar esses dados para uma nova assinatura do Azure. |

## <a name="code-examples"></a>Exemplos de código

Os snippets de código abaixo mostram como realizar as seguintes tarefas com a biblioteca de clientes de Detecção Facial para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Detectar faces em uma imagem](#detect-faces-in-an-image)
* [Encontrar rostos semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identificar um rosto](#identify-a-face)
* [Tirar um instantâneo para migração de dados](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este início rápido pressupõe que você já [criou variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e o ponto de extremidade da Detecção Facial, denominados `FACE_SUBSCRIPTION_KEY` e `FACE_ENDPOINT`.

Em um novo método, instancie um cliente com o ponto de extremidade e a chave. Crie um objeto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) com a sua chave e use-o com o ponto de extremidade para criar um objeto [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_auth)]

Provavelmente você desejará chamar esse método no método `Main`.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_client)]

## <a name="detect-faces-in-an-image"></a>Detectar faces em uma imagem

Na raiz de sua classe, defina a cadeia de caracteres de URL a seguir. Essa URL aponta para um conjunto de imagens de exemplo.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

Opcionalmente, você pode escolher qual modelo de IA usar para extrair dados dos rostos detectados. Confira [Especificar um modelo de reconhecimento](../Face-API-How-to-Topics/specify-recognition-model.md) para obter informações sobre essas opções.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

A operação de Detecção final usará um objeto [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet), uma URL de imagem e um modelo de reconhecimento.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Detectar objetos de rosto

No próximo bloco de código, o método `DetectFaceExtract` detecta rostos em três das imagens na URL fornecida e cria uma lista de objetos [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) na memória do programa. A lista de valores [FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) especifica quais recursos extrair. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Exibir dados do rosto detectado

O restante do método `DetectFaceExtract` analisa e imprime os dados de atributos para cada rosto detectado. Cada atributo deve ser especificado separadamente na chamada à API de detecção facial (na lista [FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)). O código a seguir processa todos os atributos, mas provavelmente você só precisará usar um ou alguns deles.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

O código a seguir usa um único rosto detectado (origem) e pesquisa um conjunto de outros rostos (destino) para encontrar correspondências. Quando encontra uma correspondência, ele imprime a ID do rosto correspondente no console.

### <a name="detect-faces-for-comparison"></a>Detectar rostos para comparação

Primeiro, defina um segundo método de detecção facial. Você precisa detectar rostos em imagens antes de poder compará-las. Esse método de detecção é otimizado para operações de comparação. Ele não extrai atributos de rosto detalhados, como na seção acima, e usa um modelo de reconhecimento diferente.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Encontrar correspondências

O método a seguir detecta rostos em um conjunto de imagens de destino e em uma única imagem de origem. Em seguida, compara os e encontra todas as imagens de destino semelhantes à imagem de origem.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Imprimir correspondências

O código a seguir imprime os detalhes de correspondência no console:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar_print)]

## <a name="create-and-train-a-person-group"></a>Criar e treinar um grupo de pessoas

O código a seguir cria um **PersonGroup** com seis objetos **Person** diferentes. Ele associa cada **Person** a um conjunto de imagens de exemplo e, em seguida, o treina para reconhecer cada pessoa por suas características faciais. Os objetos **Person** e **PersonGroup** são usados nas operações Verificar, Identificar e Agrupar.

Se você ainda não fez isso, defina a seguinte cadeia de caracteres de URL na raiz da sua classe. Ela aponta para um conjunto de imagens de exemplo.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

O código mais adiante nesta seção especificará um modelo de reconhecimento para extrair dados de rostos e o snippet a seguir cria referências para os modelos disponíveis. Confira [Especificar um modelo de reconhecimento](../Face-API-How-to-Topics/specify-recognition-model.md) para saber mais sobre modelos de reconhecimento.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

### <a name="create-persongroup"></a>Criar PersonGroup

Declare uma variável de cadeia de caracteres na raiz de sua classe para representar a ID do **PersonGroup** a ser criado.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_declare)]

Em um novo método, adicione o seguinte código. Esse código associa os nomes de pessoas a suas imagens de exemplo.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_files)]

Em seguida, adicione o seguinte código para criar um objeto **Person** para cada pessoa no Dicionário e adicione os dados de rosto das imagens apropriadas. Cada objeto **Person** está associado ao mesmo **PersonGroup** por meio de sua cadeia de caracteres de ID exclusiva. Lembre-se de passar as variáveis `client`, `url` e `RECOGNITION_MODEL1` para esse método.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_create)]

### <a name="train-persongroup"></a>Treinar PersonGroup

Após ter extraído dados de rosto de suas imagens e os ter classificado em diferentes objetos **Person**, você deverá treinar o **PersonGroup** para identificar os recursos associados a cada um de seus objetos **Person**. O código a seguir chama o método **train** assíncrono e sonda os resultados, imprimindo o status no console.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_train)]

Esse grupo **Person** e seus objetos **Person** associados agora estão prontos para serem usados nas operações Verificar, Identificar ou Agrupar.

## <a name="identify-a-face"></a>Identificar um rosto

A operação Identificar usa uma imagem de uma pessoa (ou de várias pessoas) e procura encontrar a identidade de cada rosto na imagem. Ele compara cada rosto detectado com um **PersonGroup**, um banco de dados de diferentes objetos **Person** cujas características faciais são conhecidas.

> [!IMPORTANT]
> Para executar este exemplo, você deve primeiro executar o código em [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group). As variáveis usadas nessa seção&mdash;`client`, `url` e `RECOGNITION_MODEL1`&mdash;também devem estar disponíveis aqui.

### <a name="get-a-test-image"></a>Obter uma imagem de teste

Observe que o código para [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group) define uma variável `sourceImageFileName`. A variável corresponde à imagem de origem&mdash;à imagem que contém pessoas a serem identificadas.

### <a name="identify-faces"></a>Identificar faces em imagem

O código a seguir usa a imagem de origem e cria uma lista de todos os rostos detectados nela. Esses são os rostos que serão identificados no **PersonGroup**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify_sources)]

O snippet de código a seguir chama a operação Identificar e imprime os resultados no console. Aqui o serviço tenta corresponder cada rosto da imagem de origem a uma **Pessoa** no **PersonGroup** fornecido.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Tirar um instantâneo para migração de dados

O recurso Instantâneos permite que você mova os dados salvos da Detecção Facial, como um **PersonGroup** treinado, para uma assinatura de Detecção Facial dos Serviços Cognitivos do Azure diferente. Talvez você queira usar esse recurso se, por exemplo, tiver criado um objeto **PersonGroup** usando uma assinatura de avaliação gratuita e quiser migrá-lo para uma assinatura paga. Confira [Migrar os dados de rosto](../Face-API-How-to-Topics/how-to-migrate-face-data.md) para ter uma visão geral do recurso Instantâneos.

Neste exemplo, você migrará o **PersonGroup** que criou em [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group). Você pode concluir essa seção primeiro ou criar seus próprios constructos de dados de Detecção Facial a serem migrados.

### <a name="set-up-target-subscription"></a>Configurar a assinatura de destino

Primeiro, você precisa obter uma segunda assinatura do Azure com um recurso de Detecção Facial; você pode fazer isso seguindo as etapas na seção [Configuração](#setting-up). 

Em seguida, defina as variáveis a seguir no método `Main` do seu programa. Você precisará criar variáveis de ambiente para a ID da assinatura da conta do Azure, bem como a chave, o ponto de extremidade e a ID da assinatura da nova conta (de destino). 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

Neste exemplo, declare uma variável para a ID do objeto **PersonGroup**&mdash;de destino que pertence à nova assinatura, para a qual você copiará seus dados.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>Autenticar o cliente de destino

Em seguida, adicione o código para autenticar sua assinatura da Detecção Facial secundária.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Usar um instantâneo

O restante das operações de instantâneo deve ocorrer em um método assíncrono. 

1. A primeira etapa é **tirar** o instantâneo, que salva os dados de rosto da assinatura original em um local de nuvem temporário. Esse método retorna uma ID que você usa para consultar o status da operação.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take)]

1. Em seguida, consulte a ID até que a operação seja concluída.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take_wait)]

1. Em seguida, use a operação **aplicar** para gravar seus dados de rosto na assinatura de destino. Esse método também retorna um valor de ID.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Novamente, consulte a nova ID até que a operação seja concluída.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Por fim, conclua o bloco try/catch e termine o método.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_trycatch)]

Nesse momento, seu novo objeto **PersonGroup** deve ter os mesmos dados que o original e deve ser acessível de sua nova assinatura de Detecção Facial do Azure (de destino).

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo do seu diretório de aplicativo com o comando `dotnet run`.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Se você tiver criado um **PersonGroup** neste início rápido e desejar excluí-lo, execute o seguinte código em seu programa:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_delete)]

Defina o método de exclusão com o seguinte código:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_deletepersongroup)]

Além disso, se você tiver migrado dados usando o recurso Instantâneo neste início rápido, também precisará excluir o **PersonGroup** salvo na assinatura de destino.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a usar a biblioteca de Detecção Facial para .NET para realizar tarefas básicas. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência da API de Detecção Facial (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [O que é o serviço de Detecção Facial?](../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/Face/Program.cs).
