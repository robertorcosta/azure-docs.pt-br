---
title: Autenticação de uma identidade gerenciada com o Azure Active Directory
description: Este artigo fornece informações sobre a autenticação de uma identidade gerenciada com o Azure Active Directory para acessar os recursos do Azure Event Hubs
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 672b663a9cab72d465ea00e0a5ade364eadbf64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251522"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Autenticar uma identidade gerenciada com o Azure Active Directory para acessar recursos do Event Hubs
O Azure Event Hubs suporta a autenticação do Azure Active Directory (Azure AD) com [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md). As identidades gerenciadas para os recursos do Azure podem autorizar o acesso aos recursos do Event Hubs usando credenciais Azure AD de aplicativos em execução em VMs (Azure Virtual Machines), aplicativos de função, conjuntos de escala de máquinas virtuais e outros serviços. Ao usar identidades gerenciadas para recursos do Azure, juntamente com a autenticação do Azure AD, você pode evitar armazenar credenciais com seus aplicativos que são executados na nuvem.

Este artigo mostra como autorizar o acesso a um hub de eventos usando uma identidade gerenciada de uma VM Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Habilitar identidades gerenciadas em uma VM
Antes de usar identidades gerenciadas para recursos do Azure para autorizar os recursos do Event Hubs a partir de sua VM, você deve primeiro habilitar identidades gerenciadas para recursos do Azure na VM. Para saber como habilitar identidades gerenciadas para Recursos do Azure, confira um dos seguintes artigos:

