---
title: Introdução ao repositório de conhecimento (versão prévia)
titleSuffix: Azure Cognitive Search
description: Envie documentos aprimorados para o armazenamento do Azure, onde você pode exibir, remodelar e consumir documentos aprimorados no Azure Pesquisa Cognitiva e em outros aplicativos. Esse recurso está em uma versão prévia.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0ad780c04954c09ddfd432b3c7de3dc65f0841bf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78942991"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Introdução aos repositórios de conhecimento na Pesquisa Cognitiva do Azure

> [!IMPORTANT] 
> O repositório de conhecimento está atualmente em versão prévia pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece recursos de versão prévia. Atualmente, há suporte limitado ao portal e não há suporte para o SDK do .NET.

O repositório de conhecimento é um recurso do Azure Pesquisa Cognitiva que persiste a saída de um [pipeline de enriquecimento de ia](cognitive-search-concept-intro.md) para análise independente ou processamento downstream. Um *documento enriquecido* é a saída de um pipeline, criado com base no conteúdo que foi extraído, estruturado e analisado usando processos de IA. Em um pipeline de IA padrão, documentos aprimorados são transitórios, usados apenas durante a indexação e descartados em seguida. Com o repositório de conhecimento, os documentos enriquecidos são preservados. 

Se você usou habilidades cognitivas no passado, já sabe que o *habilidades* move um documento por uma sequência de aprimoramentos. O resultado pode ser um índice de pesquisa ou projeções (uma novidade nesta versão prévia) em um repositório de conhecimento. As duas saídas, índice de pesquisa e repositório de conhecimento, são produtos do mesmo pipeline; derivado das mesmas entradas, mas resultando na saída que é estruturada, armazenada e usada de maneiras muito diferentes.

Fisicamente, um repositório de conhecimento é um [Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview), podendo ser o Armazenamento de Tabelas do Azure, o Armazenamento de Blobs do Azure ou ambos. Qualquer ferramenta ou processo que pode se conectar ao Armazenamento do Azure pode consumir o conteúdo de um repositório de conhecimento.

