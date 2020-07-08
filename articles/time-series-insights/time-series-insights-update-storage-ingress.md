---
title: Armazenamento de dados e entrada em Versão Prévia — Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre armazenamento de dados e entrada na Versão Prévia do Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: d3bfb589ec4c152b136e8e1f432864b719c97d58
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85509312"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Entrada e armazenamento de dados na Versão Prévia do Azure Time Series Insights

Este artigo descreve as atualizações à entrada e ao armazenamento de dados da Versão Prévia do Azure Time Series Insights. Ele descreve a estrutura subjacente do armazenamento, o formato de arquivo e a propriedade da ID de Série Temporal. Ele também descreve o processo de entrada subjacente, as práticas recomendadas e as limitações de visualização atuais.

## <a name="data-ingress"></a>Entrada de dados

Seu ambiente do Azure Time Series Insights contém um *mecanismo de ingestão* para coletar, processar e armazenar dados de série temporal.

Há algumas considerações a serem observadas para garantir que todos os dados de entrada sejam processados, para obter alta escala de entrada e minimizar a *latência de ingestão* (o tempo gasto pelo Time Series Insights para ler e processar dados da origem do evento) ao [planejar seu ambiente](time-series-insights-update-plan.md).

As políticas de entrada de dados da Versão Prévia do Time Series Insights determinam de onde os dados podem ser originados e qual formato os dados devem ter.

### <a name="ingress-policies"></a>Políticas de entrada

A *entrada de dados* envolve como os dados são enviados para um ambiente da Versão Prévia do Azure Time Series Insights.

As principais configurações, formatação e práticas recomendadas são resumidas abaixo.

#### <a name="event-sources"></a>Origens de eventos

A Versão Prévia do Azure Time Series Insights suporta as duas origens de evento a seguir:

- [Hub IoT do Azure](../iot-hub/about-iot-hub.md)
- [Hubs de eventos do Azure](../event-hubs/event-hubs-about.md)

A Versão Prévia do Azure Time Series Insights dá suporte a um máximo de duas origens de evento por instância. Quando você se conecta a uma origem de evento, seu ambiente de TSI lerá todos os eventos atualmente armazenados em seu IOT ou hub de eventos, começando com o evento mais antigo.

> [!IMPORTANT]
>
> * Você pode experimentar alta latência inicial ao anexar uma origem de evento ao seu ambiente de versão prévia.
> A latência de origem do evento depende do número de eventos atualmente no Hub IoT ou no Hub de Eventos.
> * A alta latência diminuirá após os dados de origem do evento serem ingeridos pela primeira vez. Envie um tíquete de suporte pelo portal do Azure se você tiver uma alta latência contínua.

#### <a name="supported-data-format-and-types"></a>Tipos e formato de dados com suporte

O Azure Time Series Insights dá suporte ao JSON codificado em UTF-8 enviado do Hub IoT do Azure ou dos Hubs de Eventos do Azure. 

Os tipos de dados com suporte são:

