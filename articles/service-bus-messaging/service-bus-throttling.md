---
title: Visão geral do estrangulamento do Ônibus de Serviço Azure | Microsoft Docs
description: Visão geral do estrangulamento do Ônibus de Serviço - Níveis Padrão e Premium.
services: service-bus-messaging
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 10/01/2019
ms.author: aschhab
ms.openlocfilehash: f852ad70b2eb97e2b8b3e40d086e98b3836c3592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598282"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Operações de estrangulamento no Ônibus de Serviço Azure

As soluções nativas em nuvem dão uma noção de recursos ilimitados que podem ser dimensionados com sua carga de trabalho. Embora essa noção seja mais verdadeira na nuvem do que com sistemas locais, ainda existem limitações que existem na nuvem.

Essas limitações podem causar o estrangulamento de solicitações de aplicativos clientes nos níveis Standard e Premium, conforme discutido neste artigo. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Estrangulamento no nível padrão do ônibus de serviço azure

O nível Azure Service Bus Standard funciona como uma configuração de vários inquilinos com um modelo de preços de pagamento à medida que você vai. Aqui vários namespaces no mesmo cluster compartilham os recursos alocados. O nível padrão é a escolha recomendada para ambientes de desenvolvedor, teste e QA, juntamente com sistemas de produção de baixo throughput.

No passado, a Azure Service Bus tinha limites de estrangulamento grosseiros estritamente dependentes da utilização de recursos. No entanto, há uma oportunidade de refinar a lógica de estrangulamento e fornecer um comportamento previsível de estrangulamento para todos os namespaces que estão compartilhando esses recursos.

Na tentativa de garantir o uso justo e a distribuição de recursos em todos os espaços de nome Azure Service Bus Standard que usam os mesmos recursos, a lógica de estrangulamento foi modificada para ser baseada em crédito.

> [!NOTE]
> É importante notar que o estrangulamento não é ***novo*** no Azure Service Bus, ou em qualquer serviço nativo na nuvem.
>
> O estrangulamento baseado em crédito está simplesmente refinando a maneira como vários namespaces compartilham recursos em um ambiente de nível padrão de vários locatários e, assim, permitindo o uso justo por todos os namespaces compartilhando os recursos.

### <a name="what-is-credit-based-throttling"></a>O que é estrangulamento baseado em crédito?

O estrangulamento baseado em crédito limita o número de operações que podem ser realizadas em um determinado namespace em um período de tempo específico. 

Abaixo está o fluxo de trabalho para estrangulamento baseado em crédito - 

  * No início de cada período de tempo, fornecemos um certo número de créditos para cada namespace.
  * Quaisquer operações realizadas pelos aplicativos do cliente remetente ou receptor serão contadas contra esses créditos (e subtraídas dos créditos disponíveis).
  * Se os créditos forem esgotados, as operações subsequentes serão estranguladas até o início do próximo período.
  * Os créditos são repostos no início do próximo período.

### <a name="what-are-the-credit-limits"></a>Quais são os limites de crédito?

Os limites de crédito são atualmente definidos para créditos '1000' a cada segundo (por namespace).

Nem todas as operações são criadas iguais. Aqui estão os custos de crédito de cada uma das operações - 

| Operação | Custo de crédito|
|-----------|-----------|
| Operações de dados (Enviar, EnviarAsync, Receber, ReceberAsync, Peek) |1 crédito por mensagem |
| Operações de gerenciamento (Criar, Ler, Atualizar, Excluir em Filas, Tópicos, Assinaturas, Filtros) | 10 créditos |

> [!NOTE]
> Observe que ao enviar para um Tópico, cada mensagem é avaliada contra filtros antes de ser disponibilizada na Assinatura.
> Cada avaliação do filtro também conta com o limite de crédito (ou seja, 1 crédito por avaliação de filtro).
>

### <a name="how-will-i-know-that-im-being-throttled"></a>Como saberei que estou sendo estrangulado?

Quando as solicitações de aplicativo do cliente estiverem sendo estranguladas, a resposta abaixo do servidor será recebida pelo seu aplicativo e registrada.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>Como posso evitar ser estrangulado?

Com recursos compartilhados, é importante impor algum tipo de uso justo em vários espaços de nome do Service Bus que compartilham esses recursos. O estrangulamento garante que qualquer pico em uma única carga de trabalho não faça com que outras cargas de trabalho nos mesmos recursos sejam estranguladas.

Como mencionado posteriormente no artigo, não há risco de ser estrangulado porque os SDKs do cliente (e outras ofertas do Azure PaaS) têm a política de reavaliação padrão incorporada a eles. Quaisquer pedidos estrangulados serão julgados com recuo exponencial e eventualmente passarão quando os créditos forem repostos.

