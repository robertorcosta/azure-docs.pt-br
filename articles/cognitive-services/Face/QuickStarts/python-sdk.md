---
title: 'Início Rápido: Biblioteca de clientes de Detecção Facial para Python | Microsoft Docs'
description: Introdução à biblioteca de clientes de Detecção Facial para Python...
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: pafarley
ms.openlocfilehash: 617e9a40c253d98c32ae8b4c9426f3148c23d125
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176129"
---
# <a name="quickstart-face-client-library-for-python"></a>Início Rápido: Biblioteca de clientes de Detecção Facial para Python

Introdução à biblioteca de clientes de Detecção Facial para Python. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas. O serviço de API de Detecção Facial fornece acesso a algoritmos avançados para detectar e reconhecer rostos humanos em imagens.

Use a biblioteca de clientes de Detecção Facial para Python para:

* Detectar faces em uma imagem
* Encontrar rostos semelhantes
* Criar e treinar um grupo de pessoas
* Identificar um rosto
* Verificar rostos
* Tirar um instantâneo para migração de dados

[Documentação de referência](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [Pacote (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [Exemplos](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=Face&sort=0)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configurando

### <a name="create-a-face-azure-resource"></a>Criar um recurso de Detecção Facial do Azure

Os Serviços Cognitivos do Azure são representados por recursos do Azure que você assina. Crie um recurso para Detecção Facial usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou a [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Você também pode:

* Obter uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias gratuitamente. Depois de se inscrever, ela estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exibir o recurso no [portal do Azure](https://portal.azure.com/)

Depois de obter uma chave do recurso ou da assinatura de avaliação, [crie uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave, chamada `FACE_SUBSCRIPTION_KEY`.
 
### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

Crie um script do Python&mdash;*quickstart-file.py*, por exemplo. Em seguida, abra-o em seu editor ou IDE preferencial e importe as bibliotecas a seguir.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Em seguida, crie variáveis para a chave e o ponto de extremidade do Azure do recurso. Talvez seja necessário alterar a primeira parte do ponto de extremidade (`westus`) para corresponder à assinatura.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e recarregar o editor, o IDE ou o shell em execução para acessar a variável.

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

É possível instalar a biblioteca de clientes com:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir lidam com alguns dos principais recursos do SDK da Detecção Facial do Python.

|NOME|DESCRIÇÃO|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Essa classe representa sua autorização para usar o serviço de Detecção Facial e é necessária para todas as funcionalidades desse serviço. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes. |
|[FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Essa classe manipula as tarefas básicas de detecção e reconhecimento que você pode realizar com rostos humanos. |
|[DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Essa classe representa todos os dados detectados de um único rosto em uma imagem. Você pode usá-la para recuperar informações detalhadas sobre o rosto.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Essa classe gerencia os constructos **FaceList** armazenados em nuvem, que armazenam um conjunto classificado de rostos. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Essa classe gerencia os constructos **Person** armazenados em nuvem, que armazenam um conjunto de rostos que pertencem a uma única pessoa.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Essa classe gerencia os constructos **PersonGroup** armazenados em nuvem, que armazenam um conjunto classificado de objetos **Person**. |
|[ShapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Essa classe gerencia a funcionalidade de instantâneo; você pode usá-la para salvar temporariamente todos os dados de rosto baseados em nuvem e migrar esses dados para uma nova assinatura do Azure. |

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes de Detecção Facial para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Detectar faces em uma imagem](#detect-faces-in-an-image)
* [Encontrar rostos semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identificar um rosto](#identify-a-face)
* [Verificar rostos](#verify-faces)
* [Tirar um instantâneo para migração de dados](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este guia de início rápido pressupõe que você já [criou uma variável de ambiente](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para a chave da Detecção Facial, denominada `FACE_SUBSCRIPTION_KEY`.

Instancie um cliente com o ponto de extremidade e a chave. Crie um objeto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) com a sua chave e use-o com o ponto de extremidade para criar um objeto [FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python).

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Detectar faces em uma imagem

O código a seguir detecta um rosto em uma imagem remota. Ele imprime a ID do rosto detectada no console e também a armazena na memória do programa. Em seguida, ele detecta os rostos em uma imagem com várias pessoas e imprime as IDs deles no console também. Ao alterar os parâmetros no método [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-), você pode retornar informações diferentes com cada objeto [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python).

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Confira o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) para obter mais cenários de detecção.

### <a name="display-and-frame-faces"></a>Exibir e enquadrar rostos

O código a seguir gera a imagem fornecida para a exibição e desenha retângulos em volta dos rostos usando a propriedade DetectedFace.faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Uma moça com um retângulo vermelho desenhado ao redor da face](../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

O código a seguir usa um único rosto detectado e pesquisa um conjunto de outros rostos para encontrar correspondências. Quando encontra uma correspondência, ele imprime as coordenadas de retângulo do rosto correspondente no console. 

### <a name="find-matches"></a>Encontrar correspondências

Primeiro, execute o código na seção acima ([Detectar faces em uma imagem](#detect-faces-in-an-image)) para salvar uma referência a um único rosto. Em seguida, execute o código a seguir para obter referências a vários rostos em uma imagem de grupo.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Em seguida, adicione o bloco de código a seguir para localizar instâncias do primeiro rosto no grupo. Confira o método [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) para saber como modificar esse comportamento.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Imprimir correspondências

Use o código a seguir para imprimir os detalhes da correspondência no console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Criar e treinar um grupo de pessoas

O código a seguir cria um **PersonGroup** com três objetos **Person** diferentes. Ele associa cada **Person** a um conjunto de imagens de exemplo e, em seguida, treina para ser capaz de reconhecer cada pessoa. 

### <a name="create-persongroup"></a>Criar PersonGroup

Para percorrer esse cenário, você precisa salvar as seguintes imagens no diretório raiz do seu projeto: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Esse grupo de imagens contém três conjuntos de imagens de rosto correspondentes a três pessoas diferentes. O código definirá três objetos **Person** e os associará a arquivos de imagem que começam com `woman`, `man` e `child`.

Depois de configurar suas imagens, defina um rótulo na parte superior do script para o objeto **PersonGroup** que você criará.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Adicione o código a seguir à parte inferior do script. Esse código cria um objeto **PersongGroup** e três objetos **Person**.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Atribuir rostos a pessoas

O código a seguir classifica as imagens pelo respectivo prefixo, detecta os rostos e atribui esses rostos a cada objeto **Person**.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>Treinar PersonGroup

Depois de atribuir os rostos, você precisa treinar o **PersonGroup** para que ele possa identificar as características visuais associadas a cada um dos objetos **Person** nele contidos. O código a seguir chama o método **train** assíncrono e sonda o resultado, imprimindo o status no console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Identificar um rosto

O código a seguir usa uma imagem com vários rostos e procura encontrar a identidade de cada pessoa na imagem. Ele compara cada rosto detectado com um **PersonGroup**, um banco de dados de diferentes objetos **Person** cujas características faciais são conhecidas.

> [!IMPORTANT]
> Para executar este exemplo, você deve primeiro executar o código em [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Obter uma imagem de teste

O código a seguir procura na raiz do projeto uma imagem _test-image-person-group.jpg_ e detecta os rostos existentes nela. Você pode encontrar essa imagem junto com aquelas usadas para o gerenciamento do **PersonGroup**: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Identificar faces em imagem

O método **Identify** usa uma matriz de rostos detectados e os compara a um **PersonGroup**. Se consegue fazer a correspondência entre um rosto detectado e um **Person**, ele salva o resultado. Esse código imprime os resultados de correspondência detalhados no console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Verificar rostos

A operação de verificação toma uma ID de rosto e outra ID de rosto ou um objeto **Person** e determina se elas pertencem à mesma pessoa.

O código a seguir detecta rostos em duas imagens de origem e os verifica em relação a um rosto detectado em uma imagem de destino.

### <a name="get-test-images"></a>Obter imagens de teste

Os blocos de código a seguir declaram variáveis que apontarão para as imagens de origem e de destino para a operação de verificação.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Detectar rostos para verificação

O código a seguir detecta rostos nas imagens de origem e de destino e os salva em variáveis.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Obter resultados da verificação

O código a seguir compara cada uma das imagens de origem com a imagem de destino e gera uma mensagem indicando se elas pertencem à mesma pessoa.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="take-a-snapshot-for-data-migration"></a>Tirar um instantâneo para migração de dados

O recurso de instantâneos permite que você mova os dados de rosto salvos, tais como um **PersonGroup** treinado, para uma assinatura de Detecção Facial dos Serviços Cognitivos do Azure diferente. Talvez você queira usar esse recurso se, por exemplo, tiver criado um objeto **PersonGroup** usando uma assinatura de avaliação gratuita e agora quiser migrá-lo para uma assinatura paga. Confira [Migrar os dados de rosto](../Face-API-How-to-Topics/how-to-migrate-face-data.md) para ter uma visão geral ampla do recurso de instantâneos.

Neste exemplo, você migrará o **PersonGroup** que criou em [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group). Você pode concluir essa seção primeiro ou usar seus próprios constructos de dados de Detecção Facial.

### <a name="set-up-target-subscription"></a>Configurar a assinatura de destino

Primeiro, você precisa obter uma segunda assinatura do Azure com um recurso de Detecção Facial; você pode fazer isso seguindo as etapas na seção [Configuração](#setting-up). 

Em seguida, crie as variáveis a seguir próximo da parte superior do seu script. Você também precisará criar variáveis de ambiente para a ID da assinatura da conta do Azure, bem como a chave, o ponto de extremidade e a ID da assinatura da nova conta (de destino). 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Autenticar o cliente de destino

Posteriormente, no script, salve o objeto de cliente atual como o cliente de origem e, em seguida, autentique um novo objeto de cliente para a assinatura de destino. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Usar um instantâneo

O restante das operações de instantâneo ocorre em uma função assíncrona. 

1. A primeira etapa é **tirar** o instantâneo, que salva os dados de rosto da assinatura original em um local de nuvem temporário. Esse método retorna uma ID que você usa para consultar o status da operação.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Em seguida, consulte a ID até que a operação seja concluída.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Esse código usa a função `wait_for_operation`, que você deve definir separadamente:

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Volte para a função assíncrona. Use a operação **aplicar** para gravar os dados de rosto na assinatura de destino. Esse método também retorna uma ID.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Novamente, use a função `wait_for_operation` para consultar a ID até que a operação seja concluída.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

Depois de concluir essas etapas, você poderá acessar os constructos de dados de rosto da nova assinatura (de destino).

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `python` no seu arquivo de início rápido.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Se você criou um **PersonGroup** neste início rápido e deseja excluí-lo, execute o seguinte código no script:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

Se você migrou dados usando o recurso de instantâneo neste guia de início rápido, também precisará excluir o **PersonGroup** salvo na assinatura de destino.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a usar a biblioteca de Detecção Facial para Python para realizar tarefas básicas. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência da API de Detecção Facial (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [O que é a API de Detecção Facial?](../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).