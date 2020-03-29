---
title: Pareamento e alinhamento de frases - Custom Translator
titleSuffix: Azure Cognitive Services
description: Durante a execução do treinamento, as sentenças presentes em documentos paralelos são emparelhadas ou alinhadas. Tradutor personalizado aprende traduções uma frase de cada vez, lendo uma frase, a tradução desta frase. Em seguida, alinha palavras e frases nessas duas frases entre si.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cf5b2b84142c9104ea5b3afa3ad179fd0ec07449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80370129"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Emparelhamento de frases e alinhamento em documentos paralelos

Durante o treinamento, as frases presentes em documentos paralelos são emparelhadas ou alinhadas. O Custom Translator relata o número de sentenças que conseguiu emparelhar como as Sentenças Alinhadas em cada um dos conjuntos de dados.

## <a name="pairing-and-alignment-process"></a>Processo de emparelhamento e alinhamento

O Custom Translator aprende traduções de sentenças uma sentença de cada vez. Ele lê uma sentença da fonte e, em seguida, a tradução desta frase do alvo. Em seguida, alinha palavras e frases nessas duas frases entre si. Este processo permite criar um mapa das palavras e frases em uma frase para as palavras e frases equivalentes na tradução desta frase. O alinhamento tenta garantir que o sistema treine em sentenças que são traduções uma da outra.

## <a name="pre-aligned-documents"></a>Documentos previamente alinhados

Se você souber que possui documentos paralelos, poderá substituir o alinhamento de sentenças fornecendo arquivos de texto pré-alinhados. Você pode extrair todas as sentenças de ambos os documentos em um arquivo de texto, organizar uma sentença por linha e fazer o upload com uma extensão `.align`. A extensão `.align` sinaliza ao Custom Translator que deve pular o alinhamento das frases.

Para obter melhores resultados, verifique se você tem uma frase por linha em seus arquivos.Não tem caracteres de nova linha dentro de uma frase, pois isso causará alinhamentos ruins.

## <a name="suggested-minimum-number-of-sentences"></a>Número mínimo sugerido de frases

Para que um treinamento tenha sucesso, a tabela abaixo mostra o número mínimo de frases exigidas em cada tipo de documento.Essa limitação é uma rede de segurança para garantir que suas frases paralelas contenham vocabulário único suficiente para treinar com sucesso um modelo de tradução. A diretriz geral é ter mais frases paralelas em domínio da qualidade da tradução humana deve produzir modelos de maior qualidade.

| Tipo de documento   | Contagem mínima sugerida de sentenças | Contagem máxima de sentenças |
|------------|--------------------------------------------|--------------------------------|
| Treinamento   | 10.000                                     | Nenhum limite superior                 |
| Ajuste     | 500                                      | 2.500       |
| Testes    | 500                                      | 2.500  |
| Dicionário | 0                                          | Nenhum limite superior                 |

> [!NOTE]
> - O treinamento não começará e falhará se a contagem mínima de 10.000 sentenças para treinamento não for cumprida. 
> - A finação e o teste são opcionais. Se você não fornecê-los, o sistema removerá uma porcentagem apropriada do treinamento para usar para validação e teste. 
> - Você pode treinar um modelo usando apenas dados do dicionário. Por favor, consulte [o que é dicionário](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary).

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar um [dicionário](what-is-dictionary.md) no tradutor personalizado.
