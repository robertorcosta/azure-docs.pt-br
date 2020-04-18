---
title: Problemas de solução de problemas com hubs de eventos do Azure para Apache Kafka
description: Este artigo mostra como solucionar problemas com o Azure Event Hubs para Apache Kafka
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 12ddc5fa74b7a1b42bbd64fde9ec3410b1c1e425
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606724"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Guia de solução de problemas apache Kafka para Hubs de Eventos
Este artigo fornece dicas de solução de problemas para problemas que você pode encontrar ao usar Hubs de Eventos para Apache Kafka. 

## <a name="server-busy-exception"></a>Exceção ocupada do servidor
Você pode receber exceção do Server Busy devido ao estrangulamento kafka. Com clientes AMQP, o Event Hubs retorna imediatamente uma exceção **do Server Busy** após o estrangulamento do serviço. É equivalente a uma mensagem de "tente de novo depois". Em Kafka, as mensagens são atrasadas antes de serem concluídas. O comprimento de atraso é `throttle_time_ms` devolvido em milissegundos como na resposta de produção/busca. Na maioria dos casos, essas solicitações atrasadas não são registradas como exceções do ServerBusy nos painéis do Event Hubs. Em vez disso, `throttle_time_ms` o valor da resposta deve ser usado como um indicador de que o throughput excedeu a cota provisionada.

Se o tráfego for excessivo, o serviço tem o seguinte comportamento:

- Se o atraso da solicitação exceder o tempo de solicitação, o Event Hubs retorna o código de erro **de violação de** diretiva.
- Se o atraso da solicitação de busca exceder o tempo de solicitação, o Event Hubs registra a solicitação como estrangulada e responde com um conjunto vazio de registros e sem código de erro.

[Clusters dedicados](event-hubs-dedicated-overview.md) não têm mecanismos de estrangulamento. Você é livre para consumir todos os seus recursos de cluster.

## <a name="no-records-received"></a>Nenhum registro recebido
Você pode ver os consumidores não recebendo nenhum registro e constantemente reequilibrando. Nesse cenário, os consumidores não recebem registros e se reequilibram constantemente. Não há exceção ou erro quando isso acontece, mas os registros de Kafka mostrarão que os consumidores estão presos tentando se juntar ao grupo e atribuir partições. Existem algumas causas possíveis:

- Certifique-se `request.timeout.ms` de que o seu é pelo menos o `session.timeout.ms` valor recomendado de 60000 e o seu é pelo menos o valor recomendado de 30000. Ter essas configurações muito baixas pode causar tempo sumido dos consumidores, que então causam reequilíbrios (que, em seguida, causam mais tempo outs, que causam mais reequilíbrio, e assim por diante) 
- Se sua configuração corresponder aos valores recomendados, e você ainda estiver vendo um reequilíbrio constante, sinta-se livre para abrir um problema (certifique-se de incluir toda a sua configuração no problema para que possamos ajudar a depurar)!

## <a name="compressionmessage-format-version-issue"></a>Problema da versão do formato compressão/mensagem
Kafka suporta compressão, e event Hubs para Kafka atualmente não. Erros que mencionam uma versão em `The message format version on the broker does not support the request.`formato de mensagem (por exemplo, ) são causados quando um cliente tenta enviar mensagens Kafka compactadas para nossos corretores.

Se os dados compactados forem necessários, comprimir seus dados antes de enviá-los para os corretores e descomprimir após o recebimento é uma solução válida. O corpo de mensagem é apenas uma matriz de bytes para o serviço, de modo que a compactação/descompactação do lado do cliente não causará problemas.

## <a name="unknownserverexception"></a>DesconhecidoServerException
Você pode receber uma UnknownServerException de bibliotecas de clientes Kafka semelhante ao exemplo a seguir: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Abra um ticket com suporte à Microsoft.  O registro em nível de depuração e os carimbos de tempo de exceção no UTC são úteis para depurar o problema. 

## <a name="other-issues"></a>Outros problemas
Verifique os itens a seguir se você ver problemas ao usar Kafka em Event Hubs.

- **Firewall bloqueando o tráfego** - Certifique-se de que a porta **9093** não esteja bloqueada pelo firewall.
- **Agência de autorização** de tópicos - As causas mais comuns dessa exceção são:
    - Um erro de digitação na seqüência de conexão em seu arquivo de configuração, ou
    - Tentando usar hubs de eventos para Kafka em um namespace de nível básico. Event Hubs for Kafka [é suportado apenas para namespaces de nível padrão e dedicados](https://azure.microsoft.com/pricing/details/event-hubs/).
- **Kafka versão incompatibilidade** - Event Hubs for Kafka Ecosystems suporta versões Kafka 1.0 e posteriores. Alguns aplicativos que usam a versão 0.10 de Kafka e posteriores podem ocasionalmente funcionar devido à retrocompatibilidade do protocolo Kafka, mas recomendamos fortemente o uso de versões antigas da API. As versões Kafka 0.9 e anteriores não suportam os protocolos SASL necessários e não podem se conectar aos Hubs de eventos.
- **Codificações estranhas em cabeçalhos AMQP ao consumir com Kafka** - ao enviar eventos para um hub de eventos sobre AMQP, quaisquer cabeçalhos de carga AMQP são serializados na codificação AMQP. Os consumidores kafka não desserializam os cabeçalhos da AMQP. Para ler os valores do cabeçalho, decodifique manualmente os cabeçalhos AMQP. Alternativamente, você pode evitar o uso de cabeçalhos AMQP se você sabe que você estará consumindo via protocolo Kafka. Para obter mais informações, consulte [este problema do GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56).
- **Autenticação SASL** - Fazer com que sua estrutura coopere com o protocolo de autenticação SASL exigido pelos Event Hubs pode ser mais difícil do que parece. Veja se você pode solucionar problemas da configuração usando os recursos da sua estrutura na autenticação SASL. 

## <a name="limits"></a>limites
Apache Kafka vs. Event Hubs Kafka. Na maioria das vezes, os Hubs de Eventos para Ecossistemas Kafka têm os mesmos padrões, propriedades, códigos de erro e comportamento geral que o Apache Kafka faz. Os casos em que esses dois diferem explicitamente (ou onde o Event Hubs impõe um limite que Kafka não faz) estão listados abaixo:

- O comprimento máximo `group.id` da propriedade é de 256 caracteres
- O tamanho `offset.metadata.max.bytes` máximo de é de 1024 bytes
- Os confirmadores de deslocamento são estrangulados a 4 chamadas/segundo por partição com um tamanho máximo de log interno de 1 MB


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os Hubs de Eventos e Hubs de Eventos para o Kafka, confira os artigos a seguir:  

- [Guia de desenvolvedores Apache Kafka para Hubs de Eventos](apache-kafka-developer-guide.md)
- [Guia de migração Apache Kafka para Hubs de Eventos](apache-kafka-migration-guide.md)
- [Perguntas frequentes - Hubs de eventos para Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Configurações recomendadas](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)
