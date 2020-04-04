---
title: 'Tutorial: Gerencie a computação com funções do Azure'
description: Como usar as funções do Azure para gerenciar a computação do seu pool SQL no Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: aa2cff552b49bceeaf6fd46510bf78384f0e7bfb
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631959"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-synapse-analytics-sql-pool"></a>Use funções do Azure para gerenciar recursos de computação no pool SqL do Azure Synapse Analytics

Este tutorial usa funções do Azure para gerenciar recursos de computação para um pool SQL no Azure Synapse Analytics.

Para usar o Azure Function App com o pool SQL, você deve criar uma [conta principal de serviço](../../active-directory/develop/howto-create-service-principal-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) com acesso ao contribuinte sob a mesma assinatura da sua instância de pool SQL.

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Implantar o escalonador baseado em temporizador com um modelo do Azure Resource Manager

Para implantar o modelo, você precisa das informações a seguir:

- Nome do grupo de recursos em que a instância do pool SQL está
- Nome do servidor lógico sua instância de pool SQL está em
- Nome da sua instância de pool SQL
- A ID de locatário (ID do diretório) do Azure Active Directory
- ID da assinatura
- ID do aplicativo da entidade de serviço
- Chave secreta da entidade de serviço

Após reunir as informações anteriores, implante este modelo:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Depois de implantar o modelo, você deve encontrar três novos recursos: um plano do Serviço de Aplicativo do Azure gratuito, um plano do Aplicativo de funções baseado em consumo e uma conta de armazenamento que manipula o registro em log e a fila de operações. Continue a ler as outras seções para saber como modificar as funções implantadas de acordo com as suas necessidades.

## <a name="change-the-compute-level"></a>Alterar o nível de computação

1. Navegue até o serviço d do Aplicativo de funções. Se você implantou o modelo com os valores padrão, esse serviço deve ser chamado *DWOperations*. Quando seu Aplicativo de funções for aberto, você deve observar que há cinco funções implantadas para a função do Serviço de Aplicativo de funções.

   ![Funções implantadas com o modelo](./media/manage-compute-with-azure-functions/five-functions.png)

2. Selecione *DWScaleDownTrigger* ou *DWScaleUpTrigger* dependendo se você deseja alterar a hora para escalar ou reduzir verticalmente. No menu suspenso, selecione Integrar.

   ![Selecione Integrar para a função](./media/manage-compute-with-azure-functions/select-integrate.png)

