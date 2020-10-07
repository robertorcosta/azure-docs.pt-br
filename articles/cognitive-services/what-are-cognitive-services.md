---
title: O que são os Serviços Cognitivos do Azure?
titleSuffix: Azure Cognitive Services
description: Os Serviços Cognitivos do Azure são serviços de nuvem com APIs REST e SDKs de biblioteca de clientes que podem ser usados com o Microsoft Azure para criar aplicativos inteligentes.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: serviços cognitivos, inteligência cognitiva, soluções cognitivas, serviços de IA, compreensão cognitiva, recursos cognitivos
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 08/28/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: bcb3fdbe12a2704e585a9b13484c2528c9cdc559
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90894446"
---
# <a name="what-are-azure-cognitive-services"></a>O que são os Serviços Cognitivos do Azure?

Os Serviços Cognitivos do Azure são serviços baseados em nuvem com APIs REST e SDKs de biblioteca de cliente disponíveis para ajudar os desenvolvedores a incutir a inteligência cognitiva nos aplicativos, sem ter conhecimentos ou habilidades diretas sobre os domínios de IA (inteligência artificial) ou ciência de dados. Os Serviços Cognitivos do Azure permitem que os desenvolvedores adicionem com facilidade recursos cognitivos a seus aplicativos, com soluções cognitivas capazes de ver, ouvir, falar, entender e até mesmo começar a racionalizar.

O catálogo de serviços de IA que fornecem compreensão cognitiva é categorizado em cinco pilares principais:

* Visão
* Fala
* Linguagem
* Pesquisa na Web
* Decisão

A lista atual da nova documentação está disponível em [Novidades dos documentos dos Serviços Cognitivos](whats-new-docs.md).

