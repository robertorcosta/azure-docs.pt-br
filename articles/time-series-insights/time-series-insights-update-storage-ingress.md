---
title: Armazenamento de dados e entrada em visualização-Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre o armazenamento de dados e a entrada no Azure Time Series Insights Preview.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 44c942e43cd4be1d04f56e828e3e17c58713a706
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2020
ms.locfileid: "77559837"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Entrada e armazenamento de dados na Versão Prévia do Azure Time Series Insights

Este artigo descreve as atualizações para o armazenamento de dados e entrada para Azure Time Series Insights versão prévia. Ele descreve a estrutura de armazenamento subjacente, o formato de arquivo e a propriedade ID da série temporal. O processo de entrada subjacente, as práticas recomendadas e as limitações de visualização atuais também são descritos.

## <a name="data-ingress"></a>Entrada de dados

Seu ambiente de Azure Time Series Insights contém um *mecanismo de ingestão* para coletar, processar e armazenar dados de série temporal. 

Há algumas considerações a serem observadas para garantir que todos os dados de entrada sejam processados, para obter alta escala de entrada e minimizar a *latência de ingestão* (o tempo gasto pelo time Series insights para ler e processar dados da origem do evento) ao [planejar seu ambiente](time-series-insights-update-plan.md).

Time Series Insights Visualizar as políticas de entrada de dados determinam de onde os dados podem ser originados e qual formato os dados devem ter.

### <a name="ingress-policies"></a>Políticas de entrada

A *entrada de dados* envolve como os dados são enviados para um ambiente de visualização de Azure Time Series insights. 

Principais configurações, formatação e práticas recomendadas são resumidas abaixo.

#### <a name="event-sources"></a>Origens de eventos

Azure Time Series Insights visualização dá suporte às seguintes origens de evento:

- [Hub IoT do Azure](../iot-hub/about-iot-hub.md)
- [Hubs de eventos do Azure](../event-hubs/event-hubs-about.md)

Azure Time Series Insights visualização dá suporte a um máximo de duas origens de evento por instância.

> [!IMPORTANT] 
> * Você pode experimentar alta latência inicial ao anexar uma origem do evento ao seu ambiente de visualização. 
> A latência de origem do evento depende do número de eventos atualmente no Hub IoT ou Hub de eventos.
> * A alta latência será sublado após os dados de origem do evento serem ingeridos pela primeira vez. Envie um tíquete de suporte por meio do portal do Azure se você tiver uma alta latência contínua.

#### <a name="supported-data-format-and-types"></a>Tipos e formato de dados com suporte

O Azure Time Series Insights dá suporte ao JSON codificado em UTF-8 enviado do Hub IoT do Azure ou dos hubs de eventos do Azure. 

Os tipos de dados com suporte são:

