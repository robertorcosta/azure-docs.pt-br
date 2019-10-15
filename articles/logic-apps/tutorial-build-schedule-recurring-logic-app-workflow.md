---
title: Criar fluxos de trabalho automáticos com base em agendamento – Aplicativos Lógicos do Azure
description: Tutorial – Criar um fluxo de trabalho automático com base em agendamento e recorrente usando Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: eae2319e8d1c162969a04f8dafa18eec671ee1d0
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034664"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>Tutorial: Criar fluxos de trabalho automatizados, recorrentes e com base em agendamento usando Aplicativos Lógicos do Azure

Este tutorial mostra como criar um [aplicativo lógico](../logic-apps/logic-apps-overview.md) e automatizar um fluxo de trabalho recorrente executado em um agendamento. Especificamente, este aplicativo lógico de exemplo é executado todas as manhãs dos dias da semana e verifica o tempo de viagem, incluindo o tráfego, entre dois locais. Se o tempo excede um limite específico, o aplicativo lógico envia email com o tempo de viagem e o tempo extra necessário para seu destino.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um aplicativo lógico em branco.
> * Adicionar um gatilho Recorrência que especifica a agenda para seu aplicativo lógico.
> * Adicionar uma ação de Mapas do Bing que obtém o tempo de viagem para uma rota.
> * Adicionar uma ação que cria uma variável, converte o tempo de viagem de segundos a minutos e armazena o resultado na variável.
> * Adicionar uma condição que compara o tempo de viagem em relação a um limite especificado.
> * Adicionar uma ação que enviará um email a você se o tempo de viagem exceder o limite.

Quando terminar, o aplicativo lógico ficará parecido com este fluxo de trabalho em alto nível:

