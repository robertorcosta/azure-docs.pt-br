---
title: Melhorar um modelo para o serviço de Fala Personalizada-fala
titleSuffix: Azure Cognitive Services
description: Tipos específicos de transcrições com rótulo humano e texto relacionado podem melhorar a precisão do reconhecimento de um modelo de fala para texto com base no cenário de discurso.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: v-demjoh
ms.openlocfilehash: bb904482f3cb5900b724803816269f1b10ab3720
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83727873"
---
# <a name="improve-custom-speech-accuracy"></a>Aprimorar a precisão da Fala Personalizada

Neste artigo, você aprenderá a melhorar a qualidade de seu modelo personalizado adicionando áudio, transcrições com rótulo humano e texto relacionado.

## <a name="accuracy-in-different-scenarios"></a>Precisão em cenários diferentes

Os cenários de reconhecimento de fala variam de acordo com a qualidade e a linguagem de áudio (vocabulário e estilo de fala). A tabela a seguir examina quatro cenários comuns:

| Cenário | Qualidade de áudio | Vocabulário | Estilo de fala |
|----------|---------------|------------|----------------|
| Call center | Baixo, 8 kHz, poderia ser 2 seres humanos em 1 canal de áudio, poderia ser compactado | Estreito, exclusivo de domínio e produtos | De conversação, estruturada sem rigidez |
| Assistente de voz (como a Cortana ou uma janela de unidade por meio) | Alta, 16 kHz | Entidade pesada (títulos de música, produtos, locais) | Palavras e frases claramente declaradas |
| Ditado (mensagem instantânea, observações, pesquisa) | Alta, 16 kHz | Variado | Observação-tomando |
| Legenda codificada em vídeo | Variado, incluindo uso variado de microfone, música adicionada | Variadas, de reuniões, fala recitada, música musical | Lido, preparado ou estruturado de forma flexível |

Diferentes cenários produzem resultados de qualidade diferentes. A tabela a seguir examina como o conteúdo desses quatro cenários classifica a [taxa de erros do Word (WER)](how-to-custom-speech-evaluate-data.md). A tabela mostra quais tipos de erro são mais comuns em cada cenário.

| Cenário | Qualidade do reconhecimento de fala | Erros de inserção | Erros de exclusão | Erros de substituição |
|----------|----------------------------|------------------|-----------------|---------------------|
| Call center | Médio (< 30% WER) | Baixo, exceto quando outras pessoas falam em segundo plano | Pode ser alto. As centrais de chamadas podem ser ruidosas e os alto-falantes sobrepostos podem confundir o modelo | Média. Os nomes de produtos e pessoas podem causar esses erros |
| Assistente de voz | Alta (pode ser < de 10% WER) | Baixo | Baixo | Médio, devido a títulos de música, nomes de produtos ou locais |
| Ditado | Alta (pode ser < de 10% WER) | Baixo | Baixo | Alto |
| Legenda codificada em vídeo | Depende do tipo de vídeo (pode ser < 50% WER) | Baixo | Pode ser alto devido a música, ruídos, qualidade do microfone | O jargão pode causar esses erros |

