---
title: Exemplos & cenários comuns
description: Encontre exemplos, cenários comuns, tutoriais e passo a passo para aplicativos azure logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: c64483f18b2deb061704a4eb7171e575fdd561e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164620"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Cenários comuns, exemplos, tutoriais e instruções passo a passo para os Aplicativos Lógicos do Azure

[O Azure Logic Apps](../logic-apps/logic-apps-overview.md) ajuda você a orquestrar e integrar [diferentes serviços, fornecendo centenas de conectores prontos para uso,](../connectors/apis-list.md)desde o SQL Server ou SAP no local até os Serviços Cognitivos do Azure. O serviço de Aplicativos Lógicos é "sem servidor", portanto, você não precisa se preocupar sobre escala ou instâncias. Tudo o que você precisa fazer é definir o fluxo de trabalho com um gatilho e as ações que o fluxo de trabalho executa. A plataforma subjacente lida com a escala, disponibilidade e desempenho. Logic Apps é especialmente útil para casos de uso e cenários onde você precisa coordenar ações em vários sistemas e serviços.

Para ajudá-lo a aprender sobre os recursos e padrões que o Azure Logic Apps suporta, este artigo descreve pontos de partida comuns, exemplos e cenários.

## <a name="common-starting-points-for-logic-app-workflows"></a>Pontos de partida comuns para fluxos de trabalho de aplicativos lógicos

