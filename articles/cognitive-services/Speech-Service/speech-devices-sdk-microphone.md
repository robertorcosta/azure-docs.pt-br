---
title: Dispositivos de fala Recomendações do conjunto de microfones SDK
titleSuffix: Azure Cognitive Services
description: Dispositivos de fala Recomendações do conjunto de microfones SDK. Essas geometrias de matriz são recomendadas para uso com o Microsoft Audio Stack.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: a87bdd7a55036e8b70f0bc5816d2b587c1569202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77168132"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Dispositivos de fala Recomendações do conjunto de microfones SDK

Neste artigo, você aprende a projetar um conjunto de microfones para o Speech Devices SDK.

O Speech Devices SDK funciona melhor com um conjunto de microfones que foi projetado de acordo com as seguintes diretrizes, incluindo a geometria do microfone e a seleção de componentes. Também são orientadas as questões de integração e elétrica.

## <a name="microphone-geometry"></a>Geometria do microfone

As seguintes geometrias de matriz são recomendadas para uso com o Microsoft Audio Stack. A localização de fontes de som e a rejeição do ruído ambiente é melhorada com maior número de microfones com dependências de aplicativos específicos, cenários de usuários e o fator de forma do dispositivo.

|     | Matriz circular |     | Matriz Linear |     |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \#Microfones | 7 | 4 | 4 | 2 |
| Geometry | 6 Exterior, 1 Centro, Raio = 42,5 mm, Uniformemente Espaçado | 3 Exterior, 1 Centro, Raio = 42,5 mm, Uniformemente Espaçado | Comprimento = 120 mm, Espaçamento = 40 mm | Espaçamento = 40 mm |

Os canais de microfone devem ser ordenados de acordo com a numeração representada para cada matriz acima, aumentando a partir de 0. O Microsoft Audio Stack exigirá um fluxo de referência adicional de reprodução de áudio para realizar o cancelamento do eco.

## <a name="component-selection"></a>Seleção de componentes

Os componentes do microfone devem ser selecionados para reproduzir com precisão um sinal livre de ruído e distorção.

As propriedades recomendadas ao selecionar microfones são:

| Parâmetro | Recomendadas |
| --------- | ----------- |
| Snr | \>= 65 dB (sinal de 1 kHz 94 dBSPL, ruído ponderado a A) |
| Correspondência de amplitude | ± 1 dB @ 1 kHz |
| Correspondência de fase | ± 2° @ 1 kHz |
| Ponto de sobrecarga acústico (AOP) | \>= 120 dBSPL (THD = 10%) |
| Taxa de bits | Mínimo de 24 bits |
| Taxa de amostragem | Mínimo de 16 kHz\* |
| Resposta de freqüência | ± 3 dB, 200-8000 Hz Máscara flutuante\* |
| Confiabilidade | Faixa de temperatura de armazenamento -40°C a 70°C<br />Faixa de temperatura de operação -20°C a 55°C |

\*_Taxas de amostragem mais altas ou faixas de freqüência "mais amplas" podem ser necessárias para aplicações de comunicação de alta qualidade (VoIP)_

Uma boa seleção de componentes deve ser emparelhada com uma boa integração eletroacústica, a fim de evitar prejudicar o desempenho dos componentes utilizados. Casos de uso exclusivos também podem exigir requisitos adicionais (por exemplo: faixas de temperatura de funcionamento).

## <a name="microphone-array-integration"></a>Integração do conjunto de microfones

O desempenho do conjunto de microfones quando integrado em um dispositivo difere da especificação do componente. É importante garantir que os microfones estejam bem combinados após a integração. Portanto, o desempenho do dispositivo medido após qualquer ganho fixo ou EQ deve atender às seguintes recomendações:

| Parâmetro          | Recomendadas                                        |
| ------------------ | -------------------------------------------------- |
| Snr                | \>63 dB (sinal de 1 kHz 94 dBSPL, ruído ponderado a A) |
| Sensibilidade de saída | -26 dBFS/Pa @ 1 kHz (recomendado)                  |
| Correspondência de amplitude | ± 2 dB, 200-8000 Hz                                |
| THD%\*             | ≤ 1%, 200-8000 Hz, 94 dBSPL, 5ª Ordem             |
| Resposta de freqüência | ± 6 dB, 200-8000 Hz Máscara flutuante\*\*              |

