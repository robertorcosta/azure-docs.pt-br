---
title: Limites de serviço para níveis e skus
titleSuffix: Azure Cognitive Search
description: Limites de serviço utilizados para o planejamento de capacidade e limites máximos de solicitações e respostas para a Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 6ee339cb709a5d825b39b4accf294761c99ee41a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282972"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Limites de serviço no Azure Cognitive Search

Os limites máximos de armazenamento, cargas de trabalho e quantidades de índices e outros objetos dependem se você [fornece a Pesquisa Cognitiva Do Azure](search-create-service-portal.md) em níveis de preços **gratuitos,** **básicos,** **padrão**ou otimizados de **armazenamento.**

+ **Gratuito** é um serviço compartilhado multilocatário fornecido com sua assinatura do Azure. As solicitações de indexação e consulta são executadas em réplicas e partições que são usadas por outros inquilinos.

+ **O Basic** fornece recursos de computação dedicados para cargas de trabalho de produção em menor escala, mas compartilha alguma infra-estrutura de rede com outros inquilinos.

+ **O standard** é executado em máquinas dedicadas com mais capacidade de armazenamento e processamento em todos os níveis. Standard vem em quatro níveis: S1, S2, S3 e S3 HD.

+ **O armazenamento otimizado** é executado em máquinas dedicadas com mais armazenamento total, largura de banda de armazenamento e memória do que **o Standard**. O armazenamento otimizado vem em dois níveis: L1 e L2

