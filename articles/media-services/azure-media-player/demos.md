---
title: Demonstrações de Player de Mídia do Azure
description: Esta página contém uma lista de links para demonstrações do Player de Mídia do Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: article
ms.date: 04/24/2020
ms.openlocfilehash: 584748b23f526e6f03b543b8298927e3f202f743
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82139297"
---
# <a name="azure-media-player-demos"></a>Demonstrações de Player de Mídia do Azure

Veja a seguir uma lista de links para demonstrações do Player de Mídia do Azure. Você pode baixar todos os [exemplos de player de mídia do Azure](https://github.com/Azure-Samples/azure-media-player-samples) do github.

## <a name="demo-listing"></a>Listagem de demonstração

| Nome da amostra | Programático via JavaScript | Estático via elemento de vídeo HTML5 | Descrição |
| ------------|----------------------------|-------------------------------------|--------------|
| Basic |
| Definir origem | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_setsource.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_setsource.html) |Reproduzir conteúdo desprotegido.|
| Recursos |
| Inserção de anúncios do VOD – vasto | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_vast_ads_vod.html) | N/D | Insira anúncios VASTOs e posteriores ao lance em um ativo VOD. |
| Velocidade de reprodução | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_playback_speed.html)| N/D | Permite que os visualizadores controlem em que velocidade eles estão assistindo seu vídeo. |
| Capa de AMP flush | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_flush_skin.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_flush_skin.html) | Habilita o novo AMP Skin. **Observação:** O AMP flush só tem suporte no AMP versões 2.1.0 + |
| Legendas e legendas | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_webvtt.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_webvtt.html) | Reprodução com legendas WebVTT.
| Legendas de CEA 708 ao vivo | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_live_captions.html) | N/D | Reprodução com legendas de entrada CEA 708 em tempo real com as legendas alinhadas à esquerda. |
| Streaming com fallback progressivo | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveFallback.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveFallback.html) | Configuração básica de reprodução adaptável com fallback para progressivo se não houver suporte para streaming na plataforma. |
| MP4 de vídeo progressivo | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveVideo.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveVideo.html) | Reprodução de MP4 de áudio progressivo. |
| MP3 de áudio progressivo | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveAudio.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveAudio.html) | Reprodução de MP3 de áudio progressivo. |
| DD + | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_dolbyDigitalPlus.html) | N/D | Reprodução de conteúdo com DD + Audio. |
| Opções |
| Perfil heurístico | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_heuristicsProfile.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_heuristicsProfile.html) | Alterando o perfil de heurística |
| Localização | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_localization.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_localization.html) |
Definindo a localização |
| Menu faixas de áudio | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiAudio.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_multiAudio.html) |
Opções para mostrar como exibir o menu faixas de áudio na aparência padrão. |
| Teclas | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_hotKeys.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_hotKeys.html) | Este exemplo mostra como configurar quais teclas de entrada estão habilitadas no Player |
| Eventos, registro em log e diagnóstico |
| Registrar eventos | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_registerEvents.html) | N/D | Reprodução com ouvintes de evento. |
| Registro em log | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_logging.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_logging.html) | Ativar o log detalhado para o console. |
| Diagnóstico | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_diagnostics.html) | N/D | Obtendo dados de diagnóstico. Este exemplo funciona apenas em alguns techs. |
| AES |
| AES sem token | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_notoken.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_notoken.html) | Reprodução de conteúdo AES sem token. |
| Token AES | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token.html) | Reprodução de conteúdo AES com token. |
| Simulação de proxy AES HLS | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token_withHLSProxy.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token_withHLSProxy.html) | Reprodução de conteúdo AES com token, mostrando um proxy para HLS para que o token possa ser usado com dispositivos iOS. |
| Flash de força de token AES | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token_forceFlash.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token_forceFlash.html) | Reprodução de conteúdo AES com token, forçando a tecnologia flashss. |
| DRM |
| Multi-DRM com PlayReady, Widevine e FairPlay | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_multiDRM_PlayReadyWidevineFairPlay_notoken.html) | Reprodução de conteúdo DRM sem token, com os cabeçalhos PlayReady, Widevine e FairPlay. |
| PlayReady sem token | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_notoken.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_notoken.html) | Reprodução de conteúdo do PlayReady sem token. |
| PlayReady sem token Force Silverlight | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_notoken_forceSilverlight.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_notoken_forceSilverlight.html) | Reprodução de conteúdo do PlayReady sem token, forçando o silverlightss Tech. |
| Token PlayReady | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_token.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_token.html) | Reprodução de conteúdo do PlayReady com token. |
| O Silverlight de força de token do PlayReady | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_token_forceSilverlight.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_token_forceSilverlight.html) | Reprodução de conteúdo PlayReady com token, forçando o silverlightss Tech. |
| Protocolo e tecnologia |
| Alterar techOrder | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_techOrder.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_techOrder.html) | Alterando a ordem técnica |
| Forçar MPEG-DASH somente em UrlRewriter | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_forceDash.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_forceDash.html) | Reprodução de conteúdo desprotegido somente usando o protocolo DASH. |
| Excluir MPEG-DASH em UrlRewriter | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_forceNoDash.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_forceNoDash.html) | Reprodução de conteúdo desprotegido somente usando os protocolos Smooth e HLS. |
| Política de entrega múltipla | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_multipleDeliveryPolicy.html) | [Auto-estática](https://amp.azure.net/libs/amp/latest/samples/videotag_multipleDeliveryPolicy.html) | Configurando a origem com conteúdo que tem várias políticas de entrega dos serviços de mídia do Azure |
| Seleção programaticamente |
| Selecionar faixa de texto | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectTextTrack.html) | N/D | Selecionando uma faixa WebVTT na lista de acompanhamento. |
| Selecionar taxa de bits | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectBitrate.html) | N/D | Selecionando uma taxa de bits na lista de taxas de bits. Este exemplo funciona apenas em alguns techs. |
| Selecionar fluxo de áudio | [Dinâmico](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectAudioStream.html) | N/D | Selecionando um fluxo de áudio na lista de fluxos de áudio disponíveis. Este exemplo funciona apenas em alguns techs. |

## <a name="next-steps"></a>Próximas etapas

<!---Some context for the following links goes here--->
- [Guia de início rápido Player de Mídia do Azure](azure-media-player-quickstart.md)