- [Portal Azure](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo de Gerenciador de recursos do Azure](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de clientes do Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Conceder permissões a uma identidade gerenciada no Azure AD
Para autorizar uma solicitação ao serviço Event Hubs a partir de uma identidade gerenciada em seu aplicativo, primeiro configure as configurações de Controle de acesso baseado em função (RBAC) para essa identidade gerenciada. O Azure Event Hubs define funções RBAC que englobam permissões para envio e leitura de Hubs de Eventos. Quando a função RBAC é atribuída a uma identidade gerenciada, a identidade gerenciada é concedida acesso aos dados do Event Hubs no escopo apropriado.

Para obter mais informações sobre como atribuir funções RBAC, consulte [Authenticate with Azure Active Directory para ter acesso aos recursos do Event Hubs](authorize-access-azure-active-directory.md).

## <a name="use-event-hubs-with-managed-identities"></a>Usar os Hubs de Eventos com identidades gerenciadas
Para usar hubs de eventos com identidades gerenciadas, você precisa atribuir à identidade a função e o escopo apropriado. O procedimento nesta seção usa um aplicativo simples que é executado uma identidade gerenciada e acessa os recursos do Event Hubs.

Aqui estamos usando um aplicativo web de exemplo hospedado no [Azure App Service](https://azure.microsoft.com/services/app-service/). Para obter instruções passo a passo para criar um aplicativo web, consulte [Criar um aplicativo web ASP.NET Core no Azure](../app-service/app-service-web-get-started-dotnet.md)

Uma vez que o aplicativo seja criado, siga estas etapas: 

1. Vá para **Configurações** e selecione **Identidade**. 
1. Selecione o **Status** a ser **ligado**. 
1. Clique em **Salvar** para salvar a configuração. 

    ![Identidade gerenciada para um aplicativo web](./media/authenticate-managed-identity/identity-web-app.png)

Uma vez habilitada essa configuração, uma nova identidade de serviço é criada no Azure Active Directory (Azure AD) e configurada no host App Service.

Agora, atribua essa identidade de serviço a uma função no escopo necessário nos recursos do Event Hubs.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Para atribuir funções RBAC usando o portal Azure
Para atribuir uma função aos recursos do Event Hubs, navegue até esse recurso no portal Azure. Exiba as configurações de Controle de acesso (IAM) para o recurso e siga estas instruções para gerenciar as atribuições da função:

> [!NOTE]
> As etapas a seguir atribuem uma função de identidade de serviço aos seus namespaces do Event Hubs. Você pode seguir os mesmos passos para atribuir uma função escopo a qualquer recurso do Event Hubs. 

1. No portal Azure, navegue até o namespace do Event Hubs e exiba a **visão geral** do namespace. 
1. Selecione **Controle de acesso (IAM)** no menu esquerdo para exibir as configurações de controle de acesso para o hub de eventos.
1.  Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.
3.  Selecione **Adicionar** para adicionar uma nova função.
4.  Na página **Adicionar funções,** selecione as funções de Eventos Hubs que você deseja atribuir. Em seguida, pesquise para localizar a identidade de serviço que você registrou para atribuir a função.
    
    ![Adicionar página de atribuição de função](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Selecione **Salvar**. A identidade à qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra que a identidade do serviço tem o proprietário do Event Hubs Data.
    
    ![Identidade atribuída a um papel](./media/authenticate-managed-identity/role-assigned.png)

Depois de atribuir a função, o aplicativo web terá acesso aos recursos do Event Hubs o escopo definido. 

### <a name="test-the-web-application"></a>Testar o aplicativo Web
1. Criar um namespace de Hubs de Eventos e um hub de eventos. 
2. Implante o aplicativo web no Azure. Veja a seção a seguir de guias para links para o aplicativo web no GitHub. 
3. Certifique-se de que o SendReceive.aspx é definido como o documento padrão para o aplicativo web. 
3. Habilite a **identidade** para o aplicativo web. 
4. Atribua essa identidade à função de Proprietário de dados do **Event Hubs** no nível de namespace ou no nível do hub de eventos. 
5. Execute o aplicativo web, digite o nome do namespace e o nome do hub de eventos, uma mensagem e selecione **Enviar**. Para receber o evento, **selecione Receber**. 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure.Messaging.EventHubs (mais recente)](#tab/latest)
Agora você pode iniciar seu aplicativo web e apontar seu navegador para a página aspx de exemplo. Você pode encontrar o aplicativo web de exemplo que envia e recebe dados dos recursos do Event Hubs no [repo gitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp).

Instale o pacote mais recente do [NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)e comece a enviar eventos para hubs de eventos usando **EventHubProducerClient** e recebendo eventos usando **EventHubConsumerClient**. 

> [!NOTE]
> Para obter uma amostra Java que usa uma identidade gerenciada para publicar eventos em um hub de eventos, consulte [Publicar eventos com a amostra de identidade do Azure no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs).

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs (herdado)](#tab/old)
Agora você pode iniciar seu aplicativo web e apontar seu navegador para a página aspx de exemplo. Você pode encontrar o aplicativo web de exemplo que envia e recebe dados dos recursos do Event Hubs no [repo gitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Instale o pacote mais recente do [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)e comece a enviar e receber dados dos hubs event usando o EventHubClient, conforme mostrado no código a seguir: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Hubs de Eventos para Kafka
Você pode usar aplicativos Apache Kafka para enviar mensagens e receber mensagens do Azure Event Hubs usando a identidade gerenciada OAuth. Veja a seguinte amostra no GitHub: [Event Hubs for Kafka - envie e receba mensagens usando a identidade gerenciada OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity).

## <a name="samples"></a>Exemplos
- **Azure.Messaging.EventHubs** amostras
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Amostras do Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Essas amostras usam a antiga biblioteca **Microsoft.Azure.EventHubs,** mas você pode atualizá-la facilmente para usar a biblioteca mais recente **do Azure.Messaging.EventHubs.** Para mover a amostra do uso da biblioteca antiga para uma nova, consulte o [Guia para migrar do Microsoft.Azure.EventHubs para o Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md).
    Esta amostra foi atualizada para usar a biblioteca Mais recente **do Azure.Messaging.EventHubs.**
- [Hubs de eventos para Kafka - enviar e receber mensagens usando a identidade gerenciada OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>Próximas etapas
- Veja o artigo a seguir para saber sobre identidades gerenciadas para recursos do Azure: [O que é identidadegerenciada para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)
- Veja os seguintes artigos relacionados:
    - [Autenticar solicitações ao Azure Event Hubs a partir de um aplicativo usando o Azure Active Directory](authenticate-application.md)
    - [Autenticar solicitações ao Azure Event Hubs usando assinaturas de acesso compartilhadas](authenticate-shared-access-signature.md)
    - [Autorize o acesso aos recursos do Event Hubs usando o Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorize o acesso aos recursos do Event Hubs usando assinaturas de acesso compartilhado](authorize-access-shared-access-signature.md)