---
title: Enviar eventos para um ponto final da Web usando a configuração do aplicativo Azure
description: Aprenda a usar as assinaturas de eventos de configuração do aplicativo Azure para enviar eventos de modificação de valor-chave para um ponto final da Web
services: azure-app-configuration
author: lisaguthrie
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: da64f22981cc33772783093cfe75daa3eac5cef1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672149"
---
# <a name="route-azure-app-configuration-events-to-a-web-endpoint-with-azure-cli"></a>Rotear eventos da Configuração de Aplicativos do Azure para um ponto de extremidade da Web com a CLI do Azure

Neste artigo, você aprende como configurar assinaturas de eventos de configuração do aplicativo Azure para enviar eventos de modificação de valor-chave para um ponto final da Web. Os usuários da Configuração do Aplicativo Azure podem se inscrever em eventos emitidos sempre que os valores-chave forem modificados. Esses eventos podem ativar ganchos web, funções do Azure, filas de armazenamento do Azure ou qualquer outro manipulador de eventos que seja suportado pelo Azure Event Grid. Normalmente, você envia eventos para um ponto de extremidade que processa os dados de evento e realiza ações. No entanto, para simplificar este artigo, você enviará os eventos para um aplicativo Web que coleta e exibe as mensagens.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [crie um gratuitamente](https://azure.microsoft.com/free/). Você também pode usar o Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o CLI localmente, este artigo exige que você esteja executando a versão mais recente do Azure CLI (2.0.70 ou posterior). Para saber qual é a versão, execute `az --version`. Se você precisar instalar ou atualizar, consulte [Install Azure CLI](/cli/azure/install-azure-cli).

Caso não esteja usando o Cloud Shell, primeiro você deve entrar usando `az login`.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos de Grade de Eventos são recursos do Azure e devem ser colocados em um grupo de recursos do Azure. O grupo de recursos do Azure é uma coleção lógica na qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create.](/cli/azure/group) 

O exemplo a seguir cria um grupo de recursos chamado `<resource_group_name>` na localização *westus*.  Substitua `<resource_group_name>` por um nome exclusivo para o grupo de recursos.

```azurecli-interactive
az group create --name <resource_group_name> --location westus
```

## <a name="create-an-app-configuration-store"></a>Criar um repositório de Configuração de Aplicativos

Substitua por `<appconfig_name>` um nome exclusivo `<resource_group_name>` para sua loja de configuração e pelo grupo de recursos que você criou anteriormente. O nome precisa ser exclusivo porque ele é usado como um nome DNS.

```azurecli-interactive
az appconfig create \
  --name <appconfig_name> \
  --location westus \
  --resource-group <resource_group_name> \
  --sku free
```

## <a name="create-a-message-endpoint"></a>Criar um ponto de extremidade de mensagem

Antes de assinar o tópico, vamos criar o ponto de extremidade para a mensagem do evento. Normalmente, o ponto de extremidade executa ações com base nos dados de evento. Para simplificar este início rápido, você implanta um [aplicativo Web criado previamente](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens de eventos. A solução implantada inclui um plano do Serviço de Aplicativo, um aplicativo Web do Aplicativo do Serviço de e o código-fonte do GitHub.

Substitua `<your-site-name>` por um nome exclusivo para o aplicativo Web. O nome do aplicativo Web deve ser exclusivo, pois é parte de uma entrada DNS.

```azurecli-interactive
$sitename=<your-site-name>

az group deployment create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

A implantação pode levar alguns minutos para ser concluída. Depois que a implantação for bem-sucedida, exiba seu aplicativo Web para garantir que ele esteja em execução. Em um navegador da Web, navegue até: `https://<your-site-name>.azurewebsites.net`

Você deve ver o site sem mensagens exibidas no momento.

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store"></a>Inscreva-se em sua loja de configuração de aplicativos

Assine um tópico para indicar à Grade de Eventos quais eventos você deseja acompanhar e para onde enviar esses eventos. O exemplo a seguir assina a Configuração de Aplicativos criada e passa a URL do aplicativo Web como o ponto de extremidade para a notificação de eventos. Substitua `<event_subscription_name>` por um nome para a assinatura de evento. Em `<resource_group_name>` e `<appconfig_name>`, use os valores criados anteriormente.

O ponto de extremidade para seu aplicativo Web deve incluir o sufixo `/api/updates/`.

```azurecli-interactive
appconfigId=$(az appconfig show --name <appconfig_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $appconfigId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

Exiba novamente o seu aplicativo Web e observe que um evento de validação de assinatura foi enviado a ele. Selecione o ícone de olho para expandir os dados de evento. A Grade de Eventos envia o evento de validação de modo que o ponto de extremidade possa verificar se ele deseja receber os dados de evento. O aplicativo Web inclui o código para validar a assinatura.

![Exibição do evento de assinatura](./media/quickstarts/event-grid/view-subscription-event.png)

## <a name="trigger-an-app-configuration-event"></a>Disparar um evento da Configuração de Aplicativos

Agora, vamos disparar um evento para ver como a Grade de Eventos distribui a mensagem para o ponto de extremidade. Crie uma chave-valor usando o `<appconfig_name>` usado anteriormente.

```azurecli-interactive
az appconfig kv set --name <appconfig_name> --key Foo --value Bar --yes
```

Você disparou o evento, e a Grade de Eventos enviou a mensagem para o ponto de extremidade configurado durante a assinatura. Exiba seu aplicativo Web para ver o evento que você acabou de enviar.

```json
[{
  "id": "deb8e00d-8c64-4b6e-9cab-282259c7674f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/eventDemoGroup/providers/microsoft.appconfiguration/configurationstores/{appconfig-name}",
  "subject": "https://{appconfig-name}.azconfig.io/kv/Foo",
  "data": {
    "key": "Foo",
    "etag": "a1LIDdNEIV6wCnfv3xaip7fMXD3"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T18:59:54Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="clean-up-resources"></a>Limpar recursos
Se planejar continuar trabalhando com essa Configuração de Aplicativos e essa assinatura de evento, não limpe os recursos criados neste artigo. Caso contrário, use os comandos a seguir para excluir os recursos criados por você neste artigo.

Substitua `<resource_group_name>` pelo recurso de grupo criado acima.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como criar tópicos e assinaturas de eventos, saiba mais sobre os eventos de chave-valor e como a Grade de Eventos pode ajudá-lo:

- [Como responder a eventos de chave-valor](concept-app-configuration-event.md)
- [Sobre a Grade de Eventos](../event-grid/overview.md)
- [Manipuladores da Grade de Eventos do Azure](../event-grid/event-handlers.md)
