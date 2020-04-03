---
title: Armazenamento e entrada de dados no Preview - Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre armazenamento e inserção de dados no Azure Time Series Insights Preview.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 8987cbe6860422ff92119a9f3b13a0a365e6d1a4
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618313"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Entrada e armazenamento de dados na Versão Prévia do Azure Time Series Insights

Este artigo descreve atualizações para o armazenamento de dados e a ingestão do Azure Time Series Insights Preview. Ele descreve a estrutura de armazenamento subjacente, o formato do arquivo e a propriedade Time Series ID. O processo de ingresss subjacente, as melhores práticas e as limitações atuais de visualização também são descritos.

## <a name="data-ingress"></a>Ingresss de dados

O ambiente Azure Time Series Insights contém um *mecanismo de ingestão* para coletar, processar e armazenar dados de séries tempontos. 

Existem algumas considerações a serem atentas para garantir que todos os dados de entrada sejam processados, para alcançar alta escala de entrada e minimizar a *latência de ingestão* (o tempo levado pelo Time Series Insights para ler e processar dados da fonte do evento) ao [planejar seu ambiente](time-series-insights-update-plan.md).

As políticas de visualização de dados de visualização de datas de previsão de tempo determinam de onde os dados podem ser originados e de que formato os dados devem ter.

### <a name="ingress-policies"></a>Políticas de ingressa

*A ingestão de dados* envolve como os dados são enviados para um ambiente de visualização de visualizações da série de tempo do Azure. 

As principais práticas de configuração, formatação e melhores práticas são resumidas abaixo.

#### <a name="event-sources"></a>Origens de eventos

O Azure Time Series Insights Preview suporta as seguintes fontes de evento:

- [Hub IoT do Azure](../iot-hub/about-iot-hub.md)
- [Hubs de eventos do Azure](../event-hubs/event-hubs-about.md)

O Azure Time Series Insights Preview suporta um máximo de duas fontes de evento por instância.

> [!IMPORTANT] 
> * Você pode experimentar alta latência inicial ao anexar uma fonte de evento ao seu ambiente de visualização. 
> A latência da fonte do evento depende do número de eventos atualmente em seu IoT Hub ou Event Hub.
> * A alta latência diminuirá depois que os dados da fonte do evento forem ingeridos pela primeira vez. Envie um bilhete de suporte através do portal Azure se você tiver uma latência contínua.

#### <a name="supported-data-format-and-types"></a>Formato e tipos de dados suportados

O Azure Time Series Insights suporta JSON codificado utf-8 enviado do Azure IoT Hub ou Azure Event Hubs. 

Os tipos de dados suportados são:

