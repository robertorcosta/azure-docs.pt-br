---
title: Gerenciar pontos de extremidade e rotas (Portal)
titleSuffix: Azure Digital Twins
description: Consulte como configurar e gerenciar pontos de extremidade e rotas de eventos para dados de gêmeos digital do Azure usando o portal do Azure.
author: v-lakast
ms.author: v-lakast
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7786f970f612d2856948e2286ed234e2b0895072
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836948"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Gerenciar pontos de extremidade e rotas no gêmeos digital do Azure (Portal)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

No Azure digital gêmeos, você pode rotear [notificações de eventos](how-to-interpret-event-data.md) para serviços de downstream ou recursos de computação conectados. Isso é feito primeiro Configurando **pontos de extremidade** que podem receber os eventos. Em seguida, você pode criar [**rotas de eventos**](concepts-route-events.md) que especificam quais eventos gerados pelo Azure digital gêmeos são entregues a quais pontos de extremidade.

Este artigo orienta você pelo processo de criação de pontos de extremidade e rotas usando o [portal do Azure](https://portal.azure.com).

Você também pode gerenciar pontos de extremidade e rotas com as [APIs do EventRoutes](how-to-use-apis-sdks.md), o [SDK do .net (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)ou a [CLI do gêmeos digital do Azure](how-to-use-cli.md). Para obter uma versão deste artigo que usa esses mecanismos em vez do portal, consulte [*como: gerenciar pontos de extremidade e rotas (APIs e CLI)*](how-to-manage-routes-apis-cli.md).

## <a name="prerequisites"></a>Pré-requisitos

* Você precisará de uma **conta do Azure** (você pode configurar uma gratuitamente [aqui](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* Você precisará de uma **instância do gêmeos digital do Azure** em sua assinatura do Azure. Se você ainda não tiver uma instância, poderá criar uma usando as etapas em [*como: configurar uma instância e uma autenticação*](how-to-set-up-instance-scripted.md). Faça com que os seguintes valores da configuração sejam úteis para uso posterior neste artigo:
    - Nome da instância
    - Grupo de recursos

Você pode encontrar esses detalhes no [portal do Azure](https://portal.azure.com) depois de configurar sua instância. Faça logon no portal e procure o nome da sua instância na barra de pesquisa do Portal.
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Captura de tela da barra de pesquisa portal do Azure.":::

Selecione sua instância nos resultados para ver a página de detalhes da sua instância:

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Captura de tela dos detalhes da instância de ADT." border="false":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Criar um ponto de extremidade para o gêmeos digital do Azure

Estes são os tipos de pontos de extremidade com suporte que você pode criar para sua instância:
* [Grade de Eventos](../event-grid/overview.md) 
* [Hubs de Evento](../event-hubs/event-hubs-about.md)
* [Barramento de Serviço](../service-bus-messaging/service-bus-messaging-overview.md)

Para obter mais informações sobre os diferentes tipos de ponto de extremidade, consulte [*escolher entre os serviços de mensagens do Azure*](../event-grid/compare-messaging-services.md).

Para vincular um ponto de extremidade ao Azure digital gêmeos, o tópico da grade de eventos, o Hub de eventos ou o barramento de serviço que você está usando para o ponto de extremidade precisa já existir. 

### <a name="create-an-event-grid-endpoint"></a>Criar um ponto de extremidade de grade de eventos

**Pré-requisito**: Crie um tópico da grade de eventos seguindo as etapas na [seção *criar um tópico personalizado* ](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) do guia de início rápido *eventos personalizados* da grade de eventos.

Depois de criar o tópico, você pode vinculá-lo ao Azure digital gêmeos na página da instância do gêmeos digital do Azure na [portal do Azure](https://portal.azure.com) (você pode encontrar a instância inserindo seu nome na barra de pesquisa do Portal).

No menu instância, selecione _pontos de extremidade_. Em seguida, na página *pontos de extremidade* a seguir, selecione *+ criar um ponto de extremidade*. 

Na página *criar um ponto de extremidade* que é aberta, você pode criar um ponto de extremidade do tipo _grade de eventos_ selecionando o botão de opção correspondente. Preencha os outros detalhes: Insira um nome para o ponto de extremidade no campo _nome_ , escolha sua _assinatura_ na lista suspensa e escolha o tópico da _grade de eventos_ pré-criado no terceiro menu suspenso.

Em seguida, crie seu ponto de extremidade ao pressionar _salvar_.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Captura de tela da criação de um ponto de extremidade do tipo grade de eventos.":::

Você pode verificar se o ponto de extremidade foi criado com êxito verificando o ícone de notificação na barra de portal do Azure superior: 

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="Captura de tela de notificação para verificar a criação do ponto de extremidade." border="false":::

Você também pode exibir o ponto de extremidade que foi criado de volta na página *pontos de extremidade* para sua instância do gêmeos digital do Azure.

Se a criação do ponto de extremidade falhar, observe a mensagem de erro e tente novamente após alguns minutos.

Agora, o tópico da grade de eventos está disponível como um ponto de extremidade dentro do Azure digital gêmeos, sob o nome especificado no campo _nome_ . Normalmente, você usará esse nome como o destino de uma **rota de evento**, que será criada [posteriormente neste artigo](#event-routes).

### <a name="create-an-event-hubs-endpoint"></a>Criar um ponto de extremidade de hubs de eventos

**Pré-requisitos**: 
* Você precisará de um _namespace de hubs de eventos_ e um hub de _eventos_. Crie ambos seguindo as etapas nos hubs de eventos [*criar um hub de eventos*](../event-hubs/event-hubs-create.md) início rápido.
* Você precisará de uma _regra de autorização_. Para criar isso, consulte o artigo hubs de eventos [*autorizando o acesso a recursos de hubs de eventos usando assinaturas de acesso compartilhado*](../event-hubs/authorize-access-shared-access-signature.md) .

Vá para a página de detalhes da instância do gêmeos digital do Azure no [portal do Azure](https://portal.azure.com) (você pode encontrá-la inserindo seu nome na barra de pesquisa do Portal).

No menu instância, selecione _pontos de extremidade_. Em seguida, na página *pontos de extremidade* a seguir, selecione *+ criar um ponto de extremidade*. 

Na página *criar um ponto de extremidade* que é aberta, você pode criar um ponto de extremidade do tipo _Hub de eventos_ selecionando o botão de opção correspondente. Insira um nome para o ponto de extremidade no campo _nome_ . Em seguida, selecione sua _assinatura_e o namespace do _Hub de eventos_pré-criado, o _Hub de eventos_e a regra de _autorização_ nas respectivas listas suspensas.

Em seguida, crie seu ponto de extremidade ao pressionar _salvar_.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub.png" alt-text="Captura de tela da criação de um ponto de extremidade do tipo hubs de eventos.":::

Você pode verificar se o ponto de extremidade foi criado com êxito verificando o ícone de notificação na barra de portal do Azure superior. 

Se a criação do ponto de extremidade falhar, observe a mensagem de erro e tente novamente após alguns minutos.

Agora, o Hub de eventos está disponível como um ponto de extremidade dentro do Azure digital gêmeos, sob o nome especificado no campo _nome_ . Normalmente, você usará esse nome como o destino de uma **rota de evento**, que será criada [posteriormente neste artigo](#event-routes).

### <a name="create-a-service-bus-endpoint"></a>Criar um ponto de extremidade do barramento de serviço

**Pré-requisitos**: 
* Você precisará de um _namespace do barramento de serviço_ e um tópico do barramento de _serviço_. Crie ambos seguindo as etapas no início rápido [*criar tópicos e assinaturas*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) do barramento de serviço. Não é necessário concluir a seção [*criar assinaturas para o tópico*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md#create-subscriptions-to-the-topic) .
* Você precisará de uma _regra de autorização_. Para criar isso, consulte o artigo [*autenticação e autorização*](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) do barramento de serviço.

Vá para a página de detalhes da instância do gêmeos digital do Azure no [portal do Azure](https://portal.azure.com) (você pode encontrá-la inserindo seu nome na barra de pesquisa do Portal).

No menu instância, selecione _pontos de extremidade_. Em seguida, na página *pontos de extremidade* a seguir, selecione *+ criar um ponto de extremidade*. 

Na página *criar um ponto de extremidade* que é aberta, você pode criar um ponto de extremidade do tipo _barramento de serviço_ selecionando o botão de opção correspondente. Insira um nome para o ponto de extremidade no campo _nome_ . Em seguida, selecione sua _assinatura_e o namespace do _barramento de serviço_criado previamente, o tópico do _barramento de serviço_e a regra de _autorização_ nas respectivas listas suspensas.

Em seguida, crie seu ponto de extremidade ao pressionar _salvar_.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-service-bus.png" alt-text="Captura de tela da criação de um ponto de extremidade do tipo barramento de serviço.":::

Você pode verificar se o ponto de extremidade foi criado com êxito verificando o ícone de notificação na barra de portal do Azure superior. 

Se a criação do ponto de extremidade falhar, observe a mensagem de erro e tente novamente após alguns minutos.

Agora, o tópico do barramento de serviço está disponível como um ponto de extremidade dentro do Azure digital gêmeos, sob o nome especificado no campo _nome_ . Normalmente, você usará esse nome como o destino de uma **rota de evento**, que será criada [posteriormente neste artigo](#event-routes).

## <a name="event-routes"></a>Rotas de eventos

Para realmente enviar dados do Azure digital gêmeos para um ponto de extremidade, você precisará definir uma **rota de evento**. Essas rotas permitem que os desenvolvedores conectem o fluxo de eventos, em todo o sistema e em serviços downstream. Leia mais sobre as rotas de eventos em [*conceitos: roteamento de eventos do gêmeos digital do Azure*](concepts-route-events.md).

**Pré-requisito**: você precisa criar pontos de extremidade conforme descrito anteriormente neste artigo antes de passar para a criação de uma rota. Você pode prosseguir com a criação de uma rota de evento quando seus pontos de extremidade tiverem concluído a configuração.

>[!NOTE]
>Se você tiver implantado seus pontos de extremidade recentemente, valide que eles concluíram a implantação **antes** de tentar usá-los para uma nova rota de evento. Se não for possível configurar a rota porque os pontos de extremidade não estão prontos, aguarde alguns minutos e tente novamente.

### <a name="create-an-event-route"></a>Criar uma rota de eventos 

Uma definição de rota de evento pode conter estes elementos:
* A ID de rota que você deseja usar
* O nome do ponto de extremidade que você deseja usar
* Um filtro que define quais eventos são enviados para o ponto de extremidade

Se não houver nenhuma ID de rota, nenhuma mensagem será roteada fora do Azure digital gêmeos.
Se houver uma ID de rota e o filtro for `true` , todas as mensagens serão roteadas para o ponto de extremidade.
Se houver uma ID de rota e um filtro diferente for adicionado, as mensagens serão filtradas com base no filtro.

Uma rota deve permitir que várias notificações e tipos de eventos sejam selecionados.

Para criar uma rota de evento, vá para a página de detalhes da instância do gêmeos digital do Azure no [portal do Azure](https://portal.azure.com) (você pode encontrar a instância inserindo seu nome na barra de pesquisa do Portal).

No menu instância, selecione _rotas de eventos_. Em seguida, na página *rotas de eventos* a seguir, selecione *+ criar uma rota de evento*. 

Na página *criar uma rota de eventos* que é aberta, escolha no mínimo um nome para a rota no campo _nome_ e selecione o ponto de _extremidade_ que você deseja usar para criar uma rota na lista suspensa.

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="Captura de tela da criação de rota de evento para sua instância.":::

Quando terminar, pressione o botão _salvar_ para criar a rota de eventos.

### <a name="filter-events"></a>Filtrar eventos

Sem a filtragem, os pontos de extremidade recebem uma variedade de eventos do Azure digital gêmeos:
* Telemetria acionada por [gêmeos digital](concepts-twins-graph.md) usando a API do serviço gêmeos do Azure digital
* Notificações de alteração de propriedade de cópia, disparadas em alterações de propriedade para qualquer cópia na instância de gêmeos digital do Azure
* Eventos de ciclo de vida, acionados quando gêmeos ou relações são criados ou excluídos
* Eventos de alteração de modelo, acionados quando [modelos](concepts-models.md) configurados em uma instância de gêmeos digital do Azure são adicionados ou excluídos

Você pode restringir os eventos que estão sendo enviados adicionando um **filtro** para um ponto de extremidade à sua rota de eventos.

Para adicionar um filtro durante a criação de uma rota de eventos, use a seção _Adicionar um filtro de rota de eventos_ da página *criar uma rota de eventos* . 

Você pode selecionar entre algumas opções básicas de filtro comum ou usar as opções de filtro avançado para escrever seus próprios filtros personalizados.

#### <a name="use-the-basic-filters"></a>Usar os filtros básicos

Para usar os filtros básicos, expanda a opção _tipos de evento_ e marque as caixas de seleção correspondentes aos eventos que você gostaria de filtrar. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="Captura de tela da criação de uma rota de eventos com um filtro básico. Marcando as caixas de seleção dos eventos.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Isso irá preencher automaticamente a caixa de texto de filtro com o texto do filtro que você selecionou:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="Captura de tela da criação de uma rota de eventos com um filtro básico. Mostrando o texto do filtro preenchido automaticamente após a seleção dos eventos.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>Usar os filtros avançados

Como alternativa, você pode usar a opção de filtro avançado para escrever seus próprios filtros personalizados.

Para criar uma rota de eventos com opções de filtro avançadas, alterne a opção para o _Editor avançado_ para habilitá-la. Em seguida, você pode escrever seus próprios filtros de eventos na caixa de *filtro* :

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="Captura de tela da criação de uma rota de evento com um filtro avançado.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Aqui estão os filtros de rota com suporte. Os detalhes na coluna *esquema de texto de filtro* são o texto que pode ser inserido na caixa de filtro.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Próximas etapas

Leia sobre os diferentes tipos de mensagens de evento que você pode receber:
* [*Como: interpretar dados de evento*](how-to-interpret-event-data.md)
