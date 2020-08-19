---
title: Início rápido da biblioteca de clientes Go de Detecção Facial
description: Comece a usar a biblioteca de clientes de Detecção Facial para Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: include
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: bb8f80baac5ce49db612a7d3a599d727b824bffc
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505845"
---
Comece a usar a biblioteca de clientes de Detecção Facial para Go. Siga estas etapas para instalar a biblioteca e testar nossos exemplos para tarefas básicas. O serviço de Detecção Facial fornece acesso a algoritmos avançados para detectar e reconhecer rostos humanos em imagens.

Use a biblioteca de clientes do serviço de Detecção Facial para Go para:

* [Detectar faces em uma imagem](#detect-faces-in-an-image)
* [Encontrar rostos semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identificar um rosto](#identify-a-face)
* [Tirar um instantâneo para migração de dados](#take-a-snapshot-for-data-migration)

[Documentação de referência](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | [Download do SDK](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Go](https://golang.org/dl/)
* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Crie um recurso da Detecção Facial"  target="_blank">crie um recurso da Detecção Facial <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo à API de Detecção Facial. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
* Depois de obter uma chave e um ponto de extremidade, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e o ponto de extremidade, chamadas `FACE_SUBSCRIPTION_KEY` e `FACE_ENDPOINT`, respectivamente.

## <a name="setting-up"></a>Configurando

### <a name="create-a-go-project-directory"></a>Criar um diretório do projeto Go

Em uma janela de console (cmd, PowerShell, Terminal, Bash), crie um novo workspace para seu projeto do Go, chamado `my-app`, e navegue até ele.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Seu workspace conterá três pastas:

* **src** – Esse diretório terá o código-fonte e os pacotes. Pacotes instalados com o comando `go get` estarão nessa pasta.
* **pkg** – Esse diretório terá os objetos de pacote Go compilados. Esses arquivos têm uma extensão `.a`.
* **bin** – Esse diretório terá os arquivos executáveis binários que são criados quando você executa o `go install`.

> [!TIP]
> Para saber mais sobre a estrutura de um workspace Go, consulte a [documentação da linguagem Go](https://golang.org/doc/code.html#Workspaces). Este guia inclui informações para configurar `$GOPATH` e `$GOROOT`.

### <a name="install-the-client-library-for-go"></a>Instalar a biblioteca de clientes para Go

Em seguida, instale a biblioteca de clientes para Go:

```bash
go get -u github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

Ou se você usar o DEP, no repositório, execute:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Criar um aplicativo Go

Em seguida, crie um arquivo no diretório **src** chamado `sample-app.go`:

```bash
cd src
touch sample-app.go
```

Abra `sample-app.go` em seu IDE ou editor de texto preferido. Em seguida, adicione o nome do pacote e importe as seguintes bibliotecas:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Em seguida, você começará a adicionar código para realizar diferentes operações do serviço de Detecção Facial.

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir lidam com alguns dos principais recursos da biblioteca de cliente do Go de Detecção Facial.

|Nome|Descrição|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Essa classe representa sua autorização para usar o serviço de Detecção Facial e é necessária para todas as funcionalidades desse serviço. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes. |
|[Cliente](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Essa classe manipula as tarefas básicas de detecção e reconhecimento que você pode realizar com rostos humanos. |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Essa classe representa todos os dados detectados de um único rosto em uma imagem. Você pode usá-la para recuperar informações detalhadas sobre o rosto.|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Essa classe gerencia os constructos **FaceList** armazenados em nuvem, que armazenam um conjunto classificado de rostos. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Essa classe gerencia os constructos **Person** armazenados em nuvem, que armazenam um conjunto de rostos que pertencem a uma única pessoa.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Essa classe gerencia os constructos **PersonGroup** armazenados em nuvem, que armazenam um conjunto classificado de objetos **Person**. |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Essa classe gerencia a funcionalidade de instantâneo. Você pode usá-la para salvar temporariamente todos os dados de Detecção Facial baseados em nuvem e migrar esses dados para uma nova assinatura do Azure. |

## <a name="code-examples"></a>Exemplos de código

Esses exemplos de código mostram como concluir tarefas básicas usando a biblioteca de clientes do serviço de Detecção Facial para Go:

* [Autenticar o cliente](#authenticate-the-client)
* [Detectar faces em uma imagem](#detect-faces-in-an-image)
* [Encontrar rostos semelhantes](#find-similar-faces)
* [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group)
* [Identificar um rosto](#identify-a-face)
* [Tirar um instantâneo para migração de dados](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE] 
> Este início rápido pressupõe que você já [criou variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e o ponto de extremidade da Detecção Facial, denominados `FACE_SUBSCRIPTION_KEY` e `FACE_ENDPOINT`, respectivamente.

Crie uma função **main** e adicione o código a seguir a ela para criar uma instância de um cliente com seu ponto de extremidade e sua chave. Crie um objeto **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** com sua chave e use-a com seu ponto de extremidade para criar um objeto **[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** . Esse código também cria uma instância de um objeto de contexto, necessária para a criação de objetos de cliente. Ele também define uma localização remota em que algumas das imagens de exemplo neste início rápido são encontradas.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Detectar faces em uma imagem

Adicione o código a seguir ao método **main**. Esse código define uma imagem de exemplo remota e especifica quais recursos de detecção facial extrair da imagem. Ele também especifica qual modelo de IA usar para extrair dados dos rostos detectados. Confira [Especificar um modelo de reconhecimento](../../Face-API-How-to-Topics/specify-recognition-model.md) para obter informações sobre essas opções. Por fim, o método **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** realiza a operação de detecção facial na imagem e salva os resultados na memória do programa.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Exibir dados do rosto detectado

O próximo bloco de código usa o primeiro elemento na matriz de objetos **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** e imprime os atributos dele no console. Caso tenha usado uma imagem com vários rostos, você deverá iterar a matriz.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

O código a seguir usa um único rosto detectado (origem) e pesquisa um conjunto de outros rostos (destino) para encontrar correspondências. Quando encontra uma correspondência, ele imprime a ID do rosto correspondente no console.

### <a name="detect-faces-for-comparison"></a>Detectar rostos para comparação

Primeiro, salve uma referência ao rosto detectado na seção [Detectar rostos em uma imagem](#detect-faces-in-an-image). Esse rosto será a origem.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Em seguida, insira o código a seguir para detectar um conjunto de rostos em uma imagem diferente. Esses rostos será o destino.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Encontrar correspondências

O código a seguir usa o método **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** para encontrar todos os rostos de destino que correspondem ao rosto de origem.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Imprimir correspondências

O código a seguir imprime os detalhes de correspondência no console.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Criar e treinar um grupo de pessoas

Para percorrer esse cenário, você precisa salvar as seguintes imagens no diretório raiz do seu projeto: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Esse grupo de imagens contém três conjuntos de imagens de rosto único correspondentes a três pessoas diferentes. O código definirá três objetos **PersonGroup Person** e os associará a arquivos de imagem que começam com `woman`, `man` e `child`.

### <a name="create-persongroup"></a>Criar PersonGroup

Depois de baixar suas imagens, adicione o código a seguir à parte inferior do seu método **main**. Esse código autentica um objeto **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** e o usa para definir um novo **PersonGroup**.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>Criar PersonGroup Persons

O próximo bloco de código autentica um **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** e o usa para definir três novos objetos **PersonGroup Person**. Cada um dos objetos representa uma pessoa no conjunto de imagens.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Atribuir rostos a pessoas

O código a seguir classifica as imagens pelo prefixo, detecta rostos e os atribui a cada respectivo objeto **PersonGroup Person**, com base no nome do arquivo de imagem.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>Treinar PersonGroup

Depois de atribuir os rostos, treine o **PersonGroup** para que ele possa identificar as características visuais associadas a cada um dos objetos **Person** nele contidos. O código a seguir chama o método **train** assíncrono e sonda o resultado, imprimindo o status no console.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>Identificar um rosto

O código a seguir usa uma imagem com vários rostos e procura encontrar a identidade de cada pessoa na imagem. Ele compara cada rosto detectado com um **PersonGroup**, um banco de dados de diferentes objetos **Person** cujas características faciais são conhecidas.

> [!IMPORTANT]
> Para executar este exemplo, você deve primeiro executar o código em [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Obter uma imagem de teste

O código a seguir procura na raiz do projeto uma imagem _test-image-person-group.jpg_ e a carrega para a memória do programa. Você pode encontrar essa imagem no mesmo repositório que as imagens usadas em [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group): https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Detectar rostos de origem na imagem de teste

O próximo bloco de código faz a detecção facial comum na imagem de teste para recuperar todos os rostos e salvá-los em uma matriz.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Identificar faces em imagem

O método **[Identify](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** usa a matriz de rostos detectados e os compara com o **PersonGroup** fornecido (definido e treinado na seção anterior). Se ele puder fazer a correspondência de um rosto detectado com um **Person** no grupo, ele salvará o resultado.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Esse código imprime os resultados de correspondência detalhados no console.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Verificar rostos

A operação de verificação toma uma ID de rosto e outra ID de rosto ou um objeto **Person** e determina se elas pertencem à mesma pessoa.

O código a seguir detecta rostos em duas imagens de origem e verifica cada um deles em relação a um rosto detectado em uma imagem de destino.

### <a name="get-test-images"></a>Obter imagens de teste

Os blocos de código a seguir declaram variáveis que apontarão para as imagens de destino e de origem para a operação de verificação.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Detectar rostos para verificação

O código a seguir detecta rostos nas imagens de origem e de destino e os salva em variáveis.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Obter resultados da verificação

O código a seguir compara cada uma das imagens de origem com a imagem de destino e gera uma mensagem indicando se elas pertencem à mesma pessoa.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>Tirar um instantâneo para migração de dados

O recurso Instantâneos permite que você mova os dados salvos da Detecção Facial, como um **PersonGroup** treinado, para uma assinatura de Detecção Facial dos Serviços Cognitivos do Azure diferente. Você poderá usar esse recurso se, por exemplo, tiver criado um objeto **PersonGroup** usando uma assinatura gratuita e agora quiser migrá-lo para uma assinatura paga. Confira [Migrar os dados de rosto](../../Face-API-How-to-Topics/how-to-migrate-face-data.md) para ter uma visão geral ampla do recurso de instantâneos.

Neste exemplo, você migrará o **PersonGroup** que criou em [Criar e treinar um grupo de pessoas](#create-and-train-a-person-group). Você pode concluir essa seção primeiro ou usar seus próprios constructos de dados de Detecção Facial.

### <a name="set-up-target-subscription"></a>Configurar a assinatura de destino

Primeiro, você precisa ter uma segunda assinatura do Azure com um recurso de Detecção Facial; você pode fazer isso repetindo as etapas na seção [Configurar](#setting-up). 

Depois, crie as variáveis a seguir perto da parte superior do seu método **main**. Você também precisará criar variáveis de ambiente para a ID da assinatura da conta do Azure, bem como a chave, o ponto de extremidade e a ID da assinatura da nova conta (de destino).

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

Em seguida, coloque o valor de sua ID da assinatura em uma matriz para as próximas etapas.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>Autenticar o cliente de destino

Posteriormente, no script, salve o objeto de cliente original como o cliente de origem e, em seguida, autentique um novo objeto de cliente para a assinatura de destino. 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>Tirar um instantâneo

A próxima etapa é tirar o instantâneo com **[Tirar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)** , que salva os dados de rosto de sua assinatura original em uma localização de nuvem temporário. Esse método retorna uma ID que você usa para consultar o status da operação.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

Em seguida, consulte a ID até que a operação seja concluída.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>Aplicar o instantâneo

Use a operação **[Aplicar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** para gravar seus dados de rosto recém-carregados na assinatura de destino. Esse método também retorna uma ID.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

Novamente, consulte a ID até que a operação seja concluída.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

Depois de concluir essas etapas, você poderá acessar os constructos de dados de rosto da nova assinatura (de destino).

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo Go com o comando `go run [arguments]` do seu diretório do aplicativo.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Se você criou um **PersonGroup** neste início rápido e deseja excluí-lo, chame o método **[Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** . Se você migrou dados usando o recurso de instantâneo neste guia de início rápido, também precisará excluir o **PersonGroup** salvo na assinatura de destino.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a usar a biblioteca de Detecção Facial para Go para realizar tarefas básicas. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência da API de Detecção Facial (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [O que é o serviço de Detecção Facial?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go).
