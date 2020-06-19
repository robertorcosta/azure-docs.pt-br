---
title: Entrega de eventos com identidade do serviço gerenciado
description: Este artigo descreve como habilitar a identidade do serviço gerenciado para um tópico da grade de eventos do Azure. Use-o para encaminhar eventos para destinos com suporte.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 04/24/2020
ms.author: spelluru
ms.openlocfilehash: 4d96f28b98cccada2ac5c77589acc6df1430bb02
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700663"
---
# <a name="event-delivery-with-managed-identity"></a>Entrega de eventos com identidade gerenciada
Este artigo descreve como habilitar a [identidade do serviço gerenciado](../active-directory/managed-identities-azure-resources/overview.md) para um tópico ou domínio da grade de eventos. Use-o para encaminhar eventos para destinos com suporte, como filas e tópicos de Barramento de Serviço, hubs de eventos e contas de armazenamento.

Aqui estão as etapas abordadas em detalhes neste artigo:
1. Crie um tópico ou domínio com uma identidade atribuída pelo sistema (ou) atualize um tópico ou domínio existente para habilitar a identidade. 
2. Adicione a identidade a uma função apropriada (por exemplo: remetente de dados do Barramento de Serviço) no destino (por exemplo: uma fila do Barramento de Serviço)
3. Habilite o uso da identidade para entregar eventos ao destino ao criar assinaturas de evento. 

## <a name="create-a-topic-or-domain-with-an-identity"></a>Criar um tópico ou domínio com uma identidade
Primeiro, vamos dar uma olhada em como criar um tópico ou domínio com uma identidade gerenciada pelo sistema.

### <a name="using-azure-portal"></a>Usando o Portal do Azure
É possível habilitar a identidade atribuída pelo sistema para um tópico/domínio ao criá-lo no portal do Azure. A imagem a seguir mostra como habilitar a identidade gerenciada pelo sistema para um tópico. Basicamente, você seleciona a opção **Habilitar identidade atribuída pelo sistema** na página **Avançado** do assistente de criação de tópico. Você verá essa opção na página **Avançado** do assistente de criação de domínios também. 

