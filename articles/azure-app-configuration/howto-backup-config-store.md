---
title: Fazer backup automaticamente de valores de chave do repositório de configuração Azure App
description: Saiba como configurar um backup automático de valores de chave entre repositórios de configuração de aplicativo
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: ef777f235c85c423ea126fb7249f8e6a11492d3a
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87528541"
---
# <a name="backup-app-configuration-stores-automatically"></a>Armazenamento de configuração de aplicativo de backup automaticamente

Neste artigo, você aprenderá a configurar um backup automático de valores de chave de um repositório de configuração de aplicativo primário para um repositório secundário. Ele aproveita a integração da grade de eventos do Azure com a configuração do aplicativo. Uma vez configurado, a configuração do aplicativo publicará eventos na grade de eventos para quaisquer alterações feitas nos valores de chave em um repositório de configuração. A grade de eventos dá suporte a uma variedade de serviços do Azure, dos quais os usuários podem assinar os eventos emitidos sempre que os valores de chave são criados, atualizados ou excluídos.

## <a name="overview"></a>Visão geral

Neste tutorial, você usará uma fila de armazenamento do Azure para receber eventos da grade de eventos e usará um gatilho de temporizador de Azure Functions para processar eventos na fila de armazenamento em lotes. Quando disparado, com base nos eventos, a função buscará os valores mais recentes das chaves que foram alterados do repositório de configuração do aplicativo primário e atualizará o repositório secundário adequadamente. Essa configuração ajuda a combinar várias alterações que ocorrem em um curto período em uma operação de backup e evitar solicitações excessivas feitas em seus repositórios de configuração de aplicativo.

