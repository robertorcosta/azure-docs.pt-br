---
title: Funções do Azure processamento de eventos confiável
description: Evite a falta de mensagens do Event Hub em funções do Azure
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: cshoe
ms.openlocfilehash: e4f35495d8a01146068cffb9159c29c46c3c0d29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561860"
---
# <a name="azure-functions-reliable-event-processing"></a>Funções do Azure processamento de eventos confiável

O processamento de eventos é um dos cenários mais comuns associados à arquitetura sem servidor. Este artigo descreve como criar um processador de mensagens confiável com funções do Azure para evitar a perda de mensagens.

## <a name="challenges-of-event-streams-in-distributed-systems"></a>Desafios dos fluxos de eventos em sistemas distribuídos

Considere um sistema que envia eventos a uma taxa constante de 100 eventos por segundo. Nesse ritmo, em poucos minutos várias instâncias de funções paralelas podem consumir os 100 eventos que chegam a cada segundo.

No entanto, qualquer uma das seguintes condições menos ideais é possível:

- E se o editor de eventos enviar um evento corrupto?
- E se a instância functions encontrar exceções não tratadas?
- E se um sistema a jusante ficar off-line?

Como você lida com essas situações preservando o throughput de sua aplicação?

Com filas, mensagens confiáveis vêm naturalmente. Quando emparelhado com um gatilho Funções, a função cria um bloqueio na mensagem de fila. Se o processamento falhar, o bloqueio será liberado para permitir que outra instância tente novamente o processamento. O processamento continua até que a mensagem seja avaliada com sucesso ou seja adicionada a uma fila de veneno.

Mesmo que uma única mensagem de fila possa permanecer em um ciclo de repetição, outras execuções paralelas continuam a manter a fila mento enfileirando mensagens restantes. O resultado é que o throughput global permanece em grande parte não afetado por uma mensagem ruim. No entanto, as filas de armazenamento não garantem o pedido e não são otimizadas para as demandas de alto desempenho exigidas pelos Event Hubs.

Em contraste, o Azure Event Hubs não inclui um conceito de bloqueio. Para permitir recursos como alta capacidade de throughput, vários grupos de consumidores e capacidade de replay, os eventos do Event Hubs se comportam mais como um player de vídeo. Os eventos são lidos a partir de um único ponto no fluxo por partição. A partir do ponteiro você pode ler para frente ou para trás a partir desse local, mas você tem que optar por mover o ponteiro para que os eventos processem.

Quando ocorrem erros em um fluxo, se você decidir manter o ponteiro no mesmo local, o processamento de eventos é bloqueado até que o ponteiro esteja avançado. Em outras palavras, se o ponteiro for parado para lidar com problemas no processamento de um único evento, os eventos não processados começam a se acumular.

As funções do Azure evitam impasses avançando o ponteiro do fluxo, independentemente do sucesso ou fracasso. Uma vez que o ponteiro continua avançando, suas funções precisam lidar com falhas adequadamente.

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Como o Azure Funciona consome eventos do Event Hubs

As funções do Azure consomem eventos do Event Hub enquanto pedalam pelas seguintes etapas:

1. Um ponteiro é criado e persistido no Azure Storage para cada partição do hub de eventos.
2. Quando novas mensagens são recebidas (em um lote por padrão), o host tenta acionar a função com o lote de mensagens.
3. Se a função concluir a execução (com ou sem exceção) o ponteiro avançará e um ponto de verificação será salvo na conta de armazenamento.
4. Se as condições impedirem que a execução da função seja completada, o host não conseguirá progredir o ponteiro. Se o ponteiro não estiver avançado, então as verificações posteriores acabam processando as mesmas mensagens.
5. Repita as etapas 2-4

Esse comportamento revela alguns pontos importantes:

- *Exceções não tratadas podem fazer com que você perca mensagens.* Execuções que resultam em uma exceção continuarão a progredir o ponteiro.
- *As funções garantem, pelo menos uma vez, a entrega.* Seu código e sistemas dependentes podem precisar [explicar o fato de que a mesma mensagem poderia ser recebida duas vezes](./functions-idempotent.md).

## <a name="handling-exceptions"></a>Tratamento de exceções

Como regra geral, cada função deve incluir um [bloco de tentativa/captura](./functions-bindings-error-pages.md) no mais alto nível de código. Especificamente, todas as funções que consomem `catch` eventos do Event Hubs devem ter um bloqueio. Dessa forma, quando uma exceção é levantada, o bloco de captura lida com o erro antes que o ponteiro progrida.

### <a name="retry-mechanisms-and-policies"></a>Mecanismos e políticas de repetição

Algumas exceções são transitórias por natureza e não reaparecem quando uma operação é tentada novamente momentos depois. É por isso que o primeiro passo é sempre tentar novamente a operação. Você poderia escrever regras de processamento de repetição você mesmo, mas eles são tão comuns que uma série de ferramentas estão disponíveis. O uso dessas bibliotecas permite definir políticas robustas de repetição, que também podem ajudar a preservar a ordem de processamento.

A introdução de bibliotecas de manipulação de falhas em suas funções permite definir políticas básicas e avançadas de reteste. Por exemplo, você pode implementar uma política que segue um fluxo de trabalho ilustrado pelas seguintes regras:

