---
title: 'Início Rápido: Biblioteca de clientes da Pesquisa Visual Computacional para Node.js'
description: Introdução à biblioteca de clientes da Pesquisa Visual Computacional para Node.js com este início rápido
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/22/2020
ms.author: pafarley
ms.custom: devx-track-javascript
ms.openlocfilehash: 4d025057cf8992e1dc54a0fd4a07814c7cbb13d3
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88748859"
---
<a name="HOLTop"></a>

[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [Pacote (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | [Exemplos](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão atual do [Node.js](https://nodejs.org/)
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Crie um recurso da Pesquisa Visual Computacional"  target="_blank">crie um recurso da Pesquisa Visual Computacional <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo ao serviço de Pesquisa Visual Computacional. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
* [Crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e a URL do ponto de extremidade, chamados `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`, respectivamente.

## <a name="setting-up"></a>Configurando

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

Instale os pacotes NPM `ms-rest-azure` e `@azure/cognitiveservices-computervision`:

```console
npm install @azure/cognitiveservices-computervision
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

|Nome|Descrição|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | Essa classe é necessária para toda a funcionalidade da Pesquisa Visual Computacional. Você cria uma instância delas com suas informações de assinatura e usa-a para fazer a maioria das operações de imagem.|
|[VisualFeatureTypes](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| Essa enumeração define os diferentes tipos de análise de imagem que podem ser feitos em uma operação de análise padrão. Especifique um conjunto de valores **VisualFeatureTypes** dependendo de suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes da Pesquisa Visual Computacional para Node.js:

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

O código a seguir obtém a lista de legendas geradas para a imagem. Confira [Descrever imagens](../../concept-describing-images.md) para obter mais detalhes.

Primeiro, defina a URL de uma imagem a ser analisada:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Em seguida, adicione o código a seguir para obter a descrição da imagem e imprimi-la no console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Obter categoria da imagem

O código a seguir obtém a categoria detectada da imagem. Confira [Categorizar imagens](../../concept-categorizing-images.md) para obter detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

Defina a função auxiliar `formatCategories`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Obter marcas da imagem

O código a seguir obtém o conjunto de marcas detectadas na imagem. Confira [Marcas de conteúdo](../../concept-tagging-images.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

Defina a função auxiliar `formatTags`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Detectar objetos

O código a seguir detecta objetos comuns na imagem e imprime-os no console. Confira [Detecção de objetos](../../concept-object-detection.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Defina a função auxiliar `formatRectObjects` para retornar as coordenadas superior, esquerda, inferior e direita, juntamente com a largura e a altura.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Detectar marcas

O código a seguir detecta logotipos e marcas corporativas na imagem e imprime-os no console. Confira [Detecção de marca](../../concept-brand-detection.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Detectar faces

O código a seguir retorna os rostos detectados na imagem com suas coordenadas de retângulo e atributos de rosto selecionados. Confira [Detecção facial](../../concept-detecting-faces.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

Defina a função auxiliar `formatRectFaces`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detectar conteúdo para adulto, erótico ou violento

O código a seguir imprime a presença detectada de conteúdo para adulto na imagem. Confira [Conteúdo para adulto, erótico e violento](../../concept-detecting-adult-content.md) para obter mais detalhes.

Defina a URL da imagem que será usada:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

Depois, adicione o código a seguir para detectar conteúdo para adulto e imprimir os resultados no console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Obter esquema de cores da imagem

O código a seguir imprime os atributos de cor detectados na imagem, como as cores dominantes e a cor de destaque. Confira [Esquemas de cores](../../concept-detecting-color-schemes.md) para obter mais detalhes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Defina a função auxiliar `printColorScheme` para imprimir os detalhes do esquema de cores no console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Obter conteúdo específico do domínio

A Pesquisa Visual Computacional pode usar o modelo especializado para realizar análise adicional em imagens. Confira [Conteúdo específico do domínio](../../concept-detecting-domain-content.md) para obter mais detalhes.

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

## <a name="extract-text-ocr-with-read"></a>Extrair um texto (OCR) com a Leitura

A Pesquisa Visual Computacional pode extrair o texto visível de uma imagem e convertê-lo em um fluxo de caracteres. Este exemplo usa as operações de Leitura.

> [!NOTE]
> Você também pode ler um texto de uma imagem local. Confira o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) para obter cenários que envolvem imagens locais.

### <a name="set-up-test-images"></a>Configurar imagens de teste

Salve uma referência da URL das imagens das quais você deseja extrair texto.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

### <a name="call-the-read-api"></a>Chamar a API de Leitura

Adicione o código abaixo, que chama as funções `readTextFromURL` e `readTextFromFile` para as imagens especificadas.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Defina as funções `readTextFromURL` e `readTextFromFile`. Elas chamam os métodos **read** e **readInStream** no objeto de cliente, que retornam uma ID da operação e iniciam um processo assíncrono para ler o conteúdo da imagem. Em seguida, elas usam a ID da operação para verificar o status da operação até que os resultados sejam retornados. Depois, elas retornam os resultados extraídos.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Em seguida, defina a função auxiliar `printRecText`, que imprime os resultados das operações de Leitura no console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `node` no seu arquivo de início rápido.

```console
node index.js
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Referência da API da Pesquisa Visual Computacional (Node.js)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

* [O que é a Pesquisa Visual Computacional?](../../Home.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).
