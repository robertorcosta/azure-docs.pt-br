---
title: Serviços Cognitivos e Machine Learning
titleSuffix: Azure Cognitive Services
description: Veja como os Serviços Cognitivos do Azure se enquadram com outras ofertas do Azure para aprendizado de máquina.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: diberry
ms.openlocfilehash: cde505e4c95de9b9693a0e9d260d7fa84f3e905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75531472"
---
# <a name="cognitive-services-and-machine-learning"></a>Serviços Cognitivos e aprendizado de máquina

Os Serviços Cognitivos fornecem recursos de aprendizado de máquina para resolver problemas gerais, como analisar texto para sentimentos emocionais ou analisar imagens para reconhecer objetos ou rostos. Você não precisa de aprendizado de máquina especial ou conhecimento em ciência de dados para usar esses serviços. 

[Cognitive Services](welcome.md) é um grupo de serviços, cada um suportando diferentes capacidades de previsão generalizadas. Os serviços são divididos em diferentes categorias para ajudá-lo a encontrar o serviço certo. 

|Categoria de serviço|Finalidade|
|--|--|
|[Decisão](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|Crie aplicativos que apresentam recomendações para tomada de decisão eficiente e informada.|
|[Linguagem](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|Permita que os aplicativos processem idioma natural com scripts pré-criados, avaliem sentimentos e saibam como reconhecer o que os usuários desejam.|
|[Pesquisar](https://azure.microsoft.com/services/cognitive-services/directory/search/)|Adicione APIs de Pesquisa do Bing aos aplicativos e aproveite a capacidade para vasculhar bilhões de páginas da Web, imagens, vídeos e notícias com uma única chamada à API.|
|[Fala](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|Converta fala em texto e texto em fala de som natural. Traduza de um idioma para outro e habilite o reconhecimento e a verificação do locutor.|
|[Visão](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|Reconheça, identifique, legende, indexe e modere suas imagens, vídeos e conteúdo de tinta digital.|
||||

Use serviços cognitivos quando você:

* Pode usar uma solução generalizada.
* Solução de acesso a partir de uma API rest de programação ou SDK. 

Use outra solução de aprendizado de máquina quando você:

* Precisa escolher o algoritmo e precisa treinar em dados muito específicos.

## <a name="what-is-machine-learning"></a>O que aprendizado da máquina?

Machine learning é um conceito onde você reúne dados e um algoritmo para resolver uma necessidade específica. Uma vez que os dados e algoritmos são treinados, a saída é um modelo que você pode usar novamente com dados diferentes. O modelo treinado fornece insights com base nos novos dados. 

O processo de construção de um sistema de aprendizagem de máquina requer algum conhecimento de machine learning ou ciência de dados.

O aprendizado de máquina é fornecido usando [produtos e serviços de Machine Learning (AML) do Azure.](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure/machine-learning/studio/context/ml-context)

## <a name="what-is-a-cognitive-service"></a>O que é um Serviço Cognitivo?

Um Serviço Cognitivo fornece parte ou todos os componentes em uma solução de aprendizado de máquina: dados, algoritmo e modelo treinado. Esses serviços são destinados a exigir conhecimento geral sobre seus dados sem precisar de experiência com machine learning ou ciência de dados. Esses serviços fornecem api(s) rest e SDKs baseados em idioma. Como resultado, você precisa ter conhecimento de linguagem de programação para usar os serviços.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>Como os Serviços Cognitivos e o Azure Machine Learning (AML) são semelhantes?

Ambos têm o objetivo final de aplicar inteligência artificial (IA) para melhorar as operações de negócios, embora a forma como cada uma delas fornece isso nas respectivas ofertas seja diferente. 

Geralmente, as audiências são diferentes:

* Serviços Cognitivos são para desenvolvedores sem experiência de aprendizado de máquina.
* O Azure Machine Learning é adaptado para cientistas de dados. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>Como um Serviço Cognitivo é diferente do aprendizado de máquina?

Um serviço cognitivo fornece um modelo treinado para você. Isso une dados e um algoritmo, disponíveis a partir de uma API ou SDK REST. Você pode implementar este serviço em poucos minutos, dependendo do seu cenário.  Um Serviço Cognitivo fornece respostas para problemas gerais, como frases-chave em texto ou identificação de itens em imagens. 

O aprendizado de máquina é um processo que geralmente requer um período maior de tempo para ser implementado com sucesso. Esse tempo é gasto na coleta de dados, limpeza, transformação, seleção de algoritmos, treinamento de modelos e implantação para chegar ao mesmo nível de funcionalidade fornecido por um Serviço Cognitivo. Com o aprendizado de máquina, é possível fornecer respostas para problemas altamente especializados e/ou específicos. Problemas de aprendizagem de máquina requerem familiaridade com o assunto específico e dados do problema em análise, bem como expertise em ciência de dados.

## <a name="what-kind-of-data-do-you-have"></a>Que tipo de dados você tem?

Os Serviços Cognitivos, como um grupo de serviços, podem exigir nenhum, alguns ou todos os dados personalizados para o modelo treinado. 

### <a name="no-additional-training-data-required"></a>Não são necessários dados adicionais de treinamento

Os serviços que fornecem um modelo totalmente treinado podem ser tratados como uma _caixa preta._ Você não precisa saber como eles funcionam ou quais dados foram usados para treiná-los. Você traz seus dados para um modelo totalmente treinado para obter uma previsão. 

### <a name="some-or-all-training-data-required"></a>Alguns ou todos os dados de treinamento necessários

Alguns serviços permitem que você traga seus próprios dados, em seguida, treine um modelo. Isso permite que você amplie o modelo usando os dados e algoritmos do Serviço com seus próprios dados. A saída corresponde às suas necessidades. Quando você traz seus próprios dados, você pode precisar marcar os dados de uma forma específica para o serviço. Por exemplo, se você está treinando um modelo para identificar flores, você pode fornecer um catálogo de imagens de flores junto com a localização da flor em cada imagem para treinar o modelo. 

Um serviço pode _permitir que_ você forneça dados para melhorar seus próprios dados. Um serviço pode _exigir que_ você forneça dados. 

### <a name="real-time-or-near-real-time-data-required"></a>Dados em tempo real ou quase em tempo real necessários

Um serviço pode precisar de dados em tempo real ou quase em tempo real para construir um modelo eficaz. Esses serviços processam quantidades significativas de dados do modelo. 

## <a name="service-requirements-for-the-data-model"></a>Requisitos de serviço para o modelo de dados

Os dados a seguir categorizam cada serviço pelo tipo de dados que permite ou requer.

|Serviço Cognitivo|Não são necessários dados de treinamento|Você fornece alguns ou todos os dados de treinamento|Coleta de dados em tempo real ou quase em tempo real|
|--|--|--|--|
|[Detector de Anomalias](./Anomaly-Detector/overview.md)|x|x|x|
|Pesquisa do Bing |x|||
|[Pesquisa Visual Computacional](./Computer-vision/Home.md)|x|||
|[Moderador de conteúdo](./Content-Moderator/overview.md)|x||x|
|[Visão Personalizada](./Custom-Vision-Service/home.md)||x||
|[Face](./Face/Overview.md)|x|x||
|[Reconhecimento de Formulários](./form-recognizer/overview.md)||x||
|[Leitura Avançada](./immersive-reader/overview.md)|x|||
|[Reconhecimento de Tinta Digital](./Ink-recognizer/overview.md)|x|x||
|[Reconhecimento Vocal (LUIS)](./LUIS/what-is-luis.md)||x||
|[Personalizador](./personalizer/what-is-personalizer.md)|x*|x*|x|
|[Fabricante de QnA](./QnAMaker/Overview/overview.md)||x||
|[Reconhecimento de alto-falante](./speaker-recognition/home.md)||x||
|[Fala Texto-para-fala (TTS)](speech-service/text-to-speech.md)|x|x||
|[Fala-a-texto (STT)](speech-service/speech-to-text.md)|x|x||
|[Tradução de Fala](speech-service/speech-translation.md)|x|||
|[Análise de texto](./text-analytics/overview.md)|x|||
|[Texto do Tradutor](./translator/translator-info-overview.md)|x|||
|[Texto do Tradutor - tradutor personalizado](./translator/custom-translator/overview.md)||x||

*O personalizador só precisa de dados de treinamento coletados pelo serviço (como opera em tempo real) para avaliar sua política e dados. O personalizador não precisa de grandes conjuntos de dados históricos para treinamento inicial ou em lote. 

## <a name="where-can-you-use-cognitive-services"></a>Onde você pode usar serviços cognitivos?
 
Os serviços são usados em qualquer aplicativo que possa fazer chamadas de API ou SDK rest. Exemplos de aplicativos incluem sites, bots, realidade virtual ou mista, aplicativos de desktop e mobile. 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Como a Pesquisa Cognitiva do Azure está relacionada aos Serviços Cognitivos?

[O Azure Cognitive Search](../search/search-what-is-azure-search.md) é um serviço de pesquisa em nuvem separado que, opcionalmente, usa serviços cognitivos para adicionar processamento de imagem e linguagem natural a cargas de trabalho de indexação. Os Serviços Cognitivos são expostos na Busca Cognitiva do Azure através [de habilidades incorporadas](../search/cognitive-search-predefined-skills.md) que envolvem APIs individuais. Você pode usar um recurso gratuito para passo a passo, mas planeje criar e anexar um [recurso faturado](../search/cognitive-search-attach-cognitive-services.md) para volumes maiores.

## <a name="how-can-you-use-cognitive-services"></a>Como você pode usar serviços cognitivos?

Cada serviço fornece informações sobre seus dados. Você pode combinar serviços em conjunto com soluções em cadeia, como converter a fala (áudio) em texto, traduzir o texto em muitos idiomas e, em seguida, usar os idiomas traduzidos para obter respostas de uma base de conhecimento. Embora os Serviços Cognitivos possam ser usados para criar soluções inteligentes por conta própria, eles também podem ser combinados com projetos tradicionais de aprendizado de máquina para complementar modelos ou acelerar o processo de desenvolvimento. 

Serviços Cognitivos que fornecem modelos exportados para outras ferramentas de aprendizagem de máquina:

|Serviço Cognitivo|Informações do modelo|
|--|--|
|[Visão Personalizada](./custom-vision-service/home.md)|[Exportar](./Custom-Vision-Service/export-model-python.md) para Tensorflow para Android, CoreML para iOS11, ONNX para Windows ML|

## <a name="learn-more"></a>Saiba mais

* [Guia de Arquitetura - Quais são os produtos de aprendizado de máquina na Microsoft?](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [Aprendizado de máquina - Introdução ao deep learning vs. machine learning](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>Próximas etapas

* Crie sua conta de Serviço Cognitivo no [portal Azure](cognitive-services-apis-create-account.md) ou [com o Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli).
* Aprenda a [autenticar](authentication.md) em um serviço cognitivo.
* Use [registro de diagnóstico](diagnostic-logging.md) para identificação e depuração de problemas. 
* Implantar um serviço cognitivo em um [contêiner](cognitive-services-container-support.md)Docker .
* Mantenha-se atualizado com [as atualizações do serviço.](https://azure.microsoft.com/updates/?product=cognitive-services)
