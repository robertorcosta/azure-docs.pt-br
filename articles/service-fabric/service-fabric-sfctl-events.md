---
title: Azure Service Fabric CLI- eventos sfctl
description: Descreve os comandos de eventos CLI sfctl do fabric de serviço.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906096"
---
# <a name="sfctl-events"></a>eventos sfctl
Recupere eventos da loja de eventos (se o serviço EventStore já estiver instalado).

O serviço do sistema EventStore pode ser adicionado através de uma atualização de configuração para qualquer cluster SFRP em execução >=6.4. Por favor,\: verifique\:a url a seguir https //docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore.

## <a name="commands"></a>Comandos

|Comando|Descrição|
| --- | --- |
| lista de todos os aplicativos | Obtém todos os eventos relacionados a aplicativos. |
| lista de todos nós | Recebe todos os Eventos Relacionados com Nós. |
| lista de partições | Obtém todos os eventos relacionados a Partições. |
| lista de todos os serviços | Recebe todos os eventos relacionados aos Serviços. |
| lista de aplicativos | Obtém eventos relacionados a aplicativos. |
| cluster-list | Obtém todos os eventos relacionados ao Cluster. |
| lista de nó | Recebe um evento relacionado ao Nó. |
| partição-todas-réplicas-lista | Obtém todos os eventos relacionados a réplicas para uma partição. |
| lista de partição | Obtém eventos relacionados à partição. |
| lista de réplicas de partição | Obtém um parto de eventos relacionados à réplica. |
| lista de serviços | Recebe um serviço relacionado a eventos. |

## <a name="sfctl-events-all-applications-list"></a>sfctl eventos todos os aplicativos-lista
Obtém todos os eventos relacionados a aplicativos.

A resposta é a lista de objetos ApplicationEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de consulta no ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Obrigatório] | O tempo de início de uma consulta de consulta no ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --eventos-tipos-filtro | Esta é uma seqüência separada de comuma especificando os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --exclua-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovado. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se a verdade for aprovada. caso contrário, o campo CorrelationEvents é processado e o campo HasCorrelatedEvents em cada FabricEvent é preenchido. |
| --timeout -t | O tempo de intervalo do servidor para realizar a operação em segundos. Este tempo estipula a duração de tempo que o cliente está disposto a esperar para que a operação solicitada seja concluída. O valor padrão deste parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-all-nodes-list"></a>sfctl eventos all-nodes-list
Recebe todos os Eventos Relacionados com Nós.

A resposta é a lista de objetos NodeEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de consulta no ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Obrigatório] | O tempo de início de uma consulta de consulta no ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --eventos-tipos-filtro | Esta é uma seqüência separada de comuma especificando os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --exclua-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovado. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se a verdade for aprovada. caso contrário, o campo CorrelationEvents é processado e o campo HasCorrelatedEvents em cada FabricEvent é preenchido. |
| --timeout -t | O tempo de intervalo do servidor para realizar a operação em segundos. Este tempo estipula a duração de tempo que o cliente está disposto a esperar para que a operação solicitada seja concluída. O valor padrão deste parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-all-partitions-list"></a>sfctl eventos all-partições-list
Obtém todos os eventos relacionados a Partições.

A resposta é lista de objetos PartitionEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de consulta no ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Obrigatório] | O tempo de início de uma consulta de consulta no ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --eventos-tipos-filtro | Esta é uma seqüência separada de comuma especificando os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --exclua-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovado. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se a verdade for aprovada. caso contrário, o campo CorrelationEvents é processado e o campo HasCorrelatedEvents em cada FabricEvent é preenchido. |
| --timeout -t | O tempo de intervalo do servidor para realizar a operação em segundos. Este tempo estipula a duração de tempo que o cliente está disposto a esperar para que a operação solicitada seja concluída. O valor padrão deste parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-all-services-list"></a>sfctl eventos todos os serviços-lista
Recebe todos os eventos relacionados aos Serviços.

