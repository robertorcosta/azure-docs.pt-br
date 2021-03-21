---
title: Terminologia-Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Lista dos termos usados nos artigos do tradutor personalizado.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: reference
ms.openlocfilehash: 4461f584e365a5d47e7ceee942e33bc8b101b2d2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657815"
---
# <a name="custom-translator-terminology"></a>Terminologia personalizada do Tradutor

A tabela a seguir apresenta uma lista de termos que você pode encontrar ao trabalhar com o [Tradutor personalizado](https://portal.customtranslator.azure.ai).

| Palavra ou frase|Definição|
|------------------|-----------|
| Idioma de origem | O idioma de origem é o idioma inicial que você deseja converter em outro idioma (o "destino").|
| Idioma de destino| O idioma de destino é o idioma que você deseja que a tradução automática forneça depois de receber o idioma de origem. |
| Arquivo monolíngue | Um arquivo multilíngue tem um único idioma não emparelhado com outro arquivo de um idioma diferente. |
| Arquivos paralelos | Um arquivo paralelo é uma combinação de dois arquivos com o texto correspondente. Um arquivo com o idioma de origem. O outro tem o idioma de destino.|
| Alinhamento de frase| O conjunto de dados paralelos deve ter frases alinhadas às sentenças que representam o mesmo texto nos dois idiomas. Por exemplo, em um arquivo paralelo de origem, a primeira sentença deve, em teoria, mapear a primeira sentença no arquivo paralelo de destino.|
| Texto alinhado | Uma das etapas mais importantes da validação de arquivos é alinhar as sentenças nos documentos paralelos. As coisas são expressas de maneira diferente em diferentes idiomas. Também diferentes idiomas têm diferentes ordens de palavra. Essa etapa faz o trabalho de alinhar as sentenças com o mesmo conteúdo para que elas possam ser usadas para treinamento. Um alinhamento de sentenças baixas indica que pode haver algo errado com um ou ambos os arquivos. |
| Word significativas / Unbreaking | Quebra de palavras é a função de marcar os limites entre as palavras. Muitos sistemas de escrita usam um espaço para denotar o limite entre as palavras. Word unbreaking refere-se a remoção de qualquer marcador visível que foi inserida entre as palavras em uma etapa anterior. |
| Delimitadores   | Delimitadores são as maneiras pelas quais uma sentença é dividida em segmentos ou delimita a margem entre sentenças. Por exemplo, em espaços ingleses delimitam palavras, dois-pontos e ponto-e-vírgula delimitam cláusulas e períodos que delimitam sentenças. |
| Arquivos de treinamento | Um arquivo de treinamento é usado para ensinar o sistema de tradução automática como mapear de um idioma (a origem) para um idioma de destino (o destino). Quanto mais dados você fornecer, melhor será o desempenho do sistema. |
| Ajuste de arquivos | Esses arquivos são frequentemente derivados aleatoriamente do conjunto de treinamento (se você não selecionar um conjunto de ajuste). As frases são autoselecionadas e usadas para ajustar o sistema e garantir que ele esteja funcionando corretamente. Se você quiser criar um modelo de tradução para fins gerais e criar seus próprios arquivos de ajuste, verifique se eles são um conjunto aleatório de frases entre domínios |
| Testando arquivos| Esses arquivos geralmente são arquivos derivados, selecionados aleatoriamente no conjunto de treinamento (se você não selecionar nenhum conjunto de teste). O objetivo dessas frases é avaliar a precisão do modelo de tradução. Como essas frases são aquelas que você deseja garantir que o sistema seja convertido com precisão, talvez você queira criar um conjunto de testes e carregá-lo no tradutor. Isso garantirá que essas frases sejam usadas na avaliação do sistema (a geração de uma pontuação BLEU).   |
| Arquivo de caixa de combinação   | Um tipo de arquivo no qual as sentenças de origem e traduzida estão contidas no mesmo arquivo. Formatos de arquivo com suporte (. TMX,. XLIFF,. XLF,. ICI,. xlsx). |
| Arquivo morto | Um arquivo que contém outros arquivos. Suporte para formatos de arquivo (zip, gz, tgz).  |
| Pontuação BLEU   | [BLEU](what-is-bleu-score.md) é o método padrão da indústria para avaliar a “precisão” ou precisão do modelo de tradução. Embora existam outros métodos de avaliação, o Microsoft Translator conta com o método BLEU para informar a precisão aos proprietários do projeto.
