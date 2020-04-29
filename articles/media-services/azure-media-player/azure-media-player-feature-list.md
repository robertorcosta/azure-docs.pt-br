---
title: Lista de recursos do Player de Mídia do Azure
description: Uma referência de recurso para Player de Mídia do Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e5595620a2f888b06ad5b35d2e8a008f23861463
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81727223"
---
# <a name="feature-list"></a>Lista de recursos #
Aqui está a lista de recursos testados e recursos sem suporte:

|                                         | TESTADO | PARCIALMENTE TESTADO | Não testado | SEM suporte | OBSERVAÇÕES                                                                                                                |
|:----------------------------------------|--------|------------------|----------|-------------|:---------------------------------------------------------------------------------------------------------------------|
| Reprodução                                |        |                  |          |             |                                                                                                                      |
| Reprodução básica sob demanda                | X      |                  |          |             | Dá suporte apenas a fluxos de serviços de mídia do Azure                                                                      |
| Reprodução dinâmica básica                     | X      |                  |          |             | Dá suporte apenas a fluxos de serviços de mídia do Azure                                                                      |
| AES                                     | X      |                  |          |             | Dá suporte ao serviço de entrega de chaves dos serviços de mídia do Azure                                                                   |
| Vários DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Dá suporte ao serviço de entrega de chaves dos serviços de mídia do Azure                                                                   |
| Widevine                                |        | X                |          |             | Dá suporte às caixas Widevine PSSH descritas no manifesto                                                                    |
| FairPlay                                |        | X                |          |             | Dá suporte ao serviço de entrega de chaves dos serviços de mídia do Azure                                                                   |
| Tecnologias                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Fallback de flash (piscar)                | X      |                  |          |             | Nem todos os recursos estão disponíveis nesta tecnologia.                                                                         |
| Silverlight de fallback do Silverlight      | X      |                  |          |             | Nem todos os recursos estão disponíveis nesta tecnologia.                                                                         |
| HLS de passagem nativa (HTML5)         |        | X                |          |             | Nem todos os recursos estão disponíveis nesta tecnologia devido a restrições de plataforma.                                            |
| Recursos                                |        |                  |          |             |                                                                                                                      |
| Suporte a API                             | X      |                  |          |             | Consulte a lista de problemas conhecidos                                                                                                |
| Interface do usuário básica                                | X      |                  |          |                                                                                                                                    |
| Inicialização por meio de JavaScript       | X      |                  |          |             |                                                                                                                      |
| Inicialização por meio da marca de vídeo        |        | X                |          |             |                                                                                                                      |
| Endereçamento de segmento-baseado em tempo         | X      |                  |          |             |                                                                                                                      |
| Endereçamento de segmento-baseado em índice        |        |                  |          | X           |                                                                                                                      |
| Endereçamento de segmento-baseado em byte         |        |                  |          | X           |                                                                                                                      |
| Regravador de URL dos serviços de mídia do Azure       |        | X                |          |             |                                                                                                                      |
| Acessibilidade – legendas e legendas  |        | X                |          |             |  WebVTT com suporte para sob demanda, cabo CEA 708 parcialmente testado                                                       |
| Acessibilidade-teclas de acesso                 | X      |                  |          |             |                                                                                                                      |
| Acessibilidade-Alto Contraste           |        | X                |          |             |                                                                                                                      |
| Acessibilidade – foco na guia               |        | X                |          |             |                                                                                                                      |
| Mensagens de erro                         |        | X                |          |             | As mensagens de erro são inconsistentes entre os técnicos                                                                         |
| Gatilho de evento                        | X      |                  |          |             |                                                                                                                      |
| Diagnóstico                             |        | X                |          |             | As informações de diagnóstico estão disponíveis apenas no AzureHtml5JS Tech e parcialmente disponíveis no Silverlightss Tech. |
| Ordem tecnológica personalizável                 |        | X                |          |             |                                                                                                                      |
| Heurística-básica                      | X      |                  |          |             |                                                                                                                      |
| Heurística-personalização              |        |                  | X        |             | A personalização só está disponível com o AzureHtml5JS Tech.                                                          |
| Descontinuidades                         | X      |                  |          |             |                                                                                                                      |
| Selecionar taxa de bits                          | X      |                  |          |             | Essa API só está disponível nos Techs AzureHtml5JS e Flashss.                                                    |
| Fluxo de vários áudios                      |        | X                |          |             | A opção de áudio programática tem suporte em Techs AzureHtml5JS e Flashss e está disponível por meio da seleção de interface do usuário no AzureHtml5JS, no Flashss e no HTML5 nativo (no Safari).  A maioria das plataformas requer os mesmos dados privados do codec para alternar fluxos de áudio (mesmo codec, canal, taxa de amostragem, etc.). |
| Localização da interface do usuário                         |        | X                |          |             |                                                                                                                      |
| Reprodução de várias instâncias                 |        |                  |          | X           | Esse cenário pode funcionar para alguns Techs, mas atualmente não tem suporte e não é testado. Você também pode fazer com que isso funcione usando iframes |
| Suporte ao ads                             |        | x                |          |             | AMP dá suporte à inserção de anúncios lineares pré-intermediários e post-roll de servidores de anúncios de grande conformidade para VOD no AzureHtml5JS Tech |
| Análise                               |        | X                |          |             | O AMP fornece a capacidade de ouvir eventos de diagnóstico e análise para enviar para um back-end de análise de sua escolha.  Todos os eventos e propriedades não estão disponíveis em Techs devido a limitações da plataforma.                                                                            |
| Aparências personalizadas                            |        |                  | X        |             | Ative a configuração de controles como false no AMP e usando seu próprio HTML e CSS.           |
| Depuração da barra de busca                      |        |                  |          | X           |                                                                                                                      |
| Rodada-brincar                              |        |                  |          | X           |                                                                                                                      |
| Somente áudio                              |        |                  |          | X           | Pode funcionar em algumas tecnologias de streaming adaptável, mas não tem suporte no momento e não funciona no AzureHtml5JS. A reprodução de MP3 progressivo pode funcionar com o HTML5 Tech se a plataforma oferecer suporte a ele.                                                                                                        |
| Somente vídeo                              |        |                  |          | X           | Pode funcionar em algumas tecnologias de streaming adaptável, mas não tem suporte no momento e não funciona no AzureHtml5JS.      |
| Apresentação de vários períodos               |        |                  |          | X                                                                                                                                  |
| Vários ângulos de câmera                  |        |                  |          | X           |                                                                                                                      |
| Velocidade de reprodução                          |        | X                |          |             | A velocidade de reprodução tem suporte na maioria dos cenários, exceto no caso do Mobile devido a um bug parcial no Chrome                 |

## <a name="next-steps"></a>Próximas etapas ##
- [Guia de início rápido Player de Mídia do Azure](azure-media-player-quickstart.md)