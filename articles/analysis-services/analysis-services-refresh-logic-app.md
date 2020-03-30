---
title: Atualize com aplicativos lógicos para modelos de serviços de análise do Azure | Microsoft Docs
description: Este artigo descreve como codificar a atualização assíncrona para os Serviços de Análise do Azure usando o Azure Logic Apps.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: 78bc629598c0635b7760285d0507b7a85a4ab551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126949"
---
# <a name="refresh-with-logic-apps"></a>Atualizar com Aplicativos Lógicos

Usando aplicativos lógicos e chamadas REST, você pode executar operações automatizadas de atualização de dados em seus modelos tabulares de análise do Azure, incluindo a sincronização de réplicas somente leitura para a escala de consulta.

Para saber mais sobre como usar APIs REST com serviços de análise do Azure, consulte [Atualização assíncrona com a API REST](analysis-services-async-refresh.md).

## <a name="authentication"></a>Autenticação

Todas as chamadas devem ser autenticadas com um token válido do Azure Active Directory (OAuth 2).  Os exemplos deste artigo usarão um SPN (Service Principal) para autenticar os Serviços de Análise do Azure. Para saber mais, consulte [Criar um diretor de serviço usando o portal Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Projete o aplicativo lógico

> [!IMPORTANT]
> Os exemplos a seguir supõem que o firewall do Azure Analysis Services está desativado. Se o firewall estiver ativado, o endereço IP público do iniciador de solicitação deve ser listado no firewall dos Serviços de Análise do Azure. Para saber mais sobre os intervalos IP do Azure Logic Apps por região, consulte [Limites e informações de configuração para aplicativos de lógica do Azure](../logic-apps/logic-apps-limits-and-config.md#configuration).

### <a name="prerequisites"></a>Pré-requisitos

#### <a name="create-a-service-principal-spn"></a>Criar um SpN (Service Principal)

Para saber mais sobre a criação de um Service Principal, consulte [Criar um diretor de serviço usando o portal Azure](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Configure permissões nos Serviços de Análise do Azure
 
O Princípio do Serviço criado deve ter permissões de administrador de servidor no servidor. Para saber mais, consulte [Adicionar uma entidade de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Configure o App Logic

Neste exemplo, o App Logic é projetado para ser acionado quando uma solicitação HTTP é recebida. Isso permitirá o uso de uma ferramenta de orquestração, como a Fábrica de Dados Do Azure, para ativar a atualização do modelo azure Analysis Services.

Depois de criar um aplicativo lógico:

1. No designer do Logic App, escolha a primeira ação como **Quando uma solicitação HTTP é recebida**.

   ![Adicionar atividade recebida HTTP](./media/analysis-services-async-refresh-logic-app/1.png)

Esta etapa será preenchida com a URL HTTP POST assim que o Aplicativo Lógico for salvo.

2. Adicione um novo passo e procure **http**.  

   ![Adicionar atividade HTTP](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Adicionar atividade HTTP](./media/analysis-services-async-refresh-logic-app/10.png)

3. Selecione **HTTP** para adicionar essa ação.

   ![Adicionar atividade HTTP](./media/analysis-services-async-refresh-logic-app/2.png)

Configure a atividade HTTP da seguinte forma:

|Propriedade  |Valor  |
|---------|---------|
|**Método**     |POST         |
|**URI**     | https:// região do*servidor*/servidores/*aas nome do servidor*/modelos/ nome do banco de*dados*/atualiza <br /> <br /> Por exemplo:\/https: /westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes|
|**Cabeçalhos**     |   Tipo de conteúdo, aplicativo/json <br /> <br />  ![headers](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Corpo**     |   Para saber mais sobre a formação do corpo de solicitação, consulte [Atualização assíncrona com a API REST - POST /refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Autenticação**     |Active Directory OAuth         |
|**Inquilino**     |Preencha seu Azure Active Directory TenantId         |
|**Público**     |https://*.asazure.windows.net         |
|**ID do cliente**     |Digite o seu nome principal de serviço ClientID         |
|**Tipo de credencial**     |Segredo         |
|**Segredo**     |Insira o segredo do nome principal do seu serviço         |

Exemplo:

![Atividade HTTP concluída](./media/analysis-services-async-refresh-logic-app/7.png)

Agora teste o App Lógico.  No designer do Logic App, clique em **Executar**.

![Testar o aplicativo lógico](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Consumir o app logic com a fábrica de dados do Azure

Uma vez que o Aplicativo Lógico seja salvo, revise a atividade **Quando uma solicitação HTTP é recebida** e, em seguida, copie a URL HTTP **POST** que agora é gerada.  Esta é a URL que pode ser usada pela Fábrica de Dados Do Azure para fazer a chamada assíncrona para acionar o Logic App.

Aqui está um exemplo a azure Data Factory Web Activity que faz essa ação.

![Atividade web da fábrica de dados](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Use um aplicativo lógico autônomo

Se você não planeja usar uma ferramenta de Orquestração, como a Data Factory, para ativar a atualização do modelo, você pode configurar o aplicativo lógico para ativar a atualização com base em um cronograma.

Usando o exemplo acima, exclua a primeira atividade e substitua-a por uma atividade **Agendar.**

![Atividades de agendamento](./media/analysis-services-async-refresh-logic-app/12.png)

![Atividades de agendamento](./media/analysis-services-async-refresh-logic-app/13.png)

Este exemplo usará **Recorrência**.

Uma vez que a atividade tenha sido adicionada, configure o intervalo e a frequência e adicione um novo parâmetro e escolha **Nessas horas**.

![Atividades de agendamento](./media/analysis-services-async-refresh-logic-app/16.png)

Selecione as horas desejadas.

![Atividades de agendamento](./media/analysis-services-async-refresh-logic-app/15.png)

Salve o Aplicativo Lógico.

## <a name="next-steps"></a>Próximas etapas

[Amostras](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