![Habilitar identidade ao criar um tópico](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Usando a CLI do Azure
Você também pode usar o CLI do Azure para criar um tópico ou domínio com uma identidade atribuída pelo sistema. Use o comando `az eventgrid topic create` com o parâmetro `--identity` definido como `systemassigned`. Se você não especificar um valor para este parâmetro, o valor padrão `noidentity` será usado. 

```azurecli-interactive
# create a topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Da mesma forma, você pode usar o comando `az eventgrid domain create` para criar um domínio com uma identidade gerenciada pelo sistema.

## <a name="enable-identity-for-an-existing-topic-or-domain"></a>Habilitar identidade para um tópico ou domínio existente
Na última seção, você aprendeu como habilitar uma identidade gerenciada pelo sistema ao criar um tópico ou domínio. Nesta seção, você aprenderá como habilitar a identidade gerenciada pelo sistema para um tópico ou domínio existente. 

### <a name="using-azure-portal"></a>Usando o Portal do Azure
1. Navegue até o [portal do Azure](https://portal.azure.com)
2. Pesquise **tópicos da grade de eventos** na barra de pesquisa.
3. Selecione o **tópico** para o qual deseja habilitar a identidade gerenciada. 
4. Alterne para a guia **Identidade**. 
5. Ative a opção para habilitar a identidade. 

    É possível usar etapas semelhantes para habilitar a identidade para um domínio de grade de eventos.

### <a name="using-azure-cli"></a>Usando a CLI do Azure
Use o comando `az eventgrid topic update` com `--identity` definido como `systemassigned` para habilitar a identidade atribuída pelo sistema para um tópico existente. Se quiser desabilitar a identidade, especifique `noidentity` como o valor. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

O comando para atualizar um domínio existente é semelhante (`az eventgrid domain update`).

## <a name="supported-destinations-and-role-based-access-check-rbac-roles"></a>Destinos com suporte e funções de RBAC (verificação de acesso baseado em função)
Após habilitar a identidade para seu tópico ou domínio da grade de eventos, o Azure cria automaticamente uma identidade no Azure Active Directory (Azure AD). Adicione essa identidade às funções RBAC apropriadas para que o tópico ou domínio possa encaminhar eventos para destinos suportados. Por exemplo, adicione a identidade à função **Remetente de dados dos Hubs de Eventos do Azure** para um namespace de Hubs de Eventos para que o tópico da grade de eventos possa encaminhar eventos para os hubs de eventos nesse namespace.  

Atualmente, a Grade de Eventos do Azure dá suporte a tópicos ou domínios configurados com identidade gerenciada atribuída pelo sistema para encaminhar eventos para os seguintes destinos. Esta tabela também fornece as funções em que a identidade deve estar para que o tópico possa encaminhar os eventos.

| Destino | Função RBAC | 
| ----------- | --------- | 
| Filas e tópicos do Barramento de Serviço | [Remetente de dados do Barramento de Serviço do Azure](../service-bus-messaging/authenticate-application.md#built-in-rbac-roles-for-azure-service-bus) |
| Hub de Eventos | [Remetente de dados dos Hubs de Eventos do Azure](../event-hubs/authorize-access-azure-active-directory.md#built-in-rbac-roles-for-azure-event-hubs) | 
| Armazenamento de blob | [Colaborador de dados de blob de armazenamento](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) |
| Armazenamento de filas |[Remetente da mensagem de dados da fila de armazenamento](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) | 

## <a name="add-identity-to-rbac-roles-on-destinations"></a>Adicionar identidade a funções RBAC em destinos
Esta seção descreve como adicionar a identidade para seu tópico ou domínio a uma função RBAC. 

### <a name="using-azure-portal"></a>Usando o Portal do Azure
É possível usar o **portal do Azure** para atribuir a identidade de tópico/domínio a uma função apropriada para que o tópico/domínio possa encaminhar eventos para o destino. 

O exemplo a seguir adiciona uma identidade gerenciada para um tópico da grade de eventos chamado **msitesttopic** à função **Remetente de dados do Barramento de Serviço do Azure** para um **namespace** do Barramento de Serviço que contém um recurso de fila ou tópico. Quando você adiciona à função no nível do namespace, o tópico pode encaminhar eventos para todas as entidades dentro do namespace. 

1. Navegue até o **namespace do Barramento de Serviço** no [portal do Azure](https://portal.azure.com). 
2. Selecione **Controle de Acesso** no painel esquerdo. 
3. Selecione **Adicionar** na seção **Adicionar uma atribuição de função**. 
4. Na página **Adicionar uma atribuição de função**, siga os seguintes passos:
    1. Selecione a função. Neste caso, é **Remetente de dados do Barramento de Serviço do Azure**. 
    2. Selecione a **identidade** para seu tópico ou domínio. 
    3. selecione **Salvar** para salvar a configuração.

Para adicionar uma identidade a outras funções mencionadas na tabela, as etapas são semelhantes. 

### <a name="using-azure-cli"></a>Usando a CLI do Azure
O exemplo nesta seção mostra como usar o **CLI do Azure** para adicionar uma identidade a uma função RBAC. Os comandos de amostra são para tópicos de grade de eventos. Os comandos para domínios de grade de eventos são semelhantes. 

#### <a name="get-principal-id-for-the-topics-system-identity"></a>Obter ID da entidade de segurança para a identidade do sistema do tópico 
Primeiro, obtenha a ID da entidade de segurança da identidade gerenciada pelo sistema do tópico e atribua a identidade às funções apropriadas.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Criar uma atribuição de função para hubs de eventos em vários escopos 
O exemplo de CLI a seguir mostra como adicionar a identidade de um tópico à função **Remetente de dados dos Hubs de Eventos do Azure** no nível do namespace ou no nível do hub de eventos. Se você criar a atribuição de função no namespace, o tópico poderá encaminhar eventos para todos os hubs de eventos nesse namespace. Se você criar no nível do hub de eventos, o tópico poderá encaminhar eventos somente para esse hub de eventos específico. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-service-bus-topic-at-various-scopes"></a>Crie uma atribuição de função para o tópico de Barramento de Serviço em vários escopos 
O exemplo de CLI a seguir mostra como adicionar a identidade de um tópico à função **Remetente de dados do Barramento de Serviço do Azure** no nível de namespace ou no nível de tópico do Barramento de Serviço. Se você criar a atribuição de função no namespace, o tópico da grade de eventos poderá encaminhar eventos para todas as entidades (filas ou tópicos do Barramento de Serviço) dentro desse namespace. Se você criar na fila do Barramento de Serviço ou no nível do tópico, o tópico da grade de eventos poderá encaminhar eventos apenas para essa fila ou tópico específico do Barramento de Serviço. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-identity"></a>Criar assinaturas de eventos que usam identidade
Após ter um tópico ou um domínio com uma identidade gerenciada pelo sistema e adicionado a identidade à função apropriada no destino, você estará pronto para criar assinaturas que usam a identidade. 

### <a name="using-azure-portal"></a>Usando o Portal do Azure
Ao criar uma assinatura de evento, você verá uma opção para habilitar o uso de identidade atribuída pelo sistema para um ponto de extremidade na seção **ENDPOINT DETAILS**. 

![Habilitar identidade ao criar assinatura de evento para a fila do Barramento de Serviço](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Você também pode habilitar o uso da identidade atribuída pelo sistema para ser usada para mensagens mortas na guia **Recursos adicionais**. 

![Habilitar identidade atribuída ao sistema para mensagens mortas](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="using-azure-cli---service-bus-queue"></a>Usando a CLI do Azure - Fila do Barramento de Serviço 
Nesta seção, você aprenderá a usar a **CLI do Azure** para habilitar o uso da identidade atribuída pelo sistema para entregar eventos a uma fila do Barramento de Serviço. A identidade deve ser um membro da função **Remetente de dados do Barramento de Serviço do Azure**. Ela também deve ser membro da função **Colaborador de dados de blobs de armazenamento** na conta de armazenamento usada para mensagens mortas. 

#### <a name="define-variables"></a>Definir variáveis
Primeiro, especifique valores para as variáveis a seguir a serem usadas no comando da CLI. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery"></a>criar uma assinatura de evento usando a identidade gerenciada para entrega 
Este comando de amostra cria uma assinatura de evento para um tópico da grade de eventos com o tipo de ponto de extremidade definido como **Fila do Barramento de Serviço**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery-and-dead-lettering"></a>criar uma assinatura de evento usando a identidade gerenciada para entrega e mensagens mortas
Este comando de amostra cria uma assinatura de evento para um tópico da grade de eventos com o tipo de ponto de extremidade definido como **Fila do Barramento de Serviço**. Ele também especifica que a identidade gerenciada pelo sistema deve ser usada para mensagens mortas. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="azure-cli---event-hubs"></a>CLI do Azure - Hubs de Eventos 
Nesta seção, você aprenderá a usar a **CLI do Azure** para habilitar o uso da identidade atribuída pelo sistema para entregar eventos a um hub de eventos. A identidade deve ser um membro da função **Remetente de dados dos Hubs de Eventos do Azure**. Ela também deve ser membro da função **Colaborador de dados de blobs de armazenamento** na conta de armazenamento usada para mensagens mortas. 

#### <a name="define-variables"></a>Definir variáveis
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>criar assinatura de evento usando a identidade gerenciada para entrega 
Este comando de amostra cria uma assinatura de evento para um tópico da grade de eventos com o tipo de ponto de extremidade definido como **Hubs de Eventos**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>Criar assinatura de evento usando a identidade gerenciada para entrega + mensagem morta 
Este comando de amostra cria uma assinatura de evento para um tópico da grade de eventos com o tipo de ponto de extremidade definido como **Hubs de Eventos**. Ele também especifica que a identidade gerenciada pelo sistema deve ser usada para mensagens mortas. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="azure-cli---azure-storage-queue"></a>CLI do Azure - Fila de Armazenamento do Azure 
Nesta seção, você aprenderá a usar a **CLI do Azure** para habilitar o uso da identidade atribuída pelo sistema para entregar eventos a uma fila de Armazenamento do Azure. A identidade deve ser membro da função **Colaborador de dados de blobs de armazenamento** na conta de armazenamento.

#### <a name="define-variables"></a>Definir variáveis  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>Criar assinatura de evento usando a identidade gerenciada para entrega 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>Criar assinatura de evento usando a identidade gerenciada para entrega + mensagem morta 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```



## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre identidades de serviço gerenciado, consulte [O que são identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md). 