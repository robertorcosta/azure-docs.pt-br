---
title: Entrega e repetição da Grade de Eventos do Azure
description: Descreve como a Grade de Eventos do Azure entrega eventos e como ela trata mensagens não entregues.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: fe7574d7e17b1763afb2292c15007dd87b056ef1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087604"
---
# <a name="event-grid-message-delivery-and-retry"></a>Entrega e repetição de mensagens da Grade de Eventos

Este artigo descreve como a Grade de Eventos do Azure manipula eventos quando a entrega não é confirmada.

A entrega proporcionada pela Grade de Eventos tem um tempo de duração. Cada mensagem é entregue pelo menos uma vez para cada assinatura. Os eventos são enviados para o ponto de extremidade registrado de cada assinatura imediatamente. Se um ponto de extremidade não confirmar o recebimento de um evento, a Grade de Eventos tentará entregá-lo novamente.

## <a name="batched-event-delivery"></a>Entrega de eventos em lote

A grade de eventos usa como padrão o envio de cada evento individualmente aos assinantes. O assinante recebe uma matriz com um único evento. Você pode configurar a grade de eventos para eventos em lote para entrega para melhorar o desempenho de HTTP em cenários de alta taxa de transferência.

A entrega em lote tem duas configurações:

* **Máximo de eventos por lote** – número máximo de eventos que a grade de eventos fornecerá por lote. Esse número nunca será excedido, mas menos eventos poderão ser entregues se nenhum outro evento estiver disponível no momento da publicação. A grade de eventos não atrasa eventos para criar um lote se menos eventos estiverem disponíveis. Deve estar entre 1 e 5.000.
* **Tamanho de lote preferencial em kilobytes** -teto de destino para o tamanho do lote em kilobytes. Semelhante a Max Events, o tamanho do lote pode ser menor se mais eventos não estiverem disponíveis no momento da publicação. É possível que um lote seja maior do que o tamanho de lote preferencial *se* um único evento for maior do que o tamanho preferencial. Por exemplo, se o tamanho preferencial for de 4 KB e um evento de 10 KB for enviado para a grade de eventos, o evento de 10 KB ainda será entregue em seu próprio lote, em vez de ser Descartado.

Entrega em lote em configurada por assinatura por evento por meio do portal, da CLI, do PowerShell ou de SDKs.