![Arquitetura de backup do repositório de configurações de aplicativo](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Provisionamento de Recursos

A motivação por trás do backup de repositórios de configuração de aplicativo é usar vários repositórios de configuração em diferentes regiões do Azure para aumentar a resiliência geográfica do seu aplicativo. Para conseguir isso, os armazenamentos primários e secundários devem estar em diferentes regiões do Azure. Todos os outros recursos criados neste tutorial podem ser provisionados em qualquer região de sua escolha. Isso ocorre porque, se a região primária estiver inativa, não haverá nada de novo para backup até que a região primária seja acessível novamente.

Neste tutorial, você criará um armazenamento secundário na `centralus` região e todos os outros recursos na `westus` região.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure – [Crie uma gratuitamente](https://azure.microsoft.com/free/). Você também pode usar o Azure Cloud Shell.
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) com a carga de trabalho de desenvolvimento do Azure.
- Baixe e instale o [SDK do .NET Core](https://dotnet.microsoft.com/download).
- Versão mais recente do CLI do Azure (2.3.1 ou posterior). Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver usando CLI do Azure, você deverá primeiro entrar usando `az login` . Você também pode usar o Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

O grupo de recursos do Azure é uma coleção lógica na qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group).

O exemplo a seguir cria um grupo de recursos chamado `<resource_group_name>` na localização `westus`.  Substitua `<resource_group_name>` por um nome exclusivo para o grupo de recursos.

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>Criar repositórios de configuração de aplicativo

Crie seus armazenamentos de configuração de aplicativo primários e secundários em regiões diferentes.
Substitua  `<primary_appconfig_name>` e `<secondary_appconfig_name>` por nomes exclusivos para seus repositórios de configuração. O nome do repositório deve ser exclusivo porque é usado como um nome DNS.

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-azure-storage-queue"></a>Criar fila de armazenamento do Azure

Crie uma conta de armazenamento e uma fila de armazenamento para receber os eventos publicados pela grade de eventos.

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>Assinar seus eventos de repositório de configuração de aplicativo

Você assina esses dois eventos do repositório de configuração do aplicativo primário:

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

O comando a seguir cria uma assinatura de grade de eventos para os dois eventos enviados à sua fila de armazenamento, em que o tipo de ponto de extremidade é definido como `storagequeue` e o ponto de extremidade é definido como a ID da fila. Substitua `<event_subscription_name>` pelo nome de sua escolha para a assinatura de evento.

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-azure-functions-for-handling-events-from-storage-queue"></a>Criar Azure Functions para manipular eventos da fila de armazenamento

### <a name="setup-with-ready-to-use-azure-functions"></a>Configurar com Azure Functions pronto para uso

Neste tutorial, você trabalhará com o C# Azure Functions com as seguintes propriedades:
- Pilha de tempo de execução .NET Core 3,1
- Azure Functions Runtime versão 3. x
- Função disparada pelo temporizador a cada 10 minutos

Para facilitar o backup de seus dados, testamos e publicamos [Azure Functions](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) que você pode usar sem fazer nenhuma alteração no código. Baixe os arquivos de projeto e [publique-os em seu próprio Azure aplicativo de funções do Visual Studio.](/azure/azure-functions/functions-develop-vs#publish-to-azure)

> [!IMPORTANT]
> Não faça nenhuma alteração nas variáveis de ambiente no código que você baixou. Você criará as configurações de aplicativo necessárias na próxima seção.
>

### <a name="build-your-own-azure-functions"></a>Crie seus próprios Azure Functions

Se o código de exemplo fornecido acima não atender aos seus requisitos, você também poderá criar seu próprio Azure Functions. Sua função deve ser capaz de executar as seguintes tarefas para concluir o backup:
- Leia periodicamente o conteúdo da sua fila de armazenamento para ver se ele contém notificações da grade de eventos. Consulte o [SDK da fila de armazenamento](/azure/storage/queues/storage-quickstart-queues-dotnet) para obter detalhes de implementação.
- Se sua fila de armazenamento contiver [notificações de eventos da grade de eventos](/azure/azure-app-configuration/concept-app-configuration-event?branch=pr-en-us-112982#event-schema), extraia todas as chaves exclusivas de <, rótulo> de mensagens de evento. A combinação de chave e rótulo é o identificador exclusivo para alterações de chave-valor no repositório primário.
- Leia todas as configurações do repositório primário. Atualize somente as configurações no repositório secundário que têm um evento correspondente na fila de armazenamento. Exclua todas as configurações do repositório secundário que estavam presentes na fila de armazenamento, mas não no repositório primário. Você pode aproveitar o [SDK de configuração de aplicativo](https://github.com/Azure/AppConfiguration#sdks) para acessar os armazenamentos de configuração de forma programática.
- Exclua mensagens da fila de armazenamento se não houver nenhuma exceção durante o processamento.
- Certifique-se de implementar o tratamento de erros de acordo com suas necessidades. Você pode consultar o exemplo de código acima para ver algumas exceções comuns que talvez você queira manipular.

Para saber mais sobre como criar Azure Functions, consulte: [criar uma função no Azure que é disparada por um temporizador](/azure/azure-functions/functions-create-scheduled-function) e [desenvolver Azure Functions usando o Visual Studio](/azure/azure-functions/functions-develop-vs).


> [!IMPORTANT]
> Use o melhor Judgement para escolher a agenda do temporizador com base na frequência com que você faz alterações no repositório de configuração primário. Lembre-se de que a execução da função com muita frequência pode acabar com a limitação de solicitações para sua loja.
>


## <a name="create-azure-function-app-settings"></a>Criar configurações de Aplicativo de funções do Azure

Se estiver usando o Azure Functions que fornecemos, você precisará das seguintes configurações de aplicativo em sua Aplicativo de funções do Azure:
- `PrimaryStoreEndpoint`: Ponto de extremidade para o repositório de configuração de aplicativo primário. Por exemplo, `https://{primary_appconfig_name}.azconfig.io`
- `SecondaryStoreEndpoint`: Ponto de extremidade para o repositório de configurações do aplicativo secundário. Por exemplo, `https://{secondary_appconfig_name}.azconfig.io`
- `StorageQueueUri`: URI da fila de armazenamento. Por exemplo, `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`

O comando a seguir cria as configurações de aplicativo necessárias no Aplicativo de funções do Azure. Substitua `<function_app_name>` pelo nome do seu aplicativo de funções do Azure.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-azure-function-app"></a>Conceder acesso à identidade gerenciada do Azure Aplicativo de funções

Use o seguinte comando ou [portal do Azure](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity) para adicionar uma identidade gerenciada atribuída pelo sistema para sua aplicativo de funções do Azure.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> Para executar a criação de recursos e o gerenciamento de função necessários, sua conta precisa de `'Owner'` permissões no escopo apropriado (sua assinatura ou grupo de recursos). Se você precisar de assistência com a atribuição de função, saiba [como adicionar ou remover atribuições de função do Azure usando o portal do Azure](/azure/role-based-access-control/role-assignments-portal).

Use os seguintes comandos ou [portal do Azure](/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity#grant-access-to-app-configuration) para conceder a identidade gerenciada de seu acesso de aplicativo de funções do Azure para seus repositórios de configuração de aplicativo.
- Atribua `App Configuration Data Reader` a função no repositório de configuração do aplicativo primário.
- Atribua `App Configuration Data Owner` a função no repositório de configurações do aplicativo secundário.

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

Use o seguinte comando ou [portal do Azure](/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal) para conceder a identidade gerenciada de seu acesso de aplicativo de funções do Azure à sua fila de armazenamento. 
- Atribua `Storage Queue Data Contributor` a função na fila de armazenamento.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Disparar um evento da Configuração de Aplicativos

Para testar se tudo funciona, você pode criar/atualizar/excluir um valor de chave do repositório primário. Você deve ver automaticamente essa alteração no armazenamento secundário alguns segundos após a Azure Functions ser disparada pelo temporizador.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

Você disparou o evento e, em alguns instantes, a grade de eventos enviará a notificação de eventos para sua fila de armazenamento do Azure. ***Após a próxima execução agendada do seu Azure Functions***, exiba as definições de configuração em seu armazenamento secundário para ver se ela contém o valor de chave atualizado do repositório primário.

> [!NOTE]
> Você pode [disparar seu Azure Functions manualmente](/azure/azure-functions/functions-manually-run-non-http) durante os testes e a solução de problemas sem esperar pelo gatilho de temporizador agendado.

Depois de verificar se a função de backup foi executada com êxito, você pode ver que a chave agora está presente em seu armazenamento secundário.

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>Solução de problemas

Se você não vir a nova configuração em seu armazenamento secundário:

- Verifique se a função de backup foi disparada ***depois*** de criar a configuração em seu repositório primário.
- É possível que a grade de eventos não tenha sido capaz de enviar a notificação de eventos para a fila de armazenamento no tempo. Verifique se a fila de armazenamento ainda contém a notificação de eventos do seu repositório primário e, se houver, dispare a função de backup novamente.
- Verifique se há erros ou avisos no [Azure Functions logs](/azure/azure-functions/functions-create-scheduled-function#test-the-function) .
- Use [portal do Azure](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#get-started-in-the-azure-portal) para garantir que o aplicativo de funções do Azure contenha valores corretos para as configurações do aplicativo que seu Azure Functions está tentando ler.
- Você também pode configurar o monitoramento e os alertas para seu Azure Functions usando o [aplicativo Azure insights.](/azure/azure-functions/functions-monitoring?tabs=cmd) 


## <a name="clean-up-resources"></a>Limpar os recursos
Se planejar continuar trabalhando com essa Configuração de Aplicativos e essa assinatura de evento, não limpe os recursos criados neste artigo. Caso contrário, use os comandos a seguir para excluir os recursos criados por você neste artigo.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como configurar o backup automático de seus valores de chave, saiba mais sobre como você pode aumentar a resiliência geográfica do seu aplicativo:

- [Resiliência e recuperação de desastre](concept-disaster-recovery.md)
