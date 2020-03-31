---
title: Introdução ao repositório de conhecimento (versão prévia)
titleSuffix: Azure Cognitive Search
description: Envie documentos enriquecidos para o Azure Storage, onde você pode visualizar, remodelar e consumir documentos enriquecidos na Pesquisa Cognitiva do Azure e em outros aplicativos. Esse recurso está em uma versão prévia.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0ad780c04954c09ddfd432b3c7de3dc65f0841bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942991"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Introdução aos repositórios de conhecimento na Pesquisa Cognitiva do Azure

> [!IMPORTANT] 
> O repositório de conhecimento está atualmente em versão prévia pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece recursos de versão prévia. Atualmente, há suporte limitado ao portal e não há suporte para o SDK do .NET.

O knowledge store é uma característica do Azure Cognitive Search que persiste na saída de um pipeline de enriquecimento de [IA](cognitive-search-concept-intro.md) para análise independente ou processamento a jusante. Um *documento enriquecido* é a saída de um pipeline, criado com base no conteúdo que foi extraído, estruturado e analisado usando processos de IA. Em um pipeline de IA padrão, documentos aprimorados são transitórios, usados apenas durante a indexação e descartados em seguida. Com o repositório de conhecimento, os documentos enriquecidos são preservados. 

Se você já usou habilidades cognitivas no passado, você já sabe que *habilidades* movem um documento através de uma seqüência de enriquecimentos. O resultado pode ser um índice de pesquisa ou projeções (uma novidade nesta versão prévia) em um repositório de conhecimento. As duas saídas, índice de pesquisa e armazenamento de conhecimento, são produtos do mesmo pipeline; derivado das mesmas entradas, mas resultando em saída estruturada, armazenada e usada de maneiras muito diferentes.

Fisicamente, um repositório de conhecimento é um [Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview), podendo ser o Armazenamento de Tabelas do Azure, o Armazenamento de Blobs do Azure ou ambos. Qualquer ferramenta ou processo que pode se conectar ao Armazenamento do Azure pode consumir o conteúdo de um repositório de conhecimento.

