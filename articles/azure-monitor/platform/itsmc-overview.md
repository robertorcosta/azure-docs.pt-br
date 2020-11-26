---
title: Conector de Gerenciamento de Serviços de TI em Log Analytics
description: Este artigo fornece uma visão geral de Conector de Gerenciamento de Serviços de TI (ITSMC) e informações sobre como usá-lo para monitorar e gerenciar itens de trabalho de ITSM no Log Analytics e resolver problemas rapidamente.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: b4e35296a999070a6f536f4a52cfb7c3a1d4f42a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186483"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Conectar o Azure a ferramentas de ITSM usando o Conector de Gerenciamento de Serviços de TI

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

O Conector de Gerenciamento de Serviços de TI (ITSMC) permite que você conecte o Azure a um produto ou serviço de ITSM (gerenciamento de serviços de ti) com suporte.

Os serviços do Azure, como o Azure Log Analytics e Azure Monitor fornecem ferramentas para detectar, analisar e solucionar problemas com seus recursos do Azure e não Azure. Mas os itens de trabalho relacionados a um problema normalmente residem em um produto ou serviço de ITSM. O ITSMC fornece uma conexão bidirecional entre o Azure e as ferramentas de ITSM para ajudá-lo a resolver problemas com mais rapidez.

O ITSMC é compatível com conexões com as seguintes ferramentas de ITSM:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> A partir de 1-out-2020 Cherwell e as integrações do ITSM do Provance com o alerta do Azure não serão mais habilitadas para novos clientes. Não haverá suporte para novas conexões de ITSM. Haverá suporte para conexões de ITSM existentes.

Com o ITSMC, você pode:

-  Crie itens de trabalho em sua ferramenta de ITSM, com base em seus alertas do Azure (alertas de métrica, alertas do log de atividades e alertas de Log Analytics).
-  Opcionalmente, você pode sincronizar seu incidente e alterar os dados de solicitação na ferramenta de ITSM para um espaço de trabalho do Azure Log Analytics.

