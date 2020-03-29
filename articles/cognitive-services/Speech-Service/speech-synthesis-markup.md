---
title: Linguagem de marcação de síntese de fala (SSML) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Usando o Speech Synthesis Markup Language para controlar pronúncia e prosódia em texto para fala.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: 7d5dd79399b15ade90173a55aeb71dacbc61fa78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80365814"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Melhore a síntese com a Linguagem de Marcação da Síntese de Fala (SSML)

O SSML (Speech Synthesis Markup Language, linguagem de marcação de síntese de fala) é uma linguagem de marcação baseada em XML que permite aos desenvolvedores especificar como o texto de entrada é convertido em voz sintetizada usando o serviço texto-para-fala. Comparado ao texto simples, o SSML permite que os desenvolvedores ajustem o tom, a pronúncia, a taxa de fala, o volume e mais da saída texto-para-fala. Pontuação normal, como pausaapós um período, ou usar a entonação correta quando uma frase termina com um ponto de interrogação são automaticamente manuseadas.

A implementação do serviço de fala do SSML é baseada na [versão 1.0](https://www.w3.org/TR/speech-synthesis)da Speech Synthesis Markup Do World Wide Web Consortium.

> [!IMPORTANT]
> Caracteres chineses, japoneses e coreanos contam como dois caracteres para faturamento. Para saber mais, consulte [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Vozes padrão, neurais e personalizadas

Escolha entre vozes padrão e neurais, ou crie sua própria voz personalizada exclusiva para o seu produto ou marca. Mais de 75 vozes padrão estão disponíveis em mais de 45 idiomas e locais, e 5 vozes neurais estão disponíveis em quatro idiomas e locais. Para obter uma lista completa de vozes (neurais e padrão), localidades e idiomas com suporte, consulte [suporte para idioma](language-support.md).

Para saber mais sobre vozes padrão, neurais e personalizadas, consulte [visão geral do texto para a fala](text-to-speech.md).

## <a name="special-characters"></a>Caracteres especiais

Ao usar o SSML, tenha em mente que personagens especiais, como aspas, apóstrofes e suportes devem ser escapados. Para obter mais informações, consulte [Alingiting Language (XML) 1.0: Apêndice D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Elementos SSML suportados

Cada documento SSML é criado com elementos SSML (ou tags). Esses elementos são usados para ajustar tom, prosódia, volume e muito mais. As seções a seguir detalham como cada elemento é usado e quando um elemento é necessário ou opcional.  

> [!IMPORTANT]
> Não se esqueça de usar aspas duplas em torno dos valores de atributos. Os padrões para XML bem formados e válidos exigem que os valores de atributo sejam incluídos entre aspas duplas. Por exemplo, `<prosody volume="90">` é um elemento bem `<prosody volume=90>` formado e válido, mas não é. O SSML pode não reconhecer valores de atributo que não estão entre aspas.

## <a name="create-an-ssml-document"></a>Criar um documento SSML

`speak`é o elemento raiz, e é **necessário** para todos os documentos SSML. O `speak` elemento contém informações importantes, como versão, idioma e a definição do vocabulário de marcação.

**Sintaxe**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `version` | Indica a versão da especificação SSML usada para interpretar a marcação do documento. A versão atual é 1.0. | Obrigatório |
| `xml:lang` | Especifica a linguagem do documento raiz. O valor pode conter um código de idioma minúsculo `en`de duas letras (por exemplo, ), `en-US`ou o código de idioma e o país/região maiúscula (por exemplo, ). | Obrigatório |
| `xmlns` | Especifica o URI para o documento que define o vocabulário de marcação (os tipos de elementos e nomes de atributos) do documento SSML. O URI http://www.w3.org/2001/10/synthesisatual é . | Obrigatório |

## <a name="choose-a-voice-for-text-to-speech"></a>Escolha uma voz para texto-para-fala

O `voice` elemento é necessário. Ele é usado para especificar a voz usada para texto-para-fala.

**Sintaxe**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `name` | Identifica a voz usada para saída de texto para fala. Para obter uma lista completa de vozes suportadas, consulte [suporte ao idioma](language-support.md#text-to-speech). | Obrigatório |

**Exemplo**

> [!NOTE]
> Este exemplo `en-US-AriaRUS` usa a voz. Para obter uma lista completa de vozes suportadas, consulte [suporte ao idioma](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Usar várias vozes

Dentro `speak` do elemento, você pode especificar várias vozes para a saída de texto para voz. Essas vozes podem estar em diferentes línguas. Para cada voz, o texto `voice` deve ser embrulhado em um elemento. 

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `name` | Identifica a voz usada para saída de texto para fala. Para obter uma lista completa de vozes suportadas, consulte [suporte ao idioma](language-support.md#text-to-speech). | Obrigatório |

> [!IMPORTANT]
> Várias vozes são incompatíveis com o recurso de limite de palavras. O recurso de limite de palavras precisa ser desativado para usar várias vozes.

### <a name="disable-word-boundary"></a>Desativar o limite da palavra

Dependendo da linguagem Speech SDK, você `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` definirá a propriedade em `false` uma instância do `SpeechConfig` objeto.

# <a name="c"></a>[C #](#tab/csharp)

Para obter mais <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informações, consulte .

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Para obter mais <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informações, consulte .

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Para obter mais <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informações, consulte .

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Para obter mais <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informações, consulte .

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Para obter mais <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#setproperty-string--string-" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informações, consulte .

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Para obter mais <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informações, consulte .

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Para obter mais <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informações, consulte .

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Aria!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Ajuste os estilos de fala

> [!IMPORTANT]
> O ajuste dos estilos de fala só funcionará com vozes neurais.

Por padrão, o serviço texto-para-fala sintetiza o texto usando um estilo neutro de fala para vozes padrão e neurais. Com vozes neurais, você pode ajustar o estilo de falar `<mstts:express-as>` para expressar alegria, empatia ou sentimento com o elemento. Este é um elemento opcional exclusivo do serviço Speech.

Atualmente, ajustes de estilo de fala são suportados para essas vozes neurais:
* `en-US-AriaNeural`
* `zh-CN-XiaoxiaoNeural`
* `pt-BR-FranciscaNeural`

As alterações são aplicadas no nível da sentença, e o estilo varia de acordo com a voz. Se um estilo não for suportado, o serviço retornará a fala no estilo de fala neutro padrão.

**Sintaxe**

```xml
<mstts:express-as style="string"></mstts:express-as>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `style` | Especifica o estilo de falar. Atualmente, os estilos de fala são específicos de voz. | Necessário se ajustar o estilo de falar para uma voz neural. Se `mstts:express-as`estiver usando, então o estilo deve ser fornecido. Se um valor inválido for fornecido, esse elemento será ignorado. |

Use esta tabela para determinar quais estilos de fala são suportados para cada voz neural.

| Voz | Estilo | Descrição |
|-------|------|-------------|
| `en-US-AriaNeural` | `style="newscast"` | Expressa um tom formal e profissional para narrar notícias |
| | `style="customerservice"` | Expressa um tom amigável e útil para o suporte ao cliente |
| | `style="chat"` | Expressa um tom casual e descontraído |
| | `style="cheerful"` | Expressa um tom positivo e feliz |
| | `style="empathetic"` | Expressa um senso de cuidado e compreensão |
| `zh-CN-XiaoxiaoNeural` | `style="newscast"` | Expressa um tom formal e profissional para narrar notícias |
| | `style="customerservice"` | Expressa um tom amigável e útil para o suporte ao cliente |
| | `style="assistant"` | Expressa um tom aconchegante e descontraído para assistentes digitais  |
| | `style="lyrical"` | Expressa emoções de forma melódica e sentimental |
| `pt-BR-FranciscaNeural` | `style="cheerful"` | Expressa um tom positivo e feliz |

**Exemplo**

Este trecho do SSML ilustra `<mstts:express-as>` como o elemento é `cheerful`usado para mudar o estilo de falar para .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Adicione ou remova uma pausa/pausa

Use `break` o elemento para inserir pausas (ou pausas) entre palavras ou impedir pausas adicionadas automaticamente pelo serviço texto-para-fala.

> [!NOTE]
> Use esse elemento para substituir o comportamento padrão do texto-para-fala (TTS) para uma palavra ou frase se a fala sintetizada para essa palavra ou frase soar não natural. `strength` Configurado `none` para evitar uma quebra prosódica, que é automaticamente inserida pelo serviço texto-para-fala.

**Sintaxe**

```xml
<break strength="string" />
<break time="string" />
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `strength` | Especifica a duração relativa de uma pausa usando um dos seguintes valores:<ul><li>none</li><li>x-fraco</li><li>Fraco</li><li>meio (padrão)</li><li>forte</li><li>x-forte</li></ul> | Opcional |
| `time` | Especifica a duração absoluta de uma pausa em segundos ou milissegundos. Exemplos de valores `2s` válidos são e`500` | Opcional |

| Força | Descrição |
|----------|-------------|
| Nenhum, ou se nenhum valor fornecido | 0 ms |
| x-fraco | 250 ms |
| Fraco | 500 ms |
| média | 750 ms |
| forte | 1000 ms |
| x-forte | 1250 ms |


**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Especificar parágrafos e frases

`p`e `s` elementos são usados para denotar parágrafos e frases, respectivamente. Na ausência desses elementos, o serviço texto-para-fala determina automaticamente a estrutura do documento SSML.

O `p` elemento pode conter texto `audio`e `break` `phoneme`os `prosody` `say-as`seguintes elementos: , , , , `sub`, `mstts:express-as`, e `s`.

O `s` elemento pode conter texto `audio`e `break` `phoneme`os `prosody` `say-as`seguintes elementos: , , , , , `mstts:express-as`e `sub`.

**Sintaxe**

```XML
<p></p>
<s></s>
```

**Exemplo**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Use fonemas para melhorar a pronúncia

O `ph` elemento é usado para pronúncia fonética em documentos SSML. O `ph` elemento só pode conter texto, nenhum outro elemento. Sempre forneça um discurso de leitura humana como um recuo.

Os alfabetos fonéticos são compostos por telefones, que são compostos de letras, números ou caracteres, às vezes em combinação. Cada telefone descreve um som único de fala. Isso contrasta com o alfabeto latino, onde qualquer letra pode representar múltiplos sons falados. Considere as diferentes pronúncias da letra "c" nas palavras "doce" e "cessar", ou as diferentes pronúncias da combinação de letras "th" nas palavras "coisa" e "aqueles".

**Sintaxe**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `alphabet` | Especifica o alfabeto fonético a ser usado ao `ph` sintetizar a pronúncia da seqüência no atributo. A seqüência especificando o alfabeto deve ser especificada em letras minúsculas. A seguir estão os possíveis alfabetos que você pode especificar.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Alfabeto <span class="docon docon-navigate-external x-hidden-focus"></span> Fonético Internacional</a></li><li>`sapi`&ndash; [Alfabeto fonético do serviço de fala](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; Conjunto de telefone universal</li></ul><br>O alfabeto se `phoneme` aplica apenas ao elemento.. | Opcional |
| `ph` | Uma seqüência contendo telefones que especificam `phoneme` a pronúncia da palavra no elemento. Se a seqüência especificada contiver telefones não reconhecidos, o serviço de texto para voz (TTS) rejeitará todo o documento SSML e não produzirá nenhuma das saídas de fala especificadas no documento. | Necessário se usar fonemas. |

**Exemplos**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Use o léxico personalizado para melhorar a pronúncia

Às vezes, o TTS não consegue pronunciar uma palavra com precisão, por exemplo, uma empresa ou nome estrangeiro. Os desenvolvedores podem definir a leitura dessas `phoneme` entidades no SSML usando e `sub` marcar, ou definir a `lexicon` leitura de várias entidades, referindo-se a um arquivo léxico personalizado usando tag.

**Sintaxe**

```XML
<lexicon uri="string"/>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `uri` | O endereço do documento PLS externo. | Obrigatórios. |

**Uso**

Passo 1: Defina o léxico personalizado 

Você pode definir a leitura de entidades por uma lista de itens de léxico personalizados, armazenados como um arquivo .xml ou .pls.

**Exemplo**

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon 
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme> 
    <alias>By the way</alias> 
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme> 
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

Cada `lexeme` elemento é um item léxico. `grapheme`contém texto descrevendo o ortograma de `lexeme`. O formulário de leitura `alias`pode ser fornecido como . A seqüência telefônica `phoneme` pode ser fornecida no elemento.

O `lexicon` elemento contém `lexeme` pelo menos um elemento. Cada `lexeme` elemento contém `grapheme` pelo menos um `grapheme` `alais`elemento `phoneme` e um ou mais , e elementos. O `grapheme` elemento contém texto descrevendo a <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">ortografia <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>. Os `alias` elementos são usados para indicar a pronúncia de um acrônimo ou um termo abreviado. O `phoneme` elemento fornece texto `lexeme` descrevendo como o é pronunciado.

Para obter mais informações sobre o arquivo lexico personalizado, consulte [Pronunciation Lexicon Specification (PLS) Versão 1.0](https://www.w3.org/TR/pronunciation-lexicon/) no site do W3C.

Passo 2: Faça upload de arquivo lexicon personalizado criado na etapa 1 on-line, você pode armazená-lo em qualquer lugar, e sugerimos que você o armazene no Microsoft Azure, por [exemplo, o Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

Passo 3: Consulte o arquivo lexicon personalizado no SSML

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
<lexicon uri="http://www.example.com/customlexicon.xml"/>
BTW, we will be there probably 8:00 tomorrow morning.
Could you help leave a message to Robert Benigni for me?
</speak>
```
"BTW" será lido como "A propósito". "Benigni" será lido com ipa fornecido "bəיniיnji".  

**Limitação**
- Tamanho do arquivo: o limite máximo de tamanho do arquivo do léxico personalizado é de 100KB, se além desse tamanho, a solicitação de síntese falhará.
- Atualização de cache lexicon: o léxico personalizado será armazenado em cache com o URI como chave no TTS Service quando for carregado pela primeira vez. O léxico com o mesmo URI não será recarregado dentro de 15 minutos, então a mudança de léxico personalizado precisa esperar no máximo 15 minutos para fazer efeito.

**Conjuntos fonéticos do serviço de fala**

Na amostra acima, estamos usando o Alfabeto Fonético Internacional, também conhecido como conjunto de telefones IPA. Sugerimos que os desenvolvedores usem o IPA, porque é o padrão internacional. Considerando que o IPA não é fácil de lembrar, o serviço de`en-US` `fr-FR`fala `de-DE` `es-ES`define `ja-JP` `zh-CN`um `zh-TW`conjunto fonético para sete idiomas ( , , , , , e ).

Você pode `sapi` usar o como `alphabet` vale para o atributo com léxicos personalizados, conforme demonstrado abaixo:

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

Para obter mais informações sobre o alfabeto fonético detalhado do serviço de fala, consulte os [conjuntos fonéticos](speech-ssml-phonetic-sets.md)do serviço de fala .

## <a name="adjust-prosody"></a>Ajuste a prosódia

O `prosody` elemento é usado para especificar alterações no tom, contorno, alcance, taxa, duração e volume para a saída texto-fala. O `prosody` elemento pode conter texto `audio`e `break` `p`os `phoneme` `prosody`seguintes elementos: , , , , `say-as`, `sub`, e `s`.

Como os valores de atributo prosódicos podem variar em uma ampla gama, o reconhecimento da fala interpreta os valores atribuídos como uma sugestão de quais devem ser os valores prosódicos reais da voz selecionada. O serviço de texto-para-fala limita ou substitui valores que não são suportados. Exemplos de valores não suportados são um pitch de 1 MHz ou um volume de 120.

**Sintaxe**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `pitch` | Indica o tom de linha de base para o texto. Você pode expressar o tom como:<ul><li>Um valor absoluto, expresso como um número seguido de "Hz" (Hertz). Por exemplo, 600 Hz.</li><li>Um valor relativo, expresso como um número precedido por "+" ou "-" e seguido por "Hz" ou "st", que especifica uma quantidade para alterar o tom. Por exemplo: +80 Hz ou -2º. O "st" indica que a unidade de mudança é semitona, que é metade de um tom (meio passo) na escala diatônica padrão.</li><li>Um valor constante:<ul><li>x-baixo</li><li>low</li><li>média</li><li>high</li><li>x-alta</li><li>default</li></ul></li></ul>. | Opcional |
| `contour` | Contorno não é suportado por vozes neurais. Contorno representa mudanças no tom. Essas alterações são representadas como um conjunto de alvos em posições de tempo especificadas na saída de fala. Cada alvo é definido por conjuntos de pares de parâmetros. Por exemplo:  <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>O primeiro valor em cada conjunto de parâmetros especifica a localização da alteração de passo como porcentagem da duração do texto. O segundo valor especifica o valor para aumentar ou diminuir o pitch, usando um `pitch`valor relativo ou um valor de enumeração para pitch (ver ). | Opcional |
| `range` | Um valor que representa a faixa de passo para o texto. Você pode `range` expressar usando os mesmos valores absolutos, `pitch`valores relativos ou valores de enumeração usados para descrever . | Opcional |
| `rate` | Indica a taxa de fala do texto. Você pode `rate` expressar como:<ul><li>Um valor relativo, expresso como um número que age como um multiplicador do padrão. Por exemplo, um valor de *1* não resulta em nenhuma mudança na taxa. Um valor de *0,5* resulta em uma redução pela metade da taxa. Um valor de *3* resulta em um triplo da taxa.</li><li>Um valor constante:<ul><li>x-lento</li><li>lento</li><li>média</li><li>rápido</li><li>x-rápido</li><li>default</li></ul></li></ul> | Opcional |
| `duration` | O período de tempo que deve transcorrer enquanto o serviço de síntese de fala (TTS) lê o texto, em segundos ou milissegundos. Por exemplo, *2s* ou *1800ms*. | Opcional |
| `volume` | Indica o nível de volume da voz falante. Você pode expressar o volume como:<ul><li>Um valor absoluto, expresso como um número na faixa de 0,0 a 100,0, do *mais silencioso* ao *mais alto*. Por exemplo, 75. O padrão é 100.0.</li><li>Um valor relativo, expresso como um número precedido por "+" ou "-" que especifica uma quantidade para alterar o volume. Por exemplo, +10 ou -5,5.</li><li>Um valor constante:<ul><li>silent</li><li>x-soft</li><li>Macio</li><li>média</li><li>Alto</li><li>x-alto</li><li>default</li></ul></li></ul> | Opcional |

### <a name="change-speaking-rate"></a>Alterar taxa de fala

A taxa de fala pode ser aplicada a vozes padrão no nível da palavra ou sentença. Considerando que a taxa de fala só pode ser aplicada a vozes neurais no nível da sentença.

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Alterar volume

Alterações de volume podem ser aplicadas a vozes padrão no nível da palavra ou de sentença. Considerando que as alterações de volume só podem ser aplicadas às vozes neurais no nível da sentença.

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Alterar densidade

Alterações de pitch podem ser aplicadas a vozes padrão no nível da palavra ou de sentença. Considerando que mudanças de tom só podem ser aplicadas a vozes neurais no nível da sentença.

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Alterar contorno de densidade

> [!IMPORTANT]
> Mudanças de contorno não são suportadas com vozes neurais.

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>dizer-como elemento

`say-as`é um elemento opcional que indica o tipo de conteúdo (como número ou data) do texto do elemento. Isso fornece orientação ao mecanismo de síntese da fala sobre como pronunciar o texto.

**Sintaxe**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `interpret-as` | Indica o tipo de conteúdo do texto do elemento. Para obter uma lista de tipos, consulte a tabela abaixo. | Obrigatório |
| `format` | Fornece informações adicionais sobre a formatação precisa do texto do elemento para tipos de conteúdo que podem ter formatos ambíguos. O SSML define formatos para tipos de conteúdo que os utilizam (veja tabela abaixo). | Opcional |
| `detail` | Indica o nível de detalhes a ser falado. Por exemplo, este atributo pode solicitar que o mecanismo de síntese de fala pronuncie marcas de pontuação. Não há valores `detail`padrão definidos para . | Opcional |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

A seguir estão os tipos `interpret-as` de `format` conteúdo suportados para os e atributos. Inclua `format` o atributo somente se `interpret-as` for definido como data e hora.

| interpretar-como | format | Interpretação |
|--------------|--------|----------------|
| `address` | | O texto é falado como um endereço. O motor de síntese da fala pronuncia:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Como "Estou na 150ª corte a nordeste de Redmond Washington." |
| `cardinal`, `number` | | O texto é falado como um número cardeal. O motor de síntese da fala pronuncia:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Como "Há três alternativas." |
| `characters`, `spell-out` | | O texto é falado como letras individuais (soletradas). O motor de síntese da fala pronuncia:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Como "T E ST". |
| `date` | dmy, mdy, ymd, ydm, ym, meu, md, dm, d, m, y | O texto é falado como uma data. O `format` atributo especifica o formato da data *(d=dia, m=mês e y=ano*). O motor de síntese da fala pronuncia:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Como "Hoje é outubro de 192.600." |
| `digits`, `number_digit` | | O texto é falado como uma seqüência de dígitos individuais. O motor de síntese da fala pronuncia:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Como "1 2 3 4 5 6 7 8 9." |
| `fraction` | | O texto é falado como um número fracionado. O motor de síntese da fala pronuncia:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Como "três oitavos de uma polegada". |
| `ordinal` | | O texto é falado como um número ordinário. O motor de síntese da fala pronuncia:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Como "Selecione a terceira opção". |
| `telephone` | | O texto é falado como um número de telefone. O `format` atributo pode conter dígitos que representam um código de país. Por exemplo, "1" para os Estados Unidos ou "39" para a Itália. O mecanismo de síntese de fala pode usar essas informações para orientar sua pronúncia de um número de telefone. O número de telefone também pode incluir o código do país e, `format`se for o caso, tem precedência sobre o código do país no . O motor de síntese da fala pronuncia:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Como "Meu número é código de área oito oito oito cinco cinco cinco um dois dois." |
| `time` | hms12, hms24 | O texto é falado como um tempo. O `format` atributo especifica se a hora é especificada usando um relógio de 12 horas (hms12) ou um relógio de 24 horas (hms24). Use um cólon para separar números que representem horas, minutos e segundos. A seguir, os exemplos de horárioválidos: 12:35, 1:14:32, 08:15 e 02:50:45. O motor de síntese da fala pronuncia:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Como "O trem parte às quatro A M." |

**Uso**

O `say-as` elemento pode conter apenas texto.

**Exemplo**

O motor de síntese da fala fala o seguinte exemplo como "Seu primeiro pedido foi para uma sala em 19 de outubro vinte e dez com chegada antecipada às 12:35 PM."
 
```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Adicionar áudio gravado

`audio`é um elemento opcional que permite inserir áudio MP3 em um documento SSML. O corpo do elemento áudio pode conter texto simples ou marcação SSML que é falado se o arquivo de áudio estiver indisponível ou não jogável. Além disso, `audio` o elemento pode conter `audio`texto `break` `p`e `s` `phoneme`os `prosody` `say-as`seguintes `sub`elementos: , , , , , , e .

Qualquer áudio incluído no documento SSML deve atender a esses requisitos:

* O MP3 deve ser hospedado em um ponto final HTTPS acessível à Internet. O HTTPS é necessário e o domínio que hospeda o arquivo MP3 deve apresentar um certificado TLS/SSL válido e confiável.
* O MP3 deve ser um arquivo MP3 válido (MPEG v2).
* A taxa de bits deve ser de 48 kbps.
* A taxa de amostra deve ser de 16.000 Hz.
* O tempo total combinado para todos os arquivos de texto e áudio em uma única resposta não pode exceder noventa (90) segundos.
* O MP3 não deve conter nenhuma informação específica do cliente ou outras informações confidenciais.

**Sintaxe**

```xml
<audio src="string"/></audio>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `src` | Especifica a localização/URL do arquivo de áudio. | Necessário se usar o elemento de áudio no documento SSML. |

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>Adicionar áudio de fundo

O `mstts:backgroundaudio` elemento permite adicionar áudio em segundo plano aos documentos do SSML (ou misturar um arquivo de áudio com texto-para-voz). Com `mstts:backgroundaudio` você pode fazer loop de um arquivo de áudio em segundo plano, desaparecer no início do texto-para-fala e desaparecer no final do texto-para-fala.

Se o áudio de fundo fornecido for mais curto do que o texto-para-fala ou o fade out, ele fará loop. Se for mais longo do que o texto-para-discurso, ele vai parar quando o fade fora tiver terminado.

Apenas um arquivo de áudio em segundo plano é permitido por documento SSML. No entanto, você `audio` pode intercalar tags dentro do `voice` elemento para adicionar áudio adicional ao seu documento SSML.

**Sintaxe**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `src` | Especifica a localização/URL do arquivo de áudio em segundo plano. | Necessário se usar áudio de fundo no documento Do SSML. |
| `volume` | Especifica o volume do arquivo de áudio em segundo plano. **Valores aceitos**: `0` inclusivo. `100` O valor padrão é `1`. | Opcional |
| `fadein` | Especifica a duração do áudio de fundo "desbotar" em milissegundos. O valor `0`padrão é , que é o equivalente a nenhum desbotamento. **Valores aceitos**: `0` inclusivo. `10000`  | Opcional |
| `fadeout` | Especifica a duração do áudio de fundo desaparecer em milissegundos. O valor `0`padrão é , que é o equivalente a nenhum desvanecer. **Valores aceitos**: `0` inclusivo. `10000`  | Opcional |

**Exemplo**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Próximas etapas

* [Suporte de linguagem: vozes, localidades, idiomas](language-support.md)