A resposta é a lista de objetos ServiceEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de consulta no ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Obrigatório] | O tempo de início de uma consulta de consulta no ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --eventos-tipos-filtro | Esta é uma seqüência separada de comuma especificando os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --exclua-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovado. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se a verdade for aprovada. caso contrário, o campo CorrelationEvents é processado e o campo HasCorrelatedEvents em cada FabricEvent é preenchido. |
| --timeout -t | O tempo de intervalo do servidor para realizar a operação em segundos. Este tempo estipula a duração de tempo que o cliente está disposto a esperar para que a operação solicitada seja concluída. O valor padrão deste parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-application-list"></a>sfctl eventos lista de aplicativos
Obtém eventos relacionados a aplicativos.

A resposta é a lista de objetos ApplicationEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --application-id [Obrigatório] | A identidade do aplicativo. Normalmente, este é o nome completo do aplicativo sem o esquema de URI "fabric\:". A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do aplicativo for "fabric\:/meuaplicativo/aplicativo1", a identidade do aplicativo será "meuaplicativo\~aplicativo1" na versão 6.0 e superiores, e "meuaplicativo/aplicativo1" nas versões anteriores. |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de consulta no ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Obrigatório] | O tempo de início de uma consulta de consulta no ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --eventos-tipos-filtro | Esta é uma seqüência separada de comuma especificando os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --exclua-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovado. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se a verdade for aprovada. caso contrário, o campo CorrelationEvents é processado e o campo HasCorrelatedEvents em cada FabricEvent é preenchido. |
| --timeout -t | O tempo de intervalo do servidor para realizar a operação em segundos. Este tempo estipula a duração de tempo que o cliente está disposto a esperar para que a operação solicitada seja concluída. O valor padrão deste parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-cluster-list"></a>sfctl eventos cluster-list
Obtém todos os eventos relacionados ao Cluster.

A resposta é a lista de objetos do ClusterEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de consulta no ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --start-time-utc [Obrigatório] | O tempo de início de uma consulta de consulta no ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --eventos-tipos-filtro | Esta é uma seqüência separada de comuma especificando os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --exclua-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovado. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se a verdade for aprovada. caso contrário, o campo CorrelationEvents é processado e o campo HasCorrelatedEvents em cada FabricEvent é preenchido. |
| --timeout -t | O tempo de intervalo do servidor para realizar a operação em segundos. Este tempo estipula a duração de tempo que o cliente está disposto a esperar para que a operação solicitada seja concluída. O valor padrão deste parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-node-list"></a>sfctl eventos nó-lista
Recebe um evento relacionado ao Nó.

A resposta é a lista de objetos NodeEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de consulta no ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --node-name      [Obrigatório] | O nome do nó. |
| --start-time-utc [Obrigatório] | O tempo de início de uma consulta de consulta no ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --eventos-tipos-filtro | Esta é uma seqüência separada de comuma especificando os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --exclua-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovado. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se a verdade for aprovada. caso contrário, o campo CorrelationEvents é processado e o campo HasCorrelatedEvents em cada FabricEvent é preenchido. |
| --timeout -t | O tempo de intervalo do servidor para realizar a operação em segundos. Este tempo estipula a duração de tempo que o cliente está disposto a esperar para que a operação solicitada seja concluída. O valor padrão deste parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl eventos partição-todas-réplicas-lista
Obtém todos os eventos relacionados a réplicas para uma partição.

A resposta é a lista de objetos ReplicaEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de consulta no ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --partition-id   [Obrigatório] | A identidade da partição. |
| --start-time-utc [Obrigatório] | O tempo de início de uma consulta de consulta no ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --eventos-tipos-filtro | Esta é uma seqüência separada de comuma especificando os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --exclua-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovado. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se a verdade for aprovada. caso contrário, o campo CorrelationEvents é processado e o campo HasCorrelatedEvents em cada FabricEvent é preenchido. |
| --timeout -t | O tempo de intervalo do servidor para realizar a operação em segundos. Este tempo estipula a duração de tempo que o cliente está disposto a esperar para que a operação solicitada seja concluída. O valor padrão deste parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-partition-list"></a>sfctl eventos partição-lista
Obtém eventos relacionados à partição.

