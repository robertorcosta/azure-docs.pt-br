---
title: O que é o Video Indexer?
titleSuffix: Azure Media Services
description: Este artigo fornece uma visão geral do serviço Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: juliako
ms.openlocfilehash: 9bea9e375e00d6fc0f6c33aa1bcc766773db2059
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269934"
---
# <a name="what-is-video-indexer"></a>O que é o Video Indexer?

O Video Indexer (VI) é a solução de IA da Azure Media Services e faz parte da marca Azure Cognitive Services. O Video Indexer fornece a capacidade de extrair insights profundos (sem necessidade de análise de dados ou habilidades de codificação) usando modelos de aprendizado de máquina baseados em múltiplos canais (voz, vocais, visual). Você pode personalizar e treinar os modelos. O serviço permite uma pesquisa profunda, reduz custos operacionais, permite novas oportunidades de monetização e cria novas experiências de usuário em grandes arquivos de vídeos (com barreiras de entrada baixas).

Para começar a extrair insights com o Video Indexer, você precisa criar uma conta e enviar vídeos. Quando você envia seus vídeos para o Video Indexer, ele analisa tanto visuais quanto áudio executando diferentes modelos de IA. À medida que o Video Indexer analisa seu vídeo, os insights que são extraídos pelos modelos de IA.

O diagrama a seguir é uma ilustração e não uma explicação técnica de como o Indexador de Vídeo funciona no backend.

![Diagrama de fluxo do Indexador de Vídeo do Azure Media Services](./media/video-indexer-overview/model-chart.png)


## <a name="compliance-privacy-and-security"></a>Conformidade, privacidade e segurança

Como um lembrete importante, você deve cumprir todas as leis aplicáveis no seu uso do Video Indexer, e você não pode usar o Indexador de Vídeo ou qualquer serviço Azure de uma maneira que viole os direitos dos outros, ou que possa ser prejudicial para outros.

Antes de enviar qualquer vídeo/imagem para o Video Indexer, você deve ter todos os direitos adequados para usar o vídeo/imagem, incluindo, quando exigido por lei, todos os consentimentos necessários de indivíduos (se houver) no vídeo/imagem, para o uso, processamento e armazenamento de seus dados no Video Indexer e no Azure. Algumas jurisdições podem impor requisitos legais especiais para a coleta, processamento on-line e armazenamento de determinadas categorias de dados, como dados biométricos. Antes de usar o Video Indexer e o Azure para o processamento e armazenamento de quaisquer dados sujeitos a requisitos legais especiais, você deve garantir o cumprimento de quaisquer requisitos legais que possam se aplicar a Você.