- Tente inserir uma mensagem três vezes (potencialmente com um atraso entre as repetições).
- Se o resultado final de todas as tentativas for uma falha, adicione uma mensagem a uma fila para que o processamento possa continuar no fluxo.
- Mensagens corrompidas ou não processadas são então tratadas posteriormente.

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) é um exemplo de uma biblioteca de resiliência e manuseio de falhas transitórias para aplicações C#.

Ao trabalhar com bibliotecas de classe C# pré-cumpridas, [os filtros](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-catch) de exceção permitem que você execute código sempre que uma exceção não tratada ocorrer.

Amostras que demonstram como usar filtros de exceção estão disponíveis no repo [Azure WebJobs SDK.](https://github.com/Azure/azure-webjobs-sdk/wiki)

## <a name="non-exception-errors"></a>Erros não-exceção

Alguns problemas surgem mesmo quando um erro não está presente. Por exemplo, considere uma falha que ocorre no meio de uma execução. Neste caso, se uma função não concluir a execução, o ponteiro de deslocamento nunca será progredido. Se o ponteiro não avançar, qualquer instância que seja executada após uma execução falha continuará a ler as mesmas mensagens. Esta situação fornece uma garantia "pelo menos uma vez".

A garantia de que cada mensagem é processada pelo menos uma vez implica que algumas mensagens podem ser processadas mais de uma vez. Seus aplicativos de função precisam estar cientes dessa possibilidade e devem ser construídos em torno [dos princípios da idempotência.](./functions-idempotent.md)

## <a name="stop-and-restart-execution"></a>Pare e reinicie a execução

Embora alguns erros possam ser aceitáveis, e se seu aplicativo tiver falhas significativas? Você pode querer parar de desencadear eventos até que o sistema atinja um estado saudável. Ter a oportunidade de pausar o processamento é muitas vezes alcançado com um padrão de disjuntor. O padrão do disjuntor permite que seu aplicativo "quebre o circuito" do processo de evento e retome posteriormente.

Há duas peças necessárias para implementar um disjuntor em um processo de evento:

- Estado compartilhado em todas as instâncias para rastrear e monitorar a saúde do circuito
- Processo mestre que pode gerenciar o estado do circuito (aberto ou fechado)

Os detalhes da implementação podem variar, mas para compartilhar o estado entre as instâncias você precisa de um mecanismo de armazenamento. Você pode optar por armazenar o estado no Azure Storage, um cache Redis ou qualquer outra conta que seja acessível por uma coleção de funções.

[Os aplicativos azure logic](../logic-apps/logic-apps-overview.md) ou [entidades duráveis](./durable/durable-functions-overview.md) são um ajuste natural para gerenciar o fluxo de trabalho e o estado do circuito. Outros serviços podem funcionar tão bem, mas aplicativos lógicos são usados para este exemplo. Usando aplicativos lógicos, você pode pausar e reiniciar a execução de uma função, dando-lhe o controle necessário para implementar o padrão do disjuntor.

### <a name="define-a-failure-threshold-across-instances"></a>Defina um limiar de falha entre as instâncias

Para responder por múltiplas instâncias processando eventos simultaneamente, é necessário manter o estado externo compartilhado para monitorar a saúde do circuito.

Uma regra que você pode optar por implementar pode impor isso:

- Se houver mais de 100 eventuais falhas em 30 segundos em todas as instâncias, então quebre o circuito e pare de disparar em novas mensagens.

Os detalhes de implementação variam dadas as suas necessidades, mas em geral você pode criar um sistema que:

1. Registrar falhas em uma conta de armazenamento (Azure Storage, Redis, etc.)
1. Quando uma nova falha for registrada, inspecione a contagem de rolamentos para ver se o limite é atingido (por exemplo, mais de 100 nos últimos 30 segundos).
1. Se o limite for atingido, emita um evento para o Azure Event Grid intando o sistema para quebrar o circuito.

### <a name="managing-circuit-state-with-azure-logic-apps"></a>Gerenciamento do estado do circuito com aplicativos azure logic

A descrição a seguir destaca uma maneira de criar um aplicativo Azure Logic para impedir o processamento de um aplicativo Functions.

O Azure Logic Apps vem com conectores integrados para diferentes serviços, apresenta orquestrações estaduais e é uma escolha natural para gerenciar o estado do circuito. Depois de detectar que o circuito precisa ser quebrado, você pode construir um aplicativo lógico para implementar o seguinte fluxo de trabalho:

1. Acione um fluxo de trabalho da Grade de Eventos e interrompa a função Azure (com o conector de recursos do Azure)
1. Envie um e-mail de notificação que inclua uma opção para reiniciar o fluxo de trabalho

O destinatário do e-mail pode investigar a saúde do circuito e, quando apropriado, reiniciar o circuito através de um link no e-mail de notificação. À medida que o fluxo de trabalho reinicia a função, as mensagens são processadas a partir do último ponto de verificação do Event Hub.

Usando esta abordagem, nenhuma mensagem é perdida, todas as mensagens são processadas em ordem, e você pode quebrar o circuito o tempo necessário.

## <a name="resources"></a>Recursos

- [Amostras confiáveis de processamento de eventos](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Disjuntor de funções duráveis do Azure](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

- [Tratamento de erros do Azure Functions](./functions-bindings-error-pages.md)
- [Automatizar o redimensionamento de imagens carregadas usando a Grade de Eventos](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [Criar uma função que se integra nos Aplicativos Lógicos do Azure](./functions-twitter-email.md)