| Tipo de dados | Descrição |
|---|---|
| **bool** | Um tipo de dados com um dos dois estados: `true` ou `false`. |
| **dateTime** | Representa um momento no tempo, geralmente expresso como uma data e hora do dia. Expresso no formato [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html). |
| **long** | Um inteiro de 64 bits assinado  |
| **double** | Um ponto flutuante do [IEEE 754](https://ieeexplore.ieee.org/document/8766229) de dupla precisão de 64 bits. |
| **cadeia de caracteres** | Valores de texto, compostos por caracteres Unicode.          |

> [!IMPORTANT]
>
> * Seu ambiente de TSI é fortemente tipado. Se dispositivos ou marcas enviarem dados integral e não integral, os valores de Propriedade do dispositivo serão armazenados em duas colunas duplas e longas separadas e a [função de adesão ()](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) deverá ser usada ao fazer chamadas à API e definir suas expressões de variáveis de modelo de série temporal.

#### <a name="objects-and-arrays"></a>Objetos e matrizes

Você pode enviar tipos complexos, como objetos e matrizes, como parte da carga do evento, mas seus dados passarão por um processo de nivelamento quando armazenados.

Informações detalhadas que descrevem como formatar seus eventos JSON, enviar tipo complexo e nivelamento de objeto aninhado estão disponíveis em [Como formatar JSON para entrada e consulta](./time-series-insights-update-how-to-shape-events.md) para auxiliar no planejamento e na otimização.

### <a name="ingress-best-practices"></a>Melhores práticas de entrada

Recomendamos que você empregue as seguintes melhores práticas:

* Configure o Azure Time Series Insights e qualquer Hub IoT ou Hub de Eventos na mesma região para reduzir a latência em potencial.

* [Planeje suas necessidades de dimensionamento](time-series-insights-update-plan.md) calculando sua taxa de ingestão antecipada e verificando se ela está dentro da taxa com suporte listada abaixo.

* Entenda como otimizar e formatar seus dados JSON, bem como as limitações atuais na visualização, lendo [Como formatar JSON para entrada e consulta](./time-series-insights-update-how-to-shape-events.md).

* Use a ingestão de streaming somente para dados quase em tempo real e recentes, não há suporte para streaming de dados históricos.

#### <a name="historical-data-ingestion"></a>Ingestão de dados históricos

No momento, o uso do pipeline de streaming para importar dados históricos não tem suporte na Versão Prévia do Azure Time Series Insights. Se você precisar importar dados passados para o seu ambiente, siga as diretrizes abaixo:

* Não transmita dados dinâmicos e históricos em paralelo. A ingestão de dados fora de ordem resultará em um desempenho de consulta degradado.
* Ingira dados históricos ordenados por tempo para obter um melhor desempenho.
* Fique dentro dos limites da taxa de transferência de ingestão abaixo.
* Desabilite o Armazenamento Warm se os dados forem mais antigos do que o período de retenção do Armazenamento Warm.

### <a name="ingress-scale-and-preview-limitations"></a>Limitações de escala de entrada e de versão prévia

As limitações de entrada da Versão Prévia do Azure Time Series Insights são descritas abaixo.

> [!TIP]
> Leia [Planeje seu ambiente de Versão Prévia](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) para obter uma lista abrangente de todos os limites de Versão Prévia.

#### <a name="per-environment-limitations"></a>Limitações por ambiente

Em geral, as taxas de entrada são exibidas como o fator do número de dispositivos que estão em sua organização, a frequência de emissão de eventos e o tamanho de cada evento:

*  **Número de dispositivos** × **Frequência de emissão de eventos** × **Tamanho de cada evento**.

Por padrão, a Versão Prévia do Time Series Insights pode ingerir dados de entrada a uma taxa de **até 1 megabyte por segundo (MBps) por ambiente do Time Series Insights**. Há limitações adicionais [por partição de hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits).

> [!TIP]
>
> * O suporte de ambiente para velocidades de ingestão de até 16 MBps pode ser fornecido por solicitação.
> * Entre em contato conosco se precisar de uma taxa de transferência maior enviando um tíquete de suporte pelo portal do Azure.
 
* **Exemplo 1:**

    O envio da Contoso tem 100.000 dispositivos que emitem um evento três vezes por minuto. O tamanho de um evento é de 200 bytes. Eles estão usando um Hub IoT com quatro partições como a origem do evento do Time Series Insights.

    * A taxa de ingestão para seu ambiente do Time Series Insights seria: **100.000 dispositivos * 200 bytes/evento * (3/60 evento/s) = 1 MBps**.
    * A taxa de ingestão por partição seria de 0,25 MBps.
    * A taxa de ingestão da Contoso Shipping estaria dentro da limitação de escala de visualização.

* **Exemplo 2:**

    A Análise de Frota da Contoso tem 60.000 dispositivos que emitem um evento a cada segundo. Eles estão usando um Hub de Eventos com uma contagem de 4 partições como a origem do evento do Time Series Insights. O tamanho de um evento é de 200 bytes.

    * A taxa de ingestão do ambiente seria: **60.000 dispositivos * 200 bytes/evento * 1 evento/s = 12 MBps**.
    * A taxa por partição seria de 3 MBps.
    * A taxa de ingestão da Análise de Frota da Contoso está acima dos limites do ambiente e da partição. Eles podem enviar uma solicitação para o Time Series Insights pelo portal do Azure para aumentar a taxa de ingestão no ambiente deles e criar um Hub de Eventos com mais partições dentro dos limites da Versão Prévia.

#### <a name="hub-partitions-and-per-partition-limits"></a>Partições de Hub e limites por partição

Ao planejar seu ambiente do Time Series Insights, é importante considerar a configuração das origens de eventos com as quais você se conectará ao Time Series Insights. O Hub IoT do Azure e os Hubs de Eventos utilizam partições para habilitar a escala horizontal para processamento de eventos. 

Uma *partição* é uma sequência ordenada de eventos mantidos em um hub. A contagem de partições é definida durante a fase de criação do hub e não pode ser alterada.

Para consultar as melhores práticas de particionamento de Hubs de Eventos, analise [Quantas partições eu preciso?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> A maioria dos Hubs IoT usados com o Azure Time Series Insights só precisa de quatro partições.

Se você estiver criando um novo hub para seu ambiente do Time Series Insights ou usando um existente, você precisará calcular a taxa de ingestão por partição para determinar se ela está dentro dos limites de visualização. 

Atualmente, a Versão Prévia do Azure Time Series Insights tem um **limite geral por partição de 0,5 MBps**.

#### <a name="iot-hub-specific-considerations"></a>Considerações específicas do Hub IoT

Quando um dispositivo é criado no Hub IoT, ele é atribuído permanentemente a uma partição. Ao fazer isso, o Hub IoT é capaz de garantir a ordenação de eventos (já que a atribuição nunca é alterada).

Uma atribuição de partição fixa também afeta as instâncias do Time Series Insights que estão ingerindo dados enviados do Hub IoT downstream. Quando as mensagens de vários dispositivos são encaminhadas para o Hub usando a mesma ID de dispositivo de gateway, elas podem chegar na mesma partição ao mesmo tempo, potencialmente excedendo os limites de escala por partição.

**Impacto**:

* Se uma única partição apresentar uma taxa sustentada de ingestão sobre o limite da Versão Prévia, é possível que o Time Series Insights não sincronize toda a telemetria do dispositivo antes que o período de retenção de dados do Hub IoT seja excedido. Como resultado, os dados enviados podem ser perdidos se os limites de ingestão forem excedidos de modo consistente.

Para atenuar essa situação, recomendamos as seguintes melhores práticas:

* Calcule suas taxas de ingestão por ambiente e por partição antes de implantar sua solução.
* Verifique se os dispositivos do Hub IoT têm balanceamento de carga para a extensão mais distante possível.

> [!IMPORTANT]
> Para ambientes que usam o Hub IoT como uma origem de evento, calcule a taxa de ingestão usando o número de dispositivos de hub em uso para ter certeza de que a taxa está abaixo da limitação de 0,5 MBps por partição na visualização.
>
> * Mesmo que vários eventos cheguem simultaneamente, o limite da Versão Prévia não será excedido.

  ![Diagrama de partição do Hub IoT](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Consulte os seguintes recursos para saber mais sobre como otimizar a taxa de transferência e as partições do hub:

* [Escala do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Escala do Hub de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partições do Hub de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Armazenamento de dados

Ao criar um ambiente de SKU (PAYG) *pago conforme o uso* da Versão Prévia do Time Series Insights, você cria dois recursos do Azure:

* Um ambiente de Versão Prévia do Azure Time Series Insights que pode ser configurado para armazenamento de dados warm.
* Uma conta de blob v1 de uso geral do Armazenamento do Azure para armazenamento de dados cold.

Os dados em seu armazenamento warm estão disponíveis somente pela [Consulta do Time Series Insights](./time-series-insights-update-tsq.md) e pelo [Explorador da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md). Seu armazenamento warm conterá dados recentes dentro do [período de retenção](./time-series-insights-update-plan.md#the-preview-environment) selecionado ao criar o ambiente do Time Series Insights.

A Versão Prévia do Time Series Insights salva os dados de armazenamento cold no armazenamento de Blobs do Azure no [formato de arquivo Parquet](#parquet-file-format-and-folder-structure). A Versão Prévia do Time Series Insights gerencia esses dados de armazenamento cold exclusivamente, mas está disponível para você ler diretamente como arquivos Parquet padrão.

> [!WARNING]
> Como o proprietário da conta de armazenamento de Blobs do Azure, na qual os dados de armazenamento cold residem, você tem acesso completo a todos os dados na conta. Esse acesso inclui permissões de gravação e exclusão. Não edite ou exclua os dados que a Versão Prévia do Time Series Insights grava, pois isso pode causar perda de dados.

### <a name="data-availability"></a>Disponibilidade de dados

Partições e dados de índices da Versão Prévia do Azure Time Series Insights para obter um desempenho de consulta ideal. Os dados tornam-se disponíveis para consulta de um armazenamento warm (se habilitado) e cold após sua indexação. A quantidade de dados que está sendo ingerida pode afetar essa disponibilidade.

> [!IMPORTANT]
> Durante a visualização, você pode experimentar um período de até 60 segundos antes que os dados se tornem disponíveis. Se você enfrentar uma latência significativa que dure mais de 60 segundos, envie um tíquete de suporte pelo portal do Azure.

## <a name="azure-storage"></a>Armazenamento do Azure

Esta seção descreve os detalhes do Armazenamento do Azure relevantes para a Versão Prévia do Azure Time Series Insights.

Para obter uma descrição completa do armazenamento de Blobs do Azure, leia a [Introdução aos blobs de armazenamento](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Sua conta de armazenamento

Quando você cria um ambiente de PAYG da Versão Prévia do Azure Time Series Insights, uma conta de blob V1 de uso geral do Armazenamento do Azure é criada como seu armazenamento cold de longo prazo.  

A Versão Prévia do Time Series Insights retém até duas cópias de cada evento em sua conta de Armazenamento do Azure. Uma cópia armazena eventos ordenados pelo tempo de ingestão, sempre permitindo o acesso a eventos em uma sequência ordenada por tempo. Ao longo do tempo, a Versão Prévia do Time Series Insights também cria uma cópia reparticionada dos dados para otimizar o desempenho da consulta do Time Series Insights.

Durante a Versão Prévia pública, os dados são armazenados indefinidamente em sua conta de Armazenamento do Azure.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Escrever e editar blobs do Time Series Insights

Para garantir a disponibilidade de dados e o desempenho da consulta, não edite nem exclua nenhum blob criado pela Versão Prévia do Time Series Insights.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Acessando os dados de armazenamento cold da Versão Prévia do Time Series Insights

Além de acessar seus dados do [explorador da Versão Prévia do Time Series Insights](./time-series-insights-update-explorer.md) e a [Consulta do Time Series Insights](./time-series-insights-update-tsq.md), você também pode querer acessar seus dados diretamente dos arquivos Parquet guardados no armazenamento cold. Por exemplo, você pode ler, transformar e limpar dados em um notebook Jupyter e, em seguida, usá-lo para treinar seu modelo do Azure Machine Learning no mesmo fluxo de trabalho do Spark.

Para acessar dados diretamente da sua conta de Armazenamento do Azure, você precisa de acesso de leitura à conta usada para armazenar seus dados da Versão Prévia do Time Series Insights. Você pode ler os dados selecionados com base na hora de criação do arquivo Parquet localizado na pasta `PT=Time` descrita abaixo na seção [Formato de arquivo Parquet](#parquet-file-format-and-folder-structure).  Para obter mais informações sobre como habilitar o acesso de leitura para sua conta de armazenamento, consulte [Gerenciar o acesso aos recursos de sua conta de armazenamento](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Exclusão de dados

Não exclua seus arquivos da Versão Prévia do Time Series Insights. Gerenciar dados relacionados somente na Versão Prévia do Time Series Insights.

### <a name="parquet-file-format-and-folder-structure"></a>Formato de arquivo Parquet e estrutura de pasta

Parquet é um formato de arquivo de coluna open-source projetado para armazenamento e desempenho eficientes. A Versão Prévia do Time Series Insights usa o Parquet para habilitar o desempenho de consulta baseado em ID de série temporal em escala.  

Para obter mais informações sobre o tipo de arquivo Parquet, leia a [documentação do Parquet](https://parquet.apache.org/documentation/latest/).

A Versão Prévia do Time Series Insights armazena cópias de seus dados da seguinte maneira:

* Na primeira, a cópia inicial é particionada pelo tempo de ingestão e armazena dados aproximadamente na ordem de chegada. Esses dados residem na pasta `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Na segunda, a cópia reparticionada é agrupada por IDs da série temporal e reside na pasta `PT=TsId`:

  `V=1/PT=TsId/<TSI_INTERNAL_STRUCTURE>/<TSI_INTERNAL_NAME>.parquet`

O carimbo de data/hora nos nomes de BLOBs na `PT=Time` pasta corresponde à hora de chegada dos dados para TSI (não o carimbo de data/hora dos eventos).

Os dados na pasta `PT=TsId` serão otimizados para consulta ao longo do tempo e não são estáticos. Durante o reparticionamento, os mesmos eventos podem estar presentes em vários BLOBs. Além disso, a nomenclatura dos BLOBs pode ser alterada no futuro.

> [!NOTE]
>
> * `<YYYY>` mapeia para uma representação de ano de quatro dígitos.
> * `<MM>` mapeia para uma representação de mês de dois dígitos.
> * `<YYYYMMDDHHMMSSfff>` mapeia para uma representação de carimbo de data/hora com ano de quatro dígitos (`YYYY`), mês de dois dígitos (`MM`), dia de dois dígitos (`DD`), hora de dois dígitos (`HH`), minuto de dois dígitos (`MM`), segundo de dois dígitos (`SS`) e milissegundos de três dígitos (`fff`).

Os eventos da Versão Prévia do Time Series Insights são mapeados para o conteúdo do arquivo Parquet da seguinte maneira:

* Cada evento é mapeado para uma única linha.
* Cada linha inclui a coluna de **carimbo de data/hora** com um carimbo de data/hora do evento. A propriedade de carimbo de data/hora nunca é nula. O padrão é **tempo de enfileiramento do evento** se a propriedade de carimbo de data/hora não for especificada na fonte do evento. O carimbo de data/hora armazenado sempre está no fuso horário UTC.
* Cada linha inclui as colunas de ID da série temporal (TSID), conforme definido quando o ambiente do Time Series Insights é criado. O nome da propriedade TSID inclui o sufixo `_string`.
* Todas as outras propriedades enviadas como dados de telemetria são mapeadas para as colunas cujos nomes terminam com `_string` (cadeia de caracteres), `_bool` (booliano), `_datetime` (dateTime) ou `_double` (duplo), dependendo do tipo de propriedade.
* Esse esquema de mapeamento se aplica à primeira versão do formato de arquivo, referenciado como **V=1** e armazenado na pasta base de mesmo nome. Conforme esse recurso evolui, esse esquema de mapeamento pode ser alterado e o nome de referência é incrementado.

## <a name="next-steps"></a>Próximas etapas

- Leia [como formatar o JSON para entrada e consulta](./time-series-insights-update-how-to-shape-events.md).

- Leia sobre a nova [modelagem de dados](./time-series-insights-update-tsm.md).
