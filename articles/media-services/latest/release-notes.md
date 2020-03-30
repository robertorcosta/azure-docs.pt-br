---
title: Notas de versão dos Serviços de Mídia do Azure v3 | Microsoft Docs
description: Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece as atualizações mais recentes sobre os Serviços de Mídia do Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 02/03/2020
ms.author: juliako
ms.openlocfilehash: 1db7009096635fc1279ce8a8358e0d8131209722
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372578"
---
# <a name="azure-media-services-v3-release-notes"></a>Notas de versão dos Serviços de Mídia do Azure v3

>Seja notificado sobre quando revisitar esta página para atualizações copiando e colando esta URL: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` no leitor de feed RSS.

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

* As versões mais recentes
* Problemas conhecidos
* Correções de bug
* Funcionalidades preteridas

## <a name="known-issues"></a>Problemas conhecidos

> [!NOTE]
> Você pode usar o [portal Azure](https://portal.azure.com/) para gerenciar v3 [Eventos ao Vivo,](live-events-outputs-concept.md)ver v3 [Assets,](assets-concept.md)obter informações sobre como acessar APIs. Para todas as outras tarefas de gerenciamento (por exemplo, Transformações e Empregos), use a [API REST,](https://aka.ms/ams-v3-rest-ref) [CLI](https://aka.ms/ams-v3-cli-ref)ou uma das [SDKs](media-services-apis-overview.md#sdks)suportadas.

Para obter mais informações, consulte [Guia de migração para migrar do Serviços de Mídia v2 para v3](migrate-from-v2-to-v3.md#known-issues).
 
## <a name="january-2020"></a>Janeiro de 2020

### <a name="improvements-in-media-processors"></a>Melhorias nos processadores de mídia

- Suporte aprimorado para fontes entrelaçadas na Análise de Vídeo – esse conteúdo agora é desentrelaçado corretamente antes de ser enviado para mecanismos de inferência.
- Ao gerar miniaturas com o modo "Melhor", o codificador agora pesquisa além de 30 segundos para selecionar um quadro que não seja monocromático.

### <a name="azure-government-cloud-updates"></a>Atualizações de nuvem do Governo Azure

Serviços de Mídia GA'ed nas seguintes regiões do governo Azure: *USGov Arizona* e *USGov Texas*.

## <a name="december-2019"></a>Dezembro de 2019

Adicionado suporte a CDN para *cabeçalhos Prefetch Origin-Assist* para streaming ao vivo e vídeo demanda; disponível para clientes que têm contrato direto com a Akamai CDN. O recurso CDN-Prefetch do Origin-Assist envolve as seguintes trocas de cabeçalho HTTP entre a origem do Akamai CDN e do Azure Media Services:

|Cabeçalho HTTP|Valores|Remetente|Receptor|Finalidade|
| ---- | ---- | ---- | ---- | ----- |
|CDN-Origin-Assist-Prefetch-Enabled | 1 (padrão) ou 0 |CDN|Origem|Para indicar que o CDN está ativado para a pré-busca|
|CDN-Origin-Assist-Prefetch-Path| Exemplo: <br/>Fragmentos(vídeo=14000000000,format=mpd-time-cmaf)|Origem|CDN|Para fornecer caminho de pré-busca para cdn|
|CDN-Origin-Assist-Prefetch-Request|1 (solicitação de pré-busca) ou 0 (solicitação regular)|CDN|Origem|Para indicar a solicitação do CDN é uma pré-busca|

Para ver parte da troca de cabeçalho em ação, você pode tentar as seguintes etapas:

1. Use Carteiro ou curl para emitir uma solicitação à origem dos Serviços de Mídia para um segmento ou fragmento de áudio ou vídeo. Certifique-se de adicionar o cabeçalho CDN-Origin-Assist-Prefetch-Enabled: 1 na solicitação.
2. Na resposta, você deve ver o cabeçalho CDN-Origin-Assist-Prefetch-Path com um caminho relativo como seu valor.

## <a name="november-2019"></a>Novembro de 2019

### <a name="live-transcription-preview"></a>Pré-visualização de transcrição ao vivo

A transcrição ao vivo está agora em pré-visualização pública e disponível para uso na região oeste dos EUA 2.

A transcrição ao vivo foi projetada para funcionar em conjunto com eventos ao vivo como um recurso adicional.  Ele é suportado em eventos ao vivo de transmissão e standard ou premium.  Quando esse recurso é ativado, o serviço usa o recurso [Fala-texto](../../cognitive-services/speech-service/speech-to-text.md) dos Serviços Cognitivos para transcrever as palavras faladas no áudio recebido em texto. Este texto é então disponibilizado para entrega, juntamente com vídeo e áudio nos protocolos MPEG-DASH e HLS. O faturamento é baseado em um novo medidor adicional que é um custo adicional para o evento ao vivo quando ele está no estado "Running".  Para obter detalhes sobre transcrição e faturamento ao vivo, consulte [transcrição ao vivo](live-transcription.md)

> [!NOTE]
> Atualmente, a transcrição ao vivo só está disponível como um recurso de pré-visualização na região oeste dos EUA 2. Suporta transcrição de palavras faladas em inglês (en-us) apenas neste momento.

### <a name="content-protection"></a>Proteção de conteúdo

O *recurso Token Replay Prevention,* lançado em regiões limitadas em setembro, já está disponível em todas as regiões.
Os clientes da Media Services agora podem definir um limite no número de vezes que o mesmo token pode ser usado para solicitar uma chave ou uma licença. Para obter mais informações, consulte [Token Replay Prevention](content-protection-overview.md#token-replay-prevention).

### <a name="new-recommended-live-encoder-partners"></a>Novos parceiros de codificador ao vivo recomendados

Adicionado suporte para os seguintes novos codificadores de parceiros recomendados para transmissão ao vivo RTMP:

- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- [Câmeras de ação GoPro Hero7/8 e Max](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Aprimoramentos de codificação de arquivos

- Uma nova preconfiguração de codificação de reconhecimento de conteúdo está agora disponível. Ele produz um conjunto de MP4s alinhados ao GOP usando codificação com reconhecimento de conteúdo. Dado qualquer conteúdo de entrada, o serviço realiza uma análise leve inicial do conteúdo de entrada. Ele usa esses resultados para determinar o número ideal de camadas, taxa de bits apropriada e configurações de resolução para entrega por streaming adaptativo. Esta preconfiguração é particularmente eficaz para vídeos de baixa complexidade e média complexidade, onde os arquivos de saída estão em taxas de bits mais baixas, mas em uma qualidade que ainda oferece uma boa experiência aos espectadores. A saída conterá arquivos MP4 com vídeo e áudio intercalados. Para obter mais informações, consulte as [especificações abertas da API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json).
- Desempenho aprimorado e multi-threading para o re-sizer no Standard Encoder. Em condições específicas, o cliente deve ver um aumento de desempenho entre 5-40% de codificação de VOD. O conteúdo de baixa complexidade codificado em várias taxas de bits verá os maiores aumentos de desempenho. 
- A codificação padrão agora mantém uma cadência GOP regular para conteúdo de taxa de quadros variável (VFR) durante a codificação de VOD ao usar a configuração GOP baseada no tempo.  Isso significa que o cliente que submete conteúdo de taxa de quadros misto que varia entre 15-30 fps, por exemplo, deve agora ver distâncias regulares de GOP calculadas na saída para arquivos MP4 de bitrate adaptativos. Isso melhorará a capacidade de alternar perfeitamente entre as faixas ao fornecer mais HLS ou DASH. 
-  Sincronização AV melhorada para o conteúdo de origem da taxa de quadros variável (VFR)

### <a name="video-indexer-video-analytics"></a>Indexador de vídeo, análise de vídeo

- Os quadros-chave extraídos usando a preconfiguração VideoAnalyzer estão agora na resolução original do vídeo em vez de serem redimensionados. A extração de quadros de alta resolução oferece imagens de qualidade original e permite que você faça uso dos modelos de inteligência artificial baseados em imagem fornecidos pelos serviços Microsoft Computer Vision e Custom Vision para obter ainda mais insights do seu vídeo.

## <a name="september-2019"></a>Setembro de 2019

###  <a name="media-services-v3"></a>Serviços de Mídia v3  

#### <a name="live-linear-encoding-of-live-events"></a>Codificação linear ao vivo de eventos ao vivo

A Media Services v3 está anunciando a pré-visualização de 24 horas x 365 dias de codificação linear ao vivo de eventos ao vivo.

###  <a name="media-services-v2"></a>Serviços de Mídia v2  

#### <a name="deprecation-of-media-processors"></a>Depreciação de processadores de mídia

Estamos anunciando a depreciação do *Azure Media Indexer* e *do Azure Media Indexer 2 Preview*. Para as datas de aposentadoria, consulte o tópico [componentes legados.](../previous/legacy-components.md) [O Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) substitui esses processadores de mídia legados.

Para obter mais informações, consulte [Migrate do Azure Media Indexer e do Azure Media Indexer 2 para o Azure Media Services Video Indexer](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Agosto de 2019

###  <a name="media-services-v3"></a>Serviços de Mídia v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Par regional da África do Sul está aberto para serviços de mídia 

Os Serviços de Mídia estão agora disponíveis nas regiões Oeste da África do Sul e África do Sul.

Para obter mais informações, consulte [Nuvens e regiões nas quais existe o Media Services v3](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Serviços de Mídia v2  

#### <a name="deprecation-of-media-processors"></a>Depreciação de processadores de mídia

Estamos anunciando a depreciação dos processadores de mídia *Windows Azure Media Encoder* (WAME) e *Azure Media Encoder* (AME), que estão sendo aposentados. Para as datas de aposentadoria, consulte este tópico [de componentes legados.](../previous/legacy-components.md)

Para obter detalhes, consulte [Migrate WAME para Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) e [Migrate AME to Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335).
 
## <a name="july-2019"></a>Julho de 2019

### <a name="content-protection"></a>Proteção de conteúdo

Ao transmitir conteúdo protegido com restrição de token, os usuários finais precisam obter um token enviado como parte da solicitação de entrega chave. O recurso *Token Replay Prevention* permite que os clientes do Media Services definam um limite de quantas vezes o mesmo token pode ser usado para solicitar uma chave ou uma licença. Para obter mais informações, consulte [Token Replay Prevention](content-protection-overview.md#token-replay-prevention).

A partir de julho, o recurso de pré-visualização estava disponível apenas no Centro Central e no Centro Oeste dos EUA.

## <a name="june-2019"></a>Junho de 2019

### <a name="video-subclipping"></a>Subclipping de vídeo

Agora você pode cortar ou subclipar um vídeo ao codificar usando um [Trabalho](https://docs.microsoft.com/rest/api/media/jobs). 

Essa funcionalidade funciona com qualquer [Transformação](https://docs.microsoft.com/rest/api/media/transforms) que seja construída usando as predefinições [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou as predefinições [StandardEncoderPreset.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 

Veja exemplos:

* [Subclipe um vídeo com .NET](subclip-video-dotnet-howto.md)
* [Subclipe um vídeo com REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Maio de 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Suporte ao Monitor do Azure para registros e métricas de diagnóstico do Media Services

Agora você pode usar o Azure Monitor para visualizar dados de telemetria emitidos pelos Serviços de Mídia.

* Use os registros de diagnóstico do Azure Monitor para monitorar as solicitações enviadas pelo ponto final do Media Services Key Delivery. 
* Monitore as métricas emitidas pelos pontos [finais de streaming](streaming-endpoint-concept.md)dos serviços de mídia .   

Para obter detalhes, consulte [as métricas do Monitor Media Services e os registros de diagnóstico](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Suporte a várias faixas de áudio em Embalagens Dinâmicas 

Ao transmitir ativos que possuem várias faixas de áudio com vários codecs e idiomas, [o Dynamic Packaging](dynamic-packaging-overview.md) agora suporta várias faixas de áudio para a saída HLS (versão 4 ou superior).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Par regional da Coreia está aberto para serviços de mídia 

Os Serviços de Mídia estão agora disponíveis nas regiões sul da Coreia central e da Coreia. 

Para obter mais informações, consulte [Nuvens e regiões nas quais existe o Media Services v3](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Melhorias de desempenho

Atualizações adicionadas que incluem melhorias de desempenho dos Serviços de Mídia.

* O tamanho máximo do arquivo suportado para processamento foi atualizado. Veja, [Cotas e limitações.](limits-quotas-constraints.md)
* [A codificação acelera melhorias](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>Abril de 2019

### <a name="new-presets"></a>Novas predefinições

* [O FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) foi adicionado às predefinições do analisador incorporado.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) foi adicionado às predefinições do codificador incorporado. Para obter mais informações, consulte [codificação com reconhecimento de conteúdo](content-aware-encoding.md). 

## <a name="march-2019"></a>Março de 2019

A Dynamic Packaging agora suporta Dolby Atmos. Para obter mais informações, consulte [codecs de áudio suportados por embalagens dinâmicas](dynamic-packaging-overview.md#audio-codecs).

Agora você pode especificar uma lista de filtros de ativos ou contas, que se aplicariam ao seu Localizador de Streaming. Para obter mais informações, consulte [Associar filtros com localizador de streaming](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Fevereiro de 2019

O Media Services v3 agora é suportado nas nuvens nacionais do Azure. Nem todos os recursos estão disponíveis em todas as nuvens. Para obter mais detalhes, confira [Nuvens e regiões em que os Serviços de Mídia do Azure v3 existem](azure-clouds-regions.md).

O evento [Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) foi adicionado aos esquemas da Grade de Eventos do Azure para Serviços de Mídia.

## <a name="january-2019"></a>Janeiro de 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Arquivos do Media Encoder Standard e MPI 

Ao codificar usando o Media Encoder Standard para produzir arquivos MP4, um novo arquivo .mpi será gerado e adicionado ao Ativo de saída. Esse arquivo MPI destina-se a melhorar o desempenho dos cenários de empacotamento e [streaming dinâmicos](dynamic-packaging-overview.md).

Você não deve modificar nem remover o arquivo MPI, nem usar qualquer dependência em seu serviço na existência (ou não) desse arquivo.

## <a name="december-2018"></a>Dezembro de 2018

As atualizações da versão disponível ao público geral da API V3 incluem:
       
* As propriedades **PresentationTimeRange** não são mais “obrigatórias” para **Filtros de Ativo** e **Filtros de Conta**. 
* As opções de consulta $top e $skip para **Trabalhos** e **Transformações** foram removidas e $orderby foi adicionado. Como parte da adição da nova funcionalidade de ordenação, foi descoberto que as opções $top e $skip acidentalmente tinham sido expostas anteriormente, embora não tenham sido implementadas.
* A extensibilidade da enumeração foi reabilitada. Esse recurso estava habilitado nas versões prévias do SDK e foi acidentalmente desabilitado na versão disponível ao público geral.
* Duas políticas predefinidas de transmissão foram renomeadas. **SecureStreaming** agora é **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** agora é **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>Novembro de 2018

O módulo de CLI 2.0 agora está disponível para [serviços de mídia do Azure v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50.

### <a name="new-commands"></a>Novos comandos

- [conta do ams AZ](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [filtro de conta do ams AZ](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [ativo do ams AZ](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [ativos de ams AZ-filtro](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [AZ ams chave de política de conteúdo](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [trabalho de ams AZ](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [AZ ams-evento ao vivo](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [ams AZ saída ao vivo](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [AZ ams streaming-ponto de extremidade](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [AZ ams-localizador de streaming](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) – permite que você gerencie Unidades Reservadas para Mídia. Para obter mais informações, confira [Dimensionar Unidades Reservadas para Mídia](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Novos recursos e alterações de quebra

#### <a name="asset-commands"></a>Comandos de ativos

- ```--storage-account``` e ```--container``` argumentos adicionados.
- Valores padrão para o tempo de expiração (Now + 23h) e permissões (Read) no comando ```az ams asset get-sas-url``` adicionado.

#### <a name="job-commands"></a>Comandos de trabalho

- ```--correlation-data``` e ```--label``` argumentos adicionados
- ```--output-asset-names``` foi renomeado para ```--output-assets```. Agora, ele aceita uma lista separada por espaços dos ativos no formato 'assetName=label'. Um ativo sem rótulo pode ser enviado assim: 'assetName ='.

#### <a name="streaming-locator-commands"></a>Comandos do Localizador de Fluxo

- O comando base ```az ams streaming locator``` foi substituído por ```az ams streaming-locator```.
- ```--streaming-locator-id``` e ```--alternative-media-id support``` argumentos adicionados.
- ```--content-keys argument``` argumento atualizado.
- ```--content-policy-name``` foi renomeado para ```--content-key-policy-name```.

#### <a name="streaming-policy-commands"></a>Comandos de Política de Fluxo

- O comando base ```az ams streaming policy``` foi substituído por ```az ams streaming-policy```.
- Suporte para parâmetros de criptografia em ```az ams streaming-policy create``` adicionado.

#### <a name="transform-commands"></a>Transformar comandos

- ```--preset-names``` argumento substituído por ```--preset```. Agora, você só pode definir uma saída/predefinição de cada vez (para adicionar mais, é preciso executar ```az ams transform output add```). Além disso, você pode definir o StandardEncoderPreset personalizado passando o caminho para seu JSON personalizado.
- ```az ams transform output remove``` pode ser executado passando o índice de saída para remover.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` argumentos adicionados no ```az ams transform create``` e ```az ams transform output add``` comandos.