> [!NOTE]
> A partir de 1º de julho, todos os níveis estão geralmente disponíveis, incluindo o nível Otimizado de armazenamento. Todos os preços podem ser encontrados na página [Detalhes de Preços.](https://azure.microsoft.com/pricing/details/search/)

  O S3 HD (S3 de Alta Densidade) foi projetado para cargas de trabalho específicas: [multilocatário](search-modeling-multitenant-saas-applications.md) e grandes quantidades de índices pequenos (um milhão de documentos por índice, três mil índices por serviço). Essa camada não fornece o [recurso de indexador](search-indexer-overview.md). No S3 HD, a ingestão de dados deve aproveitar a abordagem de push, usando chamadas à API para efetuar push de dados da origem para o índice. 

> [!NOTE]
> Um serviço é provisionado em uma camada específica. Saltar camadas para obter capacidade envolve o provisionamento de um novo serviço (não há nenhuma atualização local). Para obter mais informações, confira [Escolher uma camada ou SKU](search-sku-tier.md). Para saber mais sobre o ajuste de capacidade em um serviço já provisionado, consulte [Dimensionar níveis de recursos para cargas de trabalho de indexação e consulta](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Limites de assinatura
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limites de armazenamento
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Limites de índice

| Recurso | Grátis | Básico&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Índices máximos |3 |5 ou 15 |50 |200 |200 |1000 por partição ou 3000 por serviço |10 |10 |
| Campos simples máximos por índice |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Campos de coleta complexos máximos por índice |40 |40 |40 |40 |40 |40 |40 |40 |
| Elementos máximos em todas&nbsp;as coleções complexas por documento<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Profundidade máxima de campos complexos |10 |10 |10 |10 |10 |10 |10 |10 |
| Número máximo de [encarregados da sugestão](https://docs.microsoft.com/rest/api/searchservice/suggesters) por índice |1 |1 |1 |1 |1 |1 |1 |1 |
| Número máximo de [perfis de pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) por índice |100 |100 |100 |100 |100 |100 |100 |100 |
| Máximo de funções por perfil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Os serviços básicos criados antes de dezembro de 2017 têm limites mais baixos (5 em vez de 15) nos índices. A camada tipo Básico é a única SKU com um limite inferior de 100 campos por índice.

<sup>2</sup> Ter um número muito grande de elementos em coleções complexas por documento atualmente causa alta utilização de armazenamento. Esse é um problema conhecido. Enquanto isso, um limite de 3000 é um limite superior seguro para todos os níveis de serviço. Esse limite só é aplicado para operações de indexação que utilizam a versão aPI`2019-05-06`mais antiga disponível (GA) que suporta campos de tipo complexos ( ) em diante. Para não quebrar clientes que possam estar usando versões aPI de visualização anteriores (que suportam campos de tipo complexos), não vamos impor esse limite para operações de indexação que usam essas versões de API de visualização. Observe que as versões de visualização da API não devem ser usadas para cenários de produção e recomendamos que os clientes mudem para a versão mais recente da Ga API.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limites do documento 

A partir de outubro de 2018, não haverá mais limites de documentos para novos serviços criados em qualquer camada faturável (Básica, S1, S2, S3, S3 HD), em qualquer região. Embora a maioria das regiões tenha tido contagens ilimitadas de documentos desde novembro/dezembro de 2017, houve algumas regiões que continuaram a impor limites de documentos após essa data. Dependendo de quando e onde você criou um serviço de pesquisa, talvez esteja executando um serviço ainda sujeito a limites de documentos.

Para determinar se seu serviço tem limites de documentos, use a [API GET Service Statistics REST](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). Os limites do documento são `null` refletidos na resposta, sem indicar limites.

> [!NOTE]
> Embora não existam limites de documento específicos do SKU, cada índice ainda está sujeito a um limite máximo de segurança para garantir a estabilidade do serviço. Este limite vem de Lucene. Cada documento de Pesquisa Cognitiva do Azure é indexado internamente como um ou mais documentos Lucene. O número de documentos lucene por documento de pesquisa depende do número total de elementos em campos de coleta complexos. Cada elemento é indexado como um documento luceno separado. Por exemplo, um documento com 3 elementos em um campo de coleta complexo, será indexado como 4 documentos Lucene - 1 para o documento em si e 3 para os elementos. O número máximo de documentos lucene é de cerca de 25 bilhões por índice.

### <a name="regions-previously-having-document-limits"></a>Regiões que anteriormente tinham limites de documentos

Se o portal indicar um limite de documento, seu serviço foi criado antes do final de 2017, ou foi criado em um data center usando clusters de menor capacidade para hospedar serviços de Pesquisa Cognitiva do Azure:

+ Leste da Austrália
+ Leste da Ásia
+ Índia Central
+ Oeste do Japão
+ Centro-Oeste dos EUA

Para serviços sujeitos a limites de documentos, aplicam-se os limites máximos a seguir:

|  Grátis | Basic | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10.000 |1&nbsp;milhão |15 milhões por partição ou 180 milhões por serviço |60 milhões por partição ou 720 milhões por serviço |120 milhões por partição ou 1,4 bilhão por serviço |1 milhão por serviço, 200 milhões por partição |

Se o serviço tiver limites que estejam causando bloqueio, crie um novo serviço e, em seguida, publique novamente todo o conteúdo desse serviço. Não há mecanismo para reprovisionar o serviço em um novo hardware nas cenas.

> [!Note] 
> Para serviços de Alta Densidade S3 criados no final de 2017, o documento de 200 milhões por partição foi removido, mas o limite de 1 milhão de documentos por índice permanece.


### <a name="document-size-limits-per-api-call"></a>Limites de tamanho do documento por chamada à API

O tamanho máximo do documento ao chamar uma API de Índice é de aproximadamente 16 megabytes.

O tamanho do documento é realmente um limite ao tamanho do corpo da solicitação de API do Índice. Como é possível transmitir um lote de vários documentos para a API de Índice de uma só vez, o limite de tamanho depende praticamente de quantos documentos estão no lote. Para um lote com um único documento, o tamanho máximo de documentos é de 16 MB de JSON.

Para reduzir o tamanho do documento, lembre-se de excluir dados não consultáveis da solicitação. Imagens e outros dados binários não podem ser diretamente consultados e não devem ser armazenados no índice. Para integrar dados que não podem ser consultados aos resultados da pesquisa, defina um campo não pesquisável que armazene uma referência uma URL para o recurso.

## <a name="indexer-limits"></a>Limites de indexador

Existem tempos máximos de execução para fornecer equilíbrio e estabilidade ao serviço como um todo, mas conjuntos de dados maiores podem precisar de mais tempo de indexação do que o máximo permite. Se um trabalho de indexação não puder ser concluído no tempo máximo permitido, tente executá-lo de forma agendada. O agendador mantém monitora o status da indexação. Se um trabalho de indexação agendado for interrompido por algum motivo, o indexador poderá continuar de onde parou na próxima execução agendada.


| Recurso | Gratuito&nbsp;<sup>1</sup> | Básico&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Indexadores máximos |3 |5 ou 15|50 |200 |200 |N/D |10 |10 |
| Máximo de fontes de dados |3 |5 ou 15 |50 |200 |200 |N/D |10 |10 |
| Número máximo de conjuntos de habilidades <sup>4</sup> |3 |5 ou 15 |50 |200 |200 |N/D |10 |10 |
| Carga de indexação máxima por invocação |10.000 documentos |Limitado apenas pelo máximo de documentos |Limitado apenas pelo máximo de documentos |Limitado apenas pelo máximo de documentos |Limitado apenas pelo máximo de documentos |N/D |Sem limite |Sem limite |
| Cronograma mínimo | 5 minutos |5 minutos |5 minutos |5 minutos |5 minutos |5 minutos |5 minutos | 5 minutos |
| Tempo de execução máximo <sup>5</sup> | 1 a 3 minutos |24 horas |24 horas |24 horas |24 horas |N/D  |24 horas |24 horas |
| Tempo de execução máximo de conjuntos de habilidades da pesquisa cognitiva ou de indexação de blobs com análise de imagens <sup>5</sup> | 3 a 10 minutos |2 horas |2 horas |2 horas |2 horas |N/D  |2 horas |2 horas |
| Indexador de blob: tamanho máximo do blob, MB |16 |16 |128 |256 |256 |N/D  |256 |256 |
| Indexador de blob: número máximo de caracteres de conteúdo extraído de um blob |32.000 |64.000 |4&nbsp;milhões |8&nbsp;milhões |16&nbsp;milhões |N/D |4&nbsp;milhões |4&nbsp;milhões |

<sup>1</sup> Os serviços gratuitos têm um tempo máximo de execução do indexador de 3 minutos para fontes do blob e 1 minuto para todas as outras fontes de dados. Para a indexação de IA que chama para serviços cognitivos, os serviços gratuitos são limitados a 20 transações gratuitas por dia, onde uma transação é definida como um documento que passa com sucesso pelo pipeline de enriquecimento.

<sup>2</sup> Os serviços básicos criados antes de dezembro de 2017 têm limites mais baixos (5 em vez de 15) em indexadores, fontes de dados e skillsets.

<sup>3</sup> Os serviços do S3 HD não incluem suporte ao indexador.

<sup>4</sup> Máximo de 30 habilidades por conjunto de habilidades.

<sup>5</sup> Cargas de trabalho da pesquisa cognitiva e da análise de imagens na indexação de blobs do Azure têm tempos de execução mais curtos do que a indexação de texto normal. A análise de imagens e o processamento de idioma natural fazem uso intensivo dos recursos de computação e consomem uma quantidade desproporcional da capacidade de processamento disponível. O tempo de execução foi reduzido para dar aos outros trabalhos na fila a oportunidade de serem executados.  

> [!NOTE]
> Conforme estabelecido nos limites do [Índice,](#index-limits)os indexadores também aplicarão o limite superior de 3000 elementos em`2019-05-06`todas as coleções complexas por documento, começando com a versão mais recente da API GA que suporta tipos complexos ( ) em diante. Isso significa que se você criou seu indexador com uma versão aPI anterior, você não estará sujeito a esse limite. Para preservar a compatibilidade máxima, um indexador que foi criado com uma `2019-05-06` versão aPI anterior e, em seguida, atualizado com uma versão de API ou posterior, ainda será **excluído** dos limites. Os clientes devem estar cientes do impacto adverso de ter coleções complexas muito grandes (como dito anteriormente) e recomendamos a criação de novos indexadores com a versão mais recente da Ga API.

## <a name="synonym-limits"></a>Limites de sinônimo

O número máximo de mapas de sinônimopermitidos varia de acordo com o nível de preços. Cada regra pode ter até 20 expansões, onde uma expansão é um termo equivalente. Por exemplo, dada "gato", associação com "gatinho", "felino" e "felis" (gênero para gatos) contaria como 3 expansões.

| Recurso | Grátis | Basic | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Mapas máximos de sinônimos |3 |3|5 |10 |20 |20 | 10 | 10 |
| Número máximo de regras por mapa |5.000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Consultas por segundo (QPS)

As estimativas QPS devem ser desenvolvidas independentemente por cada cliente. Tamanho do índice e complexidade, o tamanho da consulta e complexidade e a quantidade de tráfego são determinantes principais de QPS. Não é possível oferecer estimativas significativas quando esses fatores são desconhecidos.

As previsões são mais previsíveis quando calculada em serviços em execução em recursos dedicados (camadas Básico e Standard). No nível padrão, é possível estimar melhor o QPS, porque você tem controle sobre mais parâmetros. Para obter orientações sobre como abordar a estimativa, consulte [o desempenho e otimização da Pesquisa Cognitiva do Azure](search-performance-optimization.md).

Para os níveis otimizados de armazenamento, você deve esperar um throughput de consulta menor e latência mais alta do que os níveis Padrão.  A metodologia para estimar o desempenho da consulta que você experimentará é a mesma dos níveis Padrão.

## <a name="data-limits-ai-enrichment"></a>Limites de dados (enriquecimento de IA)

Um [pipeline de enriquecimento de IA](cognitive-search-concept-intro.md) que faz chamadas para um recurso de Text Analytics para [reconhecimento de entidades,](cognitive-search-skill-entity-recognition.md) [extração de frases-chave,](cognitive-search-skill-keyphrases.md) [análise de sentimentos,](cognitive-search-skill-sentiment.md) [detecção de linguagem](cognitive-search-skill-language-detection.md)e [detecção de PII](cognitive-search-skill-pii-detection.md) está sujeito a limites de dados. O tamanho máximo de um registro deve ser de 50.000 caracteres, medido por [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Se você precisar interromper o backup de seus dados antes de enviá-lo ao analisador de sentimentos, use a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Limites de estrangulamento

As solicitações de consulta e indexação de pesquisa são estranguladas à medida que o sistema se aproxima da capacidade máxima. O estrangulamento se comporta de forma diferente para diferentes APIs. As APIs de consulta (Pesquisa/Sugerir/Autocompletar) e as APIs de indexação aceleram dinamicamente com base na carga no serviço. As APIs de índice têm limites de taxa de solicitação estática. 

Limites de solicitação de taxa estática para operações relacionadas a um índice:

+ Índices de lista (GET /indexes): 5 por segundo por unidade de pesquisa
+ Obter Índice (GET /indexes/myindex): 10 por segundo por unidade de pesquisa
+ Índice de criação (POST/índices): 12 por minuto por unidade de pesquisa
+ Criar ou Atualizar Índice (PUT /indexes/myindex): 6 por segundo por unidade de pesquisa
+ Índice de exclusão (DELETE /indexes/myindex): 12 por minuto por unidade de pesquisa 

## <a name="api-request-limits"></a>Limites de solicitação de API
* Máximo de 16 MB por solicitação <sup>1</sup>
* Comprimento máximo da URL de 8 KB
* Máximo de 1000 documentos por lote de carregamentos, mesclagens ou exclusões de índice
* Máximo de 32 campos na cláusula $orderby
* O tamanho máximo do termo de pesquisa é de 32.766 bytes (32 KB menos 2 bytes) de texto codificado em UTF-8

<sup>1</sup> Na Pesquisa Cognitiva do Azure, o corpo de uma solicitação está sujeito a um limite superior de 16 MB, impondo um limite prático sobre o conteúdo de campos individuais ou coleções que não são de outra forma constrangidos por limites teóricos (ver [tipos de dados suportados](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para obter mais informações sobre composição de campo e restrições).

## <a name="api-response-limits"></a>Limites de resposta à API
* Máximo de 1000 documentos retornados por página de resultados da pesquisa
* Máximo de 100 sugestões retornadas por solicitação de Sugerir API

## <a name="api-key-limits"></a>Limites de chave da API
As teclas aPI são usadas para autenticação de serviço. Há dois tipos. Chaves de administração são especificadas no cabeçalho da solicitação e concedem acesso completo de leitura/gravação ao serviço. Chaves de consulta são somente leitura, especificadas na URL, e, geralmente, são distribuídas para aplicativos cliente.

* Máximo de duas chaves de administração por serviço
* Máximo de 50 chaves de consulta por serviço