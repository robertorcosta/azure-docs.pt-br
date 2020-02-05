---
title: O que é o Video Indexer?
titleSuffix: Azure Media Services
description: Este artigo fornece uma visão geral do serviço de Video Indexer de serviços de mídia do Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/02/2020
ms.author: juliako
ms.openlocfilehash: aee4390d9b892eddbd7dc59887f5268e4c35d3f7
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989783"
---
# <a name="what-is-video-indexer"></a>O que é o Video Indexer?

Video Indexer (VI) é a solução de ia dos serviços de mídia do Azure e parte da marca de serviços cognitivas do Azure. O Video Indexer fornece a capacidade de extrair informações aprofundadas (sem necessidade de análise de dados ou habilidades de codificação) usando modelos de aprendizado de máquina com base em vários canais (voz, vozes, Visual). Você pode personalizar e treinar os modelos. O serviço permite a pesquisa profunda, reduz os custos operacionais, permite novas oportunidades de monetização e cria novas experiências de usuário em grandes arquivos de vídeos (com barreiras de baixa entrada).

Para começar a extrair informações com Video Indexer, você precisa criar uma conta e carregar vídeos. Quando você carrega seus vídeos para Video Indexer, ele analisa os elementos visuais e áudio executando modelos de ia diferentes. Como Video Indexer analisa seu vídeo, as informações que são extraídas pelos modelos de ia.

O diagrama a seguir é uma ilustração e não uma explicação técnica de como Video Indexer funciona no back-end.

![Diagrama de fluxo de Video Indexer dos serviços de mídia do Azure](./media/video-indexer-overview/model-chart.png)

## <a name="compliance-privacy-and-security"></a>Conformidade, privacidade e segurança
 
