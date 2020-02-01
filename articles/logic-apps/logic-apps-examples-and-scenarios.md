---
title: Exemplos & cenários comuns
description: Encontre exemplos, cenários comuns, tutoriais e orientações para aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 5b72ee02c2bbf811293a2bcdb15590e16e300a02
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906678"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Cenários comuns, exemplos, tutoriais e instruções passo a passo para os Aplicativos Lógicos do Azure

Os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) ajudam a orquestrar e integrar serviços diferentes fornecendo [centenas de conectores prontos para uso](../connectors/apis-list.md), desde o SQL Server local ou o SAP para serviços cognitivas do Azure. O serviço de Aplicativos Lógicos é "sem servidor", portanto, você não precisa se preocupar sobre escala ou instâncias. Tudo o que você precisa fazer é definir o fluxo de trabalho com um gatilho e as ações que o fluxo de trabalho executa. A plataforma subjacente lida com a escala, disponibilidade e desempenho. Os Aplicativos Lógicos são especialmente úteis para casos de uso e cenários em que você precise coordenar várias ações em diversos sistemas.

Para ajudá-lo a saber mais sobre os vários padrões e recursos aos quais o aplicativo lógico do Azure dá suporte, aqui estão exemplos e cenários comuns.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Pontos de partida populares para fluxos de trabalho de aplicativo lógico

