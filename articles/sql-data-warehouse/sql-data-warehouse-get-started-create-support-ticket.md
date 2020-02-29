---
title: Como criar um tíquete de suporte
description: Como criar um tíquete de suporte no Azure Synapse Analytics.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9623a878ea009ec7363501c09c324baa6d0fa7ad
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195648"
---
# <a name="how-to-create-a-support-ticket-for-azure-synapse-analytics"></a>Como criar um tíquete de suporte para o Azure Synapse Analytics
Se você tiver problemas com o Azure Synapse Analytics, crie um tíquete de suporte para que a equipe de suporte de engenharia possa ajudá-lo.

## <a name="create-a-support-ticket"></a>Criar um tíquete de suporte
1. Abra o [Portal do Azure](https://portal.azure.com/).
1. Na tela Inicial, clique na guia **Ajuda + suporte**.
   
    ![Ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/main-page.png)

1. Examine o [plano de suporte do Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * **Gerenciamento de assinaturas, cobrança e cotas** está disponível em todos os níveis de suporte.
   * O suporte à **correção de** conserto é fornecido por meio do suporte [Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)ou [Premier](https://azure.microsoft.com/support/plans/premier/) . As questões que exigem reparos são problemas vivenciados pelo cliente ao usar o Azure, em que se espera que o problema tenha sido causado pela Microsoft.
   * Os **Serviços** de consultoria e **aconselhamento para desenvolvedores** estão disponíveis nos níveis de suporte [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) e [Premier](https://azure.microsoft.com/support/plans/premier/) . 
     
     Se você tiver um plano de suporte Premier, também poderá relatar SQL Data Warehouse problemas relacionados no [portal do Microsoft Premier Online](https://premier.microsoft.com/). Consulte [planos de suporte do Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) para saber mais sobre os vários planos de suporte, incluindo escopo, tempos de resposta, preços, etc.  Para perguntas frequentes sobre o suporte do Azure, consulte [perguntas frequentes sobre o suporte do Azure](https://azure.microsoft.com/support/faq/).
1. Na página **ajuda + suporte** , selecione **nova solicitação de suporte**. Selecione um tipo de problema no menu suspenso. Em seguida, continue a preencher as informações na guia **noções básicas** . Insira um **Resumo** do problema e, em seguida, selecione um **tipo de problema** no menu e selecione salvar.

    ![Ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/issue-type.png)

   > [!NOTE]
   > Por padrão, cada SQL Server (por exemplo, myserver.database.windows.net) tem uma **Cota de DTU** de 45.000. Essa cota é simplesmente um limite de segurança. Você pode aumentar sua cota criando um tíquete de suporte e selecionando *Cota* como o tipo de solicitação. Para calcular suas necessidades de DTU, multiplique 7,5 pelo total de [DWU](sql-data-warehouse-overview-what-is.md) necessário. Por exemplo, você gostaria de hospedar dois DW6000s em um SQL Server, então, deve solicitar uma cota DTU de 90.000.  Você pode exibir o consumo atual de DTU na folha do SQL Server no portal. Os bancos de dados em pausa e que não estão em pausa contam como a cota de DTU. 
   > 

1. Você pode ver soluções para ajudar a resolver o problema. Se as soluções apresentadas não resolverem o problema, selecione **Avançar: detalhes**. Envie os detalhes do seu problema e suas informações de contato. Selecione **Avançar: examinar + criar**
![detalhes](./media/sql-data-warehouse-get-started-create-support-ticket/details.png)

    
1. Examine suas informações e selecione **criar** na parte inferior do formulário para enviar a solicitação de suporte.

## <a name="monitor-a-support-ticket"></a>Monitorar um tíquete de suporte
Depois de enviar a solicitação de suporte, a equipe de suporte do Azure entrará em contato com você. Para verificar o status e os detalhes da solicitação, clique em **Todas as solicitações de suporte** no painel.

![Verificar o status](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Outros recursos
Você também pode se conectar com a Comunidade do SQL Data Warehouse no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) ou por meio do [fórum do MSDN SQL data warehouse do Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

 
