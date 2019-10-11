---
title: Armazenamento de dados e entrada na Versão Prévia do Azure Time Series Insights | Microsoft Docs
description: Entendendo entrada e armazenamento de dados na Versão Prévia do Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: seodec18
ms.openlocfilehash: 9af53728ee038a6511c434aeedfdb9afdab6d04b
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273879"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Entrada e armazenamento de dados na Versão Prévia do Azure Time Series Insights

Este artigo descreve as alterações à entrada e ao armazenamento de dados da Versão Prévia do Azure Time Series Insights. Ele aborda a estrutura subjacente do armazenamento, o formato de arquivo e a propriedade de ID de Série Temporal. O artigo também descreve o processo subjacente de entrada, taxa de transferência e limitações.

## <a name="data-ingress"></a>Entrada de dados

Azure Time Series Insights políticas de entrada de dados determinam de onde os dados podem ser originados e em qual formato.

[Visão geral do modelo da série @no__t 1Time](media/v2-update-storage-ingress/tsi-data-ingress.png)](media/v2-update-storage-ingress/tsi-data-ingress.png#lightbox)

### <a name="ingress-policies"></a>Políticas de entrada

A versão prévia do Time Series Insights dá suporte às mesmas fontes de eventos e tipos de arquivos aos quais Time Series Insights atualmente dá suporte:

- [Hub IoT do Azure](../iot-hub/about-iot-hub.md)
- [Hubs de eventos do Azure](../event-hubs/event-hubs-about.md)
  
O Azure Time Series Insights dá suporte a JSON enviado por meio do Hub IoT do Azure ou hubs de eventos do Azure. Para otimizar seus dados de JSON de IoT, saiba [como formatar JSON](./time-series-insights-send-events.md#supported-json-shapes).

### <a name="data-storage"></a>Armazenamento de dados

Ao criar uma Time Series Insights visualização do ambiente de SKU pago conforme o uso, você cria dois recursos:

* Um ambiente do Time Series Insights.
* Uma conta V1 de uso geral do Armazenamento do Azure em que os dados serão armazenados.

A visualização Time Series Insights usa o armazenamento de BLOBs do Azure com o tipo de arquivo parquet. O Time Series Insights gerencia todas as operações de dados, incluindo criação de blobs, indexação e particionamento de dados na conta de armazenamento do Azure. Você pode criar esses blobs usando uma conta de Armazenamento do Azure.

Como outros blobs de Armazenamento do Azure, blobs criados pelo Time Series Insights permitem ler e gravá-los para dar suporte a vários cenários de integração.

### <a name="data-availability"></a>Disponibilidade de dados

A Time Series Insights visualização indexa dados usando uma estratégia de otimização de tamanho de BLOB. Dados ficarão disponíveis para consulta depois que forem indexados, o que é baseado na quantidade de dados chegando e em que velocidade.

> [!IMPORTANT]
> * A versão de GA (disponibilidade geral) Time Series Insights disponibilizará os dados em até 60 segundos depois de chegar a uma fonte de evento.
> * Durante a versão prévia, espere um período mais longo antes de os dados ficarem disponíveis.
> * Se você tiver alguma latência significativa, fale conosco.

### <a name="scale"></a>Escala

A visualização de Time Series Insights dá suporte a uma escala de entrada inicial de até 1 Mega byte por segundo (Mbps) por ambiente. Suporte aprimorado de dimensionamento está em andamento. Planejamos atualizar nossa documentação para refletir essas melhorias.

## <a name="parquet-file-format"></a>Formato de arquivo Parquet

Parquet é um formato de arquivo de dados orientado por coluna projetado para:

* Interoperabilidade
* Eficiência do espaço
* Eficiência da consulta

O Time Series Insights escolheu o Parquet porque ele oferece esquemas de codificação e compactação de dados eficientes, com o desempenho aprimorado que pode processar dados complexos em massa.

Para obter mais informações sobre o tipo de arquivo parquet, consulte a [documentação do parquet](https://parquet.apache.org/documentation/latest/).

Para obter mais informações sobre o formato de arquivo parquet no Azure, consulte [tipos de arquivo com suporte no armazenamento do Azure](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#parquet-format).

### <a name="event-structure-in-parquet"></a>Estrutura de evento em Parquet

O Time Series Insights cria e armazena cópias de blobs nos dois formatos a seguir:

1. A primeira cópia inicial é particionada por hora de chegada:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Hora de criação de blob para blobs particionados por hora de chegada.

1. A segunda cópia reparticionada é particionada por um agrupamento dinâmico de ID de Série Temporal:

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Carimbo de data/hora mínima em um blob para blobs particionados por ID de Série Temporal.

> [!NOTE]
> * `<YYYY>` mapeia para uma representação de ano de quatro dígitos.
> * `<MM>` mapeia para uma representação de mês de dois dígitos.
> * `<YYYYMMDDHHMMSSfff>` mapeia para uma representação de carimbo de data/hora com ano de quatro dígitos (`YYYY`), mês de dois dígitos (`MM`), dia de dois dígitos (`DD`), hora de dois dígitos (`HH`), minuto de dois dígitos (`MM`), segundo de dois dígitos (`SS`) e milissegundo de três dígitos (`fff`).

Eventos do Time Series Insights são mapeados para o conteúdo do arquivo Parquet da seguinte maneira:

* Cada evento é mapeado para uma única linha.
* Coluna **Carimbo de data/hora** interna com um carimbo de data/hora do evento. A propriedade de Carimbo de data/hora nunca é nula. O padrão é **Tempo de Enfileiramento de Origem do Evento** se a propriedade Carimbo de data/hora não for especificada na fonte do evento. O carimbo de data/hora está em UTC. 
* Todas as outras propriedades mapeadas para colunas terminam com `_string` (cadeia de caracteres) `_bool` (booliano) `_datetime` (data/hora) e `_double` (duplo), dependendo do tipo de propriedade.
* Esse é o esquema de mapeamento para a primeira versão do formato de arquivo, que chamamos de **V=1**. À medida que esse recurso evolui, o nome é incrementado para **V=2**, **V=3** e assim por diante.

## <a name="azure-storage"></a>Armazenamento do Azure

Esta seção descreve os detalhes do armazenamento do Azure relevantes para Azure Time Series Insights.

Para obter uma descrição completa do serviço de armazenamento de BLOBs do Azure, leia a [introdução aos blobs de armazenamento](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Sua conta de armazenamento

Quando você cria um ambiente pago conforme o uso do Time Series Insights, cria dois recursos: um ambiente do Time Series Insights e uma conta V1 de uso geral do Armazenamento do Azure em que os dados serão armazenados. Escolhemos tornar a V1 de uso geral do Armazenamento do Azure o recurso padrão devido à sua interoperabilidade, ao seu preço e ao seu desempenho.

O Time Series Insights publica até duas cópias de cada evento em sua conta de Armazenamento do Azure. A cópia inicial é sempre preservada para que você possa consultá-la rapidamente usando outros serviços. Você pode facilmente usar Spark, Hadoop e outras ferramentas familiares em IDs de Série Temporal em relação aos arquivos Parquet brutos, pois esses mecanismos dão suporte à filtragem básica de nome de arquivo. Agrupar blobs por ano e mês é uma maneira útil de listar os blobs dentro de um período específico para um trabalho personalizado.

Além disso, o Time Series Insights reparticiona os arquivos Parquet para otimizar as APIs do Time Series Insights. O arquivo reparticionado mais recentemente também é salvo.

Durante a visualização pública, os dados são armazenados indefinidamente em sua conta de armazenamento do Azure.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Escrever e editar blobs do Time Series Insights

Para garantir a disponibilidade de dados e o desempenho da consulta, não edite nem exclua nenhum blob criado pelo Time Series Insights.

> [!TIP]
> O desempenho do Time Series Insights pode ser afetado negativamente se você ler ou gravar em seus blobs com muita frequência.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Acessando e exportando dados da Versão Prévia do Time Series Insights

Você talvez queira acessar dados armazenados no gerenciador da Versão Prévia do Time Series Insights para usá-los junto com outros serviços. Por exemplo, você talvez queira usar seus dados para o relatório no Power BI, para executar o aprendizado de máquina usando o Azure Machine Learning Studio ou para usar em um aplicativo de bloco de anotações com o Notebooks Jupyter.

Você pode acessar seus dados de três maneiras gerais:

* No Gerenciador de visualização Time Series Insights: você pode exportar dados como um arquivo CSV do Gerenciador de visualização de Time Series Insights. Para obter mais informações, confira [Gerenciador da Versão Prévia do Time Series Insights](./time-series-insights-update-explorer.md).
* Na Time Series Insights APIs de visualização: o ponto de extremidade da API pode ser acessado em `/getRecorded`. Para saber mais sobre essa API, confira [Consulta de Série Temporal](./time-series-insights-update-tsq.md).
* Diretamente de uma conta de armazenamento do Azure (abaixo).

#### <a name="from-an-azure-storage-account"></a>De uma conta de armazenamento do Azure

* Você precisa ter acesso de leitura à conta que está usando para acessar seus dados do Time Series Insights. Para obter mais informações, confira [gerenciar o acesso aos recursos da conta de armazenamento](../storage/blobs/storage-manage-access-to-resources.md).
* Para obter mais informações sobre maneiras diretas de ler dados do armazenamento de BLOBs do Azure, consulte [escolher uma solução do Azure para transferência de dados](../storage/common/storage-choose-data-transfer-solution.md).
* Para exportar dados de uma conta de armazenamento do Azure:
    * Primeiro, verifique se a conta atende aos requisitos necessários para a exportação de dados. Para obter mais informações, confira [Requisitos de importação e exportação de armazenamento](../storage/common/storage-import-export-requirements.md).
    * Para saber mais sobre outras maneiras de exportar dados da sua conta de armazenamento do Azure, confira [Importar e exportar dados de blobs](../storage/common/storage-import-export-data-from-blobs.md).

### <a name="data-deletion"></a>Exclusão de dados

Não exclua BLOBs. Eles não só são úteis para auditar e manter um registro de seus dados, a visualização Time Series Insights mantém os metadados de BLOB em cada blob.

## <a name="partitions"></a>Partições

Cada ambiente de visualização de Time Series Insights deve ter uma propriedade **ID de série temporal** e uma propriedade **timestamp** que a identificam de forma exclusiva. Sua ID de Série Temporal atua como uma partição lógica para seus dados e fornece ao ambiente da Versão Prévia do Time Series Insights um limite natural para distribuir dados entre partições físicas. As partições físicas são gerenciadas pela versão prévia do Time Series Insights em uma conta de armazenamento do Azure.

O Time Series Insights usa o particionamento dinâmico para otimizar o desempenho de consulta e armazenamento removendo e recriando partições. O algoritmo de particionamento dinâmico da Versão Prévia do Time Series Insights tenta impedir que uma única partição física tenha dados de diferentes partições lógicas distintas. Em outras palavras, o algoritmo de particionamento mantém todos os dados específicos para uma única ID de Série Temporal exclusivamente presente em arquivos Parquet sem serem intercalados com outras IDs de Série Temporal. O algoritmo de particionamento dinâmico também tenta preservar a ordem original dos eventos dentro de uma única ID de Série Temporal.

Inicialmente, no momento da entrada, os dados são particionados pelo Carimbo de data/hora de modo que uma única partição lógica dentro do intervalo de tempo fornecido possa ser distribuída em várias partições físicas. Uma única partição física também pode conter muitas ou todas as lógicas de partições. Devido às limitações de tamanho de blob, mesmo com particionamento ideal, uma única partição lógica pode ocupar várias partições físicas.

> [!NOTE]
> Por padrão, o valor do Carimbo de data/hora é a mensagem *Tempo de Enfileiramento* em sua fonte de evento configurada.

Se você estiver carregando dados históricos ou mensagens em lote, atribua o valor que deseja armazenar com os dados para a propriedade Carimbo de data/hora mapeada para o Carimbo de data/hora apropriado. A propriedade de carimbo de data/hora diferencia maiúsculas de minúsculas. Para obter mais informações, confira [Modelo de Série Temporal](./time-series-insights-update-tsm.md).

### <a name="physical-partitions"></a>Partições físicas

Uma partição física é um blob de blocos armazenado em sua conta de armazenamento. O tamanho real dos blobs pode variar, porque o tamanho depende da taxa de push. No entanto, esperamos que os blobs tenham tamanhos de aproximadamente 20 MB a 50 MB. Essa expectativa levou a equipe do Time Series Insights a selecionar 20 MB como o tamanho para otimizar o desempenho da consulta. Esse tamanho pode mudar ao longo do tempo, dependendo do tamanho do arquivo e da velocidade de entrada de dados.

> [!NOTE]
> * Blobs são dimensionados em 20 MB.
> * Blobs do Azure são ocasionalmente reparticionados para melhorar o desempenho por meio de descarte e recriação.
> * Além disso, os mesmos dados do Time Series Insights podem estar presentes em dois ou mais blobs.

### <a name="logical-partitions"></a>Partições lógicas

Uma partição lógica é uma partição em uma partição física que armazena todos os dados associados a um valor de chave de partição única. O Time Series Insights visualização logicamente particiona cada blob com base em duas propriedades:

* **ID da série temporal**: A chave de partição para todos os dados do Time Series Insights dentro do fluxo de eventos e do modelo.
* **Carimbo de data/hora**: A hora com base na entrada inicial.

A visualização de Time Series Insights fornece consultas de alto desempenho baseadas nessas duas propriedades. Essas duas propriedades também fornecem o método mais eficaz de entregar dados do Time Series Insights rapidamente.

É importante selecionar uma ID de Série Temporal apropriada, porque ela é uma propriedade imutável. Para obter mais informações, confira [Escolher IDs de Série Temporal](./time-series-insights-update-how-to-id.md).

## <a name="next-steps"></a>Próximas etapas

- Leia [Armazenamento e entrada da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-storage-ingress.md).

- Leia sobre a nova [Modelagem de dados](./time-series-insights-update-tsm.md).