Compreensivelmente, algumas aplicações podem ser sensíveis a serem estranguladas. Nesse caso, recomenda-se [migrar o seu atual espaço de nome padrão de barra de serviço para premium](service-bus-migrate-standard-premium.md). 

Na migração, você pode alocar recursos dedicados ao seu espaço de nome de Bus de Serviço e aumentar adequadamente os recursos se houver um aumento na sua carga de trabalho e reduzir a probabilidade de ser estrangulado. Além disso, quando sua carga de trabalho reduz a níveis normais, você pode reduzir os recursos alocados para o seu namespace.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Estrangulamento no nível Premium do Ônibus de Serviço Azure

O [nível Azure Service Bus Premium](service-bus-premium-messaging.md) aloca recursos dedicados, em termos de unidades de mensagens, para cada configuração de namespace pelo cliente. Esses recursos dedicados fornecem produção e latência previsíveis e são recomendados para sistemas de alto desempenho ou de grau de produção sensíveis.

Além disso, o nível Premium também permite que os clientes aumentem sua capacidade de throughput quando experimentam picos na carga de trabalho.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Como funciona o estrangulamento no Service Bus Premium?

Com alocação exclusiva de recursos para service bus premium, o estrangulamento é puramente impulsionado pelas limitações dos recursos alocados no namespace.

Se o número de solicitações for maior do que os recursos atuais podem atender, então as solicitações serão estranguladas.

### <a name="how-will-i-know-that-im-being-throttled"></a>Como saberei que estou sendo estrangulado?

Existem várias maneiras de identificar o estrangulamento no Azure Service Bus Premium - 
  * **Solicitações estranguladas** aparecem nas métricas de solicitação do [Monitor Do Azure](service-bus-metrics-azure-monitor.md#request-metrics) para identificar quantas solicitações foram estranguladas.
  * O uso elevado **da CPU** indica que a alocação atual de recursos é alta e as solicitações podem ser estranguladas se a carga de trabalho atual não reduzir.
  * O **uso de memória alta** indica que a alocação atual de recursos é alta e as solicitações podem ser estranguladas se a carga de trabalho atual não reduzir.

### <a name="how-can-i-avoid-being-throttled"></a>Como posso evitar ser estrangulado?

Uma vez que o espaço de nome Service Bus Premium já possui recursos dedicados, você pode reduzir a possibilidade de ser estrangulado aumentando o número de Unidades de Mensagens alocadas ao seu namespace no caso (ou antecipação) de um pico na carga de trabalho.

A escala para cima/para baixo pode ser alcançada criando [runbooks](../automation/automation-create-alert-triggered-runbook.md) que podem ser acionados por alterações nas métricas acima.

## <a name="faqs"></a>Perguntas frequentes

### <a name="how-does-throttling-affect-my-application"></a>Como o estrangulamento afeta minha aplicação?

Quando uma solicitação é estrangulada, implica que o serviço está ocupado porque está enfrentando mais solicitações do que os recursos permitem. Se a mesma operação for tentada novamente após alguns momentos, uma vez que o serviço tenha trabalhado através de sua carga de trabalho atual, então a solicitação pode ser honrada.

Como o estrangulamento é o comportamento esperado de qualquer serviço nativo em nuvem, construímos a lógica de repetição no próprio SDK do Azure Service Bus. O padrão é definido para tentar novamente com um back-off exponencial para garantir que não tenhamos a mesma solicitação sendo estrangulada todas as vezes.

A lógica de repetição padrão se aplicará a cada operação.

### <a name="does-throttling-result-in-data-loss"></a>O estrangulamento resulta em perda de dados?

O Ônibus de Serviço Azure é otimizado para persistência, garantimos que todos os dados enviados ao Service Bus sejam comprometidos com o armazenamento antes que o serviço reconheça o sucesso da solicitação.

Uma vez que a solicitação é bem sucedida 'ACK' (reconhecida) pela Service Bus, isso implica que a Service Bus processou com sucesso a solicitação. Se a Service Bus retornar um 'NACK' (falha), então isso implica que a Service Bus não foi capaz de processar a solicitação e o aplicativo do cliente deve tentar novamente a solicitação.

No entanto, quando uma solicitação é estrangulada, o serviço está insinuando que não pode aceitar e processar a solicitação agora devido a limitações de recursos. Isso **não** implica qualquer tipo de perda de dados porque a Service Bus simplesmente não analisou a solicitação. Neste caso, confiar na política de repetição padrão do Service Bus SDK garante que a solicitação seja eventualmente processada.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações e exemplos de uso do sistema de mensagens do Barramento de Serviço, consulte os tópicos avançados a seguir:

* [Visão geral de mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
* [Início rápido: Enviar e receber mensagens usando o portal do Azure e .NET](service-bus-quickstart-portal.md)
* [Tutorial: Atualizar estoque usando o portal do Azure e tópicos/assinaturas](service-bus-tutorial-topics-subscriptions-portal.md)

