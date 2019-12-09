---
title: Tutorial – Usar eventos do Hub IoT para disparar Aplicativos Lógicos do Azure
description: Este tutorial mostra como usar o serviço de roteamento de evento da Grade de Eventos do Azure e criar processos automatizados para executar ações dos Aplicativos Lógicos do Azure com base nos eventos do Hub IoT.
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: robinsh
ms.openlocfilehash: 334b7b2c59b328e8eff3c7c2b9c3ed46bffc3442
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706428"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Tutorial: Enviar notificações por email sobre os eventos do Hub IoT do Azure usando Aplicativos Lógicos e a Grade de Eventos

A Grade de Eventos do Azure permite que você reaja aos eventos no Hub IoT, disparando ações em seus aplicativos de negócios a jusante.

Este artigo aborda uma configuração de exemplo que usa o Hub IoT e a Grade de Eventos. No final, você terá um Aplicativo Lógico do Azure configurado para enviar um email de notificação sempre que um dispositivo for adicionado ao seu Hub IoT. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de email de qualquer provedor de email com suporte pelos Aplicativos Lógicos do Azure Apps, como Office 365 Outlook, Outlook.com ou Gmail. Essa conta de email é usada para enviar as notificações de eventos. Para obter uma lista completa de conectores de Aplicativos Lógicos com suporte, consulte a [Visão geral dos conectores](https://docs.microsoft.com/connectors/)
* Uma conta ativa do Azure. Se você não tiver uma, poderá [criar uma conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Um Hub IoT no Azure. Se ainda não criou um, consulte a [Introdução ao Hub IoT](../iot-hub/iot-hub-csharp-csharp-getstarted.md) para obter um passo a passo. 

## <a name="create-a-logic-app"></a>Criar um aplicativo lógico

Primeiro, crie um aplicativo lógico e adicione um gatilho de Grade de Eventos que monitora o grupo de recursos de sua máquina virtual. 

### <a name="create-a-logic-app-resource"></a>Criar um recurso de aplicativo lógico

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** e digite "aplicativo lógico" na caixa de pesquisa e selecione retornar. Selecione **Aplicativo Lógico** dos resultados.

   ![Criar aplicativo lógico](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. Na próxima tela, selecione **Criar**. 

1. Nomeie seu aplicativo lógico que é exclusivo na sua assinatura e, em seguida, selecione a mesma assinatura, grupo de recursos e local como o Hub IoT. 

   ![Campos para criar aplicativo lógico](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Selecione **Criar**.

1. Após o recurso ser criado, navegue até seu aplicativo lógico. Para fazer isso, selecione **grupos de recursos**e, em seguida, selecione o grupo de recursos que você criou para este tutorial. Em seguida, localize o aplicativo lógico na lista de recursos e selecione-o. 

1. No Designer de Aplicativos Lógicos, desça na página para ver **Modelos**. Escolha **Aplicativo Lógico em Branco** para poder compilar seu aplicativo lógico do zero.

### <a name="select-a-trigger"></a>Selecionar um gatilho

Um gatilho é um evento específico que inicia o aplicativo lógico. Para este tutorial, o gatilho que desencadeia o fluxo de trabalho está recebendo uma solicitação através HTTP.  

1. Na barra de pesquisa de conectores e gatilhos, digite **HTTP**.

1. Selecione **Solicitação - Quando uma solicitação HTTP é recebida** como o gatilho. 

   ![Selecionar o gatilho de solicitação HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Selecione **Use o conteúdo de amostra para gerar o esquema**. 

   ![Selecionar o gatilho de solicitação HTTP](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Cole o código JSON de exemplo a seguir na caixa de texto e, em seguida, selecione **Concluído**:

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "deviceEtag": "null",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice"
     },
     "dataVersion": "1",
     "metadataVersion": "1"
   }]
   ```

1. É possível que você receba uma notificação pop-up informando: **Lembre-se de incluir um cabeçalho Content-Type definido como application/json na sua solicitação.** Você pode ignorar essa sugestão com segurança e passar para a próxima seção. 

### <a name="create-an-action"></a>Criar uma ação

As ações são quaisquer etapas que ocorrem após o gatilho iniciar o fluxo de trabalho do aplicativos lógico. Para este tutorial, a ação é enviar uma notificação por email do seu provedor de email. 

1. Selecione **Nova etapa**. Isso abre uma janela para **Escolher uma ação**.

1. Pesquisar por **Email**.

1. Com base no seu provedor de email, localize e selecione o conector correspondente. Este tutorial usa o **Office 365 Outlook**. As etapas para outros provedores de email são semelhantes. 

   ![Selecione o conector do fornecedor de email](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Selecione a ação **Enviar um email**. 

1. Se solicitado, entre em sua conta de email. 

1. Compile seu modelo de email. 

   * **Para**: insira o endereço de email para receber os emails de notificação. Para este tutorial, use uma conta de email que você possa acessar para testar. 

   * **Entidade**: Preencha o texto do assunto. Ao clicar na caixa de texto assunto, você pode selecionar o conteúdo dinâmico a ser incluído. Por exemplo, este tutorial usa `IoT Hub alert: {event Type}`. Se não for possível ver o conteúdo dinâmico, selecione hiperlink o **Adicionar conteúdo dinâmico**, isso alterna entre ligado e desligado.

   * **Corpo**: escreva o texto de seu email. Selecione as propriedades JSON da ferramenta seletora para incluir conteúdo dinâmico com base nos dados de eventos. Se você não conseguir ver o Conteúdo dinâmico, selecione o hiperlink **Adicionar conteúdo dinâmico** na caixa de texto **Corpo**. Se ela não mostrar os campos desejados, clique em *mais* na tela Conteúdo dinâmico para incluir os campos da ação anterior.

   Seu modelo de email poderá ser semelhante a este exemplo:

   ![Preencha informações de email](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Salve seu aplicativo lógico. 

### <a name="copy-the-http-url"></a>Copiar a URL HTTP

Antes de sair do Designer de Aplicativos Lógicos, copie a URL que seus aplicativos lógicos estão ouvindo para um gatilho. Você usa essa URL para configurar a Grade de Eventos. 

1. Expanda a caixa de configuração do gatilho **Quando uma solicitação HTTP for recebida**, clicando nela. 

1. Copie o valor de **URL DE HTTP POST**, selecionando o botão de cópia ao lado dele. 

   ![Copiar a URL DE HTTP POST](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Salve essa URL para que você possa referir-se a ela na próxima seção. 

## <a name="configure-subscription-for-iot-hub-events"></a>Configurar a assinatura de eventos do Hub IoT

Nesta seção, você configura o Hub IoT para publicar eventos à medida que ocorrem. 

1. No portal do Azure, navegue para o hub IoT. Você pode fazer isso selecionando **Grupos de recursos**, selecionando o grupo de recursos para este tutorial e, em seguida, selecionando o Hub IoT na lista de recursos.

2. Selecione **Eventos**.

   ![Abra os detalhes da Grade de Eventos](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Selecione **Assinatura de evento**. 

   ![Criar nova assinatura de evento](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Crie a assinatura de evento com os seguintes valores: 

   * **Detalhes da Assinatura de Evento**: Forneça um nome descritivo e selecione **Esquema da Grade de Eventos**.

   * **Tipos de Evento**: Em **Filtrar para Tipos de Evento**, desmarque todas as opções, exceto **Dispositivo Criado**.

       ![tipos de evento de assinatura](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)

   * **Detalhes do Ponto de Extremidade**: Selecione o Tipo de Ponto de Extremidade como **Web Hook** e selecione *selecionar um ponto de extremidade*, cole a URL copiada do aplicativo lógico e confirme a seleção.

     ![selecionar a url de ponto de extremidade](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

   Quando terminar, o painel deverá ser como o seguinte exemplo: 

    ![Exemplo de formulário de inscrição de evento](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Você pode salvar a assinatura de evento aqui e receber notificações para cada dispositivo criado no seu Hub IoT. Para este tutorial, no entanto, vamos usar os campos opcionais para filtrar dispositivos específicos. Selecione **Filtros** na parte superior do painel.

6. Selecione **Adicionar novo filtro**. Preencha os campos com estes valores:

   * **Chave**: Selecione `Subject`.

   * **Operador**: Selecione `String begins with`.

   * **Valor**:  insira `devices/Building1_` para filtrar eventos de dispositivo na construção 1.
  
   Adicione outro filtro com estes valores:

   * **Chave**: Selecione `Subject`.

   * **Operador**: Selecione `String ends with`.

   * **Valor**: insira `_Temperature` para filtrar eventos de dispositivos relacionados à temperatura.

   A guia **Filtros** da sua assinatura de evento agora deve ser semelhante a esta imagem:

   ![Como adicionar filtros à assinatura de evento](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. Selecione **Criar** para salvar a assinatura de evento.

## <a name="create-a-new-device"></a>Criar um novo dispositivo

Teste seu aplicativo lógico criando um novo dispositivo para ativar um email de notificação de eventos. 

1. Do seu Hub IoT, selecione **Dispositivos IoT**. 

2. Selecione **Novo**.

3. Para**IID do dispositivo**, insira `Building1_Floor1_Room1_Light`.

4. Clique em **Salvar**. 

5. É possível adicionar vários dispositivos com diferentes IDs de dispositivo para testar os filtros de assinatura de evento. Experimente estes exemplos: 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   Se você adicionou os quatro exemplos, sua lista de dispositivos IoT deve ser parecida com a imagem a seguir:

   ![Lista de dispositivos do Hub IoT](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. Após adicionar alguns dispositivos ao seu Hub IoT, verifique seu email para consultar quais ativaram o aplicativo lógico. 

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure

Em vez de usar o Portal do Azure, é possível realizar as etapas do Hub IoT usando a CLI do Azure. Para obter detalhes, consulte as páginas da CLI do Azure para [criar uma assinatura de evento](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) e [criar um dispositivo IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial usou recursos que incorrem em encargos na sua assinatura do Azure. Quando terminar de experimentar o tutorial e testar seus resultados, desabilite ou exclua recursos que você não deseja manter. 

Para excluir todos os recursos criados neste tutorial, exclua o grupo de recursos. 

1. Selecione **Grupos de recursos** e, em seguida, selecione o grupo de recursos criado para este tutorial.

2. No painel Grupo de recursos, selecione **Excluir grupo de recursos**. Você será solicitado a inserir o nome do grupo de recursos e, em seguida, poderá excluí-lo. Todos os recursos contidos aqui também foram removidos.

Se você não quiser remover todos os recursos, poderá gerenciá-los um a um. 

Se você não quiser perder o trabalho no seu aplicativo lógico, desabilite-o em vez de excluí-lo. 

1. Navegue até seu aplicativo lógico.

2. Na folha **Visão geral**, selecione **Excluir** ou **Desabilitar**. 

Cada assinatura pode ter um Hub IoT gratuito. Se você criou um Hub gratuito para este tutorial, não será necessário excluí-lo para evitar encargos.

1. Navegue até o seu Hub IoT. 

2. Na folha **Visão geral**, selecione **Excluir**. 

Mesmo se você mantiver o Hub IoT, convém excluir a assinatura de evento que você criou. 

1. No seu Hub IoT, selecione **Grade de Eventos**.

2. Selecione a assinatura de evento que deseja remover. 

3. Selecione **Excluir**. 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Reagir aos eventos do Hub IoT usando a Grade de Eventos para disparar ações](../iot-hub/iot-hub-event-grid.md).
* [Saiba como ordenar os eventos de conexão e desconexão de dispositivo](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Saiba mais sobre o que mais você pode fazer com [Grade de Eventos](overview.md).