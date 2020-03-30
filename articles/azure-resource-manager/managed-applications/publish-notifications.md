---
title: Aplicativos gerenciados com notificações
description: Configure aplicativos gerenciados com pontos finais do webhook para receber notificações sobre criações, atualizações, exclusões e erros nas instâncias gerenciadas do aplicativo.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: ff058d7b51bd2e5efd80db69e5928d58fc5a7725
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715674"
---
# <a name="azure-managed-applications-with-notifications"></a>Aplicativos gerenciados pelo Azure com notificações

As notificações de aplicativos gerenciadas pelo Azure permitem que os editores automatizem ações com base em eventos do ciclo de vida das instâncias de aplicativos gerenciadas. Os editores podem especificar pontos finais de webhook de notificação personalizada para receber notificações de eventos sobre instâncias de aplicativos gerenciadas novas e existentes. Os editores podem configurar fluxos de trabalho personalizados no momento do provisionamento, atualizações e exclusão dos aplicativos.

## <a name="getting-started"></a>Introdução
Para começar a receber aplicativos gerenciados, gire um ponto final HTTPS público e especifique-o quando publicar a definição do aplicativo do catálogo de serviços ou a oferta do Azure Marketplace.

Aqui estão as etapas recomendadas para começar rapidamente:
1. Gire um ponto final HTTPS público que registra `200 OK`as solicitações e devoluções de POST recebidas .
2. Adicione o ponto final à definição do aplicativo de catálogo de serviços ou à oferta do Azure Marketplace, conforme explicado posteriormente neste artigo.
3. Crie uma instância de aplicativo gerenciada que refira a definição do aplicativo ou a oferta do Azure Marketplace.
4. Valide que as notificações estão sendo recebidas.
5. Habilite a autorização conforme explicado na seção de **autenticação Endpoint** deste artigo.
6. Siga as instruções na seção de esquema de **notificação** deste artigo para analisar as solicitações de notificação e implementar sua lógica de negócios com base na notificação.

## <a name="add-service-catalog-application-definition-notifications"></a>Adicionar notificações de definição de aplicativos de catálogo de serviço
#### <a name="azure-portal"></a>Portal do Azure
Para começar, consulte [Publicar um aplicativo de catálogo de serviços através do portal Azure](./publish-portal.md).

![Notificações de definição de aplicativos de catálogo de serviços no portal Azure](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>API REST

> [!NOTE]
> Atualmente, você pode fornecer apenas `notificationEndpoints` um ponto final nas propriedades de definição do aplicativo.

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="add-azure-marketplace-managed-application-notifications"></a>Adicionar notificações de aplicativos gerenciados pelo Azure Marketplace
Para obter mais informações, consulte [Criar uma oferta de aplicativo do Azure](../../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md).

![Notificações de aplicativos gerenciadas pelo Azure No portal do Azure](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Gatilhos de evento
A tabela a seguir descreve todas as combinações possíveis de EventType e ProvisioningState e seus gatilhos:

EventType | ProvisioningState | Gatilho para notificação
---|---|---
PUT | Aceita | O grupo de recursos gerenciados foi criado e projetado com sucesso após o put do aplicativo (antes que a implantação dentro do grupo de recursos gerenciadoseja iniciada).
PUT | Teve êxito | O provisionamento total do aplicativo gerenciado foi bem sucedido após um PUT.
PUT | Falhou | Falha do provisionamento de instância de aplicação em qualquer momento.
PATCH | Teve êxito | Após um PATCH bem-sucedido na instância de aplicativo gerenciada para atualizar tags, política de acesso ao JIT ou identidade gerenciada.
Delete (excluir) | Excluindo | Assim que o usuário iniciar uma EXCLUSão de uma instância gerenciada do aplicativo.
Delete (excluir) | Deleted | Após a exclusão completa e bem sucedida do aplicativo gerenciado.
Delete (excluir) | Falhou | Após qualquer erro durante o processo de desprovisionamento que bloqueia a exclusão.
## <a name="notification-schema"></a>Esquema de notificação
Quando você gira o ponto final do webhook para lidar com notificações, você precisará analisar a carga para obter propriedades importantes para, em seguida, agir após a notificação. O catálogo de serviços e as notificações gerenciadas de aplicativos do Azure Marketplace fornecem muitas das mesmas propriedades. Duas pequenas diferenças estão descritas na tabela que segue as amostras.

#### <a name="service-catalog-application-notification-schema"></a>Esquema de notificação de aplicativos de catálogo de serviços
Aqui está uma notificação de catálogo de serviço de exemplo após o provisionamento bem-sucedido de uma instância de aplicativo gerenciada:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

Se o provisionamento falhar, uma notificação com os detalhes de erro será enviada para o ponto final especificado.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="azure-marketplace-application-notification-schema"></a>Esquema de notificação de aplicativos do Azure Marketplace

Aqui está uma notificação de catálogo de serviço de exemplo após o provisionamento bem-sucedido de uma instância de aplicativo gerenciada:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Se o provisionamento falhar, uma notificação com os detalhes de erro será enviada para o ponto final especificado.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

Parâmetro | Descrição
---|---
eventType | O tipo de evento que desencadeou a notificação. (Por exemplo, PUT, PATCH, DELETE.)
applicationId | O identificador de recursos totalmente qualificado do aplicativo gerenciado para o qual a notificação foi acionada.
eventTime | O carimbo de data do evento que desencadeou a notificação. (Data e hora no formato UTC ISO 8601.)
provisioningState | O estado de provisionamento da instância de aplicação gerenciada. (Por exemplo, Sucesso, Falha, Exclusão, Exclusão.)
error | *Especificado somente quando o provisionamentoEstado é falhado*. Contém o código de erro, a mensagem e os detalhes do problema que causou a falha.
aplicativoDefinitionId | *Especificado apenas para aplicativos gerenciados pelo catálogo de serviços*. Representa o identificador de recursos totalmente qualificado da definição do aplicativo para o qual a instância de aplicativo gerenciada foi provisionada.
plan | *Especificado apenas para aplicativos gerenciados do Azure Marketplace*. Representa o editor, a oferta, o SKU e a versão da instância de aplicativo gerenciada.
detalhes de faturamento | *Especificado apenas para aplicativos gerenciados do Azure Marketplace.* Os detalhes de faturamento da instância de aplicação gerenciada. Contém os recursosUsageId que você pode usar para consultar o Azure Marketplace para obter detalhes de uso.

## <a name="endpoint-authentication"></a>Autenticação de ponto final
Para proteger o ponto final do webhook e garantir a autenticidade da notificação:
1. Forneça um parâmetro de consulta em cima do URI\:do webhook, assim: https //your-endpoint.com?sig=Guid. A cada notificação, verifique se `sig` o parâmetro `Guid`de consulta tem o valor esperado .
2. Emita um GET na instância do aplicativo gerenciado usando applicationId. Valide se o provisionamentoEstado corresponde ao provisionamentoEstado da notificação para garantir a consistência.

## <a name="notification-retries"></a>Tentativas de notificação

O serviço de notificação `200 OK` de aplicativos gerenciados espera uma resposta do ponto final do webhook à notificação. O serviço de notificação tentará novamente se o ponto final do webhook retornar um código de erro HTTP maior ou igual a 500, se ele retornar um código de erro de 429 ou se o ponto final estiver temporariamente inalcançável. Se o ponto final do webhook não estiver disponível dentro de 10 horas, a mensagem de notificação será descartada e as repetições serão paradas.
