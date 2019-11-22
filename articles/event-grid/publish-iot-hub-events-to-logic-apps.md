---
title: 'Tutorial: Usar eventos do Hub IoT para disparar Aplicativos Lógicos do Azure'
description: 'Tutorial: Usando o serviço de roteamento de evento da Grade de Eventos do Azure, crie processos automatizados para realizar as ações nos Aplicativos Lógicos do Azure com base nos eventos do Hub IoT.'
services: iot-hub
documentationcenter: ''
author: kgremban
manager: philmea
editor: ''
ms.service: iot-hub
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: kgremban
ms.openlocfilehash: e003cb650b0589ab43c984850838c56cbbf1ff2f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106785"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-logic-apps"></a>Tutorial: Enviar notificações por email sobre os eventos do Hub IoT usando Aplicativos Lógicos

A Grade de Eventos do Azure permite que você reaja aos eventos no Hub IoT, disparando ações em seus aplicativos de negócios a jusante.

Este artigo aborda uma configuração de exemplo que usa o Hub IoT e a Grade de Eventos. No final, você terá um Aplicativo Lógico do Azure configurado para enviar um email de notificação sempre que um dispositivo for adicionado ao seu Hub IoT. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de email de qualquer provedor de email com suporte pelos Aplicativos Lógicos do Azure Apps, como Office 365 Outlook, Outlook.com ou Gmail. Essa conta de email é usada para enviar as notificações de eventos. Para obter uma lista completa de conectores de Aplicativos Lógicos com suporte, consulte a [Visão geral dos conectores](https://docs.microsoft.com/connectors/)
* Uma conta ativa do Azure. Se você não tiver uma, poderá [criar uma conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Um Hub IoT no Azure. Se ainda não criou um, consulte a [Introdução ao Hub IoT](../iot-hub/iot-hub-csharp-csharp-getstarted.md) para obter um passo a passo. 

## <a name="create-a-logic-app"></a>Criar um aplicativo lógico

Primeiro, crie um aplicativo lógico e adicione um gatilho de Grade de eventos que monitora o grupo de recursos de sua máquina virtual. 

### <a name="create-a-logic-app-resource"></a>Criar um recurso de aplicativo lógico

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Integração** > **Aplicativo Lógico**.

   ![Criar aplicativo lógico](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

2. Nomeie seu aplicativo lógico que é exclusivo na sua assinatura e, em seguida, selecione a mesma assinatura, grupo de recursos e local como o Hub IoT. 
3. Selecione **Criar**.

4. Após o recurso ser criado, navegue até seu aplicativo lógico. 

5. O Designer de Aplicativos Lógicos mostra modelos para padrões comuns para que você possa começar o quanto antes. No Designer do Aplicativo Lógico em **Modelos**, escolha **Aplicativo Lógico em branco** para que você possa criar o aplicativo lógico desde o início.

### <a name="select-a-trigger"></a>Selecionar um gatilho

Um gatilho é um evento específico que inicia o aplicativo lógico. Para este tutorial, o gatilho que desencadeia o fluxo de trabalho está recebendo uma solicitação através HTTP.  

1. Na barra de pesquisa de conectores e gatilhos, digite **HTTP**.
2. Selecione **Solicitação - Quando uma solicitação HTTP é recebida** como o gatilho. 

   ![Selecionar o gatilho de solicitação HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

3. Selecione **Use o conteúdo de amostra para gerar o esquema**. 

   ![Selecionar o gatilho de solicitação HTTP](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

4. Cole o código JSON de exemplo a seguir na caixa de texto e, em seguida, selecione **Concluído**:

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

5. É possível que você receba uma notificação pop-up informando: **Lembre-se de incluir um cabeçalho Content-Type definido como application/json na sua solicitação.** Você pode ignorar essa sugestão com segurança e passar para a próxima seção. 

### <a name="create-an-action"></a>Criar uma ação

As ações são quaisquer etapas que ocorrem após o gatilho iniciar o fluxo de trabalho do aplicativos lógico. Para este tutorial, a ação é enviar uma notificação por email do seu provedor de email. 

1. Selecione **Nova etapa**. Isso abrirá uma janela para **Escolher uma ação**.

2. Pesquisar por **Email**.

3. Com base no seu provedor de email, localize e selecione o conector correspondente. Este tutorial usa o **Office 365 Outlook**. As etapas para outros provedores de email são semelhantes. 

   ![Selecione o conector do fornecedor de email](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

4. Selecione a ação **Enviar um email**. 

5. Se solicitado, entre em sua conta de email. 

6. Compile seu modelo de email. 
   * **Para**: insira o endereço de email para receber os emails de notificação. Para este tutorial, use uma conta de email que você possa acessar para testar. 
   * **Assunto** e **Corpo**: escreva o texto de seu email. Selecione as propriedades JSON da ferramenta seletora para incluir conteúdo dinâmico com base nos dados de eventos.  

   Seu modelo de email poderá ser semelhante a este exemplo:

   ![Preencha informações de email](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

7. Salve seu aplicativo lógico. 

### <a name="copy-the-http-url"></a>Copiar a URL HTTP

Antes de sair do Designer de Aplicativos Lógicos, copie a URL que seus aplicativos lógicos estão ouvindo para um gatilho. Você usa essa URL para configurar a Grade de Eventos. 

1. Expanda a caixa de configuração do gatilho **Quando uma solicitação HTTP for recebida**, clicando nela. 
2. Copie o valor de **URL DE HTTP POST**, selecionando o botão de cópia ao lado dele. 

   ![Copiar a URL DE HTTP POST](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

3. Salve essa URL para que você possa referir-se a ela na próxima seção. 

## <a name="configure-subscription-for-iot-hub-events"></a>Configurar a assinatura de eventos do Hub IoT

Nesta seção, você configura o Hub IoT para publicar eventos à medida que ocorrem. 

1. No portal do Azure, navegue para o hub IoT. 
2. Selecione **Eventos**.

   ![Abra os detalhes da Grade de Eventos](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Selecione **Assinatura de evento**. 

   ![Criar nova assinatura de evento](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Crie a assinatura de evento com os seguintes valores: 
   * **Tipo de evento**: desmarque a opção Assinar todos os tipos de evento e selecione **Dispositivo Criado** no menu.
   * **Detalhes do Ponto de Extremidade**: Selecione o Tipo de Ponto de Extremidade como **Web hook**, clique em Selecionar ponto de extremidade, cole a URL copiada do aplicativo lógico e confirme a seleção.

     ![selecionar a url de ponto de extremidade](./media/publish-iot-hub-events-to-logic-apps/endpoint-url.png)

   * **Detalhes da Assinatura de Evento**: forneça um nome descritivo e selecione **Esquema da Grade de Eventos**

   Quando terminar, o formulário deverá ser o seguinte exemplo: 

    ![Exemplo de formulário de inscrição de evento](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Você pode salvar a assinatura de evento aqui e receber notificações para cada dispositivo criado no seu Hub IoT. Para este tutorial, no entanto, vamos usar os campos opcionais para filtrar dispositivos específicos. Selecione **Recursos Adicionais** na parte superior do formulário. 

6. Crie os seguintes filtros:

   * **Entidade Começa Com**: insira `devices/Building1_` para filtrar eventos de dispositivo na construção 1.
   * **Entidade Termina Com**: insira `_Temperature` para filtrar eventos de dispositivos relacionados à temperatura.

5. Selecione **Criar** para salvar a assinatura de evento.

## <a name="create-a-new-device"></a>Criar um novo dispositivo

Teste seu aplicativo lógico criando um novo dispositivo para ativar um email de notificação de eventos. 

1. Do seu Hub IoT, selecione **Dispositivos IoT**. 
2. Selecione **Adicionar**.
3. Para**IID do dispositivo**, insira `Building1_Floor1_Room1_Temperature`.
4. Clique em **Salvar**. 
5. É possível adicionar vários dispositivos com diferentes IDs de dispositivo para testar os filtros de assinatura de evento. Experimente estes exemplos: 
   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

Após adicionar alguns dispositivos ao seu Hub IoT, verifique seu email para consultar quais ativaram o aplicativo lógico. 

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure

Em vez de usar o Portal do Azure, é possível realizar as etapas do Hub IoT usando a CLI do Azure. Para obter detalhes, consulte as páginas da CLI do Azure para [criar uma assinatura de evento](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) e [criar um dispositivo IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity)

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial usou recursos que incorrem em encargos na sua assinatura do Azure. Quando terminar de experimentar o tutorial e testar seus resultados, desabilite ou exclua recursos que você não deseja manter. 

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