Para saber mais sobre conformidade, privacidade e segurança no Video Indexer, visite o Microsoft [Trust Center](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx). Para as obrigações de privacidade da Microsoft, práticas de tratamento e retenção de dados, incluindo como excluir seus dados, por favor, revise a [Declaração](https://privacy.microsoft.com/PrivacyStatement)de Privacidade da Microsoft, os Termos de [Serviços Online](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") e [OAddendum de Processamento de Dados](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Ao usar o Video Indexer, você concorda em estar vinculado ao OST, DPA e pela Declaração de Privacidade.

## <a name="what-can-i-do-with-video-indexer"></a>O que posso fazer com o Video Indexer?

Os insights do Video Indexer podem ser aplicados a muitos cenários, entre eles estão:

* *Pesquisa profunda*: Use os insights extraídos do vídeo para melhorar a experiência de pesquisa em uma videoteca. Por exemplo, indexar palavras faladas e rostos pode permitir a experiência de pesquisa de encontrar momentos em um vídeo onde uma pessoa falou certas palavras ou quando duas pessoas foram vistas juntas. A pesquisa baseada em tais insights de vídeos é aplicável a agências de notícias, institutos educacionais, emissoras, proprietários de conteúdo de entretenimento, aplicativos DE EMPRESAS e, em geral, a qualquer indústria que tenha uma videoteca que os usuários precisem pesquisar contra.
* *Criação de conteúdo*: Crie trailers, bobinas de destaque, conteúdo de mídia social ou clipes de notícias com base nos insights que o Indexador de Vídeo extrai do seu conteúdo. Quadros de chaves, marcadores de cenas e carimbos de tempo para as pessoas e aparências de rótulos tornam o processo de criação muito mais suave e fácil, e permite que você obtenha as partes do vídeo que você precisa para o conteúdo que você está criando.
* *Acessibilidade*: Se você deseja disponibilizar seu conteúdo para pessoas com deficiência ou se deseja que seu conteúdo seja distribuído para diferentes regiões usando diferentes idiomas, você pode usar a transcrição e a tradução fornecidas pelo indexador de vídeo em vários idiomas.
* *Monetização*: O indexador de vídeo pode ajudar a aumentar o valor dos vídeos. Por exemplo, indústrias que dependem da receita de anúncios (mídia de notícias, mídia social e assim por diante) podem fornecer anúncios relevantes usando os insights extraídos como sinais adicionais ao servidor de anúncios.
* *Moderação de conteúdo*: Use modelos de moderação de conteúdo textual e visual para manter seus usuários a salvo de conteúdos inadequados e validar que o conteúdo que você publica corresponde aos valores da sua organização. Você pode bloquear automaticamente certos vídeos ou alertar seus usuários sobre o conteúdo.
* *Recomendações*: Os insights em vídeo podem ser usados para melhorar o engajamento do usuário, destacando os momentos de vídeo relevantes para os usuários. Ao marcar cada vídeo com metadados adicionais, você pode recomendar aos usuários os vídeos mais relevantes e destacar as partes do vídeo que irão corresponder às suas necessidades.

## <a name="features"></a>Recursos

A lista a seguir mostra os insights que você pode recuperar de seus vídeos usando modelos de vídeo e áudio do Indexador de Vídeo:

### <a name="video-insights"></a>Insights para vídeos

* **Detecção facial**: detecta e agrupa rostos que aparecem no vídeo.
* **Identificação de celebridades**: O Video Indexer identifica automaticamente mais de 1 milhão de celebridades — como líderes mundiais, atores, atrizes, atletas, pesquisadores, líderes de negócios e tecnologia em todo o mundo. Os dados sobre essas celebridades também podem ser encontrados em vários sites (IMDB, Wikipedia, etc.).
* **Identificação facial baseada em conta**: o Video Indexer treina um modelo para uma conta específica. Em seguida, reconhece rostos no vídeo com base no modelo treinado. Para obter mais informações, consulte [Personalizar um modelo de pessoa no site do Indexador de vídeo](customize-person-model-with-website.md) e personalizar um modelo de pessoa com a [API do Indexador de Vídeo](customize-person-model-with-api.md).
* **Extração de miniaturas para rostos** ("melhor rosto"): Identifica automaticamente o rosto mais capturado em cada grupo de rostos (com base na qualidade, tamanho e posição frontal) e extrai-o como um ativo de imagem.
* **Reconhecimento de texto visual** (OCR): Extrai texto que é exibido visualmente no vídeo.
* **Moderação de conteúdo visual**: detecta elementos visuais eróticos e/ou somente para adultos.
* **Identificação de rótulos**: identifica objetos visuais e ações exibidas.
* **Segmentação de cena**: Determina quando uma cena muda no vídeo com base em pistas visuais. Uma cena retrata um único evento e é composta por uma série de tomadas consecutivas, que estão semanticamente relacionadas.
* **Detecção de tiro**: Determina quando uma foto muda no vídeo com base em sinais visuais. Uma foto é uma série de quadros tirados da mesma câmera de filme. Para obter mais informações, consulte [Cenas, fotos e quadros-chave](scenes-shots-keyframes.md).
* **Detecção de quadro preto**: identifica quadros pretos apresentados no vídeo.
* **Extração de quadro-chave**: detecta quadros-chave estáveis em um vídeo.
* **Créditos de rolamento**: Identifica o início e o fim dos créditos de rolamento no final de programas de TV e filmes.
* **Detecção de caracteres animados** (visualização): Detecção, agrupamento e reconhecimento de personagens em conteúdo animado por meio da integração com [a visão personalizada dos Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Para obter mais informações, consulte [Detecção de caracteres animados](animated-characters-recognition.md).
* **Detecção do tipo de tiro editorial**: Marcação de tiros com base em seu tipo (como tiro largo, tiro médio, close-up, close-up extremo, dois tiros, várias pessoas, ao ar livre e interior, e assim por diante). Para obter mais informações, consulte [Detecção do tipo de tiro editorial](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Insights de áudio

* **Transcrição de áudio**: Converte a fala em texto em 12 idiomas e permite extensões. Os idiomas suportados incluem inglês, espanhol, francês, alemão, italiano, mandarim chines, japonês, árabe, russo, português brasileiro, hindi e coreano.
* **Detecção automática de idioma**: identifica automaticamente o idioma falado dominante. Os idiomas suportados incluem inglês, espanhol, francês, alemão, italiano, mandarim chines, japonês, russo e português brasileiro. Se o idioma não pode ser identificado com confiança, o Video Indexer assume que a língua falada é o inglês. Para obter mais informações, consulte [o modelo de identificação de idiomas](language-identification-model.md).
* **Identificação e transcrição de fala em várias linguagens** (visualização): Identifica automaticamente a língua falada em diferentes segmentos do áudio. Ele envia cada segmento do arquivo de mídia para ser transcrito e, em seguida, combina a transcrição de volta a uma transcrição unificada. Para obter mais informações, consulte [Identificar e transcrever automaticamente conteúdo em vários idiomas](multi-language-identification-transcription.md).
* **Legendagem oculta**: cria legendas ocultas em três formatos: VTT, TTML, SRT.
* **Processamento de dois canais**: O auto detecta transcrições separadas e se funde em uma única linha do tempo.
* **Redução de ruído**: Limpa o áudio de telefonia ou gravações barulhedas (com base em filtros skype).
* **Personalização de transcrições** (CRIS): Treina a fala personalizada para modelos de texto para criar transcrições específicas do setor. Para obter mais informações, consulte [Personalizar um modelo de idioma do site do Indexador de vídeo](customize-language-model-with-website.md) e personalizar um modelo de idioma com as [APIs do Indexador de Vídeo](customize-language-model-with-api.md).
* **Enumeração do alto-falante**: Mapeia e entende qual orador falou quais palavras e quando.
* **Estatísticas dos palestrantes**: Fornece estatísticas para as relações de fala dos palestrantes.
* **Moderação de conteúdo textual**: detecta texto explícito na transcrição de áudio.
* **Efeitos de**áudio : Identifica efeitos de áudio como palmas das mãos, fala e silêncio.
* **Detecção de emoções**: Identifica emoções com base na fala (o que está sendo dito) e tonalidade de voz (como está sendo dito). A emoção pode ser alegria, tristeza, raiva ou medo.
* **Tradução**: cria traduções da transcrição de áudio em 54 idiomas diferentes.

### <a name="audio-and-video-insights-multi-channels"></a>Insights de áudio e vídeo (multicanais)

Ao indexar por um canal, o resultado parcial desses modelos estará disponível.

* **Extração de palavras-chave**: Extrai palavras-chave da fala e do texto visual.
* **Denominada extração de entidades**: Extrai marcas, locais e pessoas a partir de texto de fala e visual através do processamento de linguagem natural (PNL).
* **Inferência de tópico**: faz inferências dos principais tópicos das transcrições. A taxonomia IPTC de 2º nível está incluída.
* **Artifacts**: extrai um conjunto avançado de artefatos com o "próximo nível de detalhes" para cada um dos modelos.
* **Análise de sentimento**: identifica sentimentos positivos, negativos e neutros de falas e de textos visuais.

## <a name="how-can-i-get-started-with-video-indexer"></a>Como posso começar com o Video Indexer?

Você pode acessar os recursos do Indexador de vídeo de três maneiras:

* Portal do Indexador de Vídeo: Uma solução fácil de usar que permite avaliar o produto, gerenciar a conta e personalizar modelos.

    Para obter mais informações sobre o portal, consulte [Comece com o site do Video Indexer](video-indexer-get-started.md).  

* Integração de API: Todos os recursos do Video Indexer estão disponíveis através de uma API REST, que permite integrar a solução em seus aplicativos e infra-estrutura.

    Para começar como desenvolvedor, consulte [Use Video Indexer REST API](video-indexer-use-apis.md).

* Widget incorporável: Permite incorporar as experiências do Indexador de Vídeo, do player e do editor em seu aplicativo.

    Para obter mais informações, consulte [Incorporar widgets visuais em seu aplicativo](video-indexer-embed-widgets.md).

Se você estiver usando o site, os insights são adicionados como metadados e são visíveis no portal. Se você estiver usando APIs, os insights estão disponíveis como um arquivo JSON.

## <a name="next-steps"></a>Próximas etapas

Você está pronto para começar com o Video Indexer. Para obter mais informações, consulte os seguintes artigos:

- [Comece com o site do Video Indexer](video-indexer-get-started.md).
- [Processe o conteúdo com a API rest do indexador de vídeo](video-indexer-use-apis.md).
- [Incorpore widgets visuais em seu aplicativo](video-indexer-embed-widgets.md).