Para obter informações sobre os termos legais e a política de privacidade, consulte a [declaração de privacidade da Microsoft](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Você pode começar a usar o ITSMC concluindo as seguintes etapas:

1.  [Adicione ITSMC.](#add-it-service-management-connector)
2.  [Crie uma conexão de ITSM.](#create-an-itsm-connection)
3.  [Use a conexão.](#use-itsmc)


##  <a name="add-it-service-management-connector"></a>Adicionar Conector de Gerenciamento de Serviços de TI

Antes de criar uma conexão, você precisa adicionar ITSMC.

1. Na portal do Azure, selecione **criar um recurso**:

   ![Captura de tela que mostra o item de menu criar um recurso.](media/itsmc-overview/azure-add-new-resource.png)

2. Pesquise **conector de gerenciamento de serviços de ti** no Azure Marketplace. Selecione **criar**:

   ![Captura de tela que mostra o botão criar no Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. Na seção **espaço de trabalho do OMS** , selecione o espaço de trabalho log Analytics do Azure onde você deseja instalar o ITSMC.
   >[!NOTE]
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
Usando modelos, você pode definir campos que serão preenchidos automaticamente de acordo com os valores fixos que são definidos como parte do grupo de ações. Você define modelos na ferramenta ITSM. Você pode definir em qual modelo você gostaria de usar como parte da definição do grupo de ações.
      
## <a name="create-itsm-work-items-from-azure-alerts"></a>Criar itens de trabalho de ITSM desde alertas do Azure

Depois de criar sua conexão de ITSM, você pode criar itens de trabalho em sua ferramenta de ITSM com base nos alertas do Azure. Para criar os itens de trabalho, você usará a ação de ITSM em grupos de ações.

Os grupos de ação fornecem uma maneira modular e reutilizável para disparar ações para seus alertas do Azure. Você pode usar grupos de ação com alertas de métrica, alertas do log de atividades e alertas de Log Analytics do Azure no portal do Azure.

> [!NOTE]
> Depois de criar a conexão de ITSM, você precisa aguardar 30 minutos para que o processo de sincronização seja concluído.
> 

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

   ![Captura de tela que mostra a janela de tíquete de ITSM.](media/itsmc-overview/itsm-action-configuration.png)
   
   * Caso você selecione no menu suspenso "evento" do item de trabalho: se você selecionar **criar itens de trabalho individuais para cada entrada de log** na seleção de botões de opção, cada alerta criará um novo item de trabalho. Se você selecionar **criar itens de trabalho individuais para cada item de configuração** na seleção de botões de opção, cada item de configuração terá seu próprio item de trabalho.
   ![Captura de tela que mostra a janela de tíquete de ITSM.](media/itsmc-overview/itsm-action-configuration-event.png)

10. Selecione **OK**.

Quando você cria ou edita uma regra de alerta do Azure, use um grupo de ações, que tem uma ação de ITSM. Quando o alerta é disparado, o item de trabalho é criado ou atualizado na ferramenta ITSM.

> [!NOTE]
>
>- Para obter informações sobre o preço da ação de ITSM, consulte a [página de preços](https://azure.microsoft.com/pricing/details/monitor/) para grupos de ações.
>
>
>- O campo Descrição curta na definição de regra de alerta é limitado a 40 caracteres quando você o envia usando a ação de ITSM.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualizar e analisar os dados de incidente e solicitação de alteração

Dependendo de sua configuração quando você configurar uma conexão, o ITSMC poderá sincronizar até 120 dias de incidentes e dados de solicitação de alteração. O esquema de registro de log para esses dados é fornecido na [próxima seção](#additional-information) deste artigo.

Você pode visualizar o incidente e alterar os dados de solicitação usando o painel do ITSMC:

![Captura de tela que mostra o painel do ITSMC.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

O painel também fornece informações sobre o status do conector, que você pode usar como ponto de partida para analisar problemas com as conexões.

Você também pode visualizar os incidentes sincronizados em relação aos computadores afetados no Mapa do Serviço.

O Mapa do Serviço descobre automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Ele permite que você exiba seus servidores da maneira como você os considera: como sistemas interconectados que fornecem serviços críticos. Mapa do Serviço mostra conexões entre servidores, processos e portas em qualquer arquitetura conectada a TCP. Além da instalação de um agente, nenhuma configuração é necessária. Para obter mais informações, consulte [usando mapa do serviço](../insights/service-map.md).

Se você estiver usando Mapa do Serviço, poderá exibir os itens da central de serviços criados em soluções de ITSM, conforme mostrado aqui:

![Captura de tela que mostra a exibição de Log Analytics.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)


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
| PlannedStartDate_t  |     Data de início planejada |
| PlannedEndDate_t  |   Data de término planejada |
| WorkStartDate_t  | Data de início real |
| WorkEndDate_t | Data de término real|
| Description_s | Descrição |
| Computador  | Item de Configuração |


## <a name="troubleshoot-itsm-connections"></a>Solução de problemas de conexões de ITSM
- Se uma conexão falhar na interface do usuário da fonte conectada e você receber um **erro ao salvar** a mensagem de conexão, execute as seguintes etapas:
   - Para as conexões ServiceNow, Cherwell e Provance:  
     - Certifique-se de que você inseriu corretamente o nome de usuário, a senha, a ID do cliente e o segredo do cliente para cada uma das conexões.  
     - Verifique se você tem privilégios suficientes no produto de ITSM correspondente para fazer a conexão.  
   - Para conexões Service Manager:  
     - Verifique se o aplicativo Web foi implantado com êxito e se a conexão híbrida foi criada. Para verificar se a conexão foi estabelecida com êxito com o computador Service Manager local, vá para a URL do aplicativo Web, conforme descrito na documentação para fazer a [conexão híbrida](./itsmc-connections.md#configure-the-hybrid-connection).  

- Se os dados do ServiceNow não estiverem sendo sincronizados com o Log Analytics, verifique se a instância do ServiceNow não está em suspensão. As instâncias de desenvolvimento do ServiceNow às vezes entram em suspensão quando estão ociosas por muito tempo. Se isso não for o que está acontecendo, relate o problema.
- Se Log Analytics alertas forem acionados, mas os itens de trabalho não forem criados no produto de ITSM, se os itens de configuração não forem criados/vinculados a itens de trabalho ou para outras informações, consulte estes recursos:
   -  ITSMC: a solução mostra um resumo de conexões, itens de trabalho, computadores e muito mais. Selecione o bloco que tem o rótulo **status do conector** . Isso o levará para a **pesquisa de logs** com a consulta relevante. Examine os registros de log com um `LogType_S` de `ERROR` para obter mais informações.
   - Página **pesquisa de logs** : exiba os erros e as informações relacionadas diretamente usando a consulta `*ServiceDeskLog_CL*` .

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Solucionar problemas Service Manager implantação de aplicativo Web
-   Se você tiver problemas com a implantação do aplicativo Web, verifique se você tem permissões para criar/implantar recursos na assinatura.
-   Se você receber uma **referência de objeto não definida como uma instância de um erro de objeto** ao executar o [script](itsmc-service-manager-script.md), certifique-se de que você inseriu valores válidos na seção **configuração do usuário** .
-   Se você não conseguir criar o namespace de retransmissão do barramento de serviço, verifique se o provedor de recursos necessário está registrado na assinatura. Se não estiver registrado, crie manualmente o namespace de retransmissão do barramento de serviço do portal do Azure. Você também pode criá-lo ao [criar a conexão híbrida](./itsmc-connections.md#configure-the-hybrid-connection) no portal do Azure.


## <a name="contact-us"></a>Fale conosco

Se você tiver consultas ou comentários sobre o Conector de Gerenciamento de Serviços de TI, entre em contato conosco em [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) .

## <a name="next-steps"></a>Próximas etapas
[Adicionar produtos/serviços de ITSM a Conector de Gerenciamento de Serviços de TI](./itsmc-connections.md)