## <a name="october-2018---ga"></a>Outubro de 2018 - GA

Esta seção descreve as atualizações de outubro do AMS (Serviços de Mídia do Azure).

### <a name="rest-v3-ga-release"></a>Versão GA v3 de REST

A [versão GA v3 de REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) inclui mais APIs para Live, filtros de manifesto de nível de Conta/Ativo e suporte a DRM.

#### <a name="azure-resource-management"></a>Gerenciamento de Recursos do Azure 

O suporte para o Gerenciamento de Recursos do Azure permite o gerenciamento unificado e a API de operações (agora, tudo em um só lugar).

A partir desta versão, é possível usar modelos do Resource Manager para criar Eventos ao Vivo.

#### <a name="improvement-of-asset-operations"></a>Melhoria das operações de Ativos 

As seguintes melhorias foram introduzidas:

- Ingestão de URLs HTTP(s) ou URLs de SAS do Armazenamento de Blobs do Azure.
- Especifique seus próprios nomes de contêiner para os Ativos. 
- Suporte de saída mais fácil para criar fluxos de trabalho personalizados com o Azure Functions.

#### <a name="new-transform-object"></a>Novo objeto de Transformação

O novo objeto de **Transformação** simplifica o modelo de Codificação. O novo objeto facilita a criação e o compartilhamento de modelos e predefinições de codificação do Resource Manager. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Autenticação do Azure Active Directory e RBAC

