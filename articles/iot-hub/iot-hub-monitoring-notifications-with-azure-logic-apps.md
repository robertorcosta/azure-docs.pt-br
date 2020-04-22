---
title: Monitoramento remoto de IoT e notificações com os Aplicativos Lógicos do Azure | Microsoft Docs
description: Use os Aplicativos Lógicos do Azure para monitoramento de temperatura de IoT em seu hub IoT e envio automático de notificações por email à sua caixa de correio sobre quaisquer anomalias detectadas.
author: robinsh
keywords: monitoramento de iot, notificações de iot, monitoramento de temperatura de iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: 2720f9acfa308294b30f9203ba80e3f9b426e1e9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680712"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Monitoramento remoto IoT e notificações com os Aplicativos Lógicos do Azure conectando o hub IoT e a caixa de correio

![Diagrama de ponta a ponta](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Os Aplicativos de Lógica do Azure](https://docs.microsoft.com/azure/logic-apps/) podem ajudá-lo a orquestrar fluxos de trabalho em locais e serviços em nuvem, uma ou mais empresas e em vários protocolos. Um aplicativo lógico começa com um gatilho, que é seguido por uma ou mais ações que podem ser sequenciadas usando controles incorporados, como condições e iterizadores. Essa flexibilidade faz da Logic Apps uma solução ideal de IoT para cenários de monitoramento de IoT. Por exemplo, a chegada de dados de telemetria de um dispositivo em um ponto final do IoT Hub pode iniciar fluxos de trabalho de aplicativos lógicos para armazenar os dados em uma bolha de armazenamento Azure, enviar alertas de e-mail para alertar sobre anomalias de dados, agendar uma visita técnica se um dispositivo relatar uma falha e assim por diante.

## <a name="what-you-learn"></a>O que você aprenderá

Aprenda a criar um aplicativo lógico que conecta o hub IoT e a caixa de correio para monitoramento e notificações de temperatura.

O código do cliente em execução `temperatureAlert`em seu dispositivo define uma propriedade de aplicativo, em cada mensagem de telemetria que ele envia para o seu hub de IoT. Quando o código do cliente detecta uma temperatura acima `true`de 30 C, ele define esta propriedade como ; caso contrário, ele define `false`a propriedade para .

As mensagens que chegam ao seu hub de IoT são semelhantes às seguintes, com os dados de telemetria contidos no corpo e a `temperatureAlert` propriedade contida nas propriedades do aplicativo (as propriedades do sistema não são mostradas):

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

Para saber mais sobre o formato de mensagem do IoT Hub, consulte [Criar e ler mensagens do IoT Hub](iot-hub-devguide-messages-construct.md).

Neste tópico, você configura o roteamento em seu hub de `temperatureAlert` IoT para enviar mensagens nas quais a propriedade está `true` para um ponto final do Service Bus. Em seguida, você configura um aplicativo lógico que aciona as mensagens que chegam ao ponto final do Ônibus de Serviço e envia uma notificação por e-mail.

## <a name="what-you-do"></a>O que fazer

* Crie um namespace de barramento de serviço e adicione uma fila de Ônibus de Serviço a ele.
* Adicione um ponto final personalizado e uma regra de roteamento ao seu hub de IoT para encaminhar mensagens que contenham um alerta de temperatura para a fila do Ônibus de Serviço.
* Crie, configure e teste um aplicativo lógico para consumir mensagens da fila do Ônibus de Serviço e envie e-mails de notificação para um destinatário desejado.

## <a name="what-you-need"></a>O que você precisa

* Complete o tutorial [do simulador online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo; por exemplo, [Raspberry Pi com nó.js](iot-hub-raspberry-pi-kit-node-get-started.md). Estes abrangem os seguintes requisitos:

  * Uma assinatura ativa do Azure.
  * Um hub IoT do Azure em sua assinatura.
  * Um aplicativo cliente em execução em seu dispositivo que envia mensagens de telemetria para o seu hub Azure IoT.

## <a name="create-service-bus-namespace-and-queue"></a>Criar espaço de nome e fila do Service Bus

Criar um namespace do Barramento de Serviço e da fila. Mais tarde, neste tópico, você cria uma regra de roteamento em seu hub de IoT para direcionar mensagens que contêm um alerta de temperatura para a fila do Ônibus de Serviço, onde elas serão pegas por um aplicativo lógico e acioná-la para enviar um e-mail de notificação.

### <a name="create-a-service-bus-namespace"></a>Criar um namespace do Barramento de Serviço

1. No [portal Azure,](https://portal.azure.com/)selecione **+ Crie um barramento** > de serviço**de integração** > **de recursos.**

1. No **painel Criar namespace,** forneça as seguintes informações:

   **Nome**: O nome do nome do ônibus de serviço. O namespace deve ser único em todo o Azure.

   **Nível de preços :** Selecione **Básico** na lista de baixa. A camada básica é suficiente para este tutorial.

   **Grupo de recursos**: Use o mesmo grupo de recursos que seu hub de IoT usa.

   **Local**: use o mesmo local que o hub IoT usa.

   ![Criar um namespace de barramento de serviço no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Selecione **Criar**. Aguarde que a implantação seja concluída antes de passar para a próxima etapa.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Adicione uma fila de barramento de serviço ao namespace

1. Abra o espaço de nome do Service Bus. A maneira mais fácil de chegar ao espaço de nome do Ônibus de Serviço é selecionar grupos de **recursos** no painel de recursos, selecionar seu grupo de recursos e selecionar o namespace do Ponto de Serviço na lista de recursos.

1. No painel Namespace do ponto de **ônibus de serviço,** selecione **+ Fila**.

1. Digite um nome para a fila e, em seguida, **selecione Criar**. Quando a fila foi criada com sucesso, o painel **Criar fila** fecha.

   ![Adicionar uma fila do barramento de serviço ao portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. De volta ao painel **Namespace do ponto** de serviço, em **Entidades,** selecione **Filas**. Abra a fila do Service Bus da lista e selecione **políticas** > de acesso compartilhado **+ Adicionar**.

1. Digite um nome para a diretiva, verifique **Gerenciar**e, em seguida, selecione **Criar**.

   ![Adicione uma política de fila de barramento de serviço no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Adicione um ponto final personalizado e uma regra de roteamento ao seu hub de IoT

Adicione um ponto final personalizado para a fila de Barra de Serviço ao seu hub de IoT e crie uma regra de roteamento de mensagens para direcionar mensagens que contenham um alerta de temperatura para esse ponto final, onde serão captadas pelo seu aplicativo lógico. A regra de roteamento usa `temperatureAlert = "true"`uma consulta de roteamento, `temperatureAlert` para encaminhar mensagens com base no valor da propriedade do aplicativo definido pelo código do cliente em execução no dispositivo. Para saber mais, consulte [consulta de roteamento de mensagens com base nas propriedades da mensagem](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Adicione um ponto final personalizado

1. Abrir seu Hub IoT. A maneira mais fácil de chegar ao hub de IoT é selecionar grupos de **recursos** no painel de recursos, selecionar seu grupo de recursos e selecionar seu hub de IoT na lista de recursos.

1. Em **Mensagens,** selecione **Roteamento de mensagens**. No painel **de roteamento de mensagem,** selecione a guia **Pontos finais personalizados** e, em seguida, selecione **+ Adicionar**. Na lista de paradas, selecione Fila de **barramento de serviço**.

   ![Adicionar um ponto de extremidade ao hub IoT no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. No painel Adicionar um ponto **final de ponto de ônibus de serviço,** digite as seguintes informações:

   **Nome do ponto final**: O nome do ponto final.

   **Espaço de nome do barramento de serviço**: Selecione o namespace que você criou.

   **Fila de ônibus de serviço**: Selecione a fila que você criou.

   ![Adicionar um ponto de extremidade ao hub IoT no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Selecione **Criar**. Depois que o ponto final for criado com sucesso, prossiga para o próximo passo.

### <a name="add-a-routing-rule"></a>Adicionar uma regra de roteamento

1. De volta ao painel **de roteamento de mensagens,** selecione a guia **Rotas** e selecione **+ Adicione**.

1. No **Painel Adicionar um** painel de rota, digite as seguintes informações:

   **Nome**: o nome da regra de roteamento.

   **Ponto de extremidade**: selecione o ponto de extremidade que você criou.

   **Fonte de dados**: Selecione **Mensagens de telemetria do dispositivo**.

   **Consulta de roteamento**: Enter `temperatureAlert = "true"`.

   ![Adicionar uma regra de roteamento no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Clique em **Salvar**. Você pode fechar o painel **de roteamento de mensagens.**

## <a name="create-and-configure-a-logic-app"></a>Criar e configurar um aplicativo lógico

Na seção anterior, você configura seu hub de IoT para encaminhar mensagens contendo um alerta de temperatura para sua fila de Ônibus de Serviço. Agora, você configura um aplicativo lógico para monitorar a fila do Ônibus de Serviço e enviar uma notificação de e-mail sempre que uma mensagem é adicionada à fila.

### <a name="create-a-logic-app"></a>Criar um aplicativo lógico

1. Selecione **Criar um aplicativo** > de lógica**de integração** > **de recursos**.

1. Insira as seguintes informações:

   **Nome**: o nome do aplicativo lógico.

   **Grupo de recursos**: Use o mesmo grupo de recursos que seu hub de IoT usa.

   **Local**: use o mesmo local que o hub IoT usa.

   ![Crie um aplicativo lógico no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Selecione **Criar**.

### <a name="configure-the-logic-app-trigger"></a>Configure o gatilho do aplicativo lógico

1. Abra o aplicativo lógico. A maneira mais fácil de chegar ao aplicativo lógico é selecionar grupos de **recursos** no painel de recursos, selecionar seu grupo de recursos e selecionar seu aplicativo lógico na lista de recursos. Quando você seleciona o aplicativo lógico, o Logic Apps Designer é aberto.

1. No Logic Apps Designer, desça até **Templates** e selecione **Blank Logic App**.

   ![Iniciar com um aplicativo lógico em branco no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Selecione a guia **All** e selecione **Service Bus**.

   ![Selecionar o barramento de serviço para começar a criar o aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Em **Triggers,** **selecione Quando uma ou mais mensagens chegarem em uma fila (auto-completada)**.

   ![Selecione o gatilho para o seu aplicativo lógico no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Crie uma conexão do barramento de serviço.
   1. Digite um nome de conexão e selecione o espaço de nome do busto de serviço na lista. A próxima tela abre.

      ![Criar uma conexão do barramento de serviço para o aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Selecione a política de barramento de serviço (RootManageSharedAccessKey). Em seguida, **selecione Criar**.

      ![Criar uma conexão do barramento de serviço para o aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Na tela final, para **nome de Fila,** selecione a fila que você criou a partir do drop-down. Digite `175` para **contagem máxima de mensagens**.

      ![Especificar a contagem máxima de mensagens para a conexão do barramento de serviço no aplicativo lógico](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Selecione **Salvar** no menu na parte superior do Logic Apps Designer para salvar suas alterações.

### <a name="configure-the-logic-app-action"></a>Configure a ação lógica do aplicativo

1. Crie uma conexão de serviço SMTP.

   1. Selecione **Nova etapa**. Em **Escolher uma ação,** selecione a guia **Tudo.**

   1. Digite `smtp` a caixa de pesquisa, selecione o serviço **SMTP** no resultado da pesquisa e, em seguida, selecione **Enviar e-mail**.

      ![Criar uma conexão SMTP em seu aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Digite as informações sMTP para sua caixa de correio e, em seguida, selecione **Criar**.

      ![Inserir informações de conexão SMTP em seu aplicativo lógico no portal do Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Obtenha as informações de SMTP para [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) e [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Talvez seja necessário desativar o TLS/SSL para estabelecer a conexão. Se este for o caso e você quiser reativar o TLS depois que a conexão for estabelecida, consulte a etapa opcional no final desta seção.

   1. A partir do **novo parâmetro** de isto na etapa **Enviar e-mail,** selecione **De**, Para , **Assunto**e **Corpo**. **Subject** Clique ou toque em qualquer lugar da tela para fechar a caixa de seleção.

      ![Escolha os campos de e-mail de conexão SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Insira seu endereço de email para **De** e **Para**, e `High temperature detected` para **Assunto** e **Corpo**. Se o **Adicionar conteúdo dinâmico dos aplicativos e conectores usados nesta** caixa de diálogo de fluxo for aberto, selecione **Ocultar** para fechá-lo. Você não usa conteúdo dinâmico neste tutorial.

      ![Preenchimento dos campos de e-mail de conexão SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Selecione **Salvar** para salvar a conexão SMTP.

1. (Opcional) Se você teve que desativar o TLS para estabelecer uma conexão com seu provedor de e-mail e deseja reativá-lo, siga estas etapas:

   1. No painel do **aplicativo Logic,** em **Ferramentas de Desenvolvimento,** selecione **conexões de API**.

   1. Na lista de conexões de API, selecione a conexão SMTP.

   1. No painel **de conexão SMTP API,** em **Geral,** selecione **Editar conexão API**.

   1. No painel **Editar conexão aPI,** **selecione Ativar SSL?**, digite novamente a senha da sua conta de e-mail e selecione **Salvar**.

      ![Editar conexão API SMTP em seu aplicativo lógico no portal Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Seu aplicativo lógico está pronto para processar alertas de temperatura da fila do Ônibus de Serviço e enviar notificações para sua conta de e-mail.

## <a name="test-the-logic-app"></a>Testar o aplicativo lógico

1. Inicie a aplicação do cliente em seu dispositivo.

1. Se você estiver usando um dispositivo físico, leve cuidadosamente uma fonte de calor perto do sensor de calor até que a temperatura exceda 30 graus C. Se você estiver usando o simulador online, o código do cliente produzirá aleatoriamente mensagens de telemetria que excedam 30 C.

1. Você deve começar a receber notificações de e-mail enviadas pelo aplicativo lógico.

   > [!NOTE]
   > O provedor de serviços de email talvez precise verificar a identidade do remetente para garantir que é você que envia o email.

## <a name="next-steps"></a>Próximas etapas

Você criou com êxito um aplicativo lógico que conecta o hub IoT e sua caixa de correio para monitoramento e notificações de temperatura.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
