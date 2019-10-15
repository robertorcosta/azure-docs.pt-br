---
title: 'Início Rápido: Biblioteca de clientes da Pesquisa Visual Computacional para Node.js | Microsoft Docs'
description: Introdução à biblioteca de clientes da Pesquisa Visual Computacional para Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: pafarley
ms.openlocfilehash: a51a8e02ec08b9a4ee353c2c45345c4d01246894
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981825"
---
# <a name="quickstart-computer-vision-client-library-for-nodejs"></a>Início Rápido: Biblioteca de clientes da Pesquisa Visual Computacional para Node.js

Introdução à biblioteca de clientes da Pesquisa Visual Computacional para Node.js. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas. 

Use a biblioteca de clientes da Pesquisa Visual Computacional para Node.js para:

* [Analisar uma imagem](#analyze-an-image)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [Pacote (npm)](https://www.npmjs.com/package/azure-cognitiveservices-computervision) | [Exemplos](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>Configurando

### <a name="create-a-computer-vision-azure-resource"></a>Criar um recurso do Azure da Pesquisa Visual Computacional

Os Serviços Cognitivos do Azure são representados por recursos do Azure que você assina. Crie um recurso da Pesquisa Visual Computacional usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou a [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) em seu computador local. Você também pode:

* Obter uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias gratuitamente. Depois de se inscrever, ela estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exibir esse recurso no [portal do Azure](https://portal.azure.com/).

Depois de obter uma chave da assinatura ou do recurso de avaliação, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e a URL de ponto de extremidade, chamadas `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`, respectivamente.
 
### <a name="create-a-new-nodejs-application"></a>Criar um novo aplicativo do Node.js

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o comando `npm init` para criar um aplicativo do Node com um arquivo `package.json`. 

```console
npm init
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Instale os pacotes NPM `ms-rest-azure` e `azure-cognitiveservices-computervision`:

```console
npm install azure-cognitiveservices-computervision ms-rest-azure
```

O arquivo `package.json` do seu aplicativo será atualizado com as dependências.

### <a name="prepare-the-nodejs-script"></a>Preparar o script Node.js

Crie um novo arquivo, *index.js*, e abra-o em um editor de texto. Adicione as seguintes instruções de importação ao arquivo.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

Em seguida, defina uma função `computerVision` e declare uma série assíncrona com a função principal e a função de retorno de chamada. Você adicionará o código de início rápido à função principal e chamará `computerVision` na parte inferior do script.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

## <a name="object-model"></a>Modelo de objeto

As classes e as interfaces a seguir lidam com alguns dos principais recursos do SDK do Node.js da Pesquisa Visual Computacional.

|NOME|DESCRIÇÃO|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | Essa classe é necessária para toda a funcionalidade da Pesquisa Visual Computacional. Você cria uma instância delas com suas informações de assinatura e usa-a para fazer a maioria das operações de imagem.|
|[VisualFeatureTypes](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| Essa enumeração define os diferentes tipos de análise de imagem que podem ser feitos em uma operação de análise padrão. Especifique um conjunto de valores **VisualFeatureTypes** dependendo de suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Estes trechos de código mostram como realizar as seguintes tarefas com a biblioteca de clientes da Pesquisa Visual Computacional para Node.js:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie variáveis para a chave e o ponto de extremidade do Azure do recurso. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e recarregar o editor, o IDE ou o shell em execução para acessar a variável.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

Instancie um cliente com o ponto de extremidade e a chave. Crie um objeto [ApiKeyCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.apikeycredentials?view=azure-python) com sua chave e ponto de extremidade e use-o para criar um objeto [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

## <a name="analyze-an-image"></a>Analisar uma imagem

O código nesta seção analisa as imagens remotas para extrair vários recursos visuais. Você pode executar essas operações como parte do método **analyzeImage** do objeto cliente ou chamá-las usando métodos individuais. Confira a [documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) para obter detalhes.

> [!NOTE]
> Você também pode analisar uma imagem local. Confira o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) para obter cenários que envolvem imagens locais.

### <a name="get-image-description"></a>Obter a descrição da imagem

O código a seguir obtém a lista de legendas geradas para a imagem. Confira [Descrever imagens](../concept-describing-images.md) para obter mais detalhes.

Primeiro, defina a URL de uma imagem a ser analisada:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Em seguida, adicione o código a seguir para obter a descrição da imagem e imprimi-la no console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Obter categoria da imagem

O código a seguir obtém a categoria detectada da imagem. Confira [Categorizar imagens](../concept-categorizing-images.md) para obter detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

Defina a função auxiliar `formatCategories`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Obter marcas da imagem

O código a seguir obtém o conjunto de marcas detectadas na imagem. Confira [Marcas de conteúdo](../concept-tagging-images.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

Defina a função auxiliar `formatTags`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Detectar objetos

O código a seguir detecta objetos comuns na imagem e imprime-os no console. Confira [Detecção de objetos](../concept-object-detection.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Defina a função auxiliar `formatRectObjects`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Detectar marcas

O código a seguir detecta logotipos e marcas corporativas na imagem e imprime-os no console. Confira [Detecção de marca](../concept-brand-detection.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Detectar faces

O código a seguir retorna os rostos detectados na imagem com suas coordenadas de retângulo e atributos de rosto selecionados. Confira [Detecção facial](../concept-detecting-faces.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

Defina a função auxiliar `formatRectFaces`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detectar conteúdo para adulto, erótico ou violento

O código a seguir imprime a presença detectada de conteúdo para adulto na imagem. Confira [Conteúdo para adulto, erótico e violento](../concept-detecting-adult-content.md) para obter mais detalhes.

Defina a URL da imagem que será usada:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)] 

Depois, adicione o código a seguir para detectar conteúdo para adulto e imprimir os resultados no console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)] 

### <a name="get-image-color-scheme"></a>Obter esquema de cores da imagem

O código a seguir imprime os atributos de cor detectados na imagem, como as cores dominantes e a cor de destaque. Confira [Esquemas de cores](../concept-detecting-color-schemes.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Defina a função auxiliar `printColorScheme` para imprimir os detalhes do esquema de cores no console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Obter conteúdo específico do domínio

A Pesquisa Visual Computacional pode usar o modelo especializado para realizar análise adicional em imagens. Confira [Conteúdo específico do domínio](../concept-detecting-domain-content.md) para obter mais detalhes. 

Primeiro, defina a URL de uma imagem a ser analisada:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

O código a seguir analisa dados sobre pontos de referência detectados na imagem.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Defina a função auxiliar `formatRectDomain` para analisar os dados de localização sobre os pontos de referência detectados.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Obter o tipo de imagem

O código a seguir imprime informações sobre o tipo de imagem – seja clip-art ou desenho de linha.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

Defina a função auxiliar `describeType`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

A Pesquisa Visual Computacional pode ler um texto visível em uma imagem e convertê-lo em um fluxo de caracteres.

> [!NOTE]
> Você também pode ler um texto de uma imagem local. Confira o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) para obter cenários que envolvem imagens locais.

### <a name="set-up-test-images"></a>Configurar imagens de teste

Salve uma referência da URL das imagens das quais você deseja extrair texto.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

### <a name="call-the-recognize-api"></a>Chamar a API de Reconhecimento

Adicione o código abaixo, que chama a função `recognizeText` para as imagens especificadas.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Defina a função `recognizeText`. Isso chama o método **recognizeText** no objeto do cliente, que retorna uma ID de operação e inicia um processo assíncrono para ler o conteúdo da imagem. Em seguida, ele usa a ID da operação para verificar a operação em intervalos de um segundo até que os resultados sejam retornados. Depois, retorna os resultados extraídos.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Em seguida, defina a função auxiliar `printRecText`, que imprime os resultados de uma operação de Reconhecimento para o console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `node` no seu arquivo de início rápido.

```console
node index.js
```

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Referência da API da Pesquisa Visual Computacional (Node.js)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

* [O que é a API da Pesquisa Visual Computacional?](../Home.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).