### <a name="azure-portal"></a>Portal do Azure: 
![Configurações de entrega em lotes](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>CLI do Azure
Ao criar uma assinatura de evento, use os seguintes parâmetros: 

- **Max – eventos por lote** – número máximo de eventos em um lote. Deve ser um número entre 1 e 5000.
- **preferencial-tamanho do lote-em quilobytes** -tamanho de lote preferido em kilobytes. Deve ser um número entre 1 e 1024.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Para obter mais informações sobre como usar CLI do Azure com a grade de eventos, consulte [rotear eventos de armazenamento para o ponto de extremidade da Web com CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md).

## <a name="retry-schedule-and-duration"></a>Agendamento de nova tentativa e duração

A grade de eventos aguarda 30 segundos por uma resposta depois de entregar uma mensagem. Após 30 segundos, se o ponto de extremidade não tiver respondido, a mensagem será enfileirada para tentar novamente. A Grade de Eventos usa uma política de repetição de retirada exponencial para a entrega de eventos. A grade de eventos repete a entrega na seguinte agenda com base no melhor esforço:

- 10 segundos
- 30 segundos
- 1 minuto
- 5 minutos
- 10 minutos
- 30 minutos
- 1 hora
- Por hora por até 24 horas

Se o ponto de extremidade responder em 3 minutos, a grade de eventos tentará remover o evento da fila de repetição em uma base de melhor esforço, mas as duplicatas ainda poderão ser recebidas.

A grade de eventos adiciona uma pequena aleatoriedade a todas as etapas de repetição e pode ignorar oportunamente determinadas tentativas se um ponto de extremidade estiver consistentemente não íntegro, inativo por um longo período ou parecer sobrecarregado.

Para comportamento determinístico, defina a vida útil do evento e as tentativas de entrega máxima nas [políticas de repetição de assinatura](manage-event-delivery.md).

Por padrão, a Grade de Eventos expira todos os eventos que não são entregues em 24 horas. Você pode [personalizar a política de repetição](manage-event-delivery.md) ao criar uma assinatura de evento. Forneça o número máximo de tentativas de entrega (o padrão é 30) e a vida útil do evento (o padrão é 1440 minutos).

## <a name="delayed-delivery"></a>Entrega atrasada

Como um ponto de extremidade apresenta falhas de entrega, a grade de eventos começará a atrasar a entrega e a repetição de eventos para esse ponto de extremidade. Por exemplo, se os 10 primeiros eventos publicados em um ponto de extremidade falharem, a grade de eventos presumirá que o ponto de extremidade está apresentando problemas e atrasará todas as novas tentativas subsequentes *e novos* entregas por algum tempo, em alguns casos, até várias horas.

A finalidade funcional da entrega atrasada é proteger pontos de extremidade não íntegros, bem como o sistema de grade de eventos. Sem retirada e atraso de entrega para pontos de extremidade não íntegros, a política de repetição da grade de eventos e os recursos de volume podem facilmente sobrecarregar um sistema.

## <a name="dead-letter-events"></a>Eventos de mensagens mortas
Quando a grade de eventos não pode entregar um evento dentro de um determinado período de tempo ou depois de tentar entregar o evento um determinado número de vezes, ele pode enviar o evento não entregue a uma conta de armazenamento. Esse processo é conhecido como **mensagens mortas**. A grade de eventos não segue um evento quando **uma das condições a seguir** é atendida. 

- O evento não é entregue dentro do período de vida útil
- O número de tentativas para entregar o evento excedeu o limite

Se qualquer uma das condições for atendida, o evento será descartado ou inativo.  Por padrão, a Grade de Eventos não ativa o armazenamento de mensagens mortas. Para habilitá-lo, você deve especificar uma conta de armazenamento para reter eventos que não foram entregues ao criar a assinatura do evento. Você aciona eventos dessa conta de armazenamento para resolver as entregas.

A Grade de Eventos enviará um evento ao local de mensagens mortas quando ela tiver tentado todas as suas tentativas de repetição. Se a Grade de Eventos receber um código de resposta 400 (Solicitação incorreta) ou 413 (A entidade da solicitação é grande demais), ela enviará o evento imediatamente ao ponto de extremidade de mensagens mortas. Esses códigos de resposta indicam que a entrega do evento nunca terá êxito.

Há um atraso de cinco minutos entre a última tentativa de entregar de um evento e quando ela é entregue para o local de inatividade. Esse atraso tem como objetivo reduzir o número de operações de armazenamento Blob. Se o local de mensagens mortas não estiver disponível por quatro horas, o evento será descartado.

Antes de configurar o local de mensagens mortas, você deve ter uma conta de armazenamento com um contêiner. É possível fornecer o ponto de extremidade para esse contêiner ao criar a assinatura do evento. O ponto de extremidade está no formato de: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Talvez você queira ser notificado quando um evento tiver sido enviado para o local de mensagens mortas. Para usar a Grade de Eventos para responder a eventos não entregues, [crie uma assinatura de evento](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) para o armazenamento de blob de mensagens mortas. Toda vez que seu armazenamento de blob de mensagens mortas recebe um evento não entregue, a Grade de Eventos notifica o manipulador. O manipulador responde com ações que você deseja executar para reconciliar eventos não entregues.

Para ver um exemplo de como configurar um local de mensagens mortas, confira [Dead letter and retry policies](manage-event-delivery.md) (Políticas de mensagens mortas e repetição).

## <a name="message-delivery-status"></a>Status de entrega da mensagem

A Grade de Eventos usa códigos de resposta HTTP para confirmar o recebimento de eventos. 

### <a name="success-codes"></a>Códigos de sucesso

A grade de eventos considera **apenas** os seguintes códigos de resposta http como entregas bem-sucedidas. Todos os outros códigos de status são considerados entregas com falha e serão repetidos ou mortodos conforme apropriado. Após receber um código de status bem-sucedido, a grade de eventos considera a entrega concluída.

- 200 OK
- 201 Criado
- 202 Aceito
- 203 informações não autoritativas
- 204 Sem Conteúdo

### <a name="failure-codes"></a>Códigos de falha

Todos os outros códigos que não estão no conjunto acima (200-204) são considerados falhas e serão repetidos. Alguns têm políticas de repetição específicas ligadas a eles descritos abaixo, todos os outros seguem o modelo de retirada exponencial padrão. É importante ter em mente que, devido à natureza altamente paralelizada da arquitetura da grade de eventos, o comportamento de repetição é não determinístico. 

| Código de status | Tentar comportamento novamente |
| ------------|----------------|
| 400 Solicitação Inválida | Tente novamente após 5 minutos ou mais (mensagens mortas imediatamente se a instalação de mensagens mortas) |
| 401 Não Autorizado | Tente novamente após 5 minutos ou mais |
| 403 Proibido | Tente novamente após 5 minutos ou mais |
| 404 Não Encontrado | Tente novamente após 5 minutos ou mais |
| 408 Tempo Limite da Solicitação | Tentar novamente após 2 minutos ou mais |
| Solicitação 413 entidade muito grande | Tente novamente após 10 segundos ou mais (mensagens mortas imediatamente se a instalação de mensagens mortas) |
| 503 Serviço Indisponível | Tentar novamente após 30 segundos ou mais |
| Todos os outros | Tentar novamente após 10 segundos ou mais |


## <a name="next-steps"></a>Próximas etapas

* Para exibir o status de entregas de evento, consulte [Entrega de mensagens da Grade de Eventos do Monitor](monitor-event-delivery.md).
* Para personalizar as opções de entrega de eventos, confira [Dead letter and retry policies](manage-event-delivery.md) (Políticas de mensagens mortas e repetição).
