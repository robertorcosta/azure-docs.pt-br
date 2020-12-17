---
title: Conector de Gerenciamento de Serviços de TI em Log Analytics
description: Este artigo fornece uma visão geral de Conector de Gerenciamento de Serviços de TI (ITSMC) e informações sobre como usá-lo para monitorar e gerenciar itens de trabalho de ITSM no Log Analytics e resolver problemas rapidamente.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 568243c6fecf26510f6e9988907d1ccad103cdc2
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618709"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Conectar o Azure a ferramentas de ITSM usando o Conector de Gerenciamento de Serviços de TI

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Este artigo fornece informações sobre como configurar o Conector de Gerenciamento de Serviços de TI (ITSMC) no Log Analytics para gerenciar centralmente seus itens de trabalho.

## <a name="add-it-service-management-connector"></a>Adicionar Conector de Gerenciamento de Serviços de TI

Antes de criar uma conexão, você precisa adicionar ITSMC.

1. Na portal do Azure, selecione **criar um recurso**:

   ![Captura de tela que mostra o item de menu criar um recurso.](media/itsmc-overview/azure-add-new-resource.png)

2. Pesquise **conector de gerenciamento de serviços de ti** no Azure Marketplace. Selecione **criar**:

   ![Captura de tela que mostra o botão criar no Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. Na seção **espaço de trabalho do OMS** , selecione o espaço de trabalho log Analytics do Azure onde você deseja instalar o ITSMC.
   >[!NOTE]
   >
   > * Como parte da transição contínua do Microsoft Operations Management Suite (OMS) para Azure Monitor, os espaços de trabalho do OMS agora são chamados de *espaços de trabalho log Analytics*.
   > * O ITSMC pode ser instalado somente em espaços de trabalho Log Analytics nas seguintes regiões: leste dos EUA, oeste dos EUA 2, Sul EUA Central, Oeste EUA Central, US Gov Arizona, US Gov-Virgínia, Canadá central, Europa Ocidental, sul do Reino Unido, Sudeste Asiático, leste do Japão, Índia central e sudeste da Austrália.

4. Na seção **log Analytics espaço de trabalho** , selecione o grupo de recursos no qual você deseja criar o recurso ITSMC:

   ![Captura de tela que mostra a seção Log Analytics espaço de trabalho.](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Como parte da transição contínua do Microsoft Operations Management Suite (OMS) para Azure Monitor, os espaços de trabalho do OMS agora são chamados de *espaços de trabalho log Analytics*.

5. Selecione **OK**.

Quando o recurso ITSMC é implantado, uma notificação é exibida no canto superior direito da janela.

## <a name="create-an-itsm-connection"></a>Criar uma conexão de ITSM

Depois de instalar o ITSMC, você poderá criar uma conexão.

Para criar uma conexão, você precisará preparar sua ferramenta de ITSM para permitir a conexão de ITSMC.  

Com base no produto de ITSM ao qual você está se conectando, selecione um dos links a seguir para obter instruções:

- [System Center Service Manager](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Depois de ter preparado suas ferramentas de ITSM, conclua estas etapas para criar uma conexão:

1. Em **todos os recursos**, procure por **assistência técnica (*seu nome de espaço de trabalho*)**:

   ![Captura de tela que mostra os recursos recentes no portal do Azure.](media/itsmc-overview/itsm-connections.png)

1. Em **fontes de dados do espaço de trabalho** no painel esquerdo, selecione **conexões de ITSM**:

   ![Captura de tela que mostra o item de menu de conexões de ITSM.](media/itsmc-overview/add-new-itsm-connection.png)
   Esta página exibe a lista de conexões.
1. Selecione **Adicionar conexão**.

4. Especifique as configurações de conexão, conforme descrito em [Configurando a conexão ITSMC com seus produtos/serviços de ITSM](./itsmc-connections.md).

   > [!NOTE]
   >
   > Por padrão, o ITSMC atualiza os dados de configuração da conexão uma vez a cada 24 horas. Para atualizar os dados da conexão instantaneamente para refletir as edições ou atualizações de modelo que você fizer, selecione o botão **sincronizar** na folha da sua conexão:
   >
   > ![Captura de tela que mostra o botão Sincronizar na folha conexão.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="use-itsmc"></a>Usar ITSMC

   Você pode usar o ITSMC para criar itens de trabalho de alertas do Azure, alertas de Log Analytics e Log Analytics registros de log.

## <a name="template-definitions"></a>Definições de modelo

   Há tipos de item de trabalho que podem usar modelos que são definidos pela ferramenta ITSM.
Usando modelos, você pode definir campos que serão preenchidos automaticamente de acordo com os valores fixos que são definidos como parte do grupo de ações. Você define modelos na ferramenta ITSM.
Você pode definir em qual modelo você gostaria de usar como parte da definição do grupo de ações.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Criar itens de trabalho de ITSM desde alertas do Azure

Depois de criar sua conexão de ITSM, você pode criar itens de trabalho em sua ferramenta de ITSM com base nos alertas do Azure. Para criar os itens de trabalho, você usará a ação de ITSM em grupos de ações.

Os grupos de ação fornecem uma maneira modular e reutilizável para disparar ações para seus alertas do Azure. Você pode usar grupos de ação com alertas de métrica, alertas do log de atividades e alertas de Log Analytics do Azure no portal do Azure.

> [!NOTE]
> Depois de criar a conexão de ITSM, você precisa aguardar 30 minutos para que o processo de sincronização seja concluído.

Use o procedimento a seguir para criar itens de trabalho:

1. No portal do Azure, selecione  **alertas**.
2. No menu na parte superior da tela, selecione **Gerenciar ações**:

    ![Captura de tela que mostra o item de menu gerenciar ações.](media/itsmc-overview/action-groups-selection-big.png)

   A janela **Criar grupo de ações** é exibida.

3. Selecione a **assinatura** e o **grupo de recursos** em que você deseja criar o grupo de ações. Forneça um **nome de grupo de ação** e um **nome de exibição** para o grupo de ação. Selecione **Avançar: notificações**.

    ![Captura de tela que mostra a janela criar grupo de ações.](media/itsmc-overview/action-groups-details.png)

4. Na lista notificação, selecione **Avançar: ações**.
5. Na lista ações, selecione **ITSM** na lista **tipo de ação** . Forneça um **nome** para a ação. Selecione o botão de caneta que representa **detalhes de edição**.
6. Na lista **assinatura** , selecione a assinatura na qual seu espaço de trabalho do log Analytics está localizado. Na lista **conexão** , selecione o nome do conector de ITSM. Ele será seguido pelo nome do seu espaço de trabalho. Por exemplo, MyITSMConnector (MyWorkspace).

7. Selecione um tipo de **item de trabalho** .

8. Se você quiser preencher os campos prontos para uso com valores fixos, selecione **usar modelo personalizado**. Caso contrário, escolha um [modelo](#template-definitions) existente na lista de **modelos** e insira os valores fixos nos campos de modelo.

9. Se você selecionar **criar itens de trabalho individuais para cada item de configuração**, cada item de configuração terá seu próprio item de trabalho. Haverá um item de trabalho por item de configuração. Ele será atualizado de acordo com os alertas que serão criados.

    * Em um caso que você selecionar no menu suspenso "incidente" ou "alerta" do item de trabalho: se você desmarcar a caixa de seleção **criar itens de trabalho individuais para cada item de configuração** , cada alerta criará um novo item de trabalho. Pode haver mais de um alerta por item de configuração.

       ![Captura de tela que mostra a janela incidente de ITSM.](media/itsmc-overview/itsm-action-configuration.png)

    * Caso você selecione no menu suspenso "evento" do item de trabalho: se você selecionar **criar itens de trabalho individuais para cada entrada de log** na seleção de botões de opção, cada alerta criará um novo item de trabalho. Se você selecionar **criar itens de trabalho individuais para cada item de configuração** na seleção de botões de opção, cada item de configuração terá seu próprio item de trabalho.
   ![Captura de tela que mostra a janela de evento de ITSM.](media/itsmc-overview/itsm-action-configuration-event.png)

10. Selecione **OK**.

Quando você cria ou edita uma regra de alerta do Azure, use um grupo de ações, que tem uma ação de ITSM. Quando o alerta é disparado, o item de trabalho é criado ou atualizado na ferramenta ITSM.

> [!NOTE]
>
>- Para obter informações sobre o preço da ação de ITSM, consulte a [página de preços](https://azure.microsoft.com/pricing/details/monitor/) para grupos de ações.
>
>
>- O campo Descrição curta na definição de regra de alerta é limitado a 40 caracteres quando você o envia usando a ação de ITSM.

## <a name="additional-information"></a>Informações adicionais

### <a name="data-synced-from-your-itsm-product"></a>Dados sincronizados de seu produto de ITSM

Incidentes e solicitações de alteração são sincronizados de seu produto ITSM para seu espaço de trabalho Log Analytics, com base na configuração da conexão.

Esta seção mostra alguns exemplos de dados coletados pelo ITSMC.

Os campos em **ServiceDesk_CL** variam de acordo com o tipo de item de trabalho que você importa para o log Analytics. Aqui está uma lista de campos para dois tipos de item de trabalho:

**Item de trabalho:** **Incidentes**  
ServiceDeskWorkItemType_s="Incident"

**Fields**

- ServiceDeskConnectionName
- ID da Central de Serviços
- Estado
- Urgência
- Impacto
- Prioridade
- Escalonamento
- Criado por
- Resolvido por
- Fechado por
- Fonte
- Atribuído a
- Categoria
- Title
- Descrição
- Data de criação
- Data de fechamento
- Data de resolução
- Data da última modificação
- Computador

**Item de trabalho:** **Solicitações de Alteração**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fields**
- ServiceDeskConnectionName
- ID da Central de Serviços
- Criado por
- Fechado por
- Fonte
- Atribuído a
- Title
- Type
- Categoria
- Estado
- Escalonamento
- Status do conflito
- Urgência
- Prioridade
- Risco
- Impacto
- Atribuído a
- Data de criação
- Data de fechamento
- Data da última modificação
- Data de solicitação
- Data de início prevista
- Data de término prevista
- Data de início do trabalho
- Data de término do trabalho
- Descrição
- Computador

## <a name="output-data-for-a-servicenow-incident"></a>Dados de saída de um incidente do ServiceNow

| Campo Log Analytics | Campo do ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Número |
| IncidentState_s | Estado |
| Urgency_s |Urgência |
| Impact_s |Impacto|
| Priority_s | Prioridade |
| CreatedBy_s | Aberto por |
| ResolvedBy_s | Resolvido por|
| ClosedBy_s  | Fechado por |
| Source_s| Tipo de contato |
| AssignedTo_s | Atribuído a  |
| Category_s | Categoria |
| Title_s|  Descrição breve |
| Description_s|  Observações |
| CreatedDate_t|  Aberto |
| ClosedDate_t| closed|
| ResolvedDate_t|Resolvido|
| Computador  | Item de configuração |

## <a name="output-data-for-a-servicenow-change-request"></a>Dados de saída para uma solicitação de alteração do ServiceNow

| Log Analytics | Campo do ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Número |
| CreatedBy_s | Solicitado por |
| ClosedBy_s | Fechado por |
| AssignedTo_s | Atribuído a  |
| Title_s|  Descrição breve |
| Type_s|  Type |
| Category_s|  Categoria |
| CRState_s|  Estado|
| Urgency_s|  Urgência |
| Priority_s| Prioridade|
| Risk_s| Risco|
| Impact_s| Impacto|
| RequestedDate_t  | Solicitado por data |
| ClosedDate_t | Data de fechamento |
| PlannedStartDate_t  | Data de início planejada |
| PlannedEndDate_t  | Data de término planejada |
| WorkStartDate_t  | Data de início real |
| WorkEndDate_t | Data de término real|
| Description_s | Descrição |
| Computador  | Item de Configuração |

## <a name="next-steps"></a>Próximas etapas

[Adicionar produtos/serviços de ITSM a Conector de Gerenciamento de Serviços de TI](./itsmc-connections.md)
