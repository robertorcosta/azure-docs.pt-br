---
title: Perguntas frequentes - Hubs de Eventos | Microsoft Docs
description: Este artigo fornece uma lista de perguntas frequentes (FAQ) para os Hubs de Eventos do Azure e suas respostas.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/02/2019
ms.author: shvija
ms.openlocfilehash: d2d74a90d6712089cff681381e11b1caec9d469d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283660"
---
# <a name="event-hubs-frequently-asked-questions"></a>Perguntas frequentes sobre os Hubs de Eventos

## <a name="general"></a>Geral

### <a name="what-is-an-event-hubs-namespace"></a>O que é um namespace dos Hubs de Eventos?
Um namespace é um contêiner de escopo do Hub de Eventos/Tópicos do Kafka. Ele fornece a você um [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) único. Um namespace serve como um contêiner de aplicativo que pode hospedar vários Hub de Eventos/Tópicos do Kafka. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Quando criarei um novo namespace vs. use um namespace existente?
As alocações de capacidade[(unidades de throughput (TUs)](#throughput-units)são cobradas no nível de namespace. Um namespace também está associado a uma região.

Você pode querer criar um novo namespace em vez de usar um existente em um dos seguintes cenários: 

- Você precisa de um Hub de Eventos associado a uma nova região.
- Você precisa de um Event Hub associado a uma assinatura diferente.
- Você precisa de um Event Hub com uma alocação de capacidade distinta (ou seja, a necessidade de capacidade para o namespace com o hub de eventos adicionado saem do limite de 40 TU e você não quer ir para o cluster dedicado)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Qual é a diferença entre os níveis Basic e Standard dos Hubs de Eventos?

A camada Standard dos Hubs de Evento do Azure fornece recursos além daqueles disponíveis na camada Básica. Os recursos abaixo estão incluídos na camada Standard:

* Maior retenção de evento
* Conexões agenciadas adicionais, com uma taxa excedente para além do número incluído
* Mais do que um único [grupo de consumidores](event-hubs-features.md#consumer-groups)
* [Capturar](event-hubs-capture-overview.md)
* [Integração com Kafka](event-hubs-for-kafka-ecosystem-overview.md)

Para obter mais informações sobre tipos de preço, incluindo Hubs de Eventos Dedicados, veja os [Detalhes de preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Onde os Hubs de Eventos do Azure estão disponíveis?

Os Hubs de Eventos do Azure está disponível em todas as regiões do Azure com suporte. Para obter uma lista, visite a página [Regiões do Azure](https://azure.microsoft.com/regions/).  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Posso usar uma única conexão AMQP para enviar e receber de vários Hubs de Eventos?

Sim, contanto que todos os Hubs de Eventos estejam no mesmo namespace.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Qual é o período de retenção máximo para eventos?

Atualmente, o tipo Standard dos Hubs de Eventos dá suporte a um período de retenção máximo de sete dias. Os hubs de eventos não são destinados como um armazenamento de dados permanente. Períodos de retenção superiores a 24 horas são destinados a cenários nos quais é conveniente reproduzir um fluxo de eventos nos mesmos sistemas; por exemplo, treinar ou verificar um novo modelo de aprendizado de máquina sobre dados existentes. Se você precisar de retenção de mensagens por mais de sete dias, a habilitação da [Captura de Hubs de Eventos](event-hubs-capture-overview.md) efetuará pull dos dados do hub de eventos para a conta de armazenamento ou conta de serviço do Azure Data Lake de sua escolha. A habilitação da Captura acarretará uma cobrança com base nas unidades de produtividade compradas.

Você pode configurar o período de retenção dos dados capturados em sua conta de armazenamento. O recurso de gerenciamento do ciclo de **vida** do Azure Storage oferece uma política rica e baseada em regras para contas de armazenamento v2 e blob de propósito geral. Use a política para fazer a transição de seus dados para as camadas de acesso apropriadas ou expirá-los ao final do ciclo de vida dos dados. Para obter mais informações, consulte [Gerenciar o ciclo de vida do armazenamento Azure Blob](../storage/blobs/storage-lifecycle-management-concepts.md). 

### <a name="how-do-i-monitor-my-event-hubs"></a>Como monitorar meus Hubs de Eventos?
Os Hubs de Eventos emitem métricas exaustivas que fornecem o estado de seus recursos ao [Azure Monitor](../azure-monitor/overview.md). Eles também permitem a avaliação da integridade geral do serviço do Hubs de Eventos, não apenas no nível do namespace, mas também no nível da entidade. Saiba qual monitoramento é oferecido para os [Hubs de Eventos do Azure](event-hubs-metrics-azure-monitor.md).

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Que portas preciso para abrir no firewall? 
Você pode usar os seguintes protocolos com o Azure Service Bus para enviar e receber mensagens:

- Advanced Message Queuing Protocol (AMQP)
- HTTP
- Apache Kafka

Veja a tabela a seguir para as portas de saída que você precisa abrir para usar esses protocolos para se comunicar com o Azure Event Hubs. 

| Protocolo | Portas | Detalhes | 
| -------- | ----- | ------- | 
| AMQP | 5671 e 5672 | Veja [o guia de protocolo samt](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Consulte [Use hubs de eventos a partir de aplicativos Kafka](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Quais endereços IP eu preciso para lista branca?
Para encontrar os endereços IP certos na lista branca para suas conexões, siga estas etapas:

1. Execute o seguinte comando a partir de um prompt de comando: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Anote o endereço IP `Non-authoritative answer`retornado em . A única hora que mudaria seria se você restaurasse o namespace em um cluster diferente.

Se você usar a redundância de região para o seu namespace, você precisa fazer algumas etapas adicionais: 

1. Primeiro, você executa nslookup no namespace.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Anote o nome na seção **de resposta não autorizada,** que está em um dos seguintes formatos: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Execute nslookup para cada um com sufixos s1, s2 e s3 para obter os endereços IP de todas as três instâncias em execução em três zonas de disponibilidade, 

## <a name="apache-kafka-integration"></a>Integração do Apache Kafka

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Como integrar o meu aplicativo existente do Kafka com os Hubs de Eventos?
Os Hubs de Eventos fornecem um ponto de extremidade Kafka que pode ser usado por seus aplicativos baseados em Apache Kafka existentes. Basta uma alteração de configuração para obter a experiência de PaaS Kafka. Ela fornece uma alternativa à execução de seu próprio cluster Kafka. Os Hubs de Eventos são compatíveis com o Apache Kafka 1.0 e com versões mais recentes do cliente e funcionam com seus aplicativos, ferramentas e estruturas existentes do Kafka. Para saber mais, confira [Hubs de Eventos para repositório Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Quais alterações de configuração precisam ser feitas para que o meu aplicativo existente se comunique com os Hubs de Eventos?
Para se conectar a um hub de eventos, você precisará atualizar as configurações do cliente Kafka. É feito criando um namespace do Event Hubs e obtendo a [seqüência de conexões](event-hubs-get-connection-string.md). Altere o bootstrap.servers para apontar o FQDN de Hubs de Eventos e a porta para 9093. Atualize o sasl.jaas.config para direcionar o cliente Kafka para o ponto final do Event Hubs (que é a seqüência de conexão que você obteve), com autenticação correta como mostrado abaixo:

bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

Exemplo:

bootstrap.servers=dummynamespace.servicebus.windows.net:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=";

Nota: Se sasl.jaas.config não for uma configuração suportada em sua estrutura, encontre as configurações usadas para definir o nome de usuário e senha do SASL e usá-las em seu lugar. Defina o nome de usuário como $ConnectionString e a senha para sua cadeia de conexão de Hubs de Eventos.

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Qual é o tamanho da mensagem/evento para hubs de eventos?
O tamanho máximo de mensagem permitido para o Event Hubs é de 1 MB.

## <a name="throughput-units"></a>Unidades de transferência

### <a name="what-are-event-hubs-throughput-units"></a>O que são unidades de produtividade dos Hubs de Eventos?
A taxa de transferência nos Hubs de Eventos define a quantidade de dados em megabytes ou o número (em milhares) de eventos de 1 KB que entram e saem por meio dos Hubs de Eventos. Essa taxa de transferência é medida em unidades de produtividade (TUs). Compre TUs antes de começar a usar o serviço Hubs de Eventos. Seleciona explicitamente as TUs dos Hubs de Eventos usando o portal do Azure ou de modelos do Resource Manager dos Hubs de Eventos. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>As unidades de produtividade se aplicam a todos os hubs de eventos em um namespace?
Sim, as TUs (unidades de produtividade) se aplicam a todos os hubs de eventos em um namespace dos Hubs de Eventos. Isso significa que você compra TUs no nível do namespace e elas são compartilhadas entre os hubs de eventos no namespace. Cada TU proporciona ao namespace os seguintes recursos:

- Até 1 MB por segundo de eventos de entrada (eventos enviados para um Hub de Eventos), mas não mais de 1.000 eventos de entrada, operações de gerenciamento ou chamadas por segundo à API de controle.
- Até 2 MB por segundo de eventos de saída (eventos consumidos de um Hub de Eventos), mas não mais do que 4096 eventos de saída.
- Até 84 GB de armazenamento de eventos (suficiente para o período de retenção padrão de 24 horas).

### <a name="how-are-throughput-units-billed"></a>Como é a cobrança das unidades de produtividade?
As TUs (unidades de produtividade) são cobradas por hora. A cobrança é baseada no número máximo de unidades selecionado durante a hora determinada. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Como posso otimizar o uso em minhas unidades de produtividade?
Você pode começar com uma TU (unidade de produtividade) e ativar a [inflação automática](event-hubs-auto-inflate.md). O recurso de inflação automática permite que você aumente suas TUs à medida que seu conteúdo/tráfego aumenta. Você também pode definir um limite superior de TUs.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Como funciona o recurso Inflação Automática dos Hubs de Eventos?
O recurso inflação automática permite que você escale verticalmente suas TUs (unidades de produtividade). Isso significa que você pode começar comprando poucas TUs e a inflação automática escala verticalmente suas TUs à medida que aumenta a entrada. Isso proporciona uma opção econômica e controle total do número de TUs para gerenciamento. Esse recurso **serve apenas para escalar verticalmente**, e você pode controlar completamente a redução do número de TUs atualizando-o. 

Convém começar com poucas TUs (unidades de produtividade), por exemplo 2 TUs. Se você prever que o tráfego pode aumentar para 15 TUs, ative o recurso inflação automática em seu namespace e defina o limite máximo como 15 TUs. Agora, você pode aumentar suas TUs automaticamente conforme seu tráfego aumenta.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Há um custo associado à ativação do recurso inflação automática?
Não **há custo** associado a este recurso. 

### <a name="how-are-throughput-limits-enforced"></a>Como os limites de taxa de transferência são impostos?
Se a produtividade de entrada total ou a taxa de eventos de entrada total em todos os Hubs de Eventos em um namespace exceder os limites de unidades de produtividade totais agregados, os remetentes serão limitados e receberão erros indicando que a cota de entrada foi excedida.

Se a produtividade de saída total ou a taxa de eventos de saída total em todos os Hubs de Eventos em um namespace exceder os limites de unidades de produtividade agregados, os receptores serão limitados e receberão erros indicando que a cota de saída foi excedida. As cotas de entrada e saída são aplicadas separadamente, para que nenhum remetente possa fazer com que o consumo de eventos se torne lento e para que um receptor não possa evitar que eventos sejam enviados para um Hub de Eventos.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Há um limite para o número de unidades de produtividade (TUs) que podem ser reservadas/selecionadas?
Em uma oferta com vários locatários, as unidades de produtividade podem chegar a 40 TUs (você pode selecionar até 20 TUs no portal e gerar um tíquete de suporte para elevá-lo até 40 TUs no mesmo namespace). Além dos 40 TUs, os Hubs de Eventos oferecem o modelo baseado em recursos/capacidade chamado de **clusters de Hubs de Eventos Dedicados**. Clusters dedicados são vendidos em Unidades de Capacidade (CUs).

## <a name="dedicated-clusters"></a>Clusters dedicados

### <a name="what-are-event-hubs-dedicated-clusters"></a>O que são clusters de Hubs de Eventos Dedicados?
Os clusters de Hubs de Eventos Dedicados oferecem implantações de locatário único para clientes com exigências mais rígidas. Essa oferta cria um cluster baseado em capacidade que não é limitado por unidades de produtividade. Isso significa que você pode usar o cluster para ingerir e transmitir seus dados conforme ditado pela CPU e uso de memória do cluster. Para saber mais, confira [Clusters dos Hubs de Eventos Dedicados](event-hubs-dedicated-overview.md).

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>O quanto posso atingir com uma unidade de capacidade única?
Para um cluster dedicado, o quanto você pode ingerr e transmitir depende de vários fatores, como seus produtores, consumidores, a taxa em que você está ingerindo e processando, e muito mais. 

A tabela a seguir mostra os resultados do parâmetro de comparação obtidos durante o teste:

| Forma da carga | Destinatários | Largura de banda de entrada| Mensagens de entrada | Largura de banda de saída | Mensagens de saída | Total de TUs | TUs por CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lotes de 100x1KB | 2 | 400 MB/s | 400k mensagens/seg | 800 MB/s | 800k mensagens/seg | 400 TUs | 100 TUs | 
| Lotes de 10x10KB | 2 | 666 MB/s | 66,6 mil mensagens/seg | 1,33 GB/s | 133k mensagens/seg | 666 TUs | 166 TUs |
| Lotes de 6x32KB | 1 | 1,05 GB/s | Mensagens 34k / seg | 1,05 GB/s | Mensagens 34k/seg | 1000 TUs | 250 TUs |

No teste, usamos os critérios a seguir:

- Usamos um cluster dedicado de Hubs de Eventos com quatro unidades de capacidade (CUs). 
- O hub de eventos usado para ingestão tinha 200 partições. 
- Os dados ingeridos foram recebidos por dois aplicativos destinatários que recebem de todas as partições.

Os resultados dão uma ideia do que pode ser alcançado com um cluster dedicado de Hubs de Eventos. Além disso, um cluster dedicado vem com a Captura de Hubs de Eventos habilitada para seus cenários de microlote e de retenção de longo prazo.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Como criar um cluster dedicado de Hubs de Eventos?
Crie um cluster dedicado de Hubs de Eventos enviando uma [solicitação de suporte de aumento de cota](https://portal.azure.com/#create/Microsoft.Support) ou contatando a [equipe dos Hubs de Eventos](mailto:askeventhubs@microsoft.com). Geralmente, demora cerca de duas semanas para implantar o cluster e disponibilizá-lo para seu uso. Esse processo é temporário até que um autoatendimento completo seja disponibilizado através do portal Azure ou modelos do Azure Resource Manager, que levam cerca de duas horas para implantar o cluster.

## <a name="best-practices"></a>Práticas recomendadas

### <a name="how-many-partitions-do-i-need"></a>De quantas partições preciso?
O número de partições é especificado na criação do Hub de Eventos e deve estar entre 2 e 32. A contagem de partições não é mutável, então você deve considerar a escala de longo prazo ao definir a contagem de partições. As partições são um mecanismo de organização de dados relacionados ao paralelismo de downstream necessário no consumo de aplicativos. O número de partições em um hub de eventos está diretamente relacionado ao número de leitores simultâneos que você espera ter. Para obter mais informações sobre partições, consulte [Partições](event-hubs-features.md#partitions).

Você pode querer defini-lo como o maior valor possível, que é 32, no momento da criação. Lembre-se que ter mais de uma partição resultará em eventos enviados para várias partições sem reter a ordem, a menos que você configure remetentes para enviar apenas para uma única partição das 32 deixando as 31 partições restantes redundantes. No primeiro caso, você terá que ler eventos em todas as 32 partições. Neste último caso, não há um custo adicional óbvio além da configuração extra que você tem que fazer no Host do processador de eventos.

Hubs de Eventos são projetados para permitir um único leitor de partição por grupo de consumidores. Na maioria dos casos de uso, a configuração padrão de quatro partições é suficiente. Se você está procurando dimensionar seu processamento de eventos, você pode querer considerar adicionar partições adicionais. Não há um limite específico de rendimento em uma partição, no entanto, o throughput agregado em seu namespace é limitado pelo número de unidades de throughput. Conforme você aumenta o número de unidades de taxa de transferência no seu namespace, talvez você deseje partições adicionais para permitir que cada um dos leitores simultâneos alcance sua taxa de transferência máxima.

No entanto, se você tiver um modelo no qual seu aplicativo tem uma afinidade com uma determinada partição, aumentar o número de partições pode não ser útil pra você. Para saber mais, confira [disponibilidade e consistência](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Preços

### <a name="where-can-i-find-more-pricing-information"></a>Onde posso encontrar mais informações sobre preços?

Para obter todas as informações sobre os preços dos Hubs de Eventos, consulte os [detalhes de preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Há um custo para a retenção de eventos de Hubs de Eventos por mais de 24 horas?

A camada Standard dos Hubs de Eventos permite a retenção de mensagens por períodos superiores a 24 horas, por um máximo de sete dias. Se o número total de eventos armazenados exceder o limite de armazenamento para o número de unidades de produtividade selecionadas (84 GB por unidade de produtividade), o tamanho que exceder o limite será cobrado com base na taxa de armazenamento de Blobs do Azure publicada. O limite de armazenamento em cada unidade de produtividade cobre todos os custos de armazenamento para períodos de retenção de 24 horas (o padrão) mesmo que a unidade de produtividade seja usada até o limite máximo de entrada.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Como o tamanho do armazenamento de Hubs de Eventos é calculado e cobrado?

O tamanho total de todos os eventos armazenados, incluindo eventuais sobrecargas internas para cabeçalhos de eventos ou estruturas de armazenamento em disco em todos os Hubs de Eventos, é medido durante todo o dia. No final do dia, o tamanho do armazenamento de pico é calculado. O limite de armazenamento diário é calculado com base no número mínimo de unidades de produtividade que foram selecionadas durante o dia (cada unidade de produtividade fornece um limite de 84 GB). Se o tamanho total exceder o limite de armazenamento diário calculado, o armazenamento em excesso será cobrado usando as taxas de armazenamento de Blob do Azure (com base na taxa de **armazenamento com redundância local** ).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Como os eventos de entrada de Hubs de Eventos são calculados?

Cada evento enviado a um Hub de Eventos conta como uma mensagem faturável. Um *evento de entrada* é definido como uma unidade de dados menor ou igual a 64 KB. Qualquer evento menor ou igual a 64 KB de tamanho é considerado um evento faturável. Se o evento for maior que 64 KB, o número de eventos passíveis de cobrança será calculado de acordo com o tamanho do evento, em múltiplos de 64 KB. Por exemplo, um evento de 8 KB enviado ao Hub de Eventos é cobrado como um evento, mas uma mensagem de 96 KB enviada ao Hub de Eventos é cobrada como dois eventos.

Eventos consumidos de um Hub de Eventos, bem como operações de gerenciamento e chamadas de controle, como pontos de verificação, não são contadas como eventos de entrada faturáveis, mas se acumulam no limite de unidade de produtividade.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Cobranças por conexões agenciadas são aplicadas aos Hubs de Eventos?

Cobranças de conexão são aplicadas somente quando o protocolo AMQP é usado. Não há cobranças de conexão para enviar eventos usando HTTP, independentemente do número de sistemas ou dispositivos remetentes. Se você pretende usar o AMQP (por exemplo, para obter transmissões de eventos mais eficientes ou para habilitar a comunicação bidirecional em cenários de comando e controle da IoT), consulte a página [Informações sobre preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/) para obter detalhes sobre quantas conexões estão incluídas em cada camada de serviço.

### <a name="how-is-event-hubs-capture-billed"></a>Como a Captura de Hubs de Eventos é cobrada?

A Captura de Hubs de Eventos será habilitada quando qualquer Hub de Eventos no namespace tiver o recurso Captura habilitado. A Captura de Hubs de Eventos é cobrada por hora de acordo com a unidade de taxa de transferência comprada. À medida que a contagem das unidades de taxa de transferência aumentar ou diminuir, a cobrança da Captura de Hubs de Eventos refletirá essas mudanças em incrementos de hora cheia. Para obter mais informações sobre a cobrança da Captura de Hubs de Eventos, confira [informações de preços de Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Serei cobrado pela conta de armazenamento selecionada para a Captura de Hubs de Eventos?

A Captura usa uma conta de armazenamento fornecida quando habilitado em um Hub de Eventos. Como essa é sua conta de armazenamento, todas as alterações nessa configuração serão cobradas em sua assinatura do Azure.

## <a name="quotas"></a>Cotas

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Existe alguma cota associada aos Hubs de Eventos?

Para obter uma lista de todas as cotas de Hubs de Eventos, consulte [cotas](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Solução de problemas

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Por que não eu consigo criar um namespace após excluí-lo de outra assinatura? 
Quando você exclui um namespace de uma assinatura, aguarde até 4 horas antes de recriá-lo com o mesmo nome em outra assinatura. Caso contrário, a seguinte mensagem de erro será exibida: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelos Hubs de Eventos e suas ações sugeridas?

Para obter uma lista de exceções possíveis dos Hubs de Eventos, confira [Visão geral das exceções](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Logs de diagnóstico

Os Hubs de Eventos dão suporte a dois tipos de [logs de diagnóstico](event-hubs-diagnostic-logs.md) – logs de erro de Captura e logs operacionais – ambos os quais são representados em json e podem ser ativados por meio do Portal do Azure.

### <a name="support-and-sla"></a>Suporte e SLA

Suporte técnico para os Hubs de Eventos está disponível por meio dos [fóruns da comunidade](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus). O suporte para gerenciamento de assinaturas e cobranças é fornecido sem custo adicional.

Para saber mais sobre nosso SLA, veja a página [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Próximas etapas

Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão geral de Hubs de Evento](event-hubs-what-is-event-hubs.md)
* [Crie um Hub de Eventos](event-hubs-create.md)
* [Inflar automaticamente de Hubs de Eventos](event-hubs-auto-inflate.md)