| Tipo de dados | DESCRIÇÃO |
|---|---|
| **bool** | Um tipo de dados com um dos dois Estados: `true` ou `false`. |
| **dateTime** | Representa um instante no tempo, normalmente expresso como uma data e hora do dia. Expresso no formato [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) . |
| **double** | Um ponto flutuante de [IEEE 754](https://ieeexplore.ieee.org/document/8766229) de precisão dupla de 64 bits. |
| **cadeia de caracteres** | Valores de texto, compostos de caracteres Unicode.          |

#### <a name="objects-and-arrays"></a>Objetos e matrizes

Você pode enviar tipos complexos, como objetos e matrizes, como parte da carga do evento, mas seus dados passarão por um processo de nivelamento quando armazenados. 

Informações detalhadas que descrevem como formatar seus eventos JSON, enviar tipo complexo e nivelamento de objeto aninhado estão disponíveis em [como formatar JSON para entrada e consulta](./time-series-insights-update-how-to-shape-events.md) para auxiliar no planejamento e na otimização.

### <a name="ingress-best-practices"></a>Práticas recomendadas de entrada

Recomendamos que você empregue as seguintes práticas recomendadas:

* Configure Azure Time Series Insights e qualquer Hub IoT ou Hub de eventos na mesma região para reduzir a latência em potencial.

* [Planeje suas necessidades de dimensionamento](time-series-insights-update-plan.md) calculando sua taxa de ingestão antecipada e verificando se ela está dentro da taxa com suporte listada abaixo.

* Entenda como otimizar e formatar seus dados JSON, bem como as limitações atuais na visualização, lendo [como formatar JSON para entrada e consulta](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-preview-limitations"></a>Limitações de escala e visualização de entrada 

Azure Time Series Insights limitações de entrada da visualização são descritas abaixo.

> [!TIP]
> Leia [planejar seu ambiente de visualização](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) para obter uma lista abrangente de todos os limites de visualização.

#### <a name="per-environment-limitations"></a>Limitações por ambiente

Em geral, as tarifas de entrada são exibidas como o fator do número de dispositivos que estão em sua organização, a frequência de emissão de eventos e o tamanho de cada evento:

*  **Número de dispositivos** × **frequência de emissão de eventos** × **tamanho de cada evento**.

Por padrão, Time Series Insights visualização pode ingerir dados de entrada a uma taxa de **até 1 megabyte por segundo (Mbps) por ambiente de time Series insights**.

> [!TIP] 
> * O suporte de ambiente para velocidades de ingestão de até 16 MBps pode ser fornecido por solicitação.
> * Entre em contato conosco se precisar de uma taxa de transferência maior enviando um tíquete de suporte por meio de portal do Azure.
 
* **Exemplo 1:**

    O envio da Contoso tem 100.000 dispositivos que emitem três vezes por minuto. O tamanho de um evento é 200 bytes. Eles estão usando um hub de eventos com quatro partições como a origem do evento Time Series Insights.

    * A taxa de ingestão para seu ambiente de Time Series Insights seria: **100.000 dispositivos * 200 bytes/evento * (3/60 evento/s) = 1 Mbps**.
    * A taxa de ingestão por partição seria de 0,25 MBps.
    * A taxa de ingestão da Contoso Shipping estaria dentro da limitação da escala de visualização.

* **Exemplo 2:**

    A análise de frota da Contoso tem 60.000 dispositivos que emitem um evento a cada segundo. Eles estão usando uma contagem de partições 4 do Hub IoT 24 como a origem do evento Time Series Insights. O tamanho de um evento é 200 bytes.

    * A taxa de ingestão de ambiente seria: **20.000 dispositivos * 200 bytes/evento * 1 evento/s = 4 Mbps**.
    * A taxa por partição seria de 1 MBps.
    * A análise da Contoso frota pode enviar uma solicitação para Time Series Insights por meio de portal do Azure para aumentar a taxa de ingestão para seu ambiente.

#### <a name="hub-partitions-and-per-partition-limits"></a>Partições de Hub e limites por partição

Ao planejar seu ambiente de Time Series Insights, é importante considerar a configuração das origens de eventos às quais você se conectará Time Series Insights. O Hub IoT do Azure e os hubs de eventos utilizam partições para habilitar a escala horizontal para processamento de eventos. 

Uma *partição* é uma sequência ordenada de Eventos mantidos em um Hub. A contagem de partições é definida durante a fase de criação do Hub e não pode ser alterada. 

Para as práticas recomendadas de particionamento dos hubs de eventos, examine [quantas partições eu preciso?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> A maioria dos hubs IoT usados com Azure Time Series Insights só precisa de quatro partições.

Se você estiver criando um novo hub para seu ambiente de Time Series Insights ou usando um existente, você precisará calcular a taxa de ingestão por partição para determinar se ela está dentro dos limites de visualização. 

O Azure Time Series Insights Preview atualmente tem um **limite geral por partição de 0,5 Mbps**.

#### <a name="iot-hub-specific-considerations"></a>Considerações específicas do Hub IoT

Quando um dispositivo é criado no Hub IoT, ele é atribuído permanentemente a uma partição. Ao fazer isso, o Hub IoT é capaz de garantir a ordenação de eventos (já que a atribuição nunca é alterada).

Uma atribuição de partição fixa também afeta Time Series Insights instâncias que estão ingerindo dados enviados do Hub IoT downstream. Quando as mensagens de vários dispositivos são encaminhadas para o Hub usando a mesma ID de dispositivo de gateway, elas podem chegar na mesma partição ao mesmo tempo, potencialmente excedendo os limites de escala por partição. 

**Impacto**:

* Se uma única partição apresentar uma taxa sustentada de ingestão sobre o limite de visualização, é possível que Time Series Insights não sincronize toda a telemetria do dispositivo antes que o período de retenção de dados do Hub IoT seja excedido. Como resultado, os dados enviados podem ser perdidos se os limites de ingestão forem excedidos consistentemente.

Para atenuar essa circunstância, recomendamos as seguintes práticas recomendadas:

* Calcule suas taxas de ingestão por ambiente e por partição antes de implantar sua solução.
* Verifique se os dispositivos do Hub IoT têm balanceamento de carga para a extensão mais distante possível.

> [!IMPORTANT]
> Para ambientes que usam o Hub IoT como uma origem de evento, calcule a taxa de ingestão usando o número de dispositivos de Hub em uso para ter certeza de que a taxa está abaixo da limitação de 0,5 MBps por partição na visualização.
> * Mesmo que vários eventos cheguem simultaneamente, o limite de visualização não será excedido.

  ![Diagrama de partição do Hub IoT](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Consulte os seguintes recursos para saber mais sobre como otimizar a taxa de transferência e as partições do Hub:

* [Escala do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Escala do hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partições do hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Armazenamento de dados

Ao criar um ambiente de SKU PAYG (pré- *pago* ) Time Series insights visualização, você cria dois recursos do Azure:

* Um ambiente de visualização Azure Time Series Insights que pode ser configurado para armazenamento quente.
* Uma conta de blob v1 de uso geral do armazenamento do Azure para armazenamento de dados frio.

Os dados em sua loja a quente estão disponíveis apenas por meio da [consulta de série temporal](./time-series-insights-update-tsq.md) e do [Azure Time Series insights Explorer Preview](./time-series-insights-update-explorer.md). 

Time Series Insights visualização salva os dados de armazenamento frio no armazenamento de BLOBs do Azure no [formato de arquivo parquet](#parquet-file-format-and-folder-structure). Time Series Insights visualização gerencia esses dados de armazenamento frio exclusivamente, mas está disponível para você ler diretamente como arquivos parquet padrão.

> [!WARNING]
> Como o proprietário da conta de armazenamento de BLOBs do Azure onde os dados de armazenamento frio residem, você tem acesso completo a todos os dados na conta. Esse acesso inclui permissões de gravação e exclusão. Não edite ou exclua os dados que Time Series Insights as gravações de visualização, pois isso pode causar perda de dados.

### <a name="data-availability"></a>Disponibilidade de dados

Azure Time Series Insights Visualizar partições e dados de índices para obter um desempenho de consulta ideal. Os dados tornam-se disponíveis para consulta de uma loja passiva (se habilitada) e fria após sua indexação. A quantidade de dados que está sendo ingerida pode afetar essa disponibilidade.

> [!IMPORTANT]
> Durante a versão prévia, você pode experimentar um período de até 60 segundos antes que os dados se tornem disponíveis. Se você enfrentar uma latência significativa além de 60 segundos, envie um tíquete de suporte por meio do portal do Azure.

## <a name="azure-storage"></a>Armazenamento do Azure

Esta seção descreve os detalhes do armazenamento do Azure relevantes para Azure Time Series Insights versão prévia.

Para obter uma descrição completa do armazenamento de BLOBs do Azure, leia a [introdução aos blobs de armazenamento](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Sua conta de armazenamento

Quando você cria um ambiente de PAYG preview do Azure Time Series Insights, uma conta de blob v1 de uso geral do armazenamento do Azure é criada como sua loja fria de longo prazo.  

Azure Time Series Insights visualização publica até duas cópias de cada evento em sua conta de armazenamento do Azure. A cópia inicial tem eventos ordenados pelo tempo de ingestão. Essa ordem de evento é **sempre preservada** para que outros serviços possam acessar seus eventos sem sequenciar problemas. 

> [!NOTE]
> Você também pode usar o Spark, o Hadoop e outras ferramentas familiares para processar os arquivos brutos do parquet. 

Time Series Insights visualização também reparticiona os arquivos parquet para otimizar para a consulta Time Series Insights. Essa cópia reparticionada dos dados também é salva. 

Durante a visualização pública, os dados são armazenados indefinidamente em sua conta de armazenamento do Azure.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Escrever e editar blobs do Time Series Insights

Para garantir o desempenho da consulta e a disponibilidade de dados, não edite nem exclua nenhum blob que Time Series Insights versão prévia cria.

#### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Acessando e exportando dados da Versão Prévia do Time Series Insights

Talvez você queira acessar os dados exibidos no Time Series Insights Explorer Preview para usar em conjunto com outros serviços. Por exemplo, você pode usar seus dados para criar um relatório no Power BI ou para treinar um modelo de aprendizado de máquina usando Azure Machine Learning Studio. Ou, você pode usar seus dados para transformar, Visualizar e modelar seus blocos de anotações do Jupyter.

Você pode acessar seus dados de três maneiras gerais:

* Pelo gerenciador da Versão Prévia do Time Series Insights. Você pode exportar dados como um arquivo CSV do Explorer. Para obter mais informações, leia [Time Series insights Gerenciador de visualização](./time-series-insights-update-explorer.md).
* Na API de visualização do Time Series Insights usando a consulta obter eventos. Para saber mais sobre essa API, leia [consulta de série temporal](./time-series-insights-update-tsq.md).
* Diretamente de uma conta de armazenamento do Azure. Você precisa de acesso de leitura para qualquer conta que esteja usando para acessar seus Time Series Insights dados de visualização. Para obter mais informações, leia [gerenciar o acesso aos recursos da sua conta de armazenamento](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Exclusão de dados

Não exclua seus arquivos de visualização Time Series Insights. Gerenciar dados relacionados somente no Time Series Insights Preview.

### <a name="parquet-file-format-and-folder-structure"></a>Formato de arquivo parquet e estrutura de pasta

Parquet é um formato de arquivo de coluna de código aberto que foi projetado para um armazenamento e desempenho eficientes. Time Series Insights visualização usa o parquet por esses motivos. Ele particiona os dados por ID de série temporal para desempenho de consulta em escala.  

Para obter mais informações sobre o tipo de arquivo parquet, leia a [documentação do parquet](https://parquet.apache.org/documentation/latest/).

Time Series Insights visualização armazena cópias de seus dados da seguinte maneira:

* A primeira, a cópia inicial é particionada pelo tempo de ingestão e armazena dados aproximadamente na ordem de chegada. Os dados residem na pasta `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* A segunda cópia reparticionada é particionada por um agrupamento de IDs de série temporal e reside na pasta `PT=TsId`:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

Em ambos os casos, os valores de tempo correspondem ao tempo de criação do blob. Os dados na pasta `PT=Time` são preservados. Os dados na pasta `PT=TsId` serão otimizados para consulta ao longo do tempo e não permanecerão estáticos.

> [!NOTE]
> * `<YYYY>` mapeia para uma representação de ano de quatro dígitos.
> * `<MM>` mapeia para uma representação de mês de dois dígitos.
> * `<YYYYMMDDHHMMSSfff>` mapeia para uma representação de carimbo de data/hora com ano de quatro dígitos (`YYYY`), mês de dois dígitos (`MM`), dia de dois dígitos (`DD`), hora de dois dígitos (`HH`), minuto de dois dígitos (`MM`), segundo de dois dígitos (`SS`) e milissegundos de três dígitos (`fff`).

Time Series Insights eventos de visualização são mapeados para o conteúdo do arquivo parquet da seguinte maneira:

* Cada evento é mapeado para uma única linha.
* Cada linha inclui a coluna timestamp com um carimbo de data **/** hora de evento. A propriedade de carimbo de data/hora nunca é nula. O padrão será o **tempo de enfileiramento do evento** se a propriedade de carimbo de data/hora não for especificada na origem do evento. O carimbo de data/hora sempre está em UTC.
* Cada linha inclui as colunas de ID de série temporal, conforme definido quando o ambiente de Time Series Insights é criado. O nome da propriedade inclui o sufixo `_string`.
* Todas as outras propriedades enviadas como dados de telemetria são mapeadas para nomes de coluna que terminam com `_string` (cadeia de caracteres), `_bool` (booliano), `_datetime` (DateTime) ou `_double` (duplo), dependendo do tipo de propriedade.
* Este esquema de mapeamento se aplica à primeira versão do formato de arquivo, referenciado como **V = 1**. Conforme esse recurso evolui, o nome pode ser incrementado.

## <a name="next-steps"></a>Próximas etapas

- Leia [como formatar JSON para entrada e consulta](./time-series-insights-update-how-to-shape-events.md).

- Leia sobre a nova [modelagem de dados](./time-series-insights-update-tsm.md).
