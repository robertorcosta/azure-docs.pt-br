---
title: Níveis de compatibilidade do Azure Stream Analytics
description: Saiba como definir um nível de compatibilidade para um trabalho do Azure Stream Analytics e as principais alterações no nível de compatibilidade mais recente
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 8f22b1ff97826dc318794aca58973b1276e74209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087865"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Nível de compatibilidade para trabalhos do Azure Stream Analytics

Este artigo descreve a opção de nível de compatibilidade no Azure Stream Analytics. O Stream Analytics é um serviço gerenciado, com atualizações regulares de recursos e melhorias de desempenho. A maioria das atualizações de tempos de execução do serviço são automaticamente disponibilizadas para usuários finais. 

No entanto, algumas novas funcionalidades no serviço podem introduzir uma grande mudança, como uma mudança no comportamento de um trabalho existente, ou uma mudança na forma como os dados são consumidos na execução de empregos. Você pode manter seus trabalhos de Stream Analytics existentes funcionando sem grandes alterações, deixando a configuração de nível de compatibilidade reduzida. Quando você estiver pronto para os últimos comportamentos de tempo de execução, você pode optar por aumentar o nível de compatibilidade. 

## <a name="choose-a-compatibility-level"></a>Escolha um nível de compatibilidade

O nível de compatibilidade controla o comportamento de runtime de um trabalho do Stream Analytics. 

Atualmente, o Azure Stream Analytics suporta três níveis de compatibilidade:

* 1.0 - Nível de compatibilidade original, introduzido durante a disponibilidade geral do Azure Stream Analytics há vários anos.
* 1.1 - Comportamento anterior
* 1.2 - Comportamento mais novo com melhorias mais recentes

Quando você cria um novo trabalho de Stream Analytics, é uma prática recomendada para criá-lo usando o nível de compatibilidade mais recente. Inicie seu projeto de trabalho contando com os comportamentos mais recentes, para evitar mudanças adicionais e complexidade mais tarde.

## <a name="set-the-compatibility-level"></a>Definir o nível de compatibilidade

Você pode definir o nível de compatibilidade para um trabalho de Stream Analytics no portal Azure ou usando a [chamada aPI DE TRABALHO CRIAR](/rest/api/streamanalytics/stream-analytics-job).

Para atualizar o nível de compatibilidade do trabalho no portal Azure:

1. Use o [portal Azure](https://portal.azure.com) para localizar seu trabalho no Stream Analytics.
2. **Interrompa** o trabalho antes de atualizar o nível de compatibilidade. Não será possível atualizar o nível de compatibilidade se o trabalho estiver em um estado de execução.
3. Na **posição Configurar, selecione** **Nível de compatibilidade**.
4. Escolha o valor de nível de compatibilidade que deseja.
5. Selecione **Salvar** na parte inferior da página.

![Nível de compatibilidade do Stream Analytics no portal do Azure](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Quando você atualiza o nível de compatibilidade, o compilador T valida o trabalho com a sintaxe que corresponde ao nível de compatibilidade selecionado.

## <a name="compatibility-level-12"></a>Nível de compatibilidade 1.2

As seguintes grandes mudanças são introduzidas no nível de compatibilidade 1.2:

###  <a name="amqp-messaging-protocol"></a>Protocolo de mensagens AMQP

**1.2 nível**: O Azure Stream Analytics usa o protocolo de mensagens [AMQP (Advanced Message Queueing Protocol,](../service-bus-messaging/service-bus-amqp-overview.md) protocolo avançado de filade de mensagens) para escrever em Filas e Tópicos de Ônibus de Serviço. O AMQP permite que você crie várias plataformas, aplicativos híbridos usando um protocolo de padrão aberto.

### <a name="geospatial-functions"></a>Funções geoespaciais

**Níveis anteriores:** O Azure Stream Analytics usou cálculos de Geografia.

**Nível 1.2:** O Azure Stream Analytics permite calcular coordenadas geométricas projetadas. Não há mudança na assinatura das funções geoespaciais. No entanto, sua semântica é ligeiramente diferente, permitindo uma computação mais precisa do que antes.

O Azure Stream Analytics suporta indexação de dados de referência geoespacial. Os dados de referência que contêm elementos geoespaciais podem ser indexados para uma computação de adesão mais rápida.

As funções geoespaciais atualizadas trazem toda a expressividade do formato geoespacial de Texto Bem Conhecido (WKT). Você pode especificar outros componentes geoespaciais que não foram suportados anteriormente com GeoJson.

Para obter mais informações, consulte [Atualizações de recursos geoespaciais no Azure Stream Analytics – Cloud e IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Execução de consulta paralela para fontes de entrada com várias partições

**Níveis anteriores:** As consultas do Azure Stream Analytics exigiram o uso da cláusula PARTITION BY para paralelemular o processamento de consultas entre partições de origem de entrada.

**Nível 1.2:** Se a lógica de consulta puder ser paralelenada entre as partições de origem de entrada, o Azure Stream Analytics criará instâncias de consulta separadas e executa cálculos em paralelo.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Integração da API em massa nativa com a saída do CosmosDB

**Níveis anteriores:** O comportamento upsert foi *inserir ou mesclar*.

**Nível 1.2:** A integração da API em massa nativa com a saída do CosmosDB maximiza o throughput e lida eficientemente com as solicitações de estrangulamento. Para obter mais informações, consulte [a saída do Azure Stream Analytics para a página azure Cosmos DB](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12).

O comportamento upsert é *inserir ou substituir*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset ao escrever para a saída SQL

**Níveis anteriores:** Os tipos [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) foram ajustados para UTC.

**Nível 1.2:** DateTimeOffset não está mais ajustado.

### <a name="long-when-writing-to-sql-output"></a>Muito tempo ao escrever para a saída SQL

**Níveis anteriores:** Os valores foram truncados com base no tipo de destino.

**Nível 1.2:** Os valores que não se encaixam no tipo de destino são tratados de acordo com a política de erro de saída.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Serialização de registro e matriz ao escrever para saída SQL

**Níveis anteriores:** Os registros foram escritos como "Record" e os arrays foram escritos como "Array".

**Nível 1.2:** Registros e matrizes são serializados no formato JSON.

### <a name="strict-validation-of-prefix-of-functions"></a>Validação rigorosa do prefixo de funções

**Níveis anteriores:** Não houve validação rigorosa dos prefixos de função.

**Nível 1.2:** O Azure Stream Analytics tem uma validação rigorosa dos prefixos de função. Adicionar um prefixo a uma função incorporada causa um erro. Por exemplo,`myprefix.ABS(…)` não é suportado.

Adicionar um prefixo aos agregados incorporados também resulta em erro. Por exemplo, `myprefix.SUM(…)` não é suportado.

O uso do prefixo "sistema" para quaisquer funções definidas pelo usuário resulta em erro.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Disallow Array and Object como propriedades-chave no adaptador de saída Cosmos DB

**Níveis anteriores:** Os tipos array e object foram suportados como uma propriedade-chave.

**Nível 1.2:** Os tipos array e object não são mais suportados como uma propriedade-chave.

## <a name="compatibility-level-11"></a>Nível de compatibilidade 1.1

As alterações principais a seguir são apresentadas no nível de compatibilidade 1.1:

### <a name="service-bus-xml-format"></a>Formato XML do Barramento de Serviço

**Nível 1.0:** O Azure Stream Analytics usou dataContractSerializer, então o conteúdo da mensagem incluía tags XML. Por exemplo: 

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**Nível 1.1:** O conteúdo da mensagem contém o fluxo diretamente sem tags adicionais. Por exemplo: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Diferenciação de maiúsculas e minúsculas persistente para nomes de campos

**Nível 1.0:** Os nomes de campo foram alterados para minúsculas quando processados pelo mecanismo Azure Stream Analytics.

**Nível 1.1:** a sensibilidade ao caso é persistida para nomes de campo quando eles são processados pelo mecanismo Azure Stream Analytics.

> [!NOTE]
> Diferenciação de maiúsculas e minúsculas persistente ainda não está disponível para trabalhos de Análise de Fluxo hospedados usando o ambiente do Edge. Como resultado, todos os nomes de campo são convertidos em minúsculas se o trabalho estiver hospedado no Edge.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**Nível 1.0:** O comando CREATE TABLE não filtraeventos com NaN (Not-a-Number. Por exemplo, Infinity, -Infinity) em um tipo de coluna FLOAT porque eles estão fora do intervalo documentado para esses números.

**Nível 1.1:** CRIAR TABELA permite especificar um esquema forte. O mecanismo do Stream Analytics valida que os dados estão em conformidade com este esquema. Com esse modelo, o comando pode filtrar eventos com valores NaN.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Desativar o upcast automático para strings de data-hora no JSON

**Nível 1.0:** O analisador JSON aumentaria automaticamente os valores de seqüência de string com informações de data/hora/fuso para o tipo DateTime e, em seguida, converteria-os em UTC. Esse comportamento resultou na perda das informações do fuso horário.

**Nível 1.1:** Não há mais upcast automaticamente de valores de seqüência com informações de data/hora/fuso para o tipo DateTime. Dessa forma, as informações de fuso horário são mantidas.

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de entradas do Azure Stream Analytics](stream-analytics-troubleshoot-input.md)
* [Stream Analytics Resource health](stream-analytics-resource-health.md)