A Autenticação do Azure AD e o RBAC (Controle de Acesso Baseado em Função) permitem Transformações seguras, LiveEvents, Políticas de Chave de Conteúdo, Ativos por Função ou Usuários no Azure AD.

#### <a name="client-sdks"></a>SDKs do cliente  

Linguagens com suporte nos Serviços de Mídia v3: .NET Core, Java, Node.js, Ruby, Datilografado, Python, Go.

#### <a name="live-encoding-updates"></a>Atualizações de codificação ao vivo

As seguintes atualizações de codificação ao vivo são apresentadas:

- Novo modo de baixa latência para ao vivo (10 segundos de ponta a ponta).
- Suporte aprimorado do RTMP (maior estabilidade e mais suporte de codificador de código-fonte).
- Ingestão segura de RTMPS.

    Ao criar um evento ao vivo, você receberá quatro URLs de ingestão. As 4 URLs de ingestão são quase idênticas, têm o mesmo token de streaming (AppId) e apenas a parte do número da porta é diferente. Duas das URLs são primárias e de backup para RTMPS. 
- Suporte de transcodificação 24 horas por dia. 
- Suporte aprimorado de sinalização de anúncio no RTMP via SCTE35.

#### <a name="improved-event-grid-support"></a>Suporte aprimorado a Grade de Eventos