Cada aplicativo lógico começa com um [*gatilho*](../logic-apps/logic-apps-overview.md#logic-app-concepts) e apenas um gatilho, que inicia o fluxo de trabalho do aplicativo lógico e passa todos os dados como parte do que o gatilho. Alguns conectores fornecem gatilhos, que são fornecidos nestes tipos:

* *Gatilhos de sondagem*: verificam regularmente um ponto de extremidade de serviço para novos dados. Quando novos dados existirem, o gatilho cria e executa uma nova instância de fluxo de trabalho com os dados como entrada.

* *Gatilhos de envio por push*: escutam dados em um ponto de extremidade de serviço e aguardam até que ocorra um evento específico. Quando o evento ocorrer, o gatilho é acionado imediatamente, criando e executando uma nova instância de fluxo de trabalho que usa todos os dados disponíveis como entrada.

Aqui estão exemplos que descrevem gatilhos comumente usados:

* *Gatilhos de votação:*

  * [O gatilho **de recorrência** ](../connectors/connectors-native-recurrence.md) permite definir a data e a hora de início, além da recorrência para disparar seu aplicativo lógico. Por exemplo, você pode selecionar os dias da semana e horas do dia para disparar seu aplicativo lógico. Para saber mais, consulte esses tópicos:<p>

    * [Agendar e executar tarefas automatizadas, processos e fluxos de trabalho recorrentes com Aplicativos Lógicos do Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Tutorial: Crie fluxos de trabalho recorrentes automatizados e baseados em horários usando aplicativos de lógica do Azure](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * O **quando um e-mail é recebido,** permite que seu aplicativo lógico verifique se há novos e-mails de qualquer provedor de e-mail que seja suportado por Aplicativos Lógicos, por exemplo, [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [Outlook.com,](https://docs.microsoft.com/connectors/outlook/)e assim por diante. Para saber mais, consulte esses tópicos:<p>

    * [Tutorial: Crie fluxos de trabalho automatizados baseados em aprovação usando aplicativos azure logic](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Tutorial: Automatize tarefas para processar e-mails usando aplicativos azure logic, funções do Azure e armazenamento do Azure](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * O gatilho [ **HTTP** ](../connectors/connectors-native-http.md) pode chamar um ponto final de serviço em HTTP ou HTTPS. Para obter mais informações, consulte [Fluxos de trabalho de chamada, gatilho ou ninho usando pontos finais HTTP](../logic-apps/logic-apps-http-endpoint.md).

* *Pressione* os gatilhos:

  * O gatilho [ **Solicitar** ](../connectors/connectors-native-reqres.md) pode receber solicitações HTTPS recebidas.

  * O [**gatilho **Webhook HTTP](../connectors/connectors-native-webhook.md) inscreve em um ponto de extremidade de serviço, registrando um *URL de retorno de chamada* com esse serviço. Dessa forma, o serviço pode apenas notificar o gatilho quando o evento especificado ocorrer, para que o gatilho não precise sondar o serviço.

Depois que o evento especificado acontece, o gatilho é acionado, o que cria uma nova instância de fluxo de trabalho do aplicativo lógico e executa as ações no fluxo de trabalho. Você pode acessar todos os dados do gatilho durante todo o fluxo de trabalho. Por exemplo, o twitter **em um novo** gatilho de tweet passa o conteúdo do tweet para a execução do aplicativo lógico. Para começar com o Azure Logic Apps, experimente esses tópicos de partida rápida:

* [Quickstart: Crie seu primeiro fluxo de trabalho automatizado usando o Azure Logic Apps - portal Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Quickstart: Crie tarefas, processos e fluxos de trabalho automatizados usando aplicativos de lógica do Azure - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Quickstart: Crie e gerencie fluxos de trabalho automatizados de aplicativos lógicos usando o Visual Studio Code](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>Recursos de controle de fluxo e manipulação de erros

Os aplicativos lógicos incluem recursos ricos para fluxo de controle avançado, como condições, switches, loops e escopos. Para garantir soluções resilientes, implemente também a manipulação de erros e exceções em seus fluxos de trabalho.

* Executar ações diferentes com base em [instruções condicionais](../logic-apps/logic-apps-control-flow-conditional-statement.md) e [alternar instruções](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Repetir as etapas ou processar itens em matrizes e coleções com loops](../logic-apps/logic-apps-control-flow-loops.md)
* [Agrupar ações com os escopos](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Adicionar manipulação de erros e exceções a um fluxo de trabalho](../logic-apps/logic-apps-exception-handling.md)
* [Caso de uso: Como uma empresa de assistência médica usa a manipulação de exceção do aplicativo lógico para fluxos de trabalho HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Crie APIs e conectores personalizados

Para sistemas e serviços que não têm conectores publicados, você também pode estender aplicativos lógicos.

* [Crie APIs personalizadas para chamar a partir de aplicativos de lógica do Azure](../logic-apps/logic-apps-create-api-app.md)
* [Verifique se há novos dados ou eventos regularmente usando o padrão de gatilho de votação](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Aguarde e ouça novos dados ou eventos usando o padrão de gatilho do webhook](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Execute tarefas de longa duração usando o padrão de ação de votação](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Execute tarefas de longa duração usando o padrão de ação webhook](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Conectores personalizados em aplicativos azure logic](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>Construa soluções B2B (business-to-business)

Para soluções de integração corporativa e comunicação perfeita entre as organizações, você pode construir fluxos de trabalho escaláveis automatizados para esses cenários usando o Enterprise Integration Pack (EIP) com o Azure Logic Apps. Embora as organizações usem diferentes protocolos e formatos, elas podem trocar mensagens eletronicamente. O EIP transforma diferentes formatos em um formato que os sistemas de suas organizações podem processar e suporta protocolos padrão do setor, incluindo AS2, X12, EDIFACT e RosettaNet. Para construir essas soluções, você cria uma conta de integração, que é um recurso separado do Azure que fornece um contêiner seguro, escalável e gerenciável para os artefatos que você define e usa com seus fluxos de trabalho de aplicativos lógicos. Por exemplo, os artefatos incluem parceiros comerciais, acordos, mapas, esquemas, certificados e configurações em lote.

* [Visão geral: Soluções de integração corporativa B2B com aplicativos azure logic e pacote de integração corporativa](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Crie e gerencie contas de integração para integrações corporativas B2B em Aplicativos de Lógica do Azure](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Acesse os recursos de rede virtual do Azure

Às vezes, seus aplicativos lógicos e contas de integração precisam ter acesso a recursos protegidos, como máquinas virtuais (VMs) e outros sistemas ou serviços, que estão em uma rede virtual Do Zure. Para configurar esse acesso, você pode criar um ambiente de serviço de integração (ISE) onde você pode construir e executar seus aplicativos lógicos. Um ISE é uma instância privada e isolada do serviço Logic Apps que usa recursos dedicados, como armazenamento, e é executado separadamente do serviço de aplicativos lógicos "global", "global", multilocatários. Separar sua instância privada isolada e a instância global pública também ajuda a reduzir o impacto que outros inquilinos do Azure podem ter no desempenho de seus aplicativos, que também é conhecido como o efeito "vizinhos barulhentos".

* [Visão geral: Acesso aos recursos de rede virtual do Azure a partir de aplicativos de lógica do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Conectar-se a redes virtuais do Azure dos Aplicativos Lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Implantar, gerenciar e monitorar aplicativos lógicos

Você pode desenvolver e implantar totalmente aplicativos lógicos com o Visual Studio, o Azure DevOps ou qualquer outra ferramenta de controle do código-fonte e de build automatizada. Para dar suporte à implantação de fluxos de trabalho e conexões dependentes em um modelo de recurso, os aplicativos lógicos usam modelos de implantação de recursos do Azure. As ferramentas do Visual Studio geram automaticamente esses modelos, que você pode conferir para controlar o código-fonte a fim de controlar a versão. Para receber notificação e logs de diagnóstico para status de execução de fluxo de trabalho, os Aplicativos Lógicos do Azure também fornecem monitoramento e alertas.

### <a name="deploy"></a>Implantar

* [Quickstart: Crie tarefas, processos e fluxos de trabalho automatizados usando aplicativos de lógica do Azure - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Visão geral: Automatize a implantação do aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Crie modelos do Azure Resource Manager para automatizar a implantação de aplicativos de lógica do Azure](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Implantar modelos do Azure Resource Manager para aplicativos de lógica do Azure](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Exemplo: Conecte-se às filas de ônibus de serviço do Azure a partir de aplicativos azure logic e implante com pipelines Azure no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemplo: Conecte-se às contas de armazenamento do Azure a partir de aplicativos azure logic e implante com pipelines Azure no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemplo: Configure uma ação de aplicativo de função para aplicativos azure logic e implante com pipelines Azure no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemplo: Conecte-se a uma conta de integração do Azure Logic Apps e implante com o Azure Pipelines no Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Amostra: Orquestrar pipelines azure usando aplicativos azure logic](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>Gerenciar

* [Gerencie aplicativos lógicos usando o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Criar e gerenciar contas de integração para integrações corporativas B2B](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Gerencie o ambiente de serviçode integração (ISE) em aplicativos azure logic](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>Monitoramento

* [Monitore o status de execução, revise o histórico do gatilho e configure alertas para aplicativos azure logic](../logic-apps/monitor-logic-apps.md)
* [Configure registros do Monitor do Azure e colete dados de diagnóstico para aplicativos de lógica do Azure](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Configure registros do Monitor do Azure e colete dados de diagnóstico para mensagens B2B em Aplicativos de Lógica do Azure](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Exibir e criar consultas para monitoramento e rastreamento nos registros do Monitor do Azure para aplicativos de lógica do Azure](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>Lidar com tipos de conteúdo, conversões e transformações

Você pode acessar, converter e transformar vários tipos de conteúdo usando diversas funções na [linguagem de definição de fluxo de trabalho](https://aka.ms/logicappsdocs) dos Aplicativos Lógicos do Azure. Por exemplo, você pode converter entre uma cadeia de caracteres, JSON e XML com as expressões de fluxo de trabalho `@json()` e `@xml()`. O mecanismo do Aplicativo Lógico preserva os tipos de conteúdo a fim de dar suporte à transferência de conteúdo entre serviços sem perdas.

* [Manuseie tipos de conteúdo em aplicativos azure logic,](../logic-apps/logic-apps-content-type.md)tais como `application/`, `application/octet-stream`e`multipart/formdata`
* [Guia de referência para o uso de funções em expressões para aplicativos de lógica do Azure e automatização de energia](../logic-apps/workflow-definition-language-functions-reference.md)
* [Esquema de linguagem de definição de fluxo de trabalho para os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Outros recursos e integrações

O Azure Logic Apps integra-se a muitos serviços, como Funções Azure, Gerenciamento de API do Azure, Serviço de Aplicativos Azure e pontos finais HTTP personalizados, por exemplo, REST e SOAP.

* [Ligue para o Azure Functions a partir de aplicativos de lógica do Azure](../logic-apps/logic-apps-azure-functions.md)
* [Tutorial: Ligue ou acione aplicativos lógicos usando funções do Azure e ônibus de serviço do Azure](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Tutorial: Crie um painel de insights do cliente de streaming com aplicativos azure logic e funções do Azure](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Tutorial: Crie uma função que se integre com aplicativos azure logic e serviços cognitivos do Azure para analisar o sentimento do post do Twitter](../azure-functions/functions-twitter-email.md)
* [Tutorial: Construa um painel social alimentado por IA usando power bi e aplicativos lógicos do Azure](https://aka.ms/logicappsdemo)
* [Tutorial: Monitore as mudanças na máquina virtual usando a Grade de Eventos do Azure e aplicativos lógicos](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Tutorial: Monitoramento remoto IoT e notificações com Aplicativos Lógicos do Azure conectando o Hub IoT e a caixa de correio](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Ligue para os serviços soap usando aplicativos azure logic](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Cenários de ponta a ponta

* [White paper: Integração de gerenciamento de casos completos com serviços do Azure, como Aplicativos Lógicos](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Relatos de clientes

Saiba como os Aplicativos Lógicos do Azure, juntamente com outros serviços do Azure e produtos da Microsoft, ajudaram [estas empresas](https://aka.ms/logic-apps-customer-stories) a melhorar a agilidade e a concentração em seus negócios essenciais simplificando, organizando, automatizando e coordenando processos complexos.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [conectores para aplicativos lógicos](../connectors/apis-list.md)
* Conheça os [cenários de integração corporativa B2B com o Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-overview.md)