![Visão geral do fluxo de trabalho de aplicativo lógico de alto nível](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você não tenha uma assinatura, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

* Uma conta de email de qualquer provedor de email compatível com os Aplicativos Lógicos, como o Outlook do Office 365, o Outlook.com ou o Gmail. Para outros provedores, [revise a lista de conectores aqui](https://docs.microsoft.com/connectors/). Este início rápido usa uma conta do Office 365 Outlook. Se você usar uma conta de email diferente, as etapas gerais serão as mesmas, mas a interface do usuário será ligeiramente diferente.

* Para obter o tempo de viagem de uma rota, você precisará de uma chave de acesso para a API do Bing Mapas. Para obter essa chave, siga as etapas para saber [como obter uma chave do Bing Mapas](https://docs.microsoft.com/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com) com suas credenciais da conta do Azure.

## <a name="create-your-logic-app"></a>Criar seu aplicativo lógico

1. No menu principal do Azure, selecione **Criar um recurso** > **Integração** > **Aplicativo Lógico**.

   ![Criar seu recurso de aplicativo lógico](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. Em **Criar aplicativo lógico**, forneça essas informações sobre seu aplicativo lógico, como mostrado e descrito. Quando terminar, selecione **Criar**.

   ![Fornecer informações sobre seu aplicativo lógico](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Propriedade | Valor | DESCRIÇÃO |
   |----------|-------|-------------|
   | **Nome** | LA-TravelTime | O nome do seu aplicativo lógico, que pode conter apenas letras, números, hifens (`-`), sublinhados (`_`), parênteses (`(`, `)`) e pontos (`.`). Este exemplo usa o “LA-TravelTime”. |
   | **Assinatura** | <*nome-da-sua-assinatura-do-Azure*> | Seu nome da assinatura do Azure |
   | **Grupo de recursos** | LA-TravelTime-RG | O nome do [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md), usado para organizar os recursos relacionados. Este exemplo usa "LA-TravelTime-RG”. |
   | **Localidade** | Oeste dos EUA | A região em que as informações de seu aplicativo lógico são armazenadas. Este exemplo usa "Leste dos EUA". |
   | **Log Analytics** | Desativar | Mantenha a configuração **Desligado** para o log de diagnósticos. |
   ||||

1. Depois que o Azure implanta seu aplicativo, na barra de ferramentas do Azure, selecione **Notificações** > **Ir para o recurso** para seu aplicativo lógico implantado.

   ![Ir para o novo recurso de aplicativo lógico](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   Ou você pode encontrar e selecionar seu aplicativo lógico digitando o nome na caixa de pesquisa.

   O Designer de Aplicativos Lógicos é aberto e mostra uma página com um vídeo de introdução e os gatilhos e padrões de aplicativo lógico normalmente usados. Em **Modelos**, selecione **Aplicativo lógico em branco**.

   ![Selecione o modelo de aplicativo lógico em branco](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Em seguida, adicione o [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) Recorrência, disparado com base em uma agenda especificada. Cada aplicativo lógico deve começar com um gatilho, que é disparado quando um evento específico ocorre ou quando novos dados atendem a uma condição específica. Para saber mais, confira [Criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Adicionar gatilho Recorrência

1. No Designer de Aplicativos Lógicos na caixa de pesquisa, insira "recorrência" como filtro. Na lista **Gatilhos**, selecione o gatilho **Recorrência**.

   ![Adicionar gatilho "Recorrência"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Na forma **Recorrência**, selecione o botão de **reticências** ( **...** ) e, em seguida, **Renomear**. Renomeie o gatilho com esta descrição:`Check travel time every weekday morning`

   ![Renomear a descrição do Gatilho de recorrência](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Dentro do gatilho, altere estas propriedades.

   ![Alterar o intervalo e a frequência do Gatilho de recorrência](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Propriedade | Obrigatório | Value | DESCRIÇÃO |
   |----------|----------|-------|-------------|
   | **Intervalo** | Sim | 1 | O número de intervalos de espera entre as verificações |
   | **Frequência** | Sim | Semana | A unidade de tempo a ser usada para a recorrência |
   |||||

1. Em **Intervalo** e **frequência**, abra a lista **Adicionar novo parâmetro** e selecione essas propriedades para adicionar ao gatilho.

   * **Nestes dias**
   * **A estas horas**
   * **A estes minutos**

   ![Adicionar propriedades para o Gatilho de recorrência](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Agora defina os valores das propriedades adicionais, conforme mostrado e descrito aqui.

   ![Fornecer detalhes de agenda e recorrência](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Propriedade | Valor | DESCRIÇÃO |
   |----------|-------|-------------|
   | **Nestes dias** | Segunda-feira, terça-feira, quarta-feira, quinta-feira, sexta-feira | Disponível apenas quando **Frequência** está definido como “Semana” |
   | **A estas horas** | 7,8,9 | Disponível apenas quando **Frequência** está definido como "Semana" ou "Dia". Selecione as horas do dia para executar essa recorrência. Este exemplo é executado nas marcas de horas 7, 8 e 9. |
   | **A estes minutos** | 0,15,30,45 | Disponível apenas quando **Frequência** está definido como "Semana" ou "Dia". Selecione os minutos do dia para executar essa recorrência. Este exemplo é executado a cada 15 minutos, começando na marca da hora zero. |
   ||||

   Esse gatilho é acionado todos os dias da semana, a cada 15 minutos, começando às 7h e terminando às 9h45. A caixa **Visualização** mostra a agenda de recorrência. Para saber mais, confira [Agendar tarefas e fluxos de trabalho](../connectors/connectors-native-recurrence.md) e [Gatilhos e ações de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Por enquanto, para ocultar os detalhes do disparador, clique na barra de título da forma.

   ![Recolher forma para ocultar detalhes](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Seu aplicativo lógico agora está ativo, mas ele não faz nada além de repetir. Portanto, adicione uma ação que responde quando o gatilho é acionado.

## <a name="get-the-travel-time-for-a-route"></a>Obter o tempo de viagem para uma rota

Agora que você tem um gatilho, adicione uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) que obtém o tempo de viagem entre dois lugares. Os Aplicativos Lógicos fornecem um conector para a API do Bing Mapas para que você possa obter essas informações facilmente. Antes de começar essa tarefa, verifique se você tem uma chave de API do Bing Mapas conforme descrito nos pré-requisitos deste tutorial.

1. No Designer de Aplicativos Lógicos, em seu gatilho, selecione **Nova etapa**.

1. Em **Escolha uma ação**, selecione **Padrão**. Na caixa de pesquisa, insira “mapas do Bing” como seu filtro e selecione a ação **Obter rota**.

   ![Selecionar a ação "Obter rota"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Se você não tiver uma conexão do Bing Mapas, a criação será solicitada. Forneça esses detalhes de conexão e selecione **Criar**.

   ![Criar conexão com a API do Bing Mapas](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Propriedade | Obrigatório | Value | DESCRIÇÃO |
   |----------|----------|-------|-------------|
   | **Nome da Conexão** | Sim | BingMapsConnection | Forneça um nome para a conexão. Este exemplo usa “BingMapsConnection”. |
   | **Chave de API** | Sim | <*your-Bing-Maps-key*> | Insira a chave do Bing Mapas que você recebeu anteriormente. Se você não tiver uma chave do Bing Mapas, saiba [como obter uma chave](https://msdn.microsoft.com/library/ff428642.aspx). |
   |||||

1. Renomeie a ação com esta descrição:`Get route and travel time with traffic`

1. Dentro dessa ação, abra a **lista Adicionar novo parâmetro** e selecione essas propriedades a serem adicionadas à ação.

   * **Otimizar**
   * **Unidades de distância**
   * **Modo de navegação**

   ![Adicionar propriedades à ação "Obter rota"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Agora defina os valores para as propriedades da ação, conforme mostrado e descrito aqui.

   ![Fornecer detalhes para a ação “Obter rota”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Propriedade | Obrigatório | Value | DESCRIÇÃO |
   |----------|----------|-------|-------------|
   | **Localizador 1** | Sim | <*start-location*> | Origem da rota |
   | **Localizador 2** | Sim | <*end-location*> | Destino da rota |
   | **Otimizar** | Não | timeWithTraffic | Um parâmetro para otimizar a rota, como distância, viagem de tempo com tráfego atual e assim por diante. Selecione o parâmetro “timeWithTraffic”. |
   | **Unidades de distância** | Não | <*your-preference*> | A unidade de distância da rota. Esse exemplo usa “Milha” como a unidade. |
   | **Modo de navegação** | Não | Automóvel | O modo de viagem da rota. Selecione o modo “Condução”. |
   ||||

   Para saber mais sobre esses parâmetros, consulte [Calcular uma rota](https://docs.microsoft.com/bingmaps/rest-services/routes/calculate-a-route).

1. Salve seu aplicativo lógico.

Em seguida, crie uma variável para que você possa converter e armazenar o tempo de viagem atual como minutos em vez de segundos. Dessa forma, você pode evitar repetir a conversão e usar o valor mais facilmente em etapas posteriores. 

## <a name="create-a-variable-to-store-travel-time"></a>Criar uma variável para armazenar o tempo de viagem

Às vezes, convém executar operações em dados no fluxo de trabalho e, em seguida, usar os resultados em ações posteriores. Para salvar esses resultados e poder reutilizar ou fazer referência a eles facilmente, você pode criar variáveis para armazenar os resultados depois de processá-los. Você pode criar variáveis somente no nível superior no aplicativo lógico.

Por padrão, a ação **Obter rota** anterior retorna o tempo de viagem atual com tráfego em segundos da propriedade **Tráfego de Duração da Viagem**. Ao converter e armazenar esse valor como minutos, você facilita a reutilização do valor posteriormente sem convertê-lo novamente.

1. Na ação **Obter rota**, selecione **Nova etapa**.

1. Em **Escolha uma ação**, selecione **Interno**. Na caixa de pesquisa, insira "variáveis" e selecione a ação **Inicializar variável**.

   ![Selecionar a ação “Inicializar variável”](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Renomear a ação com esta descrição:`Create variable to store travel time`

1. Forneça os detalhes para a variável conforme descrito aqui:

   | Propriedade | Obrigatório | Value | DESCRIÇÃO |
   |----------|----------|-------|-------------|
   | **Nome** | Sim | travelTime | O nome da sua variável. Este exemplo usa “travelTime”. |
   | **Tipo** | Sim | Número inteiro | O tipo de dados da variável |
   | **Valor** | Não| Uma expressão que converte o tempo de viagem atual de segundos em minutos (consulte as etapas nesta tabela). | O valor inicial da variável |
   ||||

   1. Para criar a expressão da propriedade **Valor**, clique na caixa para que seja exibida a lista de conteúdo dinâmico. Se necessário, amplie seu navegador até que a lista seja exibida. Na lista de conteúdo dinâmico, selecione **Expressão**.

      ![Fornecer informações para a ação "Inicializar variável"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Quando você clica dentro de algumas caixas de edição, a lista de conteúdo dinâmico é exibida. A lista mostra as propriedades de ações anteriores que podem ser usadas como entradas em seu fluxo de trabalho. A lista de conteúdo dinâmico tem um editor de expressão em que você pode selecionar as funções para executar operações. Este editor de expressão aparece apenas na lista de conteúdo dinâmico.

   1. No editor de expressão, digite esta expressão:`div(,60)`

      ![Digite esta expressão: "div(,60)"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Coloque o cursor dentro da expressão entre o parêntese esquerdo ( **(** ) e a vírgula ( **,** ). 
   selecione **Conteúdo dinâmico**.

      ![Posicione o cursor, selecione "Conteúdo dinâmico"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Na lista de conteúdo dinâmico, selecione **Tráfego de Duração da Viagem**.

      ![Selecione a propriedade "Tráfego de Duração da Viagem"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Depois que o valor da propriedade for resolvido dentro da expressão, selecione **OK**.

      ![Para concluir a criação da expressão, selecione "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      A propriedade **Valor** agora é exibida conforme mostrado aqui:

      ![A propriedade "Value" aparece com a expressão resolvida](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Salve seu aplicativo lógico.

Em seguida, adicione uma condição que verifica se o tempo de viagem atual é maior que um limite específico.

## <a name="compare-the-travel-time-with-limit"></a>Comparar o tempo de viagem com o limite

1. Na ação anterior, selecione **Nova etapa**.

1. Em **Escolha uma ação**, selecione **Interno**. Na caixa de pesquisa, insira "condição" como o filtro. Na lista de ações, selecione a ação **Condição**.

   ![Selecione a ação "Condição"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Renomeie a condição com esta descrição:`If travel time exceeds limit`

1. Crie uma condição que verifica se o valor da propriedade **travelTime** excede o limite especificado, como descrito e mostrado aqui:

   1. Na condição, clique dentro da caixa **Escolher um valor** no lado esquerdo da condição.

   1. Na lista de conteúdo dinâmico exibida, em **Variáveis**, selecione a propriedade **travelTime**.

      ![Criar o lado esquerdo da condição](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. Na caixa de comparação intermediária, selecione o operador **é maior que**.

   1. Na caixa **Escolha um valor** à direita da condição, insira este limite: `15`

      Quando terminar, a condição será semelhante a este exemplo:

      ![Condição concluída para verificar o tempo de viagem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Salve seu aplicativo lógico.

Em seguida, adicione a ação a ser executada quando o tempo de viagem excede seu limite.

## <a name="send-email-when-limit-exceeded"></a>Enviar email quando o limite é excedido

Agora, adicione uma ação que envia email quando o tempo de viagem excede seu limite. Este email inclui o tempo de viagem atual e o tempo extra necessário para concluir a rota especificada.

1. No branch **Se verdadeiro** da condição, selecione **Adicionar uma ação**.

1. Em **Escolha uma ação**, selecione **Padrão**. Na caixa de pesquisa, digite "enviar email". A lista retorna muitos resultados, portanto, primeiro selecione o conector de email desejado, por exemplo:

   ![Selecionar o conector de email que você deseja](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Para as contas corporativas ou de estudante do Azure, selecione o **Outlook do Office 365**.
   * Para contas pessoais da Microsoft, selecione **Outlook.com**.

1. Quando as ações do conector forem exibidas, selecione "enviar ação de email" que você deseja usar, por exemplo:

   ![Selecione a ação "enviar email"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Se você ainda não tiver uma conexão, será solicitado a entrar na sua conta de email.

   Os Aplicativos Lógicos criam uma conexão à sua conta de email.

1. Renomeie a ação com esta descrição:`Send email with travel time`

1. Na caixa **Para**, insira o endereço de email do destinatário. Para fins de teste, use o seu endereço de email.

1. Na caixa **Subject**, especifique o assunto do email e inclua a variável **travelTime**.

   1. Insira o texto `Current travel time (minutes):` com um espaço à direita. 

   1. Na lista de conteúdo dinâmico, em **Variáveis**, selecione **Ver mais**.

      ![Localizar a variável "travelTime"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. Depois que **travelTime** for exibido em **Variáveis**, selecione **travelTime**.

      ![Insira o texto do assunto e uma expressão que retorna o tempo de viagem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Na caixa **Body**, especifique o conteúdo do corpo do email.

   1. Insira o texto `Add extra travel time (minutes):` com um espaço à direita.

   1. Na lista de conteúdo dinâmico, selecione **Expressão**.

      ![Criar expressão para o corpo do email](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. No editor de expressão, digite esta expressão para que você possa calcular o número de minutos que excedem o limite:```sub(,15)```

      ![Inserir a expressão para calcular o adicional de tempo de viagem em minutos](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Coloque o cursor dentro da expressão entre o parêntese esquerdo ( **(** ) e a vírgula ( **,** ). Selecione **Conteúdo dinâmico**.

      ![Continuar a criar a expressão para calcular o adicional de tempo de viagem em minutos](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. Em **Variáveis**, selecione **travelTime**.

      ![Selecione a propriedade "travelTime" a ser usada na expressão](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Depois que a propriedade for resolvida dentro da expressão, selecione **OK**.

      ![Depois que a propriedade "Body" for resolvida, selecione "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      A propriedade **Body** agora é exibida conforme mostrado aqui:

      ![Propriedade "Body" resolvida na expressão](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Salve seu aplicativo lógico.

Em seguida, teste seu aplicativo lógico, que agora se parece com este exemplo:

![Exemplo de fluxo de trabalho do aplicativo lógico concluído](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Executar seu aplicativo lógico

Para executar manualmente o aplicativo lógico, selecione **Executar** na barra de ferramentas do designer.

* Se o tempo de viagem atual permanecer abaixo do limite, o aplicativo lógico não fará nada e aguardará o próximo intervalo antes de verificar novamente. 

* Se o tempo de viagem atual exceder o limite, você receberá um email com o tempo de viagem atual e o número de minutos acima do limite. Aqui está um email de exemplo que seu aplicativo lógico envia:

![Exemplo de email enviado que mostra o tempo de viagem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

Se você não receber nenhum email, verifique a pasta de Lixo eletrônico. O filtro de lixo de email pode redirecionar esses tipos de mensagens. Caso contrário, se você não tiver certeza de que seu aplicativo lógico foi executado corretamente, confira [Solução de problemas do aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md).

Parabéns, você agora criou e executou um aplicativo lógico recorrente com base em agendamento. 

Para criar outros aplicativos lógicos que usam o gatilho **Recorrência**, confira estes modelos, que ficam disponíveis depois que você cria um aplicativo lógico:

* Receba lembretes diários.
* Exclua blobs do Azure mais antigos.
* Adicione uma mensagem a uma fila do Armazenamento do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais do aplicativo lógico de exemplo, exclua o grupo de recursos que contém o aplicativo lógico e os recursos relacionados. 

1. No menu principal do Azure, vá para **Grupos de recursos** e selecione o grupo de recursos do seu aplicativo lógico.

1. No menu do grupo de recursos, selecione **Visão Geral** > **Excluir grupo de recursos**. 

   ![“Visão Geral” > “Excluir grupo de recursos”.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Insira o nome do grupo de recursos como confirmação e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um aplicativo lógico que verifica o tráfego com base em um agendamento especificado (nas manhãs dos dias da semana) e executa uma ação (envia email) quando o tempo de viagem excede um limite especificado. Agora, saiba como criar um aplicativo lógico que envia solicitações de lista de endereçamento para aprovação integrando serviços do Azure, serviços da Microsoft e outros aplicativos SaaS.

> [!div class="nextstepaction"]
> [Gerenciar solicitações da lista de correspondência](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
