---
title: Monitoramento do Azure Blockchain Service (ABS)
description: Monitorando o Serviço blockchain do Azure através do Monitor Azure
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 6f2a91a8ffce67d3c4008a7587f2787f6446c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293242"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Monitore o Serviço blockchain do Azure através do Monitor Azure  

À medida que os clientes executam cenários de blockchain de grau de produção no Azure Blockchain Service (ABS), torna-se fundamental monitorar os recursos para disponibilidade, desempenho e operações. Este artigo descreve os dados de monitoramento gerados pelo Azure Blockchain Service e como se pode usar os vários recursos e integrações do Azure Monitor para analisar e alertar, para gerenciar ambientes de grau de produção.  

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?

O Azure Blockchain Service cria dados de monitoramento usando o Azure Monitor, que é um serviço de monitoramento de pilha completa no Azure que fornece um conjunto completo de recursos para monitorar seus recursos do Azure. Para obter mais informações sobre o Azure Monitor, consulte [Monitorando os recursos do Azure com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource).
 

As seções a seguir se baseiam neste artigo descrevendo os dados específicos coletados do Azure Blockchain Service e fornecendo exemplos para configurar a coleta de dados e analisar esses dados com ferramentas do Azure.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Monitore os dados coletados do Azure Blockchain Service  