É possível ver os seguintes aprimoramentos de suporte à Grade de Eventos:

- Integração à Grade de Eventos do Azure para facilitar o desenvolvimento com os Aplicativos Lógicos e o Azure Functions. 
- Inscreva-se para eventos sobre Codificação, Canais ao vivo, e muito mais.

### <a name="cmaf-support"></a>Suporte CMAF

Suporte de criptografia CMAF e 'cbcs' para players Apple HLS (iOS 11+) e MPEG-DASH que dão suporte a CMAF.

### <a name="video-indexer"></a>Video Indexer

A versão de GA do Video Indexer foi anunciada em agosto. Para obter novas informações sobre recursos atualmente com suporte, consulte [O que é Video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Planos de alterações

#### <a name="azure-cli-20"></a>CLI do Azure 2.0
 
O módulo da CLI 2.0 do Azure que inclui operações em todos os recursos (incluindo Live, Políticas de Chave de Conteúdo, Filtros de Conta/Ativo, Políticas de Streaming) estará disponível em breve. 

### <a name="known-issues"></a>Problemas conhecidos

Somente os clientes que usaram a API de versão prévia para material ativo ou AccountFilters são afetados pelo problema a seguir.

Se você criou Ativos ou Filtros de Conta entre 09/28 e 10/12 com CLI ou APIs dos Serviços de Mídia v3, será necessário remover todos os Ativos e AccountFilters e recriá-los devido a um conflito de versões. 

## <a name="may-2018---preview"></a>Maio de 2018 - Versão prévia

### <a name="net-sdk"></a>SDK .NET

As seguintes características estão presentes no .NET SDK:

* **Transformações** e **Trabalhos** para codificar ou analisar o conteúdo de mídia. Para obter exemplos, consulte [Transmitir arquivos por stream](stream-files-tutorial-with-api.md) e [Analisar](analyze-videos-tutorial-with-api.md).
* **Localizadores de streaming** para publicar e transmitir conteúdo aos dispositivos do usuário final
* **Políticas de streaming** e **Políticas de chave de conteúdo** para configurar a distribuição de chaves e o DRM (gerenciamento de direitos digitais) durante a entrega de conteúdo.
* **Eventos ao vivo** e **Saídas ao vivo** para configurar a ingestão e o arquivamento de conteúdo de transmissão ao vivo.
* **Ativos** para armazenar e publicar o conteúdo de mídia no Armazenamento do Azure. 
* **Pontos de extremidade de streaming** para configurar e dimensionar o empacotamento dinâmico, a criptografia e o streaming de conteúdo de mídia ao vivo e sob demanda.

### <a name="known-issues"></a>Problemas conhecidos

* Ao enviar um trabalho, você pode especificar para ingerir o vídeo de origem usando URLs HTTPS, URLs SAS ou caminhos para arquivos localizados no Armazenamento de Blobs do Azure. Atualmente, o AMS v3 não oferece suporte à codificação de transferência em partes sobre URLs HTTPS.

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral](media-services-overview.md)
- [Notas de versão v2 do Media Services v2](../previous/media-services-release-notes.md)