A determinação dos componentes do WER (número de erros de inserção, exclusão e substituição) ajuda a determinar o tipo de dados a ser adicionado para melhorar o modelo. Use o [portal de fala personalizada](https://speech.microsoft.com/customspeech) para exibir a qualidade de um modelo de linha de base. O portal relata as taxas de erro de inserção, substituição e exclusão combinadas na taxa de qualidade do WER.

## <a name="improve-model-recognition"></a>Melhorar o reconhecimento de modelo

Você pode reduzir os erros de reconhecimento adicionando dados de treinamento no [portal de fala personalizada](https://speech.microsoft.com/customspeech). 

Planeje manter seu modelo personalizado adicionando materiais de origem periodicamente. Seu modelo personalizado precisa de treinamento adicional para ficar atento às alterações em suas entidades. Por exemplo, você pode precisar de atualizações para nomes de produtos, nomes de músicas ou novos locais de serviço.

As seções a seguir descrevem como cada tipo de dados de treinamento adicionais pode reduzir erros.

### <a name="add-related-text-sentences"></a>Adicionar frases de texto relacionadas

Frases de texto adicionais relacionadas podem reduzir principalmente erros de substituição relacionados ao reconhecimento inconsistente de palavras comuns e palavras específicas de domínio, mostrando-as no contexto. Palavras específicas de domínio podem ser palavras incomuns ou comparadas, mas sua pronúncia deve ser simples de ser reconhecida.

> [!NOTE]
> Evite frases de texto relacionadas que incluam ruído como caracteres ou palavras não reconhecíveis.

### <a name="add-audio-with-human-labeled-transcripts"></a>Adicionar áudio com transcrições com rótulo humano

O áudio com transcrições com rótulo humano oferecerá as maiores melhorias de precisão se o áudio vier do caso de uso de destino. Os exemplos devem abranger todo o escopo de fala. Por exemplo, um Call Center para uma loja de varejo obteria a maioria das chamadas sobre swimwear e óculos durante os meses de verão. Certifique-se de que seu exemplo inclui o escopo completo de fala que você deseja detectar.

Considere estes detalhes:

* Fala Personalizada só pode capturar o contexto do Word para reduzir erros de substituição, não erros de inserção ou exclusão.
* Evite exemplos que incluam erros de transcrição, mas inclua uma diversidade de qualidade de áudio.
* Evite frases que não estejam relacionadas ao seu domínio problemático. Frases não relacionadas podem danificar seu modelo.
* Quando a qualidade das transcrições varia, você pode duplicar frases válidas com exceção (como as excelentes transcrições que incluem frases-chave) para aumentar seu peso.

### <a name="add-new-words-with-pronunciation"></a>Adicionar novas palavras com pronúncia

As palavras que são feitas ou altamente especializadas podem ter pronúncias exclusivas. Essas palavras podem ser reconhecidas se a palavra puder ser dividida em palavras menores para pronuncia-la. Por exemplo, para reconhecer o **Xbox**, pronuncia-se como **X Box**. Essa abordagem não aumentará a precisão geral, mas pode aumentar o reconhecimento dessas palavras-chave.

> [!NOTE]
> Essa técnica só está disponível para alguns idiomas no momento. Consulte personalização de pronúncia na [tabela de conversão de fala em texto](language-support.md) para obter detalhes.

## <a name="sources-by-scenario"></a>Fontes por cenário

A tabela a seguir mostra cenários de reconhecimento de voz e lista os materiais de origem a serem considerados nas três categorias de conteúdo de treinamento listadas acima.

| Cenário | Frases de texto relacionadas | Áudio + transcrições com rótulo humano | Novas palavras com pronúncia |
|----------|------------------------|------------------------------|------------------------------|
| Call center             | documentos de marketing, site, revisões de produtos relacionados à atividade do Call Center | chamadas de Call Center transcritas por seres humanos | termos que têm pronúncias ambíguas (consulte o Xbox acima) |
| Assistente de voz         | listar frases usando todas as combinações de comandos e entidades | gravar os comandos de fala de vozes no dispositivo e transcrever em texto | nomes (filmes, músicas, produtos) que têm pronúncias exclusivas |
| Ditado               | entrada escrita, como mensagens instantâneas ou emails | semelhante ao anterior | semelhante ao anterior |
| Legenda codificada em vídeo | TV mostrar scripts, filmes, conteúdo de marketing, resumos de vídeo | transcrições exatas de vídeos | semelhante ao anterior |

## <a name="next-steps"></a>Próximas etapas

- [Treinar seu modelo](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Preparar e testar seus dados](how-to-custom-speech-test-data.md)
- [Inspecione seus dados](how-to-custom-speech-inspect-data.md)