Como um lembrete importante, você deve obedecer a todas as leis aplicáveis em seu uso de Video Indexer, e você não pode usar Video Indexer ou qualquer outro serviço do Azure de uma maneira que viole os direitos de outras pessoas ou possa ser prejudicial para outras pessoas. Antes de carregar qualquer vídeo, incluindo quaisquer dados biométricos, para o serviço de Video Indexer para processamento e armazenamento, você deve ter todos os direitos apropriados, incluindo todos os condados apropriados, do (s) indivíduo (es) no vídeo. Para saber mais sobre conformidade, privacidade e segurança em Video Indexer, os [termos de serviços cognitivas](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)da Microsoft. Para obrigações de privacidade e manipulação de seus dados da Microsoft, examine a [política de privacidade](https://privacy.microsoft.com/PrivacyStatement)da Microsoft, os [termos de serviços online](https://www.microsoft.com/licensing/product-licensing/products) ("OST") e o [adendo de processamento de dados](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Informações de privacidade adicionais, incluindo a retenção de dados, exclusão/destruição, estão disponíveis no OST e [aqui](faq.md). Usando Video Indexer, você concorda em estar associado aos termos de serviços cognitivas, do OST, do DPA e da declaração de privacidade.

## <a name="what-can-i-do-with-video-indexer"></a>O que posso fazer com o Video Indexer?

As informações de Video Indexer podem ser aplicadas a vários cenários, entre elas:

* *Pesquisa profunda*: Use as informações extraídas do vídeo para aprimorar a experiência de pesquisa em uma biblioteca de vídeos. Por exemplo, a indexação de palavras faladas e rostos pode permitir a experiência de pesquisa de encontrar momentos em um vídeo em que uma pessoa falou determinadas palavras ou quando duas pessoas foram vistas juntas. A pesquisa com base em tais informações de vídeos é aplicável a agências de notícias, instituições educacionais, difusores, proprietários de conteúdo de entretenimento, aplicativos LOB empresariais e, em geral, a qualquer setor que tenha uma biblioteca de vídeos com a qual os usuários precisam Pesquisar.
* *Criação de conteúdo*: crie trailers, realce rolos, conteúdo de mídia social ou clipes de notícias com base nas informações Video indexer extraídos do seu conteúdo. Quadros-chave, marcadores de cenas e carimbos de data/hora para as pessoas e aparências de rótulo tornam o processo de criação muito mais suave e fácil e permite que você chegue às partes do vídeo de que você precisa para o conteúdo que está criando.
* *Acessibilidade*: se você deseja disponibilizar seu conteúdo para pessoas com deficiências ou se deseja que seu conteúdo seja distribuído para regiões diferentes usando idiomas diferentes, você pode usar a transcrição e a tradução fornecidos pelo Video indexer em vários idiomas.
* *Monetização*: Video indexer pode ajudar a aumentar o valor de vídeos. Por exemplo, os setores que dependem da receita do AD (mídia de notícias, mídia social e assim por diante) podem fornecer anúncios relevantes usando as informações extraídas como sinais adicionais para o servidor do AD.
* *Moderação de conteúdo*: use modelos de moderação de conteúdo visual e textual para manter seus usuários protegidos contra conteúdo inadequado e validar que o conteúdo publicado corresponde aos valores da sua organização. Você pode bloquear automaticamente determinados vídeos ou alertar seus usuários sobre o conteúdo.
* *Recomendações*: as informações de vídeo podem ser usadas para melhorar o envolvimento do usuário, destacando o tempo de vídeo relevante para os usuários. Ao marcar cada vídeo com metadados adicionais, você pode recomendar aos usuários os vídeos mais relevantes e realçar as partes do vídeo que corresponderão às suas necessidades.

## <a name="features"></a>Recursos

A lista a seguir mostra as informações que você pode recuperar de seus vídeos usando Video Indexer modelos de vídeo e áudio:

### <a name="video-insights"></a>Insights para vídeos

* **Detecção facial**: detecta e agrupa rostos que aparecem no vídeo.
* **Identificação de celebridade**: Video indexer identifica automaticamente mais de 1 milhão celebridades — como líderes mundiais, atores, actresses, atletas, pesquisadores, negócios e líderes de tecnologia em todo o mundo. Os dados sobre esses celebridades também podem ser encontrados em vários sites (IMDB, Wikipédia e assim por diante).
* **Identificação facial baseada em conta**: o Video Indexer treina um modelo para uma conta específica. Em seguida, ele reconhece os rostos no vídeo com base no modelo treinado. Para obter mais informações, consulte [Personalizar um modelo Person no site video indexer](customize-person-model-with-website.md) e [Personalizar um modelo Person com a API Video indexer](customize-person-model-with-api.md).
* **Extração de miniatura para rostos** ("melhor face"): identifica automaticamente a melhor aparência capturada em cada grupo de faces (com base na qualidade, no tamanho e na posição frontais) e a extrai como um ativo de imagem.
* **Reconhecimento de texto visual** (OCR): extrai o texto que é exibido visualmente no vídeo.
* **Moderação de conteúdo visual**: detecta elementos visuais eróticos e/ou somente para adultos.
* **Identificação de rótulos**: identifica objetos visuais e ações exibidas.
* **Segmentação de cena**: determina quando uma cena é alterada em vídeo com base em indicações visuais. Uma cena descreve um único evento e é composta por uma série de capturas consecutivas, que são semanticamente relacionadas.
* **Detecção de captura**: determina quando uma captura é alterada em vídeo com base em indicações visuais. Uma captura é uma série de quadros tirados da mesma câmera de imagem de movimento. Para obter mais informações, consulte [cenas, capturas e quadros-chave](scenes-shots-keyframes.md).
* **Detecção de quadro preto**: identifica quadros pretos apresentados no vídeo.
* **Extração de quadro-chave**: detecta quadros-chave estáveis em um vídeo.
* **Créditos sem interrupção**: identifica o início e o fim dos créditos contínuos no final de filmes e apresentações de TV.
* **Detecção de caracteres animados** (versão prévia): detecção, agrupamento e reconhecimento de caracteres em conteúdo animado por meio da integração com a [visão personalizada dos serviços cognitivas](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Para obter mais informações, consulte a [detecção de caracteres animados](animated-characters-recognition.md).
* **Detecção de tipo editorial shot**: marcação de capturas com base em seu tipo (como ampla captura, imagem média, fechamento, Extreme, duas telas, várias pessoas, externamente e em interno, etc.). Para obter mais informações, consulte [detecção do tipo editorial shot](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Insights de áudio

* **Detecção automática de idioma**: identifica automaticamente o idioma falado dominante. Os idiomas com suporte incluem Inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, russo e português brasileiro. Se o idioma não puder ser identificado com confiança, o Video Indexer assumirá que o idioma falado é o inglês. Para obter mais informações, consulte [modelo de identificação de idioma](language-identification-model.md).
* **Identificação de fala em vários idiomas e transcrição** (versão prévia): identifica automaticamente o idioma falado em segmentos diferentes do áudio. Ele envia cada segmento do arquivo de mídia a ser transcrita e, em seguida, combina a transcrição de volta a uma transcrição unificada. Para obter mais informações, consulte [identificar e transcrever automaticamente o conteúdo em vários idiomas](multi-language-identification-transcription.md).
* **Transcrição de áudio**: converte a fala em texto em 12 idiomas e permite extensões. Os idiomas com suporte incluem Inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, árabe, russo, Português do Brasil, híndi e coreano.
* **Legendagem oculta**: cria legendas ocultas em três formatos: VTT, TTML, SRT.
* **Processamento de dois canais**: detecta automaticamente transcrições e mesclagens separadas em uma única linha do tempo.
* **Redução de ruído**: limpa áudio de telefonia ou gravações com ruídos (com base em filtros do Skype).
* **Personalização de transcrição** (CRIS): treina a fala personalizada em modelos de texto para criar transcrições específicas do setor. Para obter mais informações, consulte [Personalizar um modelo de linguagem no site video indexer](customize-language-model-with-website.md) e [Personalizar um modelo de linguagem com as APIs de video indexer](customize-language-model-with-api.md).
* **Enumeração de palestrantes**: mapeia e entende em qual palestrante você tem quais palavras e quando.
* **Estatísticas do palestrante**: fornece estatísticas para as taxas de fala dos alto-falantes.
* **Moderação de conteúdo textual**: detecta texto explícito na transcrição de áudio.
* **Efeitos de áudio**: identifica os efeitos de áudio como mão Claps, fala e silêncio.
* **Detecção de emoções**: identifica emoções com base na fala (o que está sendo dito) e a tonalidade de voz (como ele está sendo dito). A emoção poderia ser a alegria, tristeza, raiva ou medo.
* **Tradução**: cria traduções da transcrição de áudio em 54 idiomas diferentes.

### <a name="audio-and-video-insights-multi-channels"></a>Informações de áudio e vídeo (vários canais)

Ao indexar por um canal, o resultado parcial para esses modelos estará disponível.

* **Extração de palavras-chave**: extrai palavras-chave de fala e texto visual.
* **Extração de entidades nomeadas**: extrai marcas, locais e pessoas de fala e texto visual por meio do NLP (processamento de idioma natural).
* **Inferência de tópico**: faz inferências dos principais tópicos das transcrições. A taxonomia IPTC de 2º nível está incluída.
* **Artifacts**: extrai um conjunto avançado de artefatos com o "próximo nível de detalhes" para cada um dos modelos.
* **Análise de sentimento**: identifica sentimentos positivos, negativos e neutros de falas e de textos visuais.

## <a name="how-can-i-get-started-with-video-indexer"></a>Como posso começar a usar o Video Indexer?

Você pode acessar recursos de Video Indexer de três maneiras:

* Video Indexer Portal: uma solução fácil de usar que permite avaliar o produto, gerenciar a conta e personalizar modelos.

    Para obter mais informações sobre o portal, consulte Introdução [ao site video indexer](video-indexer-get-started.md).  

* Integração de API: todos os recursos de Video Indexer estão disponíveis por meio de uma API REST, que permite que você integre a solução em seus aplicativos e infraestrutura.

    Para começar como desenvolvedor, consulte [usar Video INDEXER API REST](video-indexer-use-apis.md).

* Widget incorporável: permite que você incorpore as experiências de Video Indexer, o Player e o editor em seu aplicativo.

    Para obter mais informações, consulte [Inserir widgets visuais em seu aplicativo](video-indexer-embed-widgets.md).

Se você estiver usando o site, as informações serão adicionadas como metadados e estarão visíveis no Portal. Se você estiver usando APIs, as informações estarão disponíveis como um arquivo JSON.

## <a name="next-steps"></a>Próximos passos

Você está pronto para começar com o Video Indexer. Para obter mais informações, consulte os seguintes artigos:

- [Introdução ao site video indexer](video-indexer-get-started.md).
- [Processar conteúdo com Video INDEXER API REST](video-indexer-use-apis.md).
- [Incorpore widgets visuais em seu aplicativo](video-indexer-embed-widgets.md).