| Tipo de dados | Descrição |
|---|---|
| **bool** | Um tipo de dados com `true` `false`um dos dois estados: ou . |
| **Datetime** | Representa um momento no tempo, geralmente expresso como uma data e hora do dia. Expresso no formato [ISO 8601.](https://www.iso.org/iso-8601-date-and-time-format.html) |
| **double** | Um ponto flutuante [IEEE 754](https://ieeexplore.ieee.org/document/8766229) de dupla precisão de 64 bits. |
| **cadeia de caracteres** | Valores de texto, compostos de caracteres Unicode.          |

#### <a name="objects-and-arrays"></a>Objetos e matrizes

Você pode enviar tipos complexos, como objetos e arrays, como parte da carga útil do evento, mas seus dados passarão por um processo de achatamento quando armazenados. 

Informações detalhadas descrevendo como moldar seus eventos JSON, enviar tipo complexo e achatamento de objetos aninhados estão disponíveis em [Como moldar JSON para entrada e consulta](./time-series-insights-update-how-to-shape-events.md) para ajudar no planejamento e otimização.

### <a name="ingress-best-practices"></a>Melhores práticas de ingress

Recomendamos que você empregue as seguintes práticas recomendadas:

* Configure o Azure Time Series Insights e qualquer Hub de IoT ou Hub de Eventos na mesma região para reduzir a latência potencial.

* [Planeje suas necessidades de escala](time-series-insights-update-plan.md) calculando sua taxa de ingestão antecipada e verificando se ela está dentro da taxa suportada listada abaixo.

* Entenda como otimizar e moldar seus dados JSON, bem como as limitações atuais na visualização, lendo [como moldar JSON para ingresse e consulta.](./time-series-insights-update-how-to-shape-events.md)

### <a name="ingress-scale-and-preview-limitations"></a>Limitações de escala e visualização de ingresss 

As limitações de pré-visualização da série do tempo do Azure são descritas abaixo.

> [!TIP]
> Leia [Planeje seu ambiente de visualização](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) para uma lista abrangente de todos os limites de visualização.

#### <a name="per-environment-limitations"></a>Por limitações ambientais

Em geral, as taxas de ingresssão são vistas como o fator do número de dispositivos que estão em sua organização, frequência de emissão de eventos e o tamanho de cada evento:

*  **Número de dispositivos** × **Freqüência de emissão** de eventos × **Tamanho de cada evento**.

Por padrão, a visualização do Time Series Insights pode ingerir dados de entrada a uma taxa de **até 1 megabyte por segundo (MBps) por ambiente Time Series Insights**. Existem limitações adicionais [por partição do hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits).

> [!TIP] 
> * O suporte ao meio ambiente para ingerir velocidades de até 16 MBps pode ser fornecido por solicitação.
> * Entre em contato conosco se você precisar de maior rendimento enviando um bilhete de suporte através do portal Azure.
 
* **Exemplo 1:**

    Contoso Shipping tem 100.000 dispositivos que emitem um evento três vezes por minuto. O tamanho de um evento é de 200 bytes. Eles estão usando um Hub Iot com quatro partições como fonte de evento Time Series Insights.

    * A taxa de ingestão para o ambiente Time Series Insights seria: **100.000 dispositivos * 200 bytes/evento * (3/60 evento/seg) = 1 MBps**.
    * A taxa de ingestão por partição seria de 0,25 MBps.
    * A taxa de ingestão do Contoso Shipping estaria dentro da limitação da escala de visualização.

* **Exemplo 2:**

    A Contoso Fleet Analytics possui 60.000 dispositivos que emitem um evento a cada segundo. Eles estão usando um Event Hub com uma contagem de partições de 4 como fonte de evento Time Series Insights. O tamanho de um evento é de 200 bytes.

    * A taxa de ingestão de ambiente seria: **60.000 dispositivos * 200 bytes/evento * 1 evento/seg = 12 MBps**.
    * A taxa por partição seria de 3 MBps.
    * A taxa de ingestão da Contoso Fleet Analytics está acima dos limites de meio ambiente e partição. Eles podem enviar uma solicitação ao Time Series Insights através do portal Azure para aumentar a taxa de ingestão para seu ambiente e criar um Event Hub com mais partições para estar dentro dos limites de visualização.

#### <a name="hub-partitions-and-per-partition-limits"></a>Partições do hub e limites por partição

Ao planejar seu ambiente de Insights de séries tempois, é importante considerar a configuração das fontes de eventos que você estará conectando ao Time Series Insights. Tanto o Azure IoT Hub quanto o Event Hubs utilizam partições para permitir a escala horizontal para o processamento de eventos. 

Uma *partição* é uma seqüência ordenada de eventos realizados em um hub. A contagem de partições é definida durante a fase de criação do hub e não pode ser alterada. 

Para o Event Hubs particionando as práticas recomendadas, [revise quantas partições eu preciso?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> A maioria dos Hubs de IoT usados com o Azure Time Series Insights só precisa de quatro partições.

Se você está criando um novo hub para o ambiente Time Series Insights ou usando um já existente, você precisará calcular sua taxa de ingestão por partição para determinar se está dentro dos limites de visualização. 

A tualmente, o Azure Time Series Insights Preview tem um limite geral **por partição de 0,5 MBps**.

#### <a name="iot-hub-specific-considerations"></a>Considerações específicas do IoT Hub

Quando um dispositivo é criado no IoT Hub, ele é permanentemente atribuído a uma partição. Ao fazer isso, o IoT Hub é capaz de garantir o pedido de eventos (já que a atribuição nunca muda).

Uma atribuição de partição fixa também afeta as instâncias do Time Series Insights que estão ingerindo dados enviados do IoT Hub a jusante. Quando as mensagens de vários dispositivos são encaminhadas para o hub usando o mesmo ID do dispositivo gateway, elas podem chegar na mesma partição ao mesmo tempo potencialmente excedendo os limites de escala por partição. 

**Impacto**:

* Se uma única partição experimentar uma taxa sustentada de ingestão acima do limite de visualização, é possível que o Time Series Insights não sincronize toda a telemetria do dispositivo antes que o período de retenção de dados do IoT Hub seja excedido. Como resultado, os dados enviados podem ser perdidos se os limites de ingestão forem consistentemente excedidos.

Para mitigar essa circunstância, recomendamos as seguintes práticas recomendadas:

* Calcule as taxas de ingestão por ambiente e por partição antes de implantar sua solução.
* Certifique-se de que seus dispositivos IoT Hub estejam equilibrados em carga na medida do possível.

> [!IMPORTANT]
> Para ambientes que usam o IoT Hub como fonte de evento, calcule a taxa de ingestão usando o número de dispositivos de hub em uso para ter certeza de que a taxa fica abaixo da limitação de 0,5 MBps por partição na visualização.
> * Mesmo que vários eventos cheguem simultaneamente, o limite de visualização não será excedido.

  ![Diagrama de partição do hub IoT](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Consulte os seguintes recursos para saber mais sobre como otimizar o throughput e as partições do hub:

* [Escala do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Escala do Hub de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partições do Hub de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Armazenamento de dados

Quando você cria um ambiente SKU de visualização *de pay-as-you-go* (PAYG), você cria dois recursos do Azure:

* Um ambiente de visualização de insights da série de tempo do Azure que pode ser configurado para armazenamento de dados quentes.
* Uma conta blob V1 de uso geral do Azure Storage para armazenamento de dados frios.

Os dados em sua loja quente estão disponíveis apenas via [Consulta de Série sumária](./time-series-insights-update-tsq.md) e o [explorador de visualização de insights da série do tempo do Azure](./time-series-insights-update-explorer.md). Seu armazenamento quente conterá dados recentes dentro do [período de retenção](./time-series-insights-update-plan.md#the-preview-environment) selecionado ao criar o ambiente Time Series Insights.

A visualização do Time Series Insights salva os dados do seu cold store no armazenamento Do Azure Blob no [formato de arquivo Parquet](#parquet-file-format-and-folder-structure). Time Series Insights Preview gerencia esses dados de cold store exclusivamente, mas está disponível para você ler diretamente como arquivos padrão do Parquet.

> [!WARNING]
> Como proprietário da conta de armazenamento Azure Blob onde residem os dados do cold store, você tem acesso total a todos os dados da conta. Esse acesso inclui permissões de gravação e exclusão. Não edite ou exclua os dados que o Time Series Insights Preview grava, porque isso pode causar perda de dados.

### <a name="data-availability"></a>Disponibilidade de dados

Azure Time Series Insights Preview partições e indexa dados para o melhor desempenho de consulta. Os dados ficam disponíveis para consulta tanto de warm (se ativado) quanto de cold store depois de indexados. A quantidade de dados que estão sendo ingeridos pode afetar essa disponibilidade.

> [!IMPORTANT]
> Durante a visualização, você pode experimentar um período de até 60 segundos antes que os dados estejam disponíveis. Se você tiver uma latência significativa além de 60 segundos, envie um bilhete de suporte através do portal Azure.

## <a name="azure-storage"></a>Armazenamento do Azure

Esta seção descreve os detalhes do Azure Storage relevantes para a visualização do Azure Time Series Insights.

Para obter uma descrição completa do armazenamento Azure Blob, leia a [introdução de blobs de armazenamento](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Sua conta de armazenamento

Quando você cria um ambiente Azure Time Series Insights Preview PAYG, uma conta V1 de uso geral do Azure Storage é criada como sua loja de frio de longo prazo.  

A Visualização de Insights da série do tempo do Azure retém até duas cópias de cada evento em sua conta do Azure Storage. Uma cópia armazena eventos ordenados pelo tempo de ingestão, sempre permitindo o acesso a eventos em uma seqüência ordenada por tempo. Com o tempo, o Time Series Insights Preview também cria uma cópia reparticionada dos dados para otimizar a consulta de Insights de Séries Tempo. 

Durante a visualização pública, os dados são armazenados indefinidamente em sua conta do Azure Storage.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Escrever e editar blobs do Time Series Insights

Para garantir o desempenho da consulta e a disponibilidade de dados, não edite ou exclua quaisquer bolhas que a Visualização de Insights da Série Do Tempo crie.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Acessando o Time Series Insights Visualizar dados de cold store 

Além de acessar seus dados do explorador de visualização de visualizações de [séries tempoe](./time-series-insights-update-explorer.md) [consulta de séries tempo,](./time-series-insights-update-tsq.md)você também pode querer acessar seus dados diretamente dos arquivos do Parquet armazenados no cold store. Por exemplo, você pode ler, transformar e limpar dados em um notebook Jupyter e, em seguida, usá-los para treinar seu modelo de Aprendizado de Máquina Azure no mesmo fluxo de trabalho Spark.

Para acessar dados diretamente da sua conta do Azure Storage, você precisa ler o acesso à conta usada para armazenar os dados de visualização do Time Series Insights. Em seguida, você pode ler dados selecionados com base `PT=Time` no tempo de criação do arquivo Parquet localizado na pasta descrita abaixo na seção [de formato de arquivo Parquet.](#parquet-file-format-and-folder-structure)  Para obter mais informações sobre como habilitar o acesso à leitura da sua conta de armazenamento, consulte [Gerenciar o acesso aos recursos da sua conta de armazenamento](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Exclusão de dados

Não exclua os arquivos de visualização de visualizações da série do tempo. Gerencie somente dados relacionados a partir de visualização de insights de séries tempo.

### <a name="parquet-file-format-and-folder-structure"></a>Formato de arquivo parquet e estrutura de pasta

Parquet é um formato de arquivo colunar de código aberto projetado para armazenamento e desempenho eficientes. O Time Series Insights Preview usa o Parquet para habilitar o desempenho de consulta baseado em ID da série do tempo em escala.  

Para obter mais informações sobre o tipo de arquivo Parquet, leia a documentação do [Parquet.](https://parquet.apache.org/documentation/latest/)

O Time Series Insights Preview armazena cópias de seus dados da seguinte forma:

* A primeira cópia inicial é particionada pelo tempo de ingestão e armazena dados aproximadamente por ordem de chegada. Esses dados residem `PT=Time` na pasta:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* A segunda cópia reparticionada é agrupada por IDs da série temporal e reside na `PT=TsId` pasta:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

Em ambos os casos, a propriedade temporal do arquivo Parquet corresponde ao tempo de criação do blob. Os dados `PT=Time` da pasta são preservados sem alterações uma vez que são gravados no arquivo. Os dados `PT=TsId` na pasta serão otimizados para consulta ao longo do tempo e não estão estáticos.

> [!NOTE]
> * `<YYYY>`mapas para uma representação de quatro dígitos ano.
> * `<MM>`mapas para uma representação de dois dígitos por mês.
> * `<YYYYMMDDHHMMSSfff>`mapas para uma representação de carimbo`YYYY`de tempo com`MM`quatro dígitos ano`DD`( ), mês`HH`de dois dígitos ( ), dia de dois dígitos ( ), dois dígitos hora ( ), dois dígitos minuto (`MM`), dois dígitos segundo (`SS`) e três dígitos milissegundo (`fff`).

Os eventos de visualização de insights da série do tempo são mapeados para o conteúdo do arquivo Parquet da seguinte forma:

* Cada evento é mapeado para uma única linha.
* Cada linha inclui a coluna **carimbo de hora** com um carimbo de hora do evento. A propriedade do carimbo de tempo nunca é nula. Ele é padrão para o **tempo enfileirado** do evento se a propriedade do carimbo de tempo não for especificada na origem do evento. O carimbo de tempo armazenado está sempre em UTC.
* Cada linha inclui as colunas TSID (Time Series ID) conforme definido quando o ambiente Time Series Insights é criado. O nome da propriedade `_string` TSID inclui o sufixo.
* Todas as outras propriedades enviadas como dados de telemetria são mapeadas para nomes de colunas que terminam `_string` com (string), `_bool` (Boolean), `_datetime` (datetime) ou `_double` (duplo), dependendo do tipo de propriedade.
* Este esquema de mapeamento se aplica à primeira versão do formato do arquivo, referenciado como **V=1** e armazenado na pasta base de mesmo nome. À medida que esse recurso evolui, esse esquema de mapeamento pode mudar e o nome de referência incrementado.

## <a name="next-steps"></a>Próximas etapas

- Leia [como moldar json para ingresse e consulta](./time-series-insights-update-how-to-shape-events.md).

- Leia sobre a nova [modelagem de dados](./time-series-insights-update-tsm.md).