A resposta é lista de objetos PartitionEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de consulta no ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --partition-id   [Obrigatório] | A identidade da partição. |
| --start-time-utc [Obrigatório] | O tempo de início de uma consulta de consulta no ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --eventos-tipos-filtro | Esta é uma seqüência separada de comuma especificando os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --exclua-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovado. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se a verdade for aprovada. caso contrário, o campo CorrelationEvents é processado e o campo HasCorrelatedEvents em cada FabricEvent é preenchido. |
| --timeout -t | O tempo de intervalo do servidor para realizar a operação em segundos. Este tempo estipula a duração de tempo que o cliente está disposto a esperar para que a operação solicitada seja concluída. O valor padrão deste parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-partition-replica-list"></a>sfctl eventos partição-réplica-lista
Obtém um parto de eventos relacionados à réplica.

A resposta é a lista de objetos ReplicaEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de consulta no ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --partition-id   [Obrigatório] | A identidade da partição. |
| --replica-id [Obrigatório] | O identificador da réplica. |
| --start-time-utc [Obrigatório] | O tempo de início de uma consulta de consulta no ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --eventos-tipos-filtro | Esta é uma seqüência separada de comuma especificando os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --exclua-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovado. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se a verdade for aprovada. caso contrário, o campo CorrelationEvents é processado e o campo HasCorrelatedEvents em cada FabricEvent é preenchido. |
| --timeout -t | O tempo de intervalo do servidor para realizar a operação em segundos. Este tempo estipula a duração de tempo que o cliente está disposto a esperar para que a operação solicitada seja concluída. O valor padrão deste parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-events-service-list"></a>sfctl eventos lista de serviços
Recebe um serviço relacionado a eventos.

A resposta é a lista de objetos ServiceEvent.

### <a name="arguments"></a>Argumentos

|Argumento|Descrição|
| --- | --- |
| --fim-tempo-utc [Obrigatório] | O tempo final de uma consulta de consulta no ISO UTC yyyy-MM-ddTHH\:mm\:ssZ. |
| --service-id          [Obrigatório] | A identidade do serviço. Essa ID normalmente é o nome completo do serviço sem o ' malha\:' esquema de URI. A partir da versão 6.0, nomes hierárquicos são delimitados pelo caractere "\~". Por exemplo, se o nome do serviço for "fabric\:/myapp/app1/svc1", a identidade do serviço será "myapp\~app1\~svc1" na versão 6.0 e superiores, e "myapp/app1/svc1" em versões anteriores. |
| --start-time-utc [Obrigatório] | O tempo de início de uma consulta de consulta no ISO UTC\:\:yyyy-MM-ddTHH mm ssZ. |
| --eventos-tipos-filtro | Esta é uma seqüência separada de comuma especificando os tipos de FabricEvents que só devem ser incluídos na resposta. |
| --exclua-análise-eventos | Este param desativa a recuperação de AnalysisEvents se for aprovado. |
| --skip-correlation-lookup | Este param desativa a pesquisa de informações de CorrelatedEvents se a verdade for aprovada. caso contrário, o campo CorrelationEvents é processado e o campo HasCorrelatedEvents em cada FabricEvent é preenchido. |
| --timeout -t | O tempo de intervalo do servidor para realizar a operação em segundos. Este tempo estipula a duração de tempo que o cliente está disposto a esperar para que a operação solicitada seja concluída. O valor padrão deste parâmetro é de 60 segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|Descrição|
| --- | --- |
| --debug | Aumente o detalhamento do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | Formato de saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o detalhamento do log. Use --debug para logs de depuração completos. |

