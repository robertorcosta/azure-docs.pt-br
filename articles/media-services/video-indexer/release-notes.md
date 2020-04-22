---
title: Notas de lançamento do Azure Media Services Video Indexer | Microsoft Docs
description: Para ficar atualizado com os desenvolvimentos mais recentes, este artigo fornece as últimas atualizações sobre o Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: 5aa7abf7aafc14e71af5618cec892ef9f843d88a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733061"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Notas de lançamento do Azure Media Services Video Indexer

>Seja notificado sobre quando revisitar esta página para atualizações copiando e colando esta URL: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` no leitor de feed RSS.

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

* As versões mais recentes
* Problemas conhecidos
* Correções de bug
* Funcionalidades preteridas

## <a name="april-2020"></a>Abril de 2020

### <a name="new-widget-parameters-capabilities"></a>Novos recursos de parâmetros de widget

O widget **Insights** inclui `language` novos `control`parâmetros: e .

O widget **Player** `locale` tem um novo parâmetro. Ambos `locale` `language` os parâmetros controlam a linguagem do jogador.

Para obter mais informações, consulte a seção [de tipos de widget.](video-indexer-embed-widgets.md#widget-types) 

## <a name="new-player-skin"></a>Nova pele do jogador

Uma nova skin player lançada com design atualizado.

## <a name="january-2020"></a>Janeiro de 2020
 
### <a name="custom-language-support-for-additional-languages"></a>Suporte personalizado ao idioma para idiomas adicionais

O Video Indexer agora suporta `ar-SY` `en-UK`modelos `en-AU` de linguagem personalizados para , e (somente API).
 
### <a name="delete-account-timeframe-action-update"></a>Excluir atualização de ação de prazo da conta

A ação de exclusão da conta agora exclui a conta dentro de 90 dias em vez de 48 horas.
 
### <a name="new-video-indexer-github-repository"></a>Novo repositório gitHub do indexador de vídeo

Um novo Índice de Vídeo GitHub com diferentes projetos, recebendo guias de início e amostras de código está agora disponível:https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Atualização de Swagger

O Video Indexer unificou **autenticações** e **operações** em uma única [especificação openapi (swagger) do indexador](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson)de vídeo. Os desenvolvedores podem encontrar as APIs no [Portal de Desenvolvedores do Indexador de Vídeo](https://api-portal.videoindexer.ai/).

## <a name="december-2019"></a>Dezembro de 2019

### <a name="update-transcript-with-the-new-api"></a>Atualizar transcrição com a nova API

Atualize uma seção específica na transcrição usando a API [Update-Video-Index.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update)

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Corrigir a configuração da conta no portal do Indexador de vídeo

Agora você pode atualizar a configuração de conexão do Media Services para se auto-ajudar com problemas como: 

* recurso azure media services incorreto
* mudanças de senha
* Os recursos dos Serviços de Mídia foram movidos entre assinaturas  

Para corrigir a configuração da conta, no portal Dotador de vídeo navegue até configurações > conta (como proprietário).

### <a name="configure-the-custom-vision-account"></a>Configure a conta de visão personalizada

Configure a conta de visão personalizada em contas pagas usando o portal do Indexador de Vídeo (anteriormente, isso só era suportado pela API). Para isso, entre no portal do Indexador de Vídeo, escolha Personalização de modelos > personagens animados > Configurar. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Cenas, fotos e quadros - agora em um painel de insights

Cenas, fotos e quadros-chave agora são fundidos em uma visão para facilitar o consumo e a navegação. Quando você seleciona a cena desejada, você pode ver quais fotos e quadros-chave ele consiste. 

### <a name="notification-about-a-long-video-name"></a>Notificação sobre um nome de vídeo longo

Quando um nome de vídeo tem mais de 80 caracteres, o Video Indexer mostra um erro descritivo no upload.

### <a name="streaming-endpoint-is-disabled-notification"></a>O ponto final do streaming é a notificação desativada

Quando o ponto final de streaming estiver desativado, o Video Indexer mostrará um erro descritivo na página do jogador.

### <a name="error-handling-improvement"></a>Melhoria no manuseio de erros

O código de status 409 agora será devolvido das APIs de [reindexação](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) de vídeo e atualização de [vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) no caso de um vídeo ser ativamente indexado, para evitar a substituição das alterações atuais do reíndice por acidente.

## <a name="november-2019"></a>Novembro de 2019
 
* Modelos de idioma personalizado coreano suportam

    O indexador de vídeo agora`ko-KR`suporta modelos de idioma personalizados em coreano ( ) tanto na API quanto no portal. 
* Novos idiomas suportados para o discurso-texto (STT)

    As APIs do Indexador de Vídeo agora suportam STT em levantino árabe (ar-SY), dialeto inglês do Reino Unido (en-GB) e dialeto australiano inglês (en-AU).
    
    Para upload de vídeo, substituímos zh-HANS para zh-CN, ambos são suportados, mas zh-CN é recomendado e mais preciso.
    
## <a name="october-2019"></a>Outubro de 2019
 
* Procure por personagens animados na galeria

    Ao indexar caracteres animados, agora você pode procurá-los na galera de vídeo da conta. Para obter mais informações, consulte [o reconhecimento de personagens animados](animated-characters-recognition.md).

## <a name="september-2019"></a>Setembro de 2019
 
Múltiplos avanços anunciados no IBC 2019:
 
* Reconhecimento de personagens animados (visualização pública)

    Capacidade de detectar anúncios em grupo reconhecer caracteres em conteúdo animado, através da integração com visão personalizada. Para obter mais informações, consulte [Detecção de caracteres animados](animated-characters-recognition.md).
* Identificação em várias línguas (visualização pública)

    Detecte segmentos em vários idiomas na faixa de áudio e crie uma transcrição multilíndrico com base neles. Suporte inicial: Inglês, Espanhol, Alemão e Francês. Para obter mais informações, consulte [Identificar e transcrever automaticamente conteúdo em vários idiomas](multi-language-identification-transcription.md).
* Extração de entidade nomeada para Pessoas e Localização

    Extrai marcas, locais e pessoas de texto sonítero e visual através do processamento de linguagem natural (PNL).
* Classificação do tipo de tiro editorial

    Marcação de tiros com tipos editoriais como close-up, tiro médio, dois tiros, interior, exterior etc. Para obter mais informações, consulte [Detecção do tipo de tiro editorial](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Aprimoramento da inferência de tópicos - agora cobrindo o nível 2
    
    O modelo de inferência de tópicos agora suporta uma granularidade mais profunda da taxonomia do IPTC. Leia os detalhes completos da [nova inovação alimentada por IA da Azure Media Services.](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)

## <a name="august-2019"></a>Agosto de 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>Indexador de vídeo implantado no Sul do Reino Unido

Agora você pode criar uma conta paga do Video Indexer na região sul do Reino Unido.

### <a name="new-editorial-shot-type-insights-available"></a>Novos insights do Tipo de Tiro Editorial disponíveis

Novas tags adicionadas às fotos de vídeo fornecem "tipos de tiro" editoriais para identificá-los com frases editoriais comuns usadas no fluxo de trabalho de criação de conteúdo, tais como: close-up extremo, close-up, wide, medium, two shot, outdoor, indoor, left face e right face (Disponível no JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Extração de novas pessoas e locais disponíveis

O Video Indexer identifica locais e pessoas nomeadas através do processamento de linguagem natural (NLP) a partir do OCR e transcrição do vídeo. O Video Indexer usa algoritmo de aprendizagem de máquina para reconhecer quando locais específicos (por exemplo, a Torre Eiffel) ou pessoas (por exemplo, John Doe) estão sendo chamados em um vídeo.

### <a name="keyframes-extraction-in-native-resolution"></a>Extração de quadros-chave em resolução nativa

Os quadros-chave extraídos pelo Video Indexer estão disponíveis na resolução original do vídeo.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA para treinamento de modelos de rosto personalizados a partir de imagens

Rostos de treinamento a partir de imagens movidas do modo Depré-visualização para GA (disponível via API e no portal).

> [!NOTE]
> Não há impacto de preços relacionado à transição "Preview to GA".

### <a name="hide-gallery-toggle-option"></a>Opção de alternar galeria de ocultação

O usuário pode optar por ocultar a guia galeria do portal (semelhante a ocultar a guia amostras).
 
### <a name="maximum-url-size-increased"></a>O tamanho máximo da URL aumentou

Suporte para seqüência de consulta de URL de 4096 (em vez de 2048) na indexação de um vídeo.
 
### <a name="support-for-multi-lingual-projects"></a>Suporte para projetos multilíndricos

Os projetos agora podem ser criados com base em vídeos indexados em diferentes idiomas (somente API).

## <a name="july-2019"></a>Julho de 2019

### <a name="editor-as-a-widget"></a>Editor como um widget

O editor de IA do Video Indexer agora está disponível como um widget a ser incorporado em aplicativos de clientes.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Atualizar o modelo de idioma personalizado do arquivo de legenda fechada do portal

Os clientes podem fornecer formatos de arquivo VTT, SRT e TTML como entrada para modelos de idioma na página de personalização do portal.

## <a name="june-2019"></a>Junho de 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Indexador de vídeo implantado no Japão Leste

Agora você pode criar uma conta paga do Video Indexer na região leste do Japão.

### <a name="create-and-repair-account-api-preview"></a>Criar e reparar a API da conta (Preview)

Adicionada uma nova API que permite [atualizar o ponto final ou chave de conexão do Azure Media Service](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Melhore o manuseio de erros no upload 

Uma mensagem descritiva é devolvida em caso de má configuração da conta subjacente do Azure Media Services.

### <a name="player-timeline-keyframes-preview"></a>Visualização de quadros-chave da linha do tempo do jogador 

Agora você pode ver uma visualização de imagem para cada vez na linha do tempo do jogador.

### <a name="editor-semi-select"></a>Editor semi-selecionado

Agora você pode ver uma prévia de todos os insights selecionados como resultado da escolha de um prazo específico de insights no editor.

## <a name="may-2019"></a>Maio de 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Atualizar o modelo de idioma personalizado do arquivo de legenda fechada

[Crie modelo de linguagem personalizado](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) e [atualize modelos de linguagem personalizados](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) As APIs agora suportam formatos de arquivo VTT, SRT e TTML como entrada para modelos de idioma.

Ao chamar a [API de transcrição de vídeo de atualização,](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)a transcrição é adicionada automaticamente. O modelo de treinamento associado ao vídeo também é atualizado automaticamente. Para obter informações sobre como personalizar e treinar seus modelos de idioma, consulte [Personalizar um modelo de idioma com indexador de vídeo](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Novos formatos de transcrição de download – TXT e CSV

Além do formato de legendamento fechado já suportado (SRT, VTT e TTML), o Video Indexer agora suporta baixar a transcrição nos formatos TXT e CSV.

## <a name="next-steps"></a>Próximas etapas

[Visão geral](video-indexer-overview.md)
