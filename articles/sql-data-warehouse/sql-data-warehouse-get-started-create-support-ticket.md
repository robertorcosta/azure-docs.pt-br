---
title: Como criar um tíquete de suporte
description: Como criar um tíquete de suporte no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 04fd6267782ab89e12288dadb64d28d1ef1746b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717827"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Como criar um tíquete de suporte para o SQL Data Warehouse
Se você tiver problemas com seu SQL Data Warehouse, crie um tíquete de suporte para que a equipe de suporte de engenharia possa ajudá-lo.

## <a name="create-a-support-ticket"></a>Criar um tíquete de suporte
1. Abra o [Portal do Azure](https://portal.azure.com/).
2. Na tela Inicial, clique na guia **Ajuda + suporte**.
   
    ![Ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. Na folha Ajuda + Suporte, clique em **Nova solicitação de suporte** e preencha a folha **Básico**.

   Selecione seu [Plano de suporte do Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * **Gerenciamento de assinaturas, cobrança e cotas** está disponível em todos os níveis de suporte.
   * O suporte à **correção de** conserto é fornecido por meio do suporte [Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)ou [Premier](https://azure.microsoft.com/support/plans/premier/) . Os problemas que requerem reparos são problemas vivenciados pelo cliente enquanto usa o Azure, onde se espera que o problema tenha sido causado pela Microsoft.
   * Os **Serviços** de consultoria e **aconselhamento para desenvolvedores** estão disponíveis nos níveis de suporte [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) e [Premier](https://azure.microsoft.com/support/plans/premier/) . 
     
     Se você tiver um plano de suporte Premier, também poderá relatar SQL Data Warehouse problemas relacionados no [portal do Microsoft Premier Online](https://premier.microsoft.com/). Consulte [planos de suporte do Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) para saber mais sobre os vários planos de suporte, incluindo escopo, tempos de resposta, preços, etc.  Para perguntas frequentes sobre o suporte do Azure, consulte [perguntas frequentes sobre o suporte do Azure](https://azure.microsoft.com/support/faq/).  
        
     ![Folha de Noções básicas](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![Folha1 Noções básicas](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Preencha a folha **Problema**.

    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Por padrão, cada SQL Server (por exemplo, myserver.database.windows.net) tem uma **Cota de DTU** de 45.000. Essa cota é simplesmente um limite de segurança. Você pode aumentar sua cota criando um tíquete de suporte e selecionando *Cota* como o tipo de solicitação. Para calcular suas necessidades de DTU, multiplique 7,5 pelo total de [DWU](sql-data-warehouse-overview-what-is.md) necessário. Por exemplo, você gostaria de hospedar dois DW6000s em um SQL Server, então, deve solicitar uma cota DTU de 90.000.  Você pode exibir o consumo atual de DTU na folha do SQL Server no portal. Os bancos de dados em pausa e que não estão em pausa contam como a cota de DTU. 
   > 
   > 
   
5. Preencha suas **informações de contato**.

   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Clique em **Criar** para enviar a solicitação de suporte.

## <a name="monitor-a-support-ticket"></a>Monitorar um tíquete de suporte
Depois de enviar a solicitação de suporte, a equipe de suporte do Azure entrará em contato com você. Para verificar o status e os detalhes da solicitação, clique em **Todas as solicitações de suporte** no painel.

![Verificar o status](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Outros recursos
Você também pode se conectar com a Comunidade do SQL Data Warehouse no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) ou por meio do [fórum do MSDN SQL data warehouse do Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

 
