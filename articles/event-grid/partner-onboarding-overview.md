---
title: Integração como um parceiro de Grade de Eventos do Azure
description: Tipo de Tópico de Integração como um parceiro de Grade de Eventos do Azure. Entenda o modelo de recurso e o fluxo de publicação para tópicos de parceiros.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 2a1f35b86e21099c9fdd0397ae8a3b20aed3cd5d
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758819"
---
# <a name="onboard-as-an-azure-event-grid-partner"></a>Integração como um parceiro de Grade de Eventos do Azure

Este artigo descreve como usar de forma privada os recursos de parceiro da Grade de Eventos e como se tornar um tipo de tópico de parceiro disponível publicamente.

Você não precisa de permissão especial para começar a usar os tipos de recursos da Grade de Eventos associados à publicação de eventos como um parceiro de Grade de Eventos. Na verdade, você pode usá-los hoje mesmo para publicar eventos de forma privada em suas próprias Assinaturas do Azure e testar o modelo de recursos se estiver pensando em se tornar um parceiro.

## <a name="becoming-an-event-grid-partner"></a>Tornar-se um parceiro da Grade de Eventos

Se você estiver interessado em se tornar um parceiro de Grade de Eventos público, comece preenchendo [este formulário](https://aka.ms/gridpartnerform)e, em seguida, contate a equipe da Grade de Eventos em [GridPartner@microsoft.com](mailto:gridpartner@microsoft.com).

## <a name="how-partner-topics-work"></a>Como os tópicos de parceiros funcionam
Os tópicos de parceiros usam a arquitetura existente que a Grade de Eventos já usa para publicar eventos de recursos do Azure, como o armazenamento e o Hub IoT, e disponibiliza essas ferramentas publicamente para qualquer pessoa. O uso dessas ferramentas é privado por padrão somente para sua assinatura do Azure. Para disponibilizar seus eventos publicamente, preencha o formulário acima e [entre em contato com a equipe de Grade de Eventos](mailto:gridpartner@microsoft.com).

Os tópicos de parceiro permitem que você publique eventos na Grade de Eventos do Azure para consumo multilocatário.

### <a name="onboarding-and-event-publishing-overview"></a>Visão geral da integração e publicação de eventos

#### <a name="partner-flow"></a>Fluxo de parceiros

1. Crie um locatário do Azure caso você ainda não tenha um.
1. Usando a CLI, crie uma nova Grade de Eventos `partnerRegistration`. Esse recurso inclui informações como nome de exibição, descrição, URI de instalação e assim por diante.

    ![Criar o Tópico de Parceiro](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Crie um ou mais `partnerNamespaces` em cada região em que você deseja publicar eventos. Como parte disso, o serviço de Grade de Eventos provisionará um ponto de extremidade de publicação (por exemplo, `https://contoso.westus-1.eventgrid.azure.net/api/events`) e chaves de acesso.

    ![Criar namespace do parceiro](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Forneça uma maneira para os clientes se registrarem no sistema em que gostariam de um tópico de parceiro.
1. Entre em contato com a equipe da Grade de Eventos para nos informar que você gostaria que seu tipo de tópico de parceiro se torne público.

#### <a name="customer-flow"></a>Fluxo de cliente

1. O cliente visitará a portal do Azure para observar a ID de assinatura do Azure e o grupo de recursos que em que gostaria que o tópico de parceiro fosse criado.
1. O cliente solicitará um tópico de parceiro por meio do seu sistema. Em resposta, você criará um Túnel de Eventos para seu namespace de parceiro.
1. A grade de eventos criará um tópico de parceiro **pendente** na assinatura do Azure do cliente e no grupo de recursos.

    ![Criar canal de evento](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. O cliente ativa o tópico parceiro por meio do portal do Azure. Os eventos agora podem fluir de seu serviço para a assinatura do Azure do cliente.

    ![Ativar o tópico de parceiro](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Modelo de recursos

Abaixo está o modelo de recurso para tópicos de parceiros.

### <a name="partner-registrations"></a>Registros de parceiro
* Recurso: `partnerRegistrations`
* Usado por: Parceiros
* Descrição: Captura os metadados globais do parceiro SaaS (por exemplo, nome, nome de exibição, descrição, URI de instalação).
    
    Criar/atualizar um registro de parceiro é uma operação de autoatendimento para os parceiros. Essa capacidade de autoatendimento permite que os parceiros criem e testem o fluxo completo de ponta a ponta.
    
    Somente os partnerRegistrations aprovados pela Microsoft são detectáveis pelos clientes.
* Escopo: Criado na assinatura do Azure do parceiro. Metadados visíveis para clientes uma vez públicos.

### <a name="partner-namespaces"></a>Namespaces de parceiro
* Recurso: partnerNamespaces
* Usado por: Parceiros
* Descrição: Fornece um recurso regional para publicar eventos de cliente. Cada namespace de parceiro tem um ponto de extremidade de publicação e chaves de autenticação. O namespace também é como o parceiro solicita um tópico de parceiro para um determinado cliente e lista os clientes ativos.
* Escopo: Reside na assinatura do parceiro.

### <a name="event-channel"></a>Canal de evento
* Recurso: `partnerNamespaces/eventChannels`
* Usado por: Parceiros
* Descrição: Os túneis de evento são um espelho do tópico do parceiro do cliente. Ao criar um túnel de eventos e especificar a assinatura do Azure do cliente e o grupo de recursos nos metadados, você está sinalizando para a grade de eventos para criar um tópico de parceiro para o cliente. A Grade de Eventos emitirá uma chamada do ARM para criar um partnerTopic correspondente na assinatura do cliente. O tópico de parceiro será criado em um estado "pendente". Há um link 1-1 entre cada eventTunnel e um partnerTopic.
* Escopo: Reside na assinatura do parceiro.

### <a name="partner-topics"></a>Tópicos de parceiros
* Recurso: `partnerTopics`
* Usado por: Clientes
* Descrição: Os tópicos de parceiro são semelhantes ao tópico personalizado e ao Tópico do Sistema na Grade de Eventos. Cada tópico de parceiro é associado a uma "origem" específica (por exemplo, `Contoso:myaccount`) e a um tipo de tópico de parceiro específico (por exemplo, "Contoso"). Os clientes criam assinaturas de evento no tópico do parceiro para rotear eventos para vários manipuladores de eventos.

    Os clientes não podem criar esse recurso diretamente. A única maneira de criar um tópico de parceiro é por meio de uma operação de parceiro que cria um túnel de eventos.
* Escopo: Reside na assinatura do cliente.

### <a name="partner-topic-types"></a>Tipos de tópico de parceiro
* Recurso: `partnerTopicTypes`
* Usado por: Clientes
* Descrição: Os tipos de tópico de parceiro são tipos de recursos em todo o locatário, que permitem aos clientes descobrir a lista de tipos de tópico de parceiros aprovados. Aa URL será semelhantes ao seguinte: https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Escopo: Global

## <a name="publishing-events-to-event-grid"></a>Publicar eventos para a Grade de Eventos
Ao criar um partnerNamespace em uma região do Azure, você obterá um ponto de extremidade regional e as chaves de autenticação correspondentes. Publicar lotes de eventos nesse ponto de extremidade para todos os túneis de eventos do cliente nesse namespace. Com base no campo “origem” no evento, a Grade de Eventos do Azure mapeará cada evento com os tópicos de parceiro correspondentes.

### <a name="event-schema-cloudevents-v10"></a>Esquema do evento: CloudEvents v1.0
Publique eventos na grade de eventos do Azure usando o esquema CloudEvents 1.0. A Grade de Eventos dá suporte ao modo estruturado e ao modo em lote. CloudEvents 1.0 é o único esquema de evento com suporte para Namespaces de Parceiro.

### <a name="example-flow"></a>Fluxo de exemplo

1.  O serviço de publicação faz um HTTP POST para `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.
2.  Na solicitação, inclua um valor de cabeçalho chamado aeg-sas-key que contém uma chave para autenticação. Essa chave é provisionada durante a criação do partnerNamespace. Por exemplo, um valor de cabeçalho válido é aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==.
3.  Defina o cabeçalho Content-Type como “application/cloudevents-batch+json; charset=UTF-8”.
4.  Execute um HTTP POST para a URL de publicação acima com um lote de eventos correspondentes a essa região. Por exemplo:

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

Após a postagem para o ponto de extremidade partnerNamespace, você receberá uma resposta. A resposta é um código de resposta HTTP padrão. Algumas respostas comuns são:

| Result                             | Resposta              |
|------------------------------------|-----------------------|
| Sucesso                            | 200 OK                |
| Os dados de evento têm formato incorreto    | 400 Solicitação Inválida       |
| Chave de acesso inválida                 | 401 Não Autorizado      |
| Ponto de extremidade incorreto                 | 404 Não Encontrado         |
| Matriz ou evento excede os limites de tamanho | O conteúdo 413 é muito grande |

## <a name="reference"></a>Referência

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [Modelo do ARM](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)
  * [Esquema de modelo de ARM](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [APIs REST](https://docs.microsoft.com/rest/api/eventgrid/version2020-04-01-preview/partnernamespaces)
  * [Extensões da CLI](https://docs.microsoft.com/cli/azure/ext/eventgrid/?view=azure-cli-latest)

### <a name="sdks"></a>SDKs
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Go](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Próximas etapas
- [Visão geral dos tópicos de parceiros](partner-topics-overview.md)
- [Formulário de integração de tópicos de parceiros](https://aka.ms/gridpartnerform)
- [Tópico de parceiro do Auth0](auth0-overview.md)
- [Como usar o tópico de parceiro do Auth0](auth0-how-to.md)