![Diagrama do repositório de conhecimento no diagrama](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Diagrama do repositório de conhecimento no diagrama")

## <a name="benefits-of-knowledge-store"></a>Vantagens do repositório de dados de conhecimento

Uma loja de conhecimento oferece estrutura, contexto e conteúdo real - coletados de arquivos de dados não estruturados e semiestruturados, como blobs, arquivos de imagem que passaram por análise, ou mesmo dados estruturados, remodelados em novas formas. Em um [passo-a-passo](knowledge-store-create-rest.md)passo, você pode ver em primeira mão como um documento JSON denso é dividido em subestruturas, reconstituído em novas estruturas e disponibilizado para processos a jusante, como aprendizado de máquina e cargas de trabalho de ciência de dados.

Embora seja útil ver o que um pipeline de enriquecimento de IA pode produzir, o real potencial de uma loja de conhecimento é a capacidade de remodelar dados. Você pode começar com um conjunto de qualificações básicas e depois iterar sobre ele para adicionar níveis crescentes de estrutura, que podem ser combinados em novas estruturas e consumidos em outros aplicativos além da Pesquisa Cognitiva do Azure.

Se enumeradas, as vantagens do repositório de dados de conhecimento incluem o seguinte:

+ Consumir documentos enriquecidos em [ferramentas de relatórios e análise](#tools-and-apps) que não sejam de pesquisa. Power BI com Power Query é uma escolha convincente, mas qualquer ferramenta ou aplicativo que possa se conectar ao Azure Storage pode puxar de uma loja de conhecimento que você cria.

+ Refinar um pipeline de indexação por IA durante as etapas de depuração e definições de conjuntos de qualificações. Um repositório de dados de conhecimento mostra o produto de uma definição de conjuntos de qualificações em um pipeline de indexação por IA. Você pode usar esses resultados para criar um conjunto de qualificações enriquecido já que pode ver exatamente como são os enriquecimentos. Você pode usar [o Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) no Azure Storage para visualizar o conteúdo de um armazenamento de conhecimento.

+ Reformatar os dados. A reformatação é codificada em conjuntos de qualificações. No entanto, a questão é que um conjunto de qualificações já pode oferecer essa capacidade. A [habilidade do Shaper](cognitive-search-skill-shaper.md) na Pesquisa Cognitiva do Azure foi estendida para acomodar essa tarefa. A reformatação permite definir uma projeção que se alinha com o uso pretendido dos dados enquanto preserva as relações.

> [!Note]
> Novo em enriquecimento de IA e habilidades cognitivas? A Pesquisa Cognitiva do Azure integra-se aos recursos de Visão de Computação e Idioma nos Serviços Cognitivos para extrair e enriquecer os dados de origem usando OCR (Reconhecimento Óptico de Caracteres) em arquivos de imagem, reconhecimento de entidade e extração de frases-chave de arquivos de texto e muito mais. Para obter mais informações, confira [Enriquecimento de IA na Pesquisa Cognitiva do Azure](cognitive-search-concept-intro.md).

## <a name="physical-storage"></a>Armazenamento físico

A expressão física de um armazém `projections` de `knowledgeStore` conhecimento é articulada através do elemento de uma definição em um Skillset. A projeção define uma estrutura da saída para que corresponda ao seu uso pretendido.

Projeções podem ser articuladas como tabelas, objetos ou arquivos.

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

O tipo de projeção especificada nesta estrutura determina o tipo de armazenamento usado pelo armazenamento de conhecimento.

+ O armazenamento de mesa `tables`é usado quando você define . Defina uma projeção de tabela quando precisar de estruturas de relatórios tabulares para entradas em ferramentas analíticas ou exportar como quadros de dados para outros armazenamentos de dados. Você pode `tables` especificar vários para obter um subconjunto ou seção transversal de documentos enriquecidos. Dentro do mesmo grupo de projeção, as relações de tabela são preservadas para que você possa trabalhar com todos eles.

+ O armazenamento blob é `objects` `files`usado quando você define ou . A representação `object` física de um é uma estrutura Hierárquica JSON que representa um documento enriquecido. A `file` é uma imagem extraída de um documento, transferida intacta para o armazenamento Blob.

Um único objeto de `tables`projeção `files`contém um conjunto de , `objects`e para muitos cenários, criar uma projeção pode ser suficiente. 

No entanto, é possível `table` - `object` - `file` criar vários conjuntos de projeções, e você pode fazer isso se quiser diferentes relações de dados. Dentro de um conjunto, os dados estão relacionados, assumindo que essas relações existem e podem ser detectadas. Se você criar conjuntos adicionais, os documentos em cada grupo nunca serão relacionados. Um exemplo de uso de vários grupos de projeção pode ser se você quiser os mesmos dados projetados para uso com seu sistema on-line e ele precisa ser representado de forma específica, você também quer os mesmos dados projetados para uso em um pipeline de ciência de dados que é representado Diferente.

## <a name="requirements"></a>Requisitos 

[O Armazenamento Azure](https://docs.microsoft.com/azure/storage/) é necessário. Ele fornece armazenamento físico. Você pode usar armazenamento Blob, armazenamento de mesa ou ambos. O armazenamento blob é usado para documentos enriquecidos intactos, geralmente quando a saída está indo para processos a jusante. O armazenamento de mesa é para fatias de documentos enriquecidos, comumente usados para análise e emissão de relatórios.

[Skillset](cognitive-search-working-with-skillsets.md) é necessário. Contém a `knowledgeStore` definição, e determina a estrutura e composição de um documento enriquecido. Você não pode criar um armazenamento de conhecimento usando um conjunto de habilidades vazias. Você deve ter pelo menos uma habilidade em uma habilidade.

[Indexador](search-indexer-overview.md) é necessário. Um skillset é invocado por um indexador, que impulsiona a execução. Os indexadores vêm com seu próprio conjunto de requisitos e atributos. Vários desses atributos têm uma influência direta em uma loja de conhecimento:

+ Os indexadores requerem uma [fonte de dados azure suportada](search-indexer-overview.md#supported-data-sources) (o pipeline que finalmente cria o armazenamento de conhecimento começa puxando dados de uma fonte suportada no Azure). 

+ Os indexadores requerem um índice de pesquisa. Um indexador exige que você forneça um esquema de índice, mesmo que você nunca planeje usá-lo. Um índice mínimo tem um campo de string, designado como a chave.

+ Os indexadores fornecem mapeamentos de campo opcionais, usados para usar um campo de origem para um campo de destino. Se um mapeamento de campo padrão precisar de modificação (para usar um nome ou tipo diferente), você pode criar um [mapeamento de campo](search-indexer-field-mappings.md) dentro de um indexador. Para a saída do armazenamento de conhecimento, o destino pode ser um campo em um objeto ou tabela blob.

+ Os indexadores têm cronogramas e outras propriedades, como mecanismos de detecção de alterações fornecidos por várias fontes de dados, também podem ser aplicados a um armazenamento de conhecimento. Por exemplo, você pode [agendar](search-howto-schedule-indexers.md) o enriquecimento em intervalos regulares para atualizar o conteúdo. 

## <a name="how-to-create-a-knowledge-store"></a>Como criar uma loja de conhecimento

Para criar o armazenamento de conhecimento, use`api-version=2019-05-06-Preview`o portal ou a aPI REST ( ).

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

O **assistente de dados Importação** inclui opções para criar um armazenamento de conhecimento. Para exploração inicial, [crie sua primeira loja de conhecimento em quatro etapas.](knowledge-store-connect-power-bi.md)

1. Selecione uma fonte de dados suportada.

1. Especifique o enriquecimento: anexe um recurso, selecione habilidades e especifique um armazenamento de conhecimento. 

1. Crie um esquema de índice. O mago exige e pode inferir um para você.

1. Executa o mago. Extração, enriquecimento e armazenamento ocorrem nesta última etapa.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>Use Create Skillset e a API REST de visualização

A `knowledgeStore` é definido dentro de um [skillset](cognitive-search-working-with-skillsets.md), que por sua vez é invocado por um [indexador](search-indexer-overview.md). Durante o enriquecimento, o Azure Cognitive Search cria um espaço em sua conta de armazenamento Do Azure e projeta os documentos enriquecidos como blobs ou em tabelas, dependendo da sua configuração.

Atualmente, a API REST de pré-visualização é o único mecanismo pelo qual você pode criar uma loja de conhecimento programática. Uma maneira fácil de explorar é [criar sua primeira loja de conhecimento usando o Carteiro e a API REST](knowledge-store-create-rest.md).

O conteúdo de referência para este recurso de visualização está localizado na seção de referência da [API](#kstore-rest-api) deste artigo. 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Como se conectar com ferramentas e aplicativos

Após os enriquecimentos estarem no armazenamento, qualquer ferramenta ou tecnologia que se conecte ao armazenamento de Blobs ou de Tabelas do Azure poderá ser usada para explorar, analisar ou consumir o conteúdo. A lista a seguir é um começo:

+ [Gerenciador de Armazenamento](knowledge-store-view-storage-explorer.md) para exibir o conteúdo e a estrutura do documento enriquecido. Considere isso como sua ferramenta de linha de base para visualizar o conteúdo do repositório de dados de conhecimento.

+ [BI de potência](knowledge-store-connect-power-bi.md) para relatórios e análises. 

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) para manipular mais.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>Reference API

A versão `2019-05-06-Preview` da API REST fornece armazenamento de conhecimento através de definições adicionais sobre skillsets. Além da referência, consulte [Criar uma loja de conhecimento usando o Carteiro](knowledge-store-create-rest.md) para obter detalhes sobre como chamar as APIs.

+ [Criar conjunto de habilidades (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-skillset) 
+ [Update Skillset (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) 


## <a name="next-steps"></a>Próximas etapas

O repositório de conhecimento oferece persistência de documentos enriquecidos, que é útil ao criar um conjunto de habilidades ou ao criar novas estruturas e conteúdo para consumo por qualquer aplicativo cliente capaz de acessar uma conta de Armazenamento do Azure.

A abordagem mais simples para criar documentos enriquecidos é [através do portal,](knowledge-store-create-portal.md)mas você também pode usar a API do Carteiro e REST, que é mais útil se você quiser uma visão de como os objetos são criados e referenciados.

> [!div class="nextstepaction"]
> [Crie uma loja de conhecimento usando Carteiro e REST](knowledge-store-create-rest.md)

Para saber mais sobre projeções, as capacidades e como você [as define em um skillset](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projeções em uma loja de conhecimento](knowledge-store-projection-overview.md)

Para um tutorial que abrange conceitos avançados de projeções como fatiamento, modelagem inline e relacionamentos, comece com [projeções definidas em uma loja de conhecimento](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definir projeções em uma loja de conhecimento](knowledge-store-projections-examples.md)