Cada aplicativo lógico começa com um [*gatilho*](../logic-apps/logic-apps-overview.md#logic-app-concepts) e apenas um gatilho, que inicia o fluxo de trabalho do aplicativo lógico e passa todos os dados como parte do que o gatilho. Alguns conectores fornecem gatilhos, que são fornecidos nestes tipos:

* *Gatilhos de sondagem*: verificam regularmente um ponto de extremidade de serviço para novos dados. Quando novos dados existirem, o gatilho cria e executa uma nova instância de fluxo de trabalho com os dados como entrada.

* *Gatilhos de envio por push*: escutam dados em um ponto de extremidade de serviço e aguardam até que ocorra um evento específico. Quando o evento ocorrer, o gatilho é acionado imediatamente, criando e executando uma nova instância de fluxo de trabalho que usa todos os dados disponíveis como entrada.

Aqui estão alguns exemplos de gatilho populares:

* Sondagem:

  * O gatilho de [ **recorrência** ](../connectors/connectors-native-recurrence.md) permite que você defina a data e a hora de início mais a recorrência para disparar seu aplicativo lógico. Por exemplo, você pode selecionar os dias da semana e horas do dia para disparar seu aplicativo lógico. Para saber mais, consulte esses tópicos:

    * [Agendar e executar tarefas, processos e fluxos de trabalho automatizados recorrentes com aplicativos lógicos do Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Tutorial: verificar o tráfego em um agendamento com os aplicativos lógicos do Azure](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * O gatilho "Quando um email é recebido" permite que seu aplicativo lógico verifique se há novos emails de qualquer provedor de email com suporte pelos Aplicativos Lógicos, por exemplo, [Outlook do Office 365](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [Outlook.com](https://docs.microsoft.com/connectors/outlook/) e assim por diante. Para saber mais, consulte esses tópicos: 

    * [Tutorial: Gerenciar solicitações de lista de endereçamento com aplicativos lógicos do Azure](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Tutorial: automatizar o tratamento de emails e anexos com aplicativos lógicos do Azure](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * O [**gatilho**  HTTP](../connectors/connectors-native-http.md) permite que seu aplicativo lógico verifique um ponto de extremidade de serviço especificado por meio da comunicação por HTTP.
  
* Push:

  * O [gatilho de **solicitação** ](../connectors/connectors-native-reqres.md) permite que seu aplicativo lógico receba solicitações HTTP e responda em tempo real a eventos de alguma maneira.

  * O [**gatilho** Webhook HTTP](../connectors/connectors-native-webhook.md) inscreve em um ponto de extremidade de serviço, registrando um *URL de retorno de chamada* com esse serviço. Dessa forma, o serviço pode apenas notificar o gatilho quando o evento especificado ocorrer, para que o gatilho não precise sondar o serviço.

Depois de receber uma notificação sobre novos dados ou um evento, o gatilho é acionado, cria uma nova instância de fluxo de trabalho do aplicativo lógico e executa as ações no fluxo de trabalho. Você pode acessar todos os dados do gatilho durante todo o fluxo de trabalho. Por exemplo, o gatilho "em um novo tweet" passa o conteúdo do tweet para a execução do aplicativo lógico. Para começar a usar os aplicativos lógicos do Azure, Experimente estes tópicos de início rápido:

* [Início rápido: criar seu primeiro fluxo de trabalho automatizado com o aplicativo lógico do Azure no portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Início rápido: criar tarefas, processos e fluxos de trabalho automatizados com aplicativos lógicos do Azure usando o Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Início rápido: criar e gerenciar fluxos de trabalho de aplicativo lógico automatizado usando o Visual Studio Code](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="respond-to-triggers-and-extend-actions"></a>Responder a gatilhos e estender ações

Para sistemas e serviços que talvez não tenham conectores publicados, também é possível estender os aplicativos lógicos.

* [Criar gatilhos ou ações personalizadas](../logic-apps/logic-apps-create-api-app.md)
* [Configurar ações de longa execução para execução de fluxo de trabalho](../logic-apps/logic-apps-create-api-app.md)
* [Responder a eventos e ações externas com webhooks](../logic-apps/logic-apps-create-api-app.md)
* [Chamar, disparar ou aninhar fluxos de trabalho com respostas síncronas a solicitações HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Tutorial: Compilar um painel social capacitado por IA em minutos com o Aplicativo Lógicos e o Power BI](https://aka.ms/logicappsdemo)
* [Vídeo: Responder a webhooks de SMS do Twilio e enviar uma resposta de texto](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Recursos de tratamento de erro, registro em log e fluxo de controle

Os aplicativos lógicos incluem recursos avançados para o fluxo de controle avançado, como comutadores, condições, loops e escopos. Para garantir soluções resilientes, implemente também a manipulação de erros e exceções em seus fluxos de trabalho. Para receber notificação e logs de diagnóstico para status de execução de fluxo de trabalho, os Aplicativos Lógicos do Azure também fornecem monitoramento e alertas.

* Executar ações diferentes com base em [instruções condicionais](../logic-apps/logic-apps-control-flow-conditional-statement.md) e [alternar instruções](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Repetir as etapas ou processar itens em matrizes e coleções com loops](../logic-apps/logic-apps-control-flow-loops.md)
* [Agrupar ações com os escopos](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Manipulação de erro e exceções do autor em um fluxo de trabalho](../logic-apps/logic-apps-exception-handling.md)
* [Caso de uso: Como uma empresa de assistência médica usa a manipulação de exceção do aplicativo lógico para fluxos de trabalho HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Ativar o monitoramento, o registro em log e alertas para aplicativos lógicos existentes](../logic-apps/monitor-logic-apps.md)
* [Ativar o monitoramento e o registro em log de diagnóstico durante a criação de aplicativos lógicos](../logic-apps/monitor-logic-apps-log-analytics.md)

## <a name="deploy-and-manage-logic-apps"></a>Implantar e gerenciar aplicativos lógicos

Você pode desenvolver e implantar totalmente aplicativos lógicos com o Visual Studio, o Azure DevOps ou qualquer outra ferramenta de controle do código-fonte e de build automatizada. Para dar suporte à implantação de fluxos de trabalho e conexões dependentes em um modelo de recurso, os aplicativos lógicos usam modelos de implantação de recursos do Azure. As ferramentas do Visual Studio geram automaticamente esses modelos, que você pode conferir para controlar o código-fonte a fim de controlar a versão.

* [Criar e implantar aplicativos lógicos no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Ativar o monitoramento, o registro em log e alertas para aplicativos lógicos existentes](../logic-apps/monitor-logic-apps.md)
* [Automatizar a implantação do aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Exemplo: conectar-se a filas do barramento de serviço do Azure de aplicativos lógicos do Azure e implantar com Azure Pipelines no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemplo: conectar-se a contas de armazenamento do Azure de aplicativos lógicos do Azure e implantar com Azure Pipelines no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemplo: configurar uma ação de aplicativo de funções para aplicativos lógicos do Azure e implantar com Azure Pipelines no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemplo: conectar-se a uma conta de integração de aplicativos lógicos do Azure e implantar com Azure Pipelines no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Tipos de conteúdo, conversões e transformações em uma execução

Você pode acessar, converter e transformar vários tipos de conteúdo usando diversas funções na [linguagem de definição de fluxo de trabalho](https://aka.ms/logicappsdocs) dos Aplicativos Lógicos do Azure. Por exemplo, você pode converter entre uma cadeia de caracteres, JSON e XML com as expressões de fluxo de trabalho `@json()` e `@xml()`. O mecanismo do Aplicativo Lógico preserva os tipos de conteúdo a fim de dar suporte à transferência de conteúdo entre serviços sem perdas.

* [Como funcionam as expressões de fluxo de trabalho em aplicativos lógicos](../logic-apps/logic-apps-author-definitions.md)
* [Lidar com tipos de conteúdo não JSON](../logic-apps/logic-apps-content-type.md), como `application/xml`, `application/octet-stream` e `multipart/formdata`
* [Esquema de linguagem de definição de fluxo de trabalho para os Aplicativos Lógicos do Azure](https://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Outros recursos e integrações

Os aplicativos lógicos também oferecem integração com muitos serviços, como o Azure Functions, O Gerenciamento de API do Azure, Serviços de Aplicativo do Azure e pontos de extremidade HTTP personalizados, por exemplo, REST e SOAP.

* [Criar um painel social em tempo real com o Azure sem servidor](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Chamar o Azure Functions de aplicativos lógicos](../logic-apps/logic-apps-azure-functions.md)
* [Tutorial: Disparar aplicativos lógicos com o Azure Functions](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Tutorial: Como monitorar alterações de máquina virtual com a Grade de Eventos do Azure e os aplicativos lógicos](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Tutorial: criar uma função que se integre com os aplicativos lógicos do Azure e os serviços cognitivas do Azure para analisar as opiniões de postagem no Twitter](../azure-functions/functions-twitter-email.md)
* [Tutorial: Monitoramento remoto IoT e notificações com Aplicativos Lógicos do Azure conectando o Hub IoT e a caixa de correio](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Chamar pontos de extremidade SOAP de aplicativos lógicos](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Cenários de ponta a ponta

* [White paper: Integração de gerenciamento de casos completos com serviços do Azure, como Aplicativos Lógicos](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Histórias de clientes

Saiba como os Aplicativos Lógicos do Azure, juntamente com outros serviços do Azure e produtos da Microsoft, ajudaram [estas empresas](https://aka.ms/logic-apps-customer-stories) a melhorar a agilidade e a concentração em seus negócios essenciais simplificando, organizando, automatizando e coordenando processos complexos.

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [conectores para aplicativos lógicos](../connectors/apis-list.md)
* Saiba mais sobre [cenários de integração empresarial B2B com aplicativos lógicos do Azure](../logic-apps/logic-apps-enterprise-integration-overview.md)
