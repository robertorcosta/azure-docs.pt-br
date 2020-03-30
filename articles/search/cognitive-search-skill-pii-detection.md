---
title: Habilidade cognitiva de detecção de PII (visualização)
titleSuffix: Azure Cognitive Search
description: Extrair e mascarar informações pessoalmente identificáveis do texto em um pipeline de enriquecimento na Pesquisa Cognitiva do Azure. Esta habilidade está atualmente em visualização pública.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: f21200bc6f5b25f3330f5bb87c0843caa5a84e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298892"
---
#    <a name="pii-detection-cognitive-skill"></a>Habilidade cognitiva de detecção de PII

> [!IMPORTANT] 
> Esta habilidade está atualmente em visualização pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Atualmente, não há suporte a portal ou .NET SDK.

A habilidade **DE Detecção de PII** extrai informações pessoalmente identificáveis de um texto de entrada e lhe dá a opção de mascarar esse texto de várias maneiras. Essa habilidade usa os modelos de machine learning fornecidos pela [Análise de Texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) nos Serviços Cognitivos.

> [!NOTE]
> À medida que expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, você precisará [anexar um recurso de Serviços Cognitivos faturável](cognitive-search-attach-cognitive-services.md). As cobranças são geradas ao chamar APIs nos Serviços Cognitivos e para a extração de imagem, como parte do estágio de quebra de documento na Pesquisa Cognitiva do Azure. Não há encargos para extração de texto em documentos.
>
> A execução de habilidades integradas é cobrada nos [preços pagos conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. O preço da extração de imagem é descrito na [página de preços da Pesquisa Cognitiva do Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registro deve ser de 50.000 caracteres, medido por [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Se você precisar quebrar seus dados antes de enviá-los para a habilidade, considere usar a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros são sensíveis a maiúsculas e todos são opcionais.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| defaultLanguageCode |    Código de idioma do texto de entrada. Por enquanto, só `en` é suportado. |
| minimumPrecision | Um valor entre 0,0 e 1,0. Se o escore `piiEntities` de confiança (na `minimumPrecision` saída) for inferior ao valor definido, a entidade não será devolvida ou mascarada. O padrão é 0.0. |
| mascaramentoMode | Um parâmetro que fornece várias maneiras de mascarar o PII detectado no texto de entrada. Há suporte para as seguintes opções: <ul><li>`none`(padrão): Isso significa que nenhum mascaramento `maskedText` será realizado e a saída não será devolvida. </li><li> `redact`: Esta opção removerá as entidades detectadas do texto de entrada e não as substituirá por nada. Observe que, neste caso, `piiEntities` a compensação na saída será em relação ao texto original, e não ao texto mascarado. </li><li> `replace`: Esta opção substituirá as entidades detectadas `maskingCharacter` pelo caractere dado no parâmetro.  O caractere será repetido ao comprimento da entidade detectada para que as compensações correspondam corretamente `maskedText`tanto ao texto de entrada quanto à saída .</li></ul> |
| mascarandoCharacter | O caractere que será usado para mascarar o texto se o `maskingMode` parâmetro estiver definido como `replace`. As seguintes opções `*` são `#`suportadas: (padrão), . `X` Este parâmetro só `null` pode `maskingMode` ser se `replace`não for definido como . |


## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | Descrição                   |
|---------------|-------------------------------|
| languageCode    | Opcional. O padrão é `en`.  |
| text          | O texto para analisar.          |

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome de saída      | Descrição                   |
|---------------|-------------------------------|
| piiEntities | Uma matriz de tipos complexos que contêm os seguintes campos: <ul><li>texto (O PII real como extraído)</li> <li>type</li><li>Subtipo</li><li>pontuação (valor mais alto significa que é mais provável que seja uma entidade real)</li><li>deslocamento (no texto de entrada)</li><li>comprimento</li></ul> </br> [Possíveis tipos e subTipos podem ser encontrados aqui.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| mascaradoText | Se `maskingMode` for definido como `none`um valor diferente, esta saída será o resultado da seqüência `maskingMode`de máscaras realizada no texto de entrada conforme descrito pelo selecionado .  Se `maskingMode` estiver `none`definido como , esta saída não estará presente. |

##    <a name="sample-definition"></a>Definição de exemplo

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
      }
    ]
  }
```
##    <a name="sample-input"></a>Entrada de exemplo

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Saída de exemplo

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

Observe que as compensações retornadas para entidades na saída dessa habilidade são diretamente devolvidas da API de Análise de [Texto,](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)o que significa que se você estiver usando-as para indexar na seqüência original, você deve usar a classe [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) em .NET para extrair o conteúdo correto.  [Mais detalhes podem ser encontrados aqui.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-and-warning-cases"></a>Casos de erro e aviso
Se o código de idioma do documento não for suportado, um aviso será devolvido e nenhuma entidade será extraída.
Se o texto estiver vazio, um aviso será gerado.
Se o texto for maior que 50.000 caracteres, somente os primeiros 50.000 caracteres serão analisados e um aviso será emitido.

Se a habilidade retornar um `maskedText` aviso, a saída pode estar vazia.  Isso significa que, se você espera que essa saída exista para entrada em habilidades posteriores, ela não funcionará como planejado. Tenha isso em mente ao escrever sua definição de skillset.

## <a name="see-also"></a>Confira também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
