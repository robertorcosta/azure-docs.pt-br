---
title: Criar trabalhos agendados-portal do Azure
description: Criar, agendar e executar seu primeiro trabalho automatizado no portal do Azure usando o Agendador do Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: estfan
ms.reviewer: klam, estfan, logicappspm
ms.topic: conceptual
ms.date: 02/29/2020
ms.openlocfilehash: a9f7169f4b54dfc08612b1d53bfde48154ee2d1d
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524794"
---
# <a name="create-and-schedule-your-first-job-by-using-azure-scheduler---azure-portal"></a>Criar e agendar seu primeiro trabalho usando o Agendador do Azure-portal do Azure

> [!IMPORTANT]
> O [aplicativo lógico do Azure](../logic-apps/logic-apps-overview.md) está substituindo o Agendador do Azure, que está [sendo desativado](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Para continuar trabalhando com os trabalhos que você configurou no Agendador, [migre para o aplicativo lógico do Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) assim que possível.

Este tutorial mostra como você pode criar e agendar um trabalho e, em seguida, monitorar e gerenciar facilmente esse trabalho.

Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="create-job"></a>Criar trabalho

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Na caixa de pesquisa do Azure, insira `scheduler` como seu filtro. Na lista de resultados, selecione **coleções de trabalhos do Agendador**e selecione **criar**.

   ![Criar recurso do Agendador](./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png)

   Agora, crie um trabalho que envie uma solicitação GET para esta URL: `https://www.microsoft.com/` 

1. Em **Trabalho do Agendador**, insira estas informações:

   | Propriedade | Valor de exemplo | DESCRIÇÃO |
   |----------|---------------|-------------| 
   | **Nome** | getMicrosoft | O nome de seu trabalho | 
   | **Coleção de trabalhos** | <*job-collection-name*> | Crie uma coleção de trabalhos ou selecione uma coleção existente. | 
   | **Assinatura** | <*Azure-subscription-name*> | O nome e a ID da assinatura do Azure | 
   |||| 

1. Selecione **Configurações de ação – configurar**, forneça essas informações e, em seguida, escolha **OK** quando você terminar:

   | Propriedade | Valor de exemplo | DESCRIÇÃO |
   |----------|---------------|-------------| 
   | **Ação** | **Http** | O tipo de ação a ser executada | 
   | **Método** | **Get** | O método a ser chamado | 
   | **URL** | **https://www.microsoft.com** | A URL de destino | 
   |||| 
   
   ![Definir o trabalho](./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png)

1. Selecione **Agendar – configure**, defina o agendamento e, em seguida, selecione **OK** quando você terminar:

   Embora você possa criar um trabalho de ocorrência única, este exemplo configura um agendamento de recorrência.

   | Propriedade | Valor de exemplo | DESCRIÇÃO |
   |----------|---------------|-------------| 
   | **Recorrência** | **Recorrente** | Um trabalho de ocorrência única ou recorrente | 
   | **Iniciar em** | <*Data de hoje*> | A data de início do trabalho | 
   | **Repetir a cada** | **1 hora** | O intervalo de recorrência e a frequência | 
   | **Término** | **Término em** dois dias a contar da data de hoje | A data de término do trabalho | 
   | **Diferença UTC** | **UTC +08:00** | A diferença de tempo entre o UTC (Tempo Universal Coordenado) e a hora observada na sua localização | 
   |||| 

   ![Definir agendamento](./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png)

1. Quando você estiver pronto, escolha **Criar**.

   Depois de você criar seu trabalho, o Azure o implanta e ele aparece no painel do Azure. 

1. Quando o Azure mostra uma notificação de que a implantação foi bem-sucedida, escolha **Fixar no painel**. Caso contrário, escolha o ícone **Notificações** (sino) na barra de ferramentas do Azure e, em seguida, escolha **Fixar no painel**.

## <a name="monitor-and-manage-jobs"></a>Monitorar e gerenciar trabalhos

Para examinar, monitorar e gerenciar seu trabalho, no painel do Azure, escolha seu trabalho. Em **configurações**, aqui estão as áreas que você pode examinar e gerenciar para seu trabalho:

![Configurações do trabalho](./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png)

Para obter mais informações sobre essas áreas, selecione uma área:

* [**Propriedades**](#properties)
* [**Configurações de ação**](#action-settings)
* [**Agendamento**](#schedule)
* [**Histórico**](#history)
* [**Usuários**](#users)

<a name="properties"></a>

### <a name="properties"></a>Propriedades

Para exibir as propriedades somente leitura que descrevem os metadados de gerenciamento para o seu trabalho, selecione **Propriedades**.

![Exibir propriedades do trabalho](./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png)

<a name="action-settings"></a>

### <a name="action-settings"></a>Configurações de Ação

Para alterar as configurações avançadas do seu trabalho, selecione **Configurações da ação**. 

![Examinar as configurações de ação](./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png)

| Tipo de ação | DESCRIÇÃO | 
|-------------|-------------| 
| Todos os tipos | Você pode alterar as configurações de **Política de repetição** e **Ação de erro**. | 
| HTTP e HTTPS | Você pode alterar o **Método** para qualquer método permitido. Você também pode adicionar, excluir ou alterar os cabeçalhos e informações de autenticação básica. | 
| Fila de armazenamento| Você pode alterar a conta de armazenamento, o nome da fila, o token SAS e o corpo. | 
| Barramento de Serviço | Você pode alterar o namespace, o caminho de fila ou tópico, as configurações de autenticação, o tipo de transporte, a propriedades de mensagem e o corpo da mensagem. | 
||| 

<a name="schedule"></a>

### <a name="schedule"></a>Agenda

Se você configurar um agendamento por meio do assistente de trabalho, você poderá alterar essa agenda, como data e hora de início, o agendamento de recorrência e a data e a hora de término para trabalhos recorrentes.
Você também pode criar [agendamentos mais complexos e recorrências avançadas](scheduler-advanced-complexity.md).

Para alterar o modo de exibição ou alterar o agendamento do trabalho, selecione **Agendamento**:

![Exibir agenda de trabalho](./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png)

<a name="history"></a>

### <a name="history"></a>Histórico

Para exibir as métricas sobre cada execução de um trabalho selecionado, selecione **Histórico**. Essas métricas fornecem valores em tempo real sobre a integridade do seu trabalho, como status, número de repetições, número de ocorrências, hora de início e hora de término.

![Exibir histórico de trabalho e métricas](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png)

Para exibir os detalhes de histórico para cada execução, como a resposta completa para cada execução, em **Histórico**, selecione cada execução. 

![Exibir detalhes do histórico de trabalhos](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png)

<a name="users"></a>

### <a name="users"></a>Usuários

Você pode gerenciar o acesso ao Agendador do Azure para cada usuário em um nível granular usando o RBAC (controle de acesso baseado em função). Para saber como configurar o acesso com base em funções, consulte [Gerenciar acesso usando o RBAC](../role-based-access-control/role-assignments-portal.md)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [conceitos, terminologia e hierarquia de entidades](scheduler-concepts-terms.md)
* [Criar agendamentos complexos e recorrência avançada](scheduler-advanced-complexity.md)
* Saiba mais sobre [alta disponibilidade e confiabilidade para o Agendador](scheduler-high-availability-reliability.md)
* Saiba mais sobre [Limites, cotas, valores padrão e códigos de erro](scheduler-limits-defaults-errors.md)