## <a name="vision-apis"></a>APIs de visão

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Pesquisa Visual Computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "Visual Computacional")|O serviço de Pesquisa Visual Computacional fornece acesso a algoritmos cognitivos avançados para processar imagens e retornar informações.|
|[Serviço de Visão Personalizada](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/home "Serviço de Visão Personalizada")|O Serviço de Visão Personalizada permite criar classificadores de imagem personalizada.|
|[Detecção Facial](https://docs.microsoft.com/azure/cognitive-services/face/ "Face")| O serviço de Detecção Facial fornece acesso a algoritmos avançados de detecção facial, habilitando a detecção e o reconhecimento de atributos faciais.|
|[Reconhecimento de Formulários](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/ "Reconhecimento de Formulários") (versão prévia)|O Reconhecimento de Formulários identifica e extrai pares chave-valor e dados de tabela de documentos de formulário e, em seguida, gera dados estruturados, incluindo as relações no arquivo original.|
|[Reconhecimento de Tinta Digital](https://docs.microsoft.com/azure/cognitive-services/ink-recognizer/ "Reconhecimento de Tinta Digital") (desativação)|O Reconhecimento de Tinta Digital permite que você reconheça e analise dados de traço de tinta digital, formas e conteúdo manuscrito e gere uma estrutura de documento com todas as entidades reconhecidas.|
|[Video Indexer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "Video Indexer")|O Video Indexer permite extrair insights de seus vídeos.|

## <a name="speech-apis"></a>Speech APIs

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Serviço de Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Serviço de Fala")|O serviço de fala adiciona recursos habilitados para fala aos aplicativos.|
|[API de Reconhecimento do Locutor](https://docs.microsoft.com/azure/cognitive-services/speaker-recognition/home "API de Reconhecimento do Locutor") (versão prévia)|A API de Reconhecimento do Locutor fornece algoritmos para a verificação e identificação do locutor.|
|[Fala do Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home "Fala do Bing") (desativação)|A API de Fala do Bing fornece uma maneira fácil de criar recursos habilitados para fala em seus aplicativos.|
|[Tradução de Fala](https://docs.microsoft.com/azure/cognitive-services/translator-speech/ "Conversor de Fala") (desativação)|A Tradução de Fala é um serviço de tradução automática.|

> [!NOTE]
> Procurando a [Azure Cognitive Search](https://docs.microsoft.com/azure/search/)? Embora ele use os Serviços Cognitivos para algumas tarefas, é uma tecnologia de pesquisa diferente, que dá suporte a outros cenários.


## <a name="language-apis"></a>APIs de Linguagem

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[LUIS (Reconhecimento vocal)](https://docs.microsoft.com/azure/cognitive-services/luis/ "Reconhecimento Vocal")|O serviço de Reconhecimento Vocal (LUIS) permite que seu aplicativo entenda o que uma pessoa quer com suas próprias palavras.|
|[O QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/index "QnA Maker")|A QnA Maker permite que você crie um serviço de perguntas e respostas do seu conteúdo semiestruturado.|
|[Análise de Texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "Análise de texto")|A Análise de Texto oferece processamento de linguagem natural sobre texto bruto para análise de sentimento, extração de frases-chave e detecção de idioma.|
|[Tradutor](https://docs.microsoft.com/azure/cognitive-services/translator/ "Tradutor")|O Tradutor fornece uma tradução de texto baseada em máquina quase em tempo real.|


## <a name="search-apis"></a>APIs de pesquisa

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Pesquisa de Notícias do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/ "Pesquisa de Notícias do Bing")|A Pesquisa de Notícias do Bing retorna uma lista de artigos de notícias determinados como relevantes para a consulta do usuário.|
|[Pesquisa de Vídeo do Bing](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search/ "Pesquisa de Vídeo do Bing")|A Pesquisa de Vídeo do Bing retorna uma lista de vídeos determinados como relevantes para a consulta do usuário.|
|[Pesquisa na Web do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/ "Pesquisa da Web do Bing")|A Pesquisa na Web do Bing retorna uma lista de resultados de pesquisa determinados como relevantes para a consulta do usuário.|
|[Sugestão Automática do Bing](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest "Sugestão Automática do Bing")|A Sugestão Automática do Bing permite enviar um termo de consulta de pesquisa parcial ao Bing e recuperar uma lista de consultas sugeridas.|
|[Pesquisa Personalizada do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search "Pesquisa Personalizada do Bing")|A Pesquisa Personalizada do Bing permite que você crie experiências de pesquisa personalizada para os tópicos importantes para você.|
|[Pesquisa de Entidade do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/ "Pesquisa de Entidade do Bing")|A Pesquisa de Entidade do Bing retorna informações sobre entidades que o Bing determina como relevantes para a consulta de um usuário.|
|[Pesquisa de Imagem do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "Pesquisa de Imagem do Bing")|A Pesquisa de Imagem do Bing retorna uma exibição de imagens determinadas como relevantes para a consulta do usuário.|
|[Pesquisa Visual do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search "Pesquisa Visual do Bing")|A Pesquisa Visual do Bing retorna insights sobre uma imagem, como imagens visualmente semelhantes, fontes de compras de produtos encontrados na imagem e pesquisas relacionadas.|
|[Pesquisa de Negócios Locais do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-local-business-search/ "Pesquisa de Negócios Locais do Bing")| A API de Pesquisa de Negócios Locais do Bing permite que seus aplicativos encontrem informações de contato e localização sobre locais e negócios com base em consultas de pesquisa.|
|[Verificação de Ortografia do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/ "Verificação Ortográfica do Bing")|A Verificação Ortográfica do Bing permite fazer verificações de gramática contextual e ortográfica.|

## <a name="decision-apis"></a>APIs de decisão

|Nome do Serviço|Descrição do Serviço|
|:-----------|:------------------|
|[Detector de Anomalias](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Detector de Anomalias") (versão prévia)|O Detector de Anomalias permite que você monitore e detecte anormalidades em seus dados de série temporal.|
|[Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview "Content Moderator")|O Content Moderator fornece monitoramento para um possível conteúdo ofensivo, indesejável e arriscado.|
|[Assistente de Métricas](https://docs.microsoft.com/azure/cognitive-services/metrics-advisor) (versão prévia) | O Assistente de Métricas fornece detecção de anomalias personalizável em dados de série temporal multivariados, além de um portal da Web completo para ajudar você a usar o serviço.
|[Personalizador](https://docs.microsoft.com/azure/cognitive-services/personalizer/ "Personalizador")|O Personalizador permite escolher a melhor experiência a ser mostrada aos usuários, aprendendo com o comportamento deles em tempo real.|

## <a name="learn-with-the-quickstarts"></a>Saiba mais com os guias de início rápido

Saiba mais sobre como criar um recurso dos Serviços Cognitivos com inícios rápidos práticos usando:

* [Azure portal](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Portal do Azure")
* [CLI do Azure](cognitive-services-apis-create-account-cli.md?tabs=windows "CLI do Azure")
* [As bibliotecas de cliente do SDK do Azure](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programming-language-csharp")
* [Modelos do Azure Resource Manager (ARM)](resource-manager-template.md?tabs=portal "Modelos do Azure Resource Manager (ARM)")

## <a name="subscription-management"></a>Gerenciamento de assinaturas

Depois de fazer logon na sua conta Microsoft, você pode acessar [Minhas assinaturas](https://www.microsoft.com/cognitive-services/subscriptions "Minhas assinaturas") para mostrar os produtos que você está usando, a cota restante e a capacidade de adicionar produtos à sua assinatura.

## <a name="upgrade-to-unlock-higher-limits"></a>Atualizar para desbloquear limites mais altos

Todas as APIs têm uma camada gratuita com limites de uso e de taxa de transferência.  É possível aumentar esses limites usando uma oferta paga e selecionando a opção de tipo de preço adequada ao implantar o serviço no portal do Azure. [Saiba mais sobre as ofertas e os preços](https://azure.microsoft.com/pricing/details/cognitive-services/ "ofertas e preços"). Você precisará configurar uma conta de assinante do Azure com um cartão de crédito e um número de telefone. Caso você tenha um requisito especial ou apenas deseje falar com a equipe de vendas, clique no botão "Fale conosco" na parte superior da página de preços.

## <a name="regional-availability"></a>Disponibilidade regional

As APIs dos Serviços Cognitivos são hospedadas em uma rede cada vez maior de data centers gerenciados pela Microsoft. Encontre a disponibilidade regional de cada API na [lista de regiões do Azure](https://azure.microsoft.com/regions "Lista de regiões do Azure").

Procurando uma região ainda sem suporte? Informe-nos com uma solicitação de recurso em nosso [fórum do UserVoice](https://cognitive.uservoice.com/ "Fórum do UserVoice").

## <a name="supported-cultural-languages"></a>Linguagens culturais compatíveis

 Os Serviços Cognitivos oferecem suporte a uma ampla variedade de linguagens culturais no nível do serviço. Você pode encontrar a disponibilidade de linguagem para cada API na [lista de linguagens compatíveis](language-support.md "lista de idiomas com suporte").

## <a name="securing-resources"></a>Como proteger recursos

Os Serviços Cognitivos do Azure fornecem um modelo de segurança em camadas, incluindo [autenticação](authentication.md "autenticação") por meio de credenciais do Azure Active Directory, uma chave de recurso válida e [redes virtuais do Azure](cognitive-services-virtual-networks.md "Redes Virtuais do Azure").

## <a name="container-support"></a>Suporte a contêiner

 Os Serviços Cognitivos fornecem contêineres para implantação local ou na nuvem do Azure. Saiba mais sobre [Contêineres de Serviços Cognitivos](cognitive-services-container-support.md "Contêineres de Serviços Cognitivos").

## <a name="certifications-and-compliance"></a>Certificações e conformidade

Os Serviços Cognitivas receberam certificações como a Certificação CSA STAR, FedRAMP Moderado e HIPAA BAA.

Você pode [baixar](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "download") certificações para suas próprias auditorias e revisões de segurança.

Para entender a privacidade e o gerenciamento de dados, acesse a [Central de Confiabilidade](https://servicetrust.microsoft.com/ "Central de Confiabilidade").

## <a name="support"></a>Suporte

Os Serviços Cognitivos fornecem várias [opções de suporte](cognitive-services-support-options.md "opções de suporte").




## <a name="next-steps"></a>Próximas etapas

* [Criar uma conta dos Serviços Cognitivos](cognitive-services-apis-create-account.md "Criar uma conta dos Serviços Cognitivos")
