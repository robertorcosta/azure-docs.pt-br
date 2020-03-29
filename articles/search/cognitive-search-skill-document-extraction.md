---
title: Habilidade cognitiva de extração de documentos (visualização)
titleSuffix: Azure Cognitive Search
description: Extrai conteúdo de um arquivo dentro do pipeline de enriquecimento. Esta habilidade está atualmente em visualização pública.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: 0f67caad03c4ebd1cf8f3721f377d8362219016a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76837724"
---
# <a name="document-extraction-cognitive-skill"></a>Habilidade cognitiva de extração de documentos

> [!IMPORTANT] 
> Esta habilidade está atualmente em visualização pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Atualmente, não há suporte a portal ou .NET SDK.

A habilidade **de extração** de documentos extrai conteúdo de um arquivo dentro do pipeline de enriquecimento. Isso permite que você aproveite a etapa de extração de documentos que normalmente acontece antes da execução de skillset com arquivos que podem ser gerados por outras habilidades.

> [!NOTE]
> À medida que expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, você precisará [anexar um recurso de Serviços Cognitivos faturável](cognitive-search-attach-cognitive-services.md). Os encargos se acumulam ao chamar APIs em Serviços Cognitivos e para extração de imagens como parte do estágio de quebra de documentos na indexação. Não há encargos para extração de texto em documentos.
>
> A execução de habilidades integradas é cobrada nos [preços pagos conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. Os preços de extração de imagem são descritos na [página de preços](https://go.microsoft.com/fwlink/?linkid=2042400).
## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Entradas            | Valores Permitidos | Descrição |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Definido `default` para extração de documentos de arquivos que não são texto puro ou json. Configurado `text` para melhorar o desempenho em arquivos de texto simples. Definido `json` para extrair conteúdo estruturado de arquivos json. Se `parsingMode` não for definido explicitamente, ele `default`será definido como . |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Definido `contentAndMetadata` para extrair todos os metadados e conteúdo textual de cada arquivo. Definido `allMetadata` para extrair apenas os [metadados específicos do tipo de conteúdo](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (por exemplo, metadados exclusivos para apenas arquivos .png). Se `dataToExtract` não for definido explicitamente, ele `contentAndMetadata`será definido como . |
| `configuration` | Veja abaixo. | Um dicionário de parâmetros opcionais que ajustam a forma como a extração do documento é realizada. Veja a tabela abaixo para obter descrições das propriedades de configuração suportadas. |

| Parâmetro de configuração   | Valores Permitidos | Descrição |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Configurado `none` para ignorar imagens incorporadas ou arquivos de imagem no conjunto de dados. Esse é o padrão. <br/>Para [análise de imagens usando habilidades cognitivas,](cognitive-search-concept-image-scenarios.md)definido para `generateNormalizedImages` ter a habilidade criar uma matriz de imagens normalizadas como parte da quebra de documentos. Esta ação `parsingMode` requer que `default` `dataToExtract` seja definida `contentAndMetadata`e definida como . Uma imagem normalizada refere-se ao processamento adicional resultando em uma saída de imagem uniforme, dimensionada e girada para promover uma renderização consistente quando forem incluídas imagens nos resultados da pesquisa visual (por exemplo, fotografias do mesmo tamanho em um controle de grafo, conforme visto na [Demonstração sobre JFK](https://github.com/Microsoft/AzureSearch_JFK_Files)). Essas informações são geradas para cada imagem quando você usa essa opção.  <br/>Se você `generateNormalizedImagePerPage`definir para , arquivos PDF serão tratados de forma diferente em vez de extrair imagens incorporadas, cada página será renderizada como uma imagem e normalizada de acordo.  Os tipos de arquivos não PDF `generateNormalizedImages` serão tratados da mesma forma que se foram definidos.
| `normalizedImageMaxWidth` | Qualquer inteiro entre 50-10000 | A largura máxima (em pixels) para as imagens normalizadas geradas. O padrão é 2000. | 
| `normalizedImageMaxHeight` | Qualquer inteiro entre 50-10000 | A altura máxima (em pixels) para as imagens normalizadas geradas. O padrão é 2000. |

> [!NOTE]
> O padrão de 2000 pixels para a largura e altura máximas das imagens normalizadas se baseia nos tamanhos máximos compatíveis com a [habilidade de OCR](cognitive-search-skill-ocr.md) e a [habilidade de análise de imagem](cognitive-search-skill-image-analysis.md). A [habilidade OCR](cognitive-search-skill-ocr.md) suporta uma largura máxima e altura de 4200 para línguas não inglesas, e 10000 para inglês.  Se você aumentar os limites máximos, o processamento pode falhar em imagens maiores, dependendo da definição de suas habilidades e da linguagem dos documentos. 
## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada     | Descrição |
|--------------------|-------------|
| file_data | O arquivo do que o conteúdo deve ser extraído. |

A entrada "file_data" deve ser um objeto definido da seguinte forma:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Este objeto de referência de arquivo pode ser gerado de uma das 3 maneiras:

 - Definindo `allowSkillsetToReadFileData` o parâmetro na definição do indexador como "verdadeiro".  Isso criará `/document/file_data` um caminho que representa os dados originais do arquivo baixados da sua fonte de dados blob. Este parâmetro só se aplica a dados no armazenamento Blob.

 - Definir `imageAction` o parâmetro na definição do indexador para um valor diferente de `none`.  Isso cria um conjunto de imagens que segue a convenção necessária para a `/document/normalized_images/*`entrada dessa habilidade se aprovada individualmente (ou seja).

 - Ter uma habilidade personalizada retornar um objeto json definido EXATAMENTE como acima.  O `$type` parâmetro deve ser `file` definido `data` exatamente e o parâmetro deve ser os dados de matriz de byte codificados da base 64 do conteúdo do arquivo.

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome de saída    | Descrição |
|--------------|-------------|
| content | O conteúdo textual do documento. |
| normalized_images | Quando `imageAction` o valor for definido `none`para outro valor, o novo campo *de normalized_images* conterá uma matriz de imagens. Consulte [a documentação para extração de imagem](cognitive-search-concept-image-scenarios.md) para obter mais detalhes sobre o formato de saída de cada imagem. |

##  <a name="sample-definition"></a>Definição de exemplo

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "content"
      },
      {
        "name": "normalized_images",
        "targetName": "normalized_images"
      }
    ]
  }
```

##  <a name="sample-input"></a>Entrada de exemplo

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
      }
    }
  ]
}
```


##  <a name="sample-output"></a>Saída de exemplo

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>Confira também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Como processar e extrair informações de imagens em cenários da pesquisa cognitiva](cognitive-search-concept-image-scenarios.md)