\*\*_Um alto-falante de baixa distorção é necessário para medir o THD (por exemplo, Neumann KH120)_

\*\*_Faixas de freqüência "mais amplas" podem ser necessárias para aplicações de comunicação de alta qualidade (VoIP)_

## <a name="speaker-integration-recommendations"></a>Recomendações de integração de palestrantes

Como o cancelamento do eco é necessário para dispositivos de reconhecimento de fala que contenham alto-falantes, recomendações adicionais são fornecidas para seleção e integração de alto-falantes.

| Parâmetro | Recomendadas |
| --------- | ----------- |
| Considerações de linearidade | Não é necessário processamento não linear após a referência do alto-falante, caso contrário, um fluxo de referência de loopback baseado em hardware é necessário |
| Loopback do alto-falante | Fornecido via WASAPI, APIs privadas, plug-in ALSA personalizado (Linux) ou fornecido via canal de firmware |
| THD% | 3ª Octave Bandas mínimas 5ª Ordem, 70 dBA Reprodução @ 0,8 m ≤ 6,3%, 315-500 Hz ≤ 5%, 630-5000 Hz |
| Acoplamento ecológico aos microfones | \>-10 dB TCLw utilizando o método ITU-T G.122 Anexo B.4, normalizado para o nível do microfone<br />TCLw = TCLwmeasured \+ (Nível Medido - Sensibilidade da saída de destino)<br />TCLw = TCLwmeasured \+ (Nível Medido - (-26)) |

## <a name="integration-design-architecture"></a>Arquitetura de design de integração

As seguintes diretrizes para arquitetura são necessárias ao integrar microfones em um dispositivo:

| Parâmetro | Recomendação |
| --------- | -------------- |
| Similaridade da porta do microfone | Todas as portas do microfone têm o mesmo comprimento no conjunto |
| Dimensões do porto mic | Tamanho da porta Ø0.8-1.0 mm. Comprimento da porta \< / diâmetro da porta 2 |
| Selação do microfone         | Vedação de juntas uniformemente implementadas em empilhamento. Recomendo \> 70% de taxa de compressão para juntas de espuma |
| Confiabilidade do microfone     | A malha deve ser usada para evitar poeira e entrada (entre pcb para microfones portados inferiores e tampa de vedação da junta/tampa superior) |
| Isolamento mic       | Juntas de borracha e desacoplamento de vibração através da estrutura, particularmente para isolar quaisquer caminhos de vibração devido a alto-falantes integrados |
| Relógio de amostragem      | O áudio do dispositivo deve estar livre de nervosismo e desistências com baixa deriva |
| Capacidade de registro   | O dispositivo deve ser capaz de gravar fluxos brutos de canais individuais simultaneamente |
| USB                 | Todos os dispositivos de entrada de áudio USB devem definir descritores de acordo com os [dispositivos de áudio USB Rev3 Spec](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Geometria do microfone | Os drivers devem implementar [descritores de geometria do conjunto de microfones](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) corretamente |
| Detectabilidade     | Os dispositivos não devem ter nenhum hardware, firmware ou algoritmos de processamento de áudio não lineares baseados em software de terceiros para/ou a partir do dispositivo |
| Formato de captura      | Os formatos de captura devem usar uma taxa de amostragem mínima de 16 kHz e profundidade recomendada de 24 bits |

## <a name="electrical-architecture-considerations"></a>Considerações sobre arquitetura elétrica

Quando aplicável, os arrays podem ser conectados a um host USB (como um SoC que executa o Microsoft Audio Stack) e interfaces para serviços de Speech ou outros aplicativos.

Componentes de hardware, como a conversão PDM-tDM, devem garantir que o alcance dinâmico e o SNR dos microfones sejam preservados dentro de reamostradores.

A classe de áudio USB 2.0 de alta velocidade deve ser suportada em qualquer MCUs de áudio, a fim de fornecer a largura de banda necessária para até sete canais com taxas de amostra mais altas e profundidades de bits.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre os dispositivos de fala SDK](speech-devices-sdk.md)
