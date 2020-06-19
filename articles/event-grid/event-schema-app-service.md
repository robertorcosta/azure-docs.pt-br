---
title: Serviço de Aplicativo do Azure como origem da Grade de Eventos
description: Este artigo descreve como usar o Serviço de Aplicativo do Azure como uma origem do evento da Grade de Eventos. Ele fornece o esquema e os links para os artigos do tutorial e de instruções.
services: event-grid
author: jasonfreeberg
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: jafreebe
ms.openlocfilehash: 0a24e8ba84739dbc1b5de5e0546a8fe0d2e826f1
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650700"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Serviço de Aplicativo do Azure como uma origem da Grade de Eventos

Este artigo fornece as propriedades e o esquema dos eventos do Serviço de Aplicativo do Azure. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md). Ele também fornece uma lista de inícios rápidos e de tutoriais para usar o Serviço de Aplicativo do Azure como uma origem do evento.

## <a name="event-grid-event-schema"></a>Esquema de eventos da Grade de Eventos

### <a name="available-event-types"></a>Tipos de evento disponíveis

O Serviço de Aplicativo do Azure emite os tipos de evento a seguir

|    Tipo de evento                                             |    Descrição                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft.Web/sites.BackupOperationStarted             |    Disparado quando um backup é iniciado                             |
|    Microsoft.Web/sites.BackupOperationCompleted           |    Disparado quando um backup é concluído                           |
|    Microsoft.Web/sites.BackupOperationFailed              |    Disparado quando há falha em um backup                              |
|    Microsoft.Web/sites.RestoreOperationStarted            |    Disparado quando uma restauração de um backup é iniciada        |
|    Microsoft.Web/sites.RestoreOperationCompleted          |    Disparado quando uma restauração de um backup é concluída      |
|    Microsoft.Web/sites.RestoreOperationFailed             |    Disparado quando há falha na restauração de um backup         |
|    Microsoft.Web/sites.SlotSwapStarted                    |    Disparado quando uma troca de slot é iniciada                          |
|    Microsoft.Web/sites.SlotSwapCompleted                  |    Disparado quando uma troca de slot é concluída                      |
|    Microsoft.Web/sites.SlotSwapFailed                     |    Disparado quando há falha em uma troca de slot                           |
|    Microsoft.Web/sites.SlotSwapWithPreviewStarted         |    Disparado quando uma troca de slot com versão prévia é iniciada           |
|    Microsoft.Web/sites.SlotSwapWithPreviewCancelled       |    Disparado quando uma troca de slot com versão prévia é cancelada    |
|    Microsoft.Web/sites.AppUpdated.Restarted               |    Disparado quando um site é reiniciado                      |
|    Microsoft.Web/sites.AppUpdated.Stopped                 |    Disparado quando um site é interrompido                          |
|    Microsoft.Web/sites.AppUpdated.ChangedAppSettings      |    Disparado quando as configurações do aplicativo de um site são alteradas             |
|    Microsoft.Web/serverfarms.AppServicePlanUpdated        |    Disparado quando um plano do Serviço de Aplicativo é atualizado                 |

### <a name="the-contents-of-an-event-response"></a>O conteúdo de uma resposta de evento

Quando um evento é disparado, o serviço de Grade de Eventos envia dados sobre esse evento para o ponto de extremidade de assinatura.
Esta seção mostra um exemplo de como os dados seriam para cada evento. Cada evento tem os seguintes dados de nível superior:

|     Propriedade          |     Type     |     Descrição                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    topic              |    string    |    Caminho de recurso completo para a origem do evento. Esse campo não é gravável. Grade de Eventos fornece esse valor.                                      |
|    subject            |    string    |    Caminho definido pelo fornecedor para o assunto do evento.                                                                                              |
|    eventType          |    string    |    Um dos tipos de evento registrados para a origem do evento.                                                                                  |
|    eventTime          |    string    |    A hora em que o evento é gerado com base na hora UTC do provedor.                                                                         |
|    id                 |    string    |    Identificador exclusivo do evento.                                                                                                            |
|    data               |    objeto    |    Dados de eventos do armazenamento de blob.                                                                                                                    |
|    dataVersion        |    string    |    A versão do esquema do objeto de dados. O fornecedor define a versão do esquema.                                                          |
|    metadataVersion    |    string    |    A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor.    |

#### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted, BackupOperationCompleted, BackupOperationFailed

```js
{
    id:'7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject:'/Microsoft.Web/sites/<site-name>',
    eventType:'Microsoft.Web.BackupOperationStarted',
    eventTime:'2020-01-28T18:26:51.7194887Z',
    data: {
        "appEventTypeDetail": { "action": "Started" },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    }
    topic:'/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion:'1',
    metaDataVersion:'1'
}
```

O objeto de dados contém as seguintes propriedades:

|    Propriedade                |    Type      |    Descrição                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    objeto    |    Detalhe da ação no aplicativo                                                                                       |
|    ação                  |    string    |    Tipo de ação da operação                                                                                   |
|    name                    |    string    |    nome do site que teve este evento                                                                          |
|    clientRequestId         |    string    |    A ID de solicitação do cliente gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento         |
|    correlationRequestId    |    string    |    A ID da solicitação de correlação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento    |
|    requestId               |    string    |    A ID da solicitação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento                |
|    address                 |    string    |    URL de solicitação HTTP desta operação                                                                                |
|    verbo                    |    string    |    Verbo HTTP desta operação                                                                                       |

#### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted, RestoreOperationCompleted, RestoreOperationFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.RestoreOperationStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: { 
            action: "Started" 
        },
        siteName: "<site-name>",
        clientRequestId: "None",
        correlationRequestId: "None",
        requestId: "292f499d-04ee-4066-994d-c2df57b99198",
        address: "None",
        verb: "POST"
    }
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

O objeto de dados contém as seguintes propriedades:

|    Propriedade                |    Type      |    Descrição                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    objeto    |    Detalhe da ação no aplicativo                                                                                       |
|    ação                  |    string    |    Tipo de ação da operação                                                                                   |
|    name                    |    string    |    nome do site que teve este evento                                                                          |
|    clientRequestId         |    string    |    A ID de solicitação do cliente gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento         |
|    correlationRequestId    |    string    |    A ID da solicitação de correlação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento    |
|    requestId               |    string    |    A ID da solicitação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento                |
|    address                 |    string    |    URL de solicitação HTTP desta operação                                                                                |
|    verbo                    |    string    |    Verbo HTTP desta operação                                                                                       |

#### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted, SlotSwapCompleted, SlotSwapFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

O objeto de dados contém as seguintes propriedades:

|    Propriedade                |    Type      |    Descrição                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    objeto    |    Detalhe da ação no aplicativo                                                                                       |
|    ação                  |    string    |    Tipo de ação da operação                                                                                   |
|    name                    |    string    |    nome do site que teve este evento                                                                          |
|    clientRequestId         |    string    |    A ID de solicitação do cliente gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento         |
|    correlationRequestId    |    string    |    A ID da solicitação de correlação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento    |
|    requestId               |    string    |    A ID da solicitação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento                |
|    address                 |    string    |    URL de solicitação HTTP desta operação                                                                                |
|    verbo                    |    string    |    Verbo HTTP desta operação                                                                                       |
|    sourceSlot              |    string    |    O slot de origem da troca                                                                                       |

#### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted, SlotSwapWithPreviewCancelled

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapWithPreviewStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

O objeto de dados contém as seguintes propriedades:

|    Propriedade                |    Type      |    Descrição                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    objeto    |    Detalhe da ação no aplicativo                                                                                       |
|    ação                  |    string    |    Tipo de ação da operação                                                                                   |
|    name                    |    string    |    nome do site que teve este evento                                                                          |
|    clientRequestId         |    string    |    A ID de solicitação do cliente gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento         |
|    correlationRequestId    |    string    |    A ID da solicitação de correlação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento    |
|    requestId               |    string    |    A ID da solicitação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento                |
|    address                 |    string    |    URL de solicitação HTTP desta operação                                                                                |
|    verbo                    |    string    |    Verbo HTTP desta operação                                                                                       |

#### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated.Restarted, AppUpdated.Stopped, AppUpdated.ChangedAppSettings

