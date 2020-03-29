---
title: Listas de frases - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Aprenda a fornecer o serviço Speech com `PhraseListGrammar` uma lista de frases usando o objeto para melhorar os resultados de reconhecimento de voz a texto.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f84ea6b2b0f1e8246e1d765e54f663cd01f29b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77560840"
---
# <a name="phrase-lists-for-speech-to-text"></a>Listas de frases para discurso-para-texto

Ao fornecer ao serviço Speech uma lista de frases, você pode melhorar a precisão do reconhecimento de fala. Listas de frases são usadas para identificar frases conhecidas em dados de áudio, como o nome de uma pessoa ou um local específico.

Como exemplo, se você tiver um comando "Mover para" e um possível destino de "Ward" que pode ser falado, você pode adicionar uma entrada de "Mover para Ward". Adicionar uma frase aumentará a probabilidade de que quando o áudio for reconhecido que "Mover para Ward" será reconhecido em vez de "Mover-se em direção".

Palavras simples ou frases completas podem ser adicionadas a uma lista de frases. Durante o reconhecimento, uma entrada em uma lista de frases é usada se uma correspondência exata para toda a frase for incluída no áudio como uma frase separada. Se uma correspondência exata com a frase não for encontrada, o reconhecimento não será assistido.

>[!Note]
> Atualmente, as Listas de Frases suportam apenas o inglês para o discurso-a-texto.

## <a name="how-to-use-phrase-lists"></a>Como usar listas de frases

As amostras abaixo ilustram como construir `PhraseListGrammar` uma lista de frases usando o objeto.

::: zone pivot="programming-language-csharp"

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> O número máximo de Listas de Frases que o serviço Speech usará para combinar fala é de 1024 frases.

Você também pode limpar as `PhraseListGrammar` frases associadas ao chamado clear().

::: zone pivot="programming-language-csharp"

```cs
phraseList.Clear();
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
phraselist->Clear();
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
phraseListGrammar.clear();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar.clear()
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> As alterações em um `PhraseListGrammar` objeto terão efeito no próximo reconhecimento ou após uma reconexão ao serviço Speech.

## <a name="next-steps"></a>Próximas etapas

* [Documentação de referência do Speech SDK](speech-sdk.md)
