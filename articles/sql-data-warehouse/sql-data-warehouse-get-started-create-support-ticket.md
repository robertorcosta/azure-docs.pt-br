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
ms.openlocfilehash: 12a977e26f458fa8ee8a58c44985d9d1ae47d5c3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692727"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Como criar um tíquete de suporte para o SQL Data Warehouse
Caso você tenha problemas com o SQL Data Warehouse, crie um tíquete de suporte para que nossa equipe de suporte de engenharia possa ajudar você.

## <a name="create-a-support-ticket"></a>Criar um tíquete de suporte
1. Abra o [Portal do Azure][Azure portal].
2. Na tela Inicial, clique na guia **Ajuda + suporte**.
   
    ![Ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. Na folha Ajuda + Suporte, clique em **Nova solicitação de suporte** e preencha a folha **Básico**.

   Selecione seu [Plano de suporte do Azure][Azure support plan].
   
   * **Gerenciamento de assinaturas, cobrança e cotas** está disponível em todos os níveis de suporte.
   * O suporte à **correção de** conserto é fornecido por meio do suporte [Developer][Developer], [Standard][Standard], [Professional Direct][Professional Direct]ou [Premier][Premier] . As questões que exigem reparos são problemas vivenciados pelo cliente ao usar o Azure, em que se espera que o problema tenha sido causado pela Microsoft.
   * Os **Serviços** de consultoria e **aconselhamento para desenvolvedores** estão disponíveis nos níveis de suporte [Professional Direct][Professional Direct] e [Premier][Premier] . 
     
     Se você tiver um plano de suporte Premier, também poderá relatar SQL Data Warehouse problemas relacionados no [portal do Microsoft Premier Online][Microsoft Premier online portal].  Consulte [planos de suporte do Azure][Azure support plan] para saber mais sobre os vários planos de suporte, incluindo escopo, tempos de resposta, preços, etc.  Para perguntas frequentes sobre o suporte do Azure, consulte [perguntas frequentes sobre o suporte do Azure][Azure support FAQs].  
        
     ![Folha de Noções básicas](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![Folha1 Noções básicas](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Preencha a folha **Problema**.
    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Por padrão, cada SQL Server (por exemplo, myserver.database.windows.net) tem uma **Cota de DTU** de 45.000. Essa cota é simplesmente um limite de segurança. Você pode aumentar sua cota criando um tíquete de suporte e selecionando *Cota* como o tipo de solicitação. Para calcular suas necessidades de DTU, multiplique 7,5 pelo total de [DWU][DWU] necessário. Por exemplo, você gostaria de hospedar dois DW6000s em um SQL Server, então, deve solicitar uma cota DTU de 90.000.  Você pode exibir o consumo atual de DTU na folha do SQL Server no portal. Os bancos de dados em pausa e que não estão em pausa contam como a cota de DTU. 
   > 
   > 
   
5. Preencha suas **informações de contato**.
   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Clique em **Criar** para enviar a solicitação de suporte.

## <a name="monitor-a-support-ticket"></a>Monitorar um tíquete de suporte
Depois que você enviar a solicitação de suporte, a equipe de suporte do Azure entrará em contato com você. Para verificar o status e os detalhes da solicitação, clique em **Todas as solicitações de suporte** no painel.

![Verificar o status](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Outros recursos
Além disso, você pode se conectar com a Comunidade do SQL Data Warehouse no [Stack Overflow][Stack Overflow] ou no [fórum do MSDN SQL data warehouse do Azure][Azure SQL Data Warehouse MSDN forum].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