```js
{
    id: 'b74ea56b-2a3f-4de5-a5d7-38e60c81cf23',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.AppUpdated',
    eventTime: '2020-01-28T18:22:30.2760952Z',
    data: {
        appEventTypeDetail: {
            action: 'Stopped'
        },
        siteName: '<site-name>',
        clientRequestId: '64a5e0aa-7cee-4ff1-9093-b9197b820014',
        correlationRequestId: '25bb36a5-8f6c-4f04-b615-e9a0ee045756',
        requestId: 'f2e8eb3f-b190-42de-b99e-6acefe587374',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop',
        verb: 'POST'
    },
    topic: '/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

O objeto de dados tem as seguintes propriedades:

|    Propriedade                |    Type      |    Descrição                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    objeto    |    Detalhe da ação no aplicativo                                                                                       |
|    ação                  |    string    |    Tipo de ação da operação                                                                                   |
|    name                    |    string    |    nome do site que teve este evento                                                                          |
|    clientRequestId         |    string    |    A ID de solicitação do cliente gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento         |
|    correlationRequestId    |    string    |    A ID da solicitação de correlação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento    |
|    requestId               |    string    |    A ID da solicitação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento                |
|    address                 |    string    |    URL de solicitação HTTP desta operação                                                                                |
|    verbo                    |    string    |    Verbo HTTP desta operação                                                                                       |

#### <a name="serverfarmsappserviceplanupdated"></a>Serverfarms.AppServicePlanUpdated

```js
{
   id: "56501672-9150-40e1-893a-18420c7fdbf7",
   subject: "/Microsoft.Web/serverfarms/<plan-name>",
   eventType: "Microsoft.Web.AppServicePlanUpdated",
   eventTime: "2020-01-28T18:22:23.5516004Z",
   data: {
        serverFarmEventTypeDetail: {
            stampKind: "Public",
            action: "Updated",
            status: "Started"
        },
        serverFarmId: "0",
        sku: {
            name: "P1v2",
            tier: "PremiumV2",
            size: "P1v2",
            family: "Pv2",
            capacity: 1
        },
        clientRequestId: "8f880321-a991-45c7-b743-6ff63fe4c004",
        correlationRequestId: "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        requestId: "b973a8e6-6949-4783-b44c-ac778be831bb",
        address: "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        verb: "PUT"
   },
   topic: "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
   dataVersion: "1",
   metaDataVersion: "1"
}
```

O objeto de dados tem as seguintes propriedades:

|    Propriedade                         |    Type      |    Descrição                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appServicePlanEventTypeDetail    |    objeto    |    Detalhe da ação no plano do serviço de aplicativo                                                                          |
|    stampKind                        |    string    |    Tipo de ambiente em que está o plano do serviço de aplicativo                                                                     |
|    ação                           |    string    |    Tipo da ação no plano do serviço de aplicativo                                                                            |
|    status                           |    string    |    Status da operação no plano do serviço de aplicativo                                                                   |
|    sku                              |    objeto    |    sku do plano de serviço de aplicativo                                                                                       |
|    name                             |    string    |    nome do plano de serviço de aplicativo                                                                                      |
|    Camada                             |    string    |    camada do plano de serviço de aplicativo                                                                                      |
|    Tamanho                             |    string    |    tamanho do plano de serviço de aplicativo                                                                                      |
|    Família                           |    string    |    família do plano de serviço de aplicativo                                                                                        |
|    Capacity                         |    string    |    capacidade do plano de serviço de aplicativo                                                                                      |
|    ação                           |    string    |    Tipo de ação da operação                                                                                   |
|    name                             |    string    |    nome do site que teve este evento                                                                          |
|    clientRequestId                  |    string    |    A ID de solicitação do cliente gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento         |
|    correlationRequestId             |    string    |    A ID da solicitação de correlação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento    |
|    requestId                        |    string    |    A ID da solicitação gerada pelo serviço de aplicativo para a operação da API do site que disparou este evento                |
|    address                          |    string    |    URL de solicitação HTTP desta operação                                                                                |
|    verbo                             |    string    |    Verbo HTTP desta operação                                                                                       |
