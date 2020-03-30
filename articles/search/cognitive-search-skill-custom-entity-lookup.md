---
title: Habilidade de busca cognitiva da Personal Entity Lookup
titleSuffix: Azure Cognitive Search
description: Extrair diferentes entidades personalizadas do texto em um pipeline de pesquisa cognitiva do Azure Cognitive Search. Esta habilidade está atualmente em visualização pública.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 8674438032ebd925296c95e9ffa0a2a0b95322f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369770"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>Habilidade cognitiva de procurar entidades personalizadas (Visualização)

> [!IMPORTANT] 
> Esta habilidade está atualmente em visualização pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Atualmente, não há suporte a portal ou .NET SDK.

A habilidade **de Pesquisa de Entidade personalizada** procura texto de uma lista personalizada de palavras e frases definida pelo usuário. Usando esta lista, ele rotula todos os documentos com quaisquer entidades correspondentes. A habilidade também suporta um grau de correspondência difusa que pode ser aplicada para encontrar correspondências que são semelhantes, mas não muito exatas.  

Essa habilidade não está vinculada a uma API de Serviços Cognitivos e pode ser usada gratuitamente durante o período de pré-visualização. Você ainda deve [anexar um recurso de Serviços Cognitivos,](https://docs.microsoft.com/azure/search/cognitive-search-attach-cognitive-services)no entanto, para substituir o limite de enriquecimento diário. O limite diário se aplica ao acesso gratuito aos Serviços Cognitivos quando acessado através da Pesquisa Cognitiva do Azure.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.CustomEntityLookupSkill 

## <a name="data-limits"></a>Limites de dados
+ O tamanho máximo do registro de entrada suportado é de 256 MB. Se você precisar quebrar seus dados antes de enviá-los para a habilidade de pesquisa de entidade sucursada personalizada, considere usar a [habilidade Text Split](cognitive-search-skill-textsplit.md).
+ A tabela de definição máxima de entidades suportada é de 10 MB se for fornecida usando o parâmetro *entidadesDefitionUri.* 
+ Se as entidades forem definidas inline, usando o parâmetro *inlineEntitiesDefinition,* o tamanho máximo suportado é de 10 KB.

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| entidadesDefinitionUri    | Caminho para um arquivo JSON ou CSV contendo todo o texto-alvo a ser consopelado. Essa definição de entidade é lida no início de uma execução de indexador; quaisquer atualizações para este arquivo no meio da execução não serão realizadas até que as corridas subseqüentes. Esta configuração deve estar acessível em HTTPS. Consulte o formato [de definição de entidade personalizado](#custom-entity-definition-format) abaixo para o esquema CSV ou JSON esperado.|
|inlineEntitiesDefinition | Definições de entidade Inline JSON. Este parâmetro substitui as entidadesDefinitionUri parâmetro se presente. Não mais de 10 KB de configuração podem ser fornecidos em linha. Consulte [A definição de entidade personalizada](#custom-entity-definition-format) abaixo para o esquema JSON esperado. |
|defaultLanguageCode |    (Opcional) Código de idioma do texto de entrada usado para tokenizar e delinear texto de entrada. Os seguintes idiomas `da, de, en, es, fi, fr, it, ko, pt`são suportados: . O padrão é`en`inglês ( ). Se você passar um formato languagecode-countrycode, somente a parte languagecode do formato é usada.  |


## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | Descrição                   |
|---------------|-------------------------------|
| text          | O texto para analisar.          |
| languageCode    | Opcional. O padrão é `"en"`.  |


## <a name="skill-outputs"></a>Saídas de habilidades


| Nome de saída      | Descrição                   |
|---------------|-------------------------------|
| entidades | Uma série de objetos que contêm informações sobre as correspondências encontradas e metadados relacionados. Cada uma das entidades identificadas pode conter os seguintes campos:  <ul> <li> *nome*: A entidade de nível superior identificada. A entidade representa a forma "normalizada". </li> <li> *id*: Um identificador exclusivo para a entidade definido pelo usuário no "Formato de Definição de Entidade Personalizada".</li> <li> *descrição*: Descrição da entidade conforme definido pelo usuário no "Formato de Definição de Entidade Personalizada". </li> <li> *tipo:* Tipo de entidade conforme definido pelo usuário no "Formato de Definição de Entidade Personalizada".</li> <li> *subtipo:* Subtipo de entidade definido pelo usuário no "Formato de Definição de Entidade Personalizada".</li>  <li> *correspondências*: Coleção que descreve cada uma das partidas para essa entidade no texto de origem. Cada partida terá os seguintes membros: </li> <ul> <li> *texto*: O texto bruto corresponde ao documento de origem. </li> <li> *deslocamento*: O local onde a correspondência foi encontrada no texto. </li> <li> *comprimento:* O comprimento do texto combinado. </li> <li> *matchDistance*: O número de caracteres diferentes desta partida era do nome original da entidade ou alias.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>Formato de definição de entidade personalizado

Existem 3 maneiras diferentes de fornecer a lista de entidades personalizadas para a habilidade de Procurar entidades personalizadas. Você pode fornecer a lista em um . Arquivo CSV, a . Arquivo JSON ou como uma definição inline como parte da definição de habilidade.  

Se o arquivo de definição for um . CSV ou . Arquivo JSON, o caminho do arquivo precisa ser fornecido como parte do parâmetro *entidadesDefitionUri.* Neste caso, o arquivo é baixado uma vez no início de cada execução do indexador. O arquivo deve estar acessível desde que o indexador seja executado. Além disso, o arquivo deve ser codificado UTF-8.

Se a definição for fornecida inline, ela deve ser fornecida tão inline quanto o conteúdo do parâmetro de habilidade *inlineEntitiesDefinition.* 

### <a name="csv-format"></a>Formato CSV

Você pode fornecer a definição das entidades personalizadas a serem procurar em um arquivo CSV (Comma-Separated Value, valor separado pela comuma) fornecendo o caminho para o arquivo e definindo-o no parâmetro de habilidade sacaneado *DefitionUri.* O caminho deve ser em um local https. O arquivo de definição pode ter até 10 MB de tamanho.

O formato CSV é simples. Cada linha representa uma entidade única, como mostrado abaixo:

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

Neste caso, existem três entidades que podem ser devolvidas como entidades encontradas (Bill Gates, Satya Nadella, Microsoft), mas serão identificadas se algum dos termos da linha (pseudônimos) for correspondido no texto. Por exemplo, se a string "William H. Gates" for encontrada em um documento, uma correspondência para a entidade "Bill Gates" será devolvida.

### <a name="json-format"></a>Formato JSON

Você pode fornecer a definição das entidades personalizadas para procurar em um arquivo JSON também. O formato JSON lhe dá um pouco mais de flexibilidade, pois permite definir regras de correspondência por termo. Por exemplo, você pode especificar a distância de correspondência difusa (distância Damerau-Levenshtein) para cada termo ou se a correspondência deve ser sensível a maiúsculas ou não. 

 Assim como nos arquivos CSV, você precisa fornecer o caminho para o arquivo JSON e defini-lo no parâmetro de habilidade *satisfatoitário de entidadesDefitionUri.* O caminho deve ser em um local https. O arquivo de definição pode ter até 10 MB de tamanho.

A definição mais básica da lista de entidades personalizadas do JSON pode ser uma lista de entidades a combinar:

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

Um exemplo mais complexo de uma definição JSON pode fornecer opcionalmente o id, descrição, tipo e subtipo de cada entidade - bem como outros *pseudônimos*. Se um termo de alias for compatível, a entidade também será devolvida:

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

As tabelas abaixo descrevem com mais detalhes os diferentes parâmetros de configuração que você pode definir ao definir as entidades a combinar:

|  Nome do campo  |        Descrição  |
|--------------|----------------------|
| name | O descritor de entidade de alto nível. As correspondências na saída de habilidade serão agrupadas por este nome, e devem representar a forma "normalizada" do texto encontrado.  |
| descrição  | (Opcional) Este campo pode ser usado como um passo a passo para metadados personalizados sobre os textos combinados. O valor deste campo aparecerá a cada partida de sua entidade na saída de habilidade. |
| type | (Opcional) Este campo pode ser usado como um passo a passo para metadados personalizados sobre os textos combinados. O valor deste campo aparecerá a cada partida de sua entidade na saída de habilidade. |
| subtype | (Opcional) Este campo pode ser usado como um passo a passo para metadados personalizados sobre os textos combinados. O valor deste campo aparecerá a cada partida de sua entidade na saída de habilidade. |
| id | (Opcional) Este campo pode ser usado como um passo a passo para metadados personalizados sobre os textos combinados. O valor deste campo aparecerá a cada partida de sua entidade na saída de habilidade. |
| Casesensitive | (Opcional) Padrão para falso. Valor booleano denotando se as comparações com o nome da entidade devem ser sensíveis ao invólucro do personagem. Exemplo de caso insensíveis de correspondências de "Microsoft" pode ser: microsoft, microSoft, MICROSOFT |
| fuzzyEditDistance | (Opcional) Padrão para 0. Valor máximo de 5. Denota o número aceitável de caracteres divergentes que ainda constituiriam uma correspondência com o nome da entidade. A menor neblina possível para qualquer partida é devolvida.  Por exemplo, se a distância de edição estiver definida como 3, "Windows 10" ainda corresponderia a "Windows", "Windows10" e "windows 7". <br/> Quando a sensibilidade do caso é definida como falsa, as diferenças de caso NÃO contam para a tolerância à difusão, mas de outra forma contam. |
| defaultCaseSensitive | (Opcional) Altera o valor de sensibilidade do caso padrão para esta entidade. Ele será usado para alterar o valor padrão de todos os aliases caseValors sensíveis. |
| padrãoFuzzyEditDistance | (Opcional) Altera o valor padrão de distância de edição fuzzy para esta entidade. Ele pode ser usado para alterar o valor padrão de todos os valores fuzzyEditDistance de todos os aliases fuzzyEditDistance. |
| aliases | (Opcional) Uma matriz de objetos complexos que podem ser usados para especificar ortografias alternativas ou sinônimos para o nome da entidade raiz. |

| Propriedades de alias | Descrição |
|------------------|-------------|
| text  | A ortografia alternativa ou representação de algum nome de entidade alvo.  |
| Casesensitive | (Opcional) Age da mesma forma que o parâmetro "caseSensitive" da entidade raiz acima, mas se aplica apenas a este pseudônimo. |
| fuzzyEditDistance | (Opcional) Age da mesma forma que o parâmetro raiz da entidade "fuzzyEditDistance" acima, mas se aplica apenas a este alias. |


### <a name="inline-format"></a>Formato inline

Em alguns casos, pode ser mais conveniente fornecer a lista de entidades personalizadas para combinar diretamente com a definição de habilidade. Nesse caso, você pode usar um formato JSON semelhante ao descrito acima, mas é inforrado na definição de habilidade.
Somente configurações com menos de 10 KB (tamanho serializado) podem ser definidas inline. 

##    <a name="sample-definition"></a>Definição de exemplo

Uma definição de habilidade de amostra usando um formato inline é mostrada abaixo:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```
Alternativamente, se você decidir fornecer um ponteiro para o arquivo de definição de entidades, uma definição de habilidade de amostra usando o formato entidadesDefinitionUri é mostrada abaixo:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
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
             "text": "The company microsoft was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Saída de exemplo

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>Erros e avisos

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>Aviso: Atingiu a capacidade máxima para partidas, pulando todas as outras partidas duplicadas.

Este aviso será emitido se o número de correspondências detectadas for maior do que o máximo permitido. Neste caso, vamos parar de incluir partidas duplicadas. Se isso for inaceitável para você, por favor, registre um bilhete de [suporte](https://ms.portal.azure.com/#create/Microsoft.Support) para que possamos ajudá-lo com o seu caso de uso individual.

## <a name="see-also"></a>Confira também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Habilidade de reconhecimento de entidades (para procurar entidades conhecidas)](cognitive-search-skill-entity-recognition.md)