O Azure Blockchain Service coleta o mesmo tipo de dados de monitoramento que outros recursos do Azure, que são descritos no Monitoramento de [dados](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data) dos recursos do Azure. Consulte [a referência de dados do Monitor Azure Blockchain Service](#monitor-azure-blockchain-service-data-reference) para obter uma referência detalhada dos logs e métricas criados pelo Azure Blockchain Service.

A página de visão geral no portal Azure para cada recurso membro do Azure Blockchain Service inclui uma breve visão das transações, incluindo as solicitações tratadas e blocos processados. Alguns desses dados são coletados automaticamente e disponíveis para análise assim que você criar o recurso membro do Azure Blockchain Service, enquanto você pode habilitar a coleta de dados adicionais com configuração adicional.

## <a name="diagnostic-settings"></a>Configurações de Diagnóstico  

As métricas da plataforma e o registro de atividades são coletados automaticamente, mas você deve criar uma configuração de diagnóstico para coletar logs de recursos ou encaminhá-los fora do Azure Monitor. Consulte [Criar configuração de diagnóstico para coletar logs e métricas da plataforma no Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) para o processo detalhado para criar uma configuração de diagnóstico usando o portal Azure, CLI ou PowerShell.

Quando você cria uma configuração de diagnóstico, você especifica quais categorias de logs coletar. As categorias do Azure Blockchain Service estão listadas abaixo.

**Logs de proxy blockchain** – Selecione a categoria se quiser monitorar os registros de proxy do NGNIX. Todos os detalhes da transação do cliente estão disponíveis para fins de auditoria e depuração.  

**Logs de aplicativos blockchain** – Selecione a categoria para obter logs do aplicativo blockchain hospedados pelo serviço gerenciado. Por exemplo, para um membro ABS-Quorum, esses logs seriam os logs do próprio Quorum.  

**Solicitações métricas**: Selecione a opção de coletar dados métricos do Azure Cosmos DB para os destinos na configuração de diagnóstico, que é coletada automaticamente no Azure Metrics. Coletar dados métricos com registros de recursos para analisar os dois tipos de dados juntos e enviar dados métricos fora do Azure Monitor.

## <a name="analyze-metric-data"></a>Analisar dados métricos  

Você pode analisar métricas para o Azure Blockchain Service com o explorador Métricas, navegar até a guia Métricas na seção Monitoramento na lâmina de recursos ABS. Consulte [Como começar com o Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) para obter detalhes sobre o uso da ferramenta. As métricas completas para o Azure Blockchain Service estão no namespace Azure Blockchain Service métricas padrão.

Você pode usar a dimensão **nó** ao adicionar um filtro ou dividir as métricas, que basicamente fornece valores métricos por nó de transação e nós validadores do membro ABS.

## <a name="analyze-log-data"></a>Analisar dados de log

Aqui estão algumas perguntas que você pode inserir na barra de pesquisa log para ajudá-lo a monitorar seus membros do Azure Blockchain Service. Essas consultas funcionam com a [nova linguagem](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Para consultar as condições de erro nos logs do aplicativo Blockchain, use a consulta abaixo:

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

Para consultar as condições de erro nos logs de proxy Blockchain, use a consulta abaixo  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
Você pode usar os filtros de tempo disponíveis nos logs do Azure para filtrar a consulta para um intervalo de tempo específico.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Monitore a referência de dados do Azure Blockchain Service  

Este artigo fornece uma referência de dados de log e métrica coletados para analisar o desempenho e a disponibilidade do Azure Blockchain Service.  

### <a name="resource-logs"></a>Logs de recursos

Todos os registros de recursos compartilham um esquema comum de alto nível com poucas propriedades exclusivas específicas para o serviço blockchain. Você pode consultar o artigo [Esquema de logs de recursos de alto nível](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema), detalhes das propriedades específicas do Azure Blockchain Service são cobertos abaixo  

A tabela a seguir lista as propriedades dos registros proxy do Azure Blockchain quando eles são coletados no Azure Monitor Logs ou no Azure Storage.  


| Nome da propriedade  | Descrição |
|:---|:---|
| time | A data e hora (UTC) em que a operação ocorreu. |
| Resourceid  | O recurso Azure Blockchain Service para o qual os logs estão habilitados.  |
| category  |Para o Azure Blockchain Service, os valores possíveis são **Proxylogs** e **Applicationlogs**. |
| operationName  | O nome da operação representada por esse evento.   |
| Nível de log  | Por padrão, o Azure Blockchain Service habilita o nível de log **informativo.**   |
| Localização do nó  | Região do Azure onde o membro blockchain é implantado.  |
| Nome blockchainNode  | O nome do nó do membro do Azure Blockchain Service em que a operação é realizada.   |
| EthMethod  | O método, que é chamado pelo protocolo blockchain subjacente, em Quorum, poderia ser eth_sendTransactions, eth_getBlockByNumber etc.  |
| Agente  | O agente de usuário que está agindo em nome de um usuário, como o navegador mozilla, edge etc. Exemplos dos valores são: "Mozilla/5.0 (Linux x64) node.js/8.16.0 v8/6.2.414,77"  |
| Código   | Códigos de erro HTTP. Normalmente 4XX e 5XX são condições de erro.  |
| NodeHost  | O nome DNS do nó.   |
| RequestMethodName | Método HTTP chamado, os valores possíveis aqui são PUT para criar membro, OBTER para obter detalhes do membro existente, DELETE para membro excluir, PATCH para atualizar membro.   |
| Nome de Membro blockchain  | Nome de membro do Azure Blockchain Service fornecido pelo usuário.  |
| Consórcio | Nome do consórcio conforme fornecido pelo usuário.   |
| Remote  | O IP do cliente onde a solicitação está chegando.  |
| RequestSize  | Tamanho da solicitação feita em bytes.  |
| Tempo de solicitação  | A duração da solicitação em milissegundos.|




A tabela a seguir lista as propriedades dos registros de aplicativos do Azure Blockchain.


| Nome da propriedade  | Descrição |
|:---|:---|
| time | A data e hora (UTC) em que a operação ocorreu. |
| Resourceid  | O recurso Azure Blockchain Service para o qual os logs estão habilitados.|
| category  |Para o Azure Blockchain Service, o valor possível são **Proxylogs** e **Applicationlogs**.  |
| operationName  | O nome da operação representada por esse evento.   |
| Nível de log  | Por padrão, o Azure Blockchain Service habilita o nível de log **informativo.**   |
| Localização do nó  | Região do Azure onde o membro blockchain é implantado.  |
| Nome blockchainNode  | O nome do nó do membro do Azure Blockchain Service em que a operação é realizada.   |
| BlockchainMessage    | Este campo conterá o registro de aplicativo Blockchain que são os registros simples de dados. Para abs-quórum, isso teria registros de quórum. Ele tem informações sobre que tipo de entrada de registro é que é informativa, erro, aviso e uma seqüência que dá mais informações sobre a ação executada.   |
| TenantID    | O inquilino específico da região do Azure Blockchain Service. O formato deste https://westlake-rp-prodcampo é . <region>.cloudapp.azure.com onde a região especifica a região Azure do membro implantado.       |
| SourceSystem   | O sistema preenche os logs, neste caso é **o Azure**.    |



### <a name="metrics"></a>Métricas

As tabelas a seguir listam as métricas da plataforma coletadas para o Azure Blockchain Service. Todas as métricas são armazenadas no namespace **Azure Blockchain Service** métricas padrão.

Para obter uma lista de todas as métricas suportadas pelo Azure Monitor (incluindo o Azure Blockchain Service), consulte [métricas suportadas pelo Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).

### <a name="blockchain-metrics"></a>Métricas blockchain

A tabela a seguir especifica a lista de métricas blockchain coletadas para o recurso membro do Azure Blockchain Service.


| Nome da métrica | Unidade  |  Tipo de agregação| Descrição   |
|---|---|---|---|
| Transações pendentes   | Contagem  |  Média | O número de transações que estão esperando para serem mineradas.   |
| Blocos processados   | Contagem  | SUM  |  O número de blocos processados em cada intervalo de tempo. Atualmente o tamanho do bloco é de 5 segundos, portanto em um minuto cada nó processará 12 blocos e 60 blocos em 5 minutos.   |
|Transações processadas    | Contagem  | SUM  | O número de transações processadas em um bloco.    |
|Transações enfileiradas    |  Contagem | Média  | O número de transações que não podem ser imediatamente mineradas. Pode ser porque eles chegaram fora de ordem e o futuro está esperando a transação anterior chegar. Ou, pode ser duas transações ter o mesmo número usado apenas uma vez (nonce) e o mesmo valor de gás, portanto o segundo não pode ser extraído.   |

### <a name="connection-metrics"></a>Métricas de conexão  

A tabela a seguir lista as diferentes métricas de conexão coletadas para o recurso membro do Azure Blockchain Service. Estas são métricas proxy NGINX.


| Nome da métrica | Unidade  |  Tipo de agregação| Descrição |
|---|---|---|---|
| Conexões aceitas   | Contagem  |  SUM | O número total de conexões de clientes aceitas.   |
| Conexões ativas  | Contagem  | Média  |  O número atual de conexões ativas de clientes, incluindo conexões de espera.    |
|Conexões manuseadas    | Contagem  | SUM  | O número total de conexões manipuladas. Geralmente, o valor do parâmetro é o mesmo que as conexões aceitas, a menos que alguns limites de recursos tenham sido atingidos.     |
|Solicitações manipuladas     |  Contagem | SUM  | O número total de solicitações de clientes.  |


### <a name="performance-metrics"></a>Métricas de desempenho

A tabela a seguir lista as métricas de desempenho coletadas para cada um dos nós do recurso membro do Azure Blockchain.  


| Nome da métrica | Unidade  |  Tipo de agregação| Descrição   |
|---|---|---|---|
| Porcentagem de uso da CPU   | Porcentagem  |  Max | A porcentagem do uso da CPU.     |
| IO Ler Bytes   | Quilobytes   | SUM  |  A soma de IO lê bytes em todos os nós do recurso membro blockchain.      |
|IO Write Bytes     | Quilobytes   | SUM  | A soma de IO escreve bytes em todos os nós do recurso membro blockchain.     |
|Limite de memória       |  Gigabytes   | Média    | Memória máxima disponível para o processo blockchain por nó. |
|Uso de Memória     | Gigabytes  |  Média | A quantidade de memória usada foi média em todos os nós.  |
| Porcentagem de uso da memória     | Porcentagem   | Média  |  A porcentagem da memória utilizada foi mediada em todos os nós.       |
|Uso de armazenamento      | Gigabytes   | Média  | O GB de armazenamento usado foi mediado em todos os nós.       |


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [o Blockchain Data Manager](https://docs.microsoft.com/azure/blockchain/service/data-manager) para capturar e transformar dados de blockchain no Azure Event Grid.