3. O valor exibido deve ser *ScaleDownTime %* ou *ScaleUpTime %*. Esses valores indicam que o agendamento se baseia nos valores definidos nas suas [Configurações do aplicativo](../../azure-functions/functions-how-to-use-azure-function-app-settings.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Por enquanto, você pode ignorar esse valor e alterar o agendamento para a hora de sua preferência com base nas próximas etapas.

4. Na área de agendamento, adicione a hora à expressão CRON para refletir a frequência desejada para escalar verticalmente o SQL Data Warehouse.

   ![Alterar o agendamento da função](./media/manage-compute-with-azure-functions/change-schedule.png)

   O valor de `schedule` é uma [expressão CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression) que inclui estes seis campos:

   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Por exemplo *"0 30 9 * * 1-5"* refletiria um gatilho cada dia útil às 9:30. Para obter mais informações, visite [exemplos de agendamento](../../azure-functions/functions-bindings-timer.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#example) do Azure Functions.

## <a name="change-the-time-of-the-scale-operation"></a>Altere o tempo da operação de escala

1. Navegue até o serviço d do Aplicativo de funções. Se você implantou o modelo com os valores padrão, esse serviço deve ser chamado *DWOperations*. Quando seu Aplicativo de funções for aberto, você deve observar que há cinco funções implantadas para a função do Serviço de Aplicativo de funções.

2. Selecione *DWScaleDownTrigger* ou *DWScaleUpTrigger* dependendo se você deseja alterar o valor de computação para escalar ou reduzir verticalmente. Ao selecionar as funções, o painel deve mostrar o arquivo *index.js*.

   ![Alterar o nível de computação do gatilho da função](././media/manage-compute-with-azure-functions/index-js.png)

3. Altere o valor de *ServiceLevelObjective* para o nível desejado e aperte para salvar a opção. Este valor é o nível de computação para o qual a sua instância do data warehouse dimensionará com base no agendamento definido na seção Integrar.

## <a name="use-pause-or-resume-instead-of-scale"></a>Usar pausar ou retomar em vez do dimensionamento

Atualmente, as funções habilitadas por padrão são *DWScaleDownTrigger* e *DWScaleUpTrigger*. No entanto, se você quiser usar a funcionalidade de pausar e retomar, você pode habilitar *DWPauseTrigger* ou *DWResumeTrigger*.

1. Navegue até o painel Funções.

   ![Painel Funções](./media/manage-compute-with-azure-functions/functions-pane.png)

2. Clique no botão de alternância deslizante para os gatilhos correspondentes que você deseja habilitar.

3. Navegue até as guias *Integrar* dos respectivos gatilhos para alterar o agendamento.

   > [!NOTE]
   > A diferença funcional entre os gatilhos de escala e os gatilhos de pausar/retomar é a mensagem que é enviada para a fila. Para saber mais, confira [Adicionar uma nova função de gatilho](manage-compute-with-azure-functions.md#add-a-new-trigger-function).

## <a name="add-a-new-trigger-function"></a>Adicionar uma nova função de gatilho

Atualmente, há apenas duas funções de dimensionamento incluídas no modelo. Com essas funções, no decorrer de um dia, você só pode escalar e reduzir verticalmente somente uma vez. Para um controle mais granular, como reduzir verticalmente várias vezes por dia ou ter um comportamento de dimensionamento diferente nos finais de semana, você precisa adicionar outro gatilho.

1. Criar uma nova função em branco. Selecione *+* o botão perto do local de Funções para mostrar o painel do modelo de função.

   ![Criar nova função](./media/manage-compute-with-azure-functions/create-new-function.png)

2. Em Idioma, selecione *Javascript*, em seguida, selecione *TimerTrigger*.

   ![Criar nova função](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Nomeie a sua função e defina o agendamento. A imagem mostra como fazer para disparar sua função todos os sábados à meia-noite (tarde da noite de sexta-feira).

   ![Reduzir verticalmente sábado](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Copie o conteúdo de *index.js* de uma das outras funções de gatilho.

   ![Copiar index.js](././media/manage-compute-with-azure-functions/index-js.png)

5. Defina a sua variável de operação para o comportamento desejado da seguinte maneira:

   ```javascript
   // Resume the SQL pool instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the SQL pool instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the SQL pool instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```

## <a name="complex-scheduling"></a>Agendamentos complexos

Esta seção demonstra brevemente o que é necessário para aproveitar mais os recursos de agendamento complexo para pausar, retomar e dimensionar.

### <a name="example-1"></a>Exemplo 1

Escalar verticalmente diariamente às 8:00 para DW600 e reduzir verticalmente às 20:00 para DW200.

| Função  | Agenda     | Operação                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>Exemplo 2

Escalar verticalmente diariamente às 8:00 para DW1000, reduzir verticalmente uma vez para DW600 às 16:00 e reduzir verticalmente às 22:00 para DW200.

| Função  | Agenda     | Operação                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-3"></a>Exemplo 3

Escalar verticalmente às 8:00 para DW1000, reduzir verticalmente uma vez para DW600 às 16:00 em dias úteis. Pausar sexta-feira às 23:00, retomar segunda-feira de manhã às 7:00.

| Função  | Agenda       | Operação                                |
| :-------- | :------------- | :--------------------------------------- |
| Function1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Function4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as funções de [gatilho de temporizador](../../azure-functions/functions-create-scheduled-function.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) do Azure.

Confira o [repositório](https://github.com/Microsoft/sql-data-warehouse-samples)de amostras de pool SQL .
