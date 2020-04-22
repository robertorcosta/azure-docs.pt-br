---
title: Lista de recursos do Azure Media Player
description: Uma referência de recurso para o Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e5595620a2f888b06ad5b35d2e8a008f23861463
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727223"
---
# <a name="feature-list"></a>Lista de recursos #
Aqui está a lista de recursos testados e recursos não suportados:

|                                         | Testado | PARCIALMENTE TESTADO | Testado | Unsupported | OBSERVAÇÕES                                                                                                                |
|:----------------------------------------|--------|------------------|----------|-------------|:---------------------------------------------------------------------------------------------------------------------|
| Reprodução                                |        |                  |          |             |                                                                                                                      |
| Reprodução básica sob demanda                | X      |                  |          |             | Suporta fluxos apenas do Azure Media Services                                                                      |
| Reprodução básica ao vivo                     | X      |                  |          |             | Suporta fluxos apenas do Azure Media Services                                                                      |
| AES                                     | X      |                  |          |             | Suporta o serviço de entrega chave dos serviços de mídia do Azure                                                                   |
| Multi-DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Suporta o serviço de entrega chave dos serviços de mídia do Azure                                                                   |
| Widevine                                |        | X                |          |             | Suporta caixas PSSH Widevine descritas em manifesto                                                                    |
| FairPlay                                |        | X                |          |             | Suporta o serviço de entrega chave dos serviços de mídia do Azure                                                                   |
| Técnicos                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Flash Fallback (FlashSS)                | X      |                  |          |             | Nem todos os recursos estão disponíveis nesta tecnologia.                                                                         |
| Silverlight Fallback SilverlightSS      | X      |                  |          |             | Nem todos os recursos estão disponíveis nesta tecnologia.                                                                         |
| Passe do HLS nativo (Html5)         |        | X                |          |             | Nem todos os recursos estão disponíveis nesta tecnologia devido às restrições da plataforma.                                            |
| Recursos                                |        |                  |          |             |                                                                                                                      |
| Suporte a API                             | X      |                  |          |             | Ver lista de problemas conhecidos                                                                                                |
| UI básica                                | X      |                  |          |                                                                                                                                    |
| Inicialização através de JavaScript       | X      |                  |          |             |                                                                                                                      |
| Inicialização através de tag de vídeo        |        | X                |          |             |                                                                                                                      |
| Endereçamento de segmento - Baseado em Tempo         | X      |                  |          |             |                                                                                                                      |
| Endereçamento de segmento - Baseado em Índice        |        |                  |          | X           |                                                                                                                      |
| Endereçamento de segmento - Byte Based         |        |                  |          | X           |                                                                                                                      |
| Reescritor de URL do Azure Media Services       |        | X                |          |             |                                                                                                                      |
| Acessibilidade - Legendas e Legendas  |        | X                |          |             |  WebVTT suportado por demanda, CEA 708 ao vivo parcialmente testado                                                       |
| Acessibilidade - Hotkeys                 | X      |                  |          |             |                                                                                                                      |
| Acessibilidade - Alto Contraste           |        | X                |          |             |                                                                                                                      |
| Acessibilidade - Foco em Guias               |        | X                |          |             |                                                                                                                      |
| Mensagens de erro                         |        | X                |          |             | Mensagens de erro são inconsistentes entre os técnicos                                                                         |
| Acionamento de Eventos                        | X      |                  |          |             |                                                                                                                      |
| Diagnósticos                             |        | X                |          |             | As informações de diagnóstico estão disponíveis apenas na tecnologia AzureHtml5JS e estão parcialmente disponíveis na tecnologia SilverlightSS. |
| Ordem técnica personalizável                 |        | X                |          |             |                                                                                                                      |
| Heurística - Básico                      | X      |                  |          |             |                                                                                                                      |
| Heurística - Personalização              |        |                  | X        |             | A personalização só está disponível com a tecnologia AzureHtml5JS.                                                          |
| Descontinuidades                         | X      |                  |          |             |                                                                                                                      |
| Selecione Bitrate                          | X      |                  |          |             | Esta API só está disponível nos técnicos AzureHtml5JS e FlashSS.                                                    |
| Fluxo multi-áudio                      |        | X                |          |             | O switch de áudio programático é suportado em tecnologias AzureHtml5JS e FlashSS, e está disponível através da seleção de IU no AzureHtml5JS, FlashSS e html5 nativo (no Safari).  A maioria das plataformas exige os mesmos dados privados de codec para alternar fluxos de áudio (mesmo codec, canal, taxa de amostragem, etc.). |
| Localização da UI                         |        | X                |          |             |                                                                                                                      |
| Reprodução em várias instâncias                 |        |                  |          | X           | Este cenário pode funcionar para alguns técnicos, mas atualmente não é suportado e não testado. Você também pode fazer com que isso funcione usando iframes |
| Suporte a anúncios                             |        | x                |          |             | O AMP suporta a inserção de anúncios lineares pré-médio e pós-roll de servidores de anúncios compatíveis com VAST para VOD na tecnologia AzureHtml5JS |
| Análise                               |        | X                |          |             | O AMP fornece a capacidade de ouvir análises e eventos de diagnóstico para enviar para um backend do Analytics de sua escolha.  Todos os eventos e propriedades não estão disponíveis entre os técnicos devido às limitações da plataforma.                                                                            |
| Skins personalizadas                            |        |                  | X        |             | Gire os controles de configuração para falsos no AMP e usando seu próprio HTML e CSS.           |
| Procurar limpeza de barras                      |        |                  |          | X           |                                                                                                                      |
| Trick-Play                              |        |                  |          | X           |                                                                                                                      |
| Somente áudio                              |        |                  |          | X           | Pode funcionar em alguns técnicos para O Streaming Adaptativo, mas atualmente não é suportado e não funciona no AzureHtml5JS. A reprodução progressiva do MP3 pode funcionar com a tecnologia HTML5 se a plataforma o suportar.                                                                                                        |
| Somente vídeo                              |        |                  |          | X           | Pode funcionar em alguns técnicos para O Streaming Adaptativo, mas atualmente não é suportado e não funciona no AzureHtml5JS.      |
| Apresentação de vários períodos               |        |                  |          | X                                                                                                                                  |
| Múltiplos ângulos de câmera                  |        |                  |          | X           |                                                                                                                      |
| Velocidade de reprodução                          |        | X                |          |             | A velocidade de reprodução é suportada na maioria dos cenários, exceto no caso do celular devido a um bug parcial no Chrome                 |

## <a name="next-steps"></a>Próximas etapas ##
- [Azure Media Player Quickstart](azure-media-player-quickstart.md)