![Diagrama do repositório de conhecimento no diagrama](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Diagrama do repositório de conhecimento no diagrama")

## <a name="benefits-of-knowledge-store"></a>Vantagens do repositório de dados de conhecimento

Uma loja de conhecimento fornece estrutura, contexto e conteúdo real, obtidas por meio de arquivos de dados não estruturados e semiestruturados, como BLOBs, arquivos de imagem que passaram por análise ou até mesmo dados estruturados, remodelados em novos formulários. Em um [passo a](knowledge-store-create-rest.md)passo, você pode ver em primeira mão como um documento de JSON denso é particionado em subestruturações, reconstituído em novas estruturas e disponibilizado para processos downstream, como o aprendizado de máquina e cargas de trabalho de ciência de dados.

Embora seja útil ver o que um pipeline de enriquecimento de ia pode produzir, o potencial real de uma loja de conhecimento é a capacidade de remodelar dados. Você pode começar com um conjunto de qualificações básicas e depois iterar sobre ele para adicionar níveis crescentes de estrutura, que podem ser combinados em novas estruturas e consumidos em outros aplicativos além da Pesquisa Cognitiva do Azure.

Se enumeradas, as vantagens do repositório de dados de conhecimento incluem o seguinte:

+ Consumir documentos enriquecidos em [ferramentas de relatórios e análise](#tools-and-apps) que não sejam de pesquisa. Power BI com Power Query é uma opção atraente, mas qualquer ferramenta ou aplicativo que possa se conectar ao armazenamento do Azure pode efetuar pull de uma loja de conhecimento que você criar.

+ Refinar um pipeline de indexação por IA durante as etapas de depuração e definições de conjuntos de qualificações. Um repositório de dados de conhecimento mostra o produto de uma definição de conjuntos de qualificações em um pipeline de indexação por IA. Você pode usar esses resultados para criar um conjunto de qualificações enriquecido já que pode ver exatamente como são os enriquecimentos. Você pode usar [Gerenciador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) no armazenamento do Azure para exibir o conteúdo de uma loja de conhecimento.

+ Reformatar os dados. A reformatação é codificada em conjuntos de qualificações. No entanto, a questão é que um conjunto de qualificações já pode oferecer essa capacidade. A [habilidade do Shaper](cognitive-search-skill-shaper.md) na Pesquisa Cognitiva do Azure foi estendida para acomodar essa tarefa. A reformatação permite definir uma projeção que se alinha com o uso pretendido dos dados enquanto preserva as relações.

> [!Note]
> Novidade para aprimoramento de ia e habilidades cognitivas? A Pesquisa Cognitiva do Azure integra-se aos recursos de Visão de Computação e Idioma nos Serviços Cognitivos para extrair e enriquecer os dados de origem usando OCR (Reconhecimento Óptico de Caracteres) em arquivos de imagem, reconhecimento de entidade e extração de frases-chave de arquivos de texto e muito mais. Para obter mais informações, confira [Enriquecimento de IA na Pesquisa Cognitiva do Azure](cognitive-search-concept-intro.md).

## <a name="physical-storage"></a>Armazenamento físico

A expressão física de uma loja de conhecimento é articulada por `projections` meio do elemento `knowledgeStore` de uma definição em um Skill. A projeção define uma estrutura da saída para que ela corresponda ao uso pretendido.

As projeções podem ser articuladas como tabelas, objetos ou arquivos.

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

O tipo de projeção que você especificar nesta estrutura determina o tipo de armazenamento usado pela loja de conhecimento.

+ O armazenamento de tabela é usado quando `tables`você define. Defina uma projeção de tabela quando precisar de estruturas de relatório tabular para entradas em ferramentas analíticas ou exportar como quadros de dados para outros armazenamentos de dados. Você pode especificar vários `tables` para obter um subconjunto ou uma seção cruzada de documentos aprimorados. No mesmo grupo de projeção, as relações de tabela são preservadas para que você possa trabalhar com todas elas.

+ O armazenamento de BLOBs é usado `objects` quando `files`você define ou. A representação física de um `object` é uma estrutura de JSON hierárquica que representa um documento aprimorado. Um `file` é uma imagem extraída de um documento, transferida intacta para o armazenamento de BLOBs.

Um único objeto de projeção contém um `tables`conjunto `objects`de `files`,, e para muitos cenários, a criação de uma projeção pode ser suficiente. 

No entanto, é possível criar vários conjuntos de `table` - `object` - `file` projeções, e você pode fazer isso se desejar diferentes relações de dados. Dentro de um conjunto, os dados estão relacionados, supondo que essas relações existam e possam ser detectadas. Se você criar conjuntos adicionais, os documentos em cada grupo nunca serão relacionados. Um exemplo de uso de vários grupos de projeção pode ser se você quiser que os mesmos dados sejam projetados para uso com seu sistema online e ele precisar ser representado de uma maneira específica, você também deseja que os mesmos dados sejam projetados para uso em um pipeline de ciência de dados que seja representado de maneira diferente.

## <a name="requirements"></a>Requisitos 

O [armazenamento do Azure](https://docs.microsoft.com/azure/storage/) é necessário. Ele fornece armazenamento físico. Você pode usar o armazenamento de BLOBs, o armazenamento de tabelas ou ambos. O armazenamento de BLOBs é usado para documentos inaprimorados intactos, geralmente quando a saída vai para processos de downstream. O armazenamento de tabela é para fatias de documentos aprimorados, normalmente usados para análise e relatórios.

O [qualificable](cognitive-search-working-with-skillsets.md) é necessário. Ele contém a `knowledgeStore` definição e determina a estrutura e a composição de um documento aprimorado. Você não pode criar um repositório de conhecimento usando um conconhecedor de qualificações vazio. Você deve ter pelo menos uma habilidade em um skillset.

O [indexador](search-indexer-overview.md) é necessário. Um configurador de qualificações é invocado por um indexador, que orienta a execução. Os indexadores vêm com seu próprio conjunto de requisitos e atributos. Vários desses atributos têm um rumo direto em uma loja de conhecimento:

+ Os indexadores exigem uma [fonte de dados do Azure com suporte](search-indexer-overview.md#supported-data-sources) (o pipeline que, por fim, cria o repositório de conhecimento começa obtendo dados de uma fonte com suporte no Azure). 

+ Os indexadores exigem um índice de pesquisa. Um indexador requer que você forneça um esquema de índice, mesmo que você nunca planeje usá-lo. Um índice mínimo tem um campo de cadeia de caracteres designado como a chave.

+ Os indexadores fornecem mapeamentos de campo opcionais, usados para o alias de um campo de origem para um campo de destino. Se um mapeamento de campo padrão precisar de modificação (para usar um nome ou tipo diferente), você poderá criar um [mapeamento de campo](search-indexer-field-mappings.md) dentro de um indexador. Para a saída da loja de conhecimento, o destino pode ser um campo em um objeto ou tabela de BLOB.

+ Os indexadores têm agendas e outras propriedades, como mecanismos de detecção de alteração fornecidos por várias fontes de dados, também podem ser aplicados a uma loja de conhecimento. Por exemplo, você pode [agendar](search-howto-schedule-indexers.md) o enriquecimento em intervalos regulares para atualizar o conteúdo. 

## <a name="how-to-create-a-knowledge-store"></a>Como criar uma loja de conhecimento

Para criar a loja de conhecimento, use o portal ou a API REST`api-version=2019-05-06-Preview`de visualização ().

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

O assistente de **importação de dados** inclui opções para criar uma loja de conhecimento. Para a exploração inicial, [crie seu primeiro armazenamento de conhecimento em quatro etapas](knowledge-store-connect-power-bi.md).

1. Selecione uma fonte de dados com suporte.

1. Especificar enriquecimento: anexe um recurso, selecione habilidades e especifique uma loja de conhecimento. 

1. Crie um esquema de índice. O assistente requer e pode inferir um para você.

1. Execute o assistente. A extração, o enriquecimento e o armazenamento ocorrem nesta última etapa.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>Usar a API REST de criação e a versão prévia

Um `knowledgeStore` é definido dentro de um [qualificable](cognitive-search-working-with-skillsets.md), que, por sua vez, é invocado por um [indexador](search-indexer-overview.md). Durante o enriquecimento, o Azure Pesquisa Cognitiva cria um espaço em sua conta de armazenamento do Azure e projeta os documentos aprimorados como BLOBs ou tabelas, dependendo de sua configuração.

Atualmente, a API REST de visualização é o único mecanismo pelo qual você pode criar uma loja de conhecimento programaticamente. Uma maneira fácil de explorar é [criar sua primeira loja de conhecimento usando o postmaster e a API REST](knowledge-store-create-rest.md).

O conteúdo de referência para esse recurso de visualização está localizado na seção [referência de API](#kstore-rest-api) deste artigo. 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Como se conectar com ferramentas e aplicativos

Após os enriquecimentos estarem no armazenamento, qualquer ferramenta ou tecnologia que se conecte ao armazenamento de Blobs ou de Tabelas do Azure poderá ser usada para explorar, analisar ou consumir o conteúdo. A lista a seguir é um começo:

+ [Gerenciador de Armazenamento](knowledge-store-view-storage-explorer.md) para exibir o conteúdo e a estrutura do documento enriquecido. Considere isso como sua ferramenta de linha de base para visualizar o conteúdo do repositório de dados de conhecimento.

+ [Power bi](knowledge-store-connect-power-bi.md) para relatório e análise. 

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) para manipular mais.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>Reference API

A versão `2019-05-06-Preview` da API REST fornece armazenamento de conhecimento por meio de definições adicionais no habilidades. Além da referência, consulte [criar um repositório de conhecimento usando o postmaster](knowledge-store-create-rest.md) para obter detalhes sobre como chamar as APIs.

+ [Criar conjunto de habilidades (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-skillset) 
+ [Atualizar o Qualificable (API-Version = 2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) 


## <a name="next-steps"></a>Próximas etapas

O repositório de conhecimento oferece persistência de documentos enriquecidos, que é útil ao criar um conjunto de habilidades ou ao criar novas estruturas e conteúdo para consumo por qualquer aplicativo cliente capaz de acessar uma conta de Armazenamento do Azure.

A abordagem mais simples para a criação de documentos aprimorados é [por meio do portal](knowledge-store-create-portal.md), mas você também pode usar o postmaster e a API REST, que é mais útil se você quiser obter informações sobre como os objetos são criados e referenciados.

> [!div class="nextstepaction"]
> [Criar uma loja de conhecimento usando o postmaster e o REST](knowledge-store-create-rest.md)

Para saber mais sobre projeções, os recursos e como [defini-los em um](knowledge-store-projection-overview.md) degrau de habilidade

> [!div class="nextstepaction"]
> [Projeções em uma loja de conhecimento](knowledge-store-projection-overview.md)

Para obter um tutorial que aborda conceitos de projeções avançadas como divisão, modelagem embutida e relações, comece com [definir projeções em uma loja de conhecimento](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definir projeções em uma loja de conhecimento](knowledge-store-projections-examples.md)