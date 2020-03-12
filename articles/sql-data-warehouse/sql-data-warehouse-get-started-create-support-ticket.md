---
title: A cota de solicitação aumenta e obtém suporte
description: Como criar uma solicitação de suporte no portal do Azure para o Azure Synapse Analytics. A cota de solicitação aumenta ou obtém suporte à resolução de problemas.
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 88cc737fddfdd896575227e036df087c13a4d0f6
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79130139"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>A cota de solicitação aumenta e obtém suporte para o Azure Synapse Analytics

Este artigo descreve como enviar um tíquete de suporte no portal do Azure para o Azure Synapse Analytics. Esse processo permite que você solicite um aumento de cota ou envie uma solicitação de suporte técnico para a equipe de suporte de engenharia.

## <a name="create-a-support-ticket"></a>Criar um tíquete de suporte

Use as etapas a seguir para criar uma nova solicitação de suporte do portal do Azure para o Azure Synapse Analytics.

1. No menu [portal do Azure](https://portal.azure.com) , selecione **ajuda + suporte**.

   ![O link ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. Em **ajuda + suporte**, selecione **nova solicitação de suporte**.

    ![Criar uma nova solicitação de suporte](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Examine o [plano de suporte do Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * **Gerenciamento de assinaturas, cobrança e cotas** está disponível em todos os níveis de suporte.
   * O suporte à **correção de** conserto é fornecido por meio do suporte [Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)ou [Premier](https://azure.microsoft.com/support/plans/premier/) . As questões que exigem reparos são problemas vivenciados pelo cliente ao usar o Azure, em que se espera que o problema tenha sido causado pela Microsoft.
   * Os **Serviços** de consultoria e **aconselhamento para desenvolvedores** estão disponíveis nos níveis de suporte [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) e [Premier](https://azure.microsoft.com/support/plans/premier/) .

   Se você tiver um plano de suporte Premier, também poderá relatar problemas do Azure Synapse Analytics no [portal online do Microsoft Premier](https://premier.microsoft.com/). Consulte [planos de suporte do Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) para saber mais sobre os vários planos de suporte, incluindo escopo, tempos de resposta, preços, etc.  Para perguntas frequentes sobre o suporte do Azure, consulte [perguntas frequentes sobre o suporte do Azure](https://azure.microsoft.com/support/faq/).

1. Para **tipo de problema**, selecione o tipo de problema apropriado. Para problemas de conserto e correção, selecione **técnico**. Para solicitações de aumento de cota, selecione **limites de serviço e de assinatura (cotas)** .

   ![Selecione um tipo de problema](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > Este restante deste artigo aborda as solicitações de aumento de cota. Mas você também pode selecionar **técnico** aqui para solicitações de suporte à resolução de problemas. Se você selecionar **técnico**, será solicitado a fornecer um resumo e, em seguida, identificar um tipo de problema selecionando **Selecionar tipo de problema**. Você pode ver soluções para ajudar a resolver o problema. Se as soluções apresentadas não resolverem o problema, selecione **Avançar: detalhes** e preencha o formulário para enviar o tíquete de suporte.

1. Para solicitações de aumento de cota, selecione **Azure Synapse Analytics** para o **tipo de cota**. Em seguida, selecione **Avançar: soluções > >** .

   ![Selecionar um tipo de cota](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. Na janela **detalhes** , selecione **fornecer detalhes** para inserir informações adicionais.

   ![O link "fornecer detalhes"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Tipos de solicitação de cota

Clicar em **fornecer detalhes** exibe a janela **detalhes da cota** que permite que você adicione informações adicionais. As seções a seguir descrevem as diferentes solicitações de cota disponíveis para o Azure Synapse Analytics.

### <a name="data-warehouse-units-dwus-per-server"></a>Unidades de data warehouse (DWUs) por servidor

Use as etapas a seguir para solicitar um aumento no DWUs por servidor.

1. Selecione as **unidades de data warehouse (DTUs) por** tipo de cota de servidor.

1. Na lista de **recursos** , selecione o recurso para o destino.

1. No campo **cota de solicitação** , insira o novo limite de DWU que você está solicitando.

   ![Detalhes da cota de DWU](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Servidores por assinatura

Use as etapas a seguir para solicitar um aumento no número de servidores por assinatura.

1. Selecione o tipo de cota **servidores por assinatura** .

1. Na lista **local** , selecione a região do Azure a ser usada. A cota é por assinatura em cada região.

1. No campo **nova cota** , insira sua solicitação para o número máximo de servidores nessa região.

   ![Detalhes de cota de servidores](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Habilitar o acesso à assinatura para uma região

Alguns tipos de oferta não estão disponíveis em todas as regiões. Você pode ver um erro como o seguinte:

`This location is not available for subscription`

Se sua assinatura precisar de acesso em uma região específica, use a **outra** opção de solicitação de cota para solicitar acesso. Em sua solicitação, especifique a oferta e os detalhes de SKU que você deseja habilitar para a região. Para explorar as opções de oferta e SKU, consulte [preços do Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

![Outros detalhes da cota](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Enviar a solicitação

A etapa final é preencher os detalhes restantes de sua solicitação de suporte do banco de dados SQL. Em seguida, selecione **Avançar: revisar + criar > >** e, depois de examinar os detalhes da solicitação, clique em **criar** para enviar a solicitação.

## <a name="monitor-a-support-ticket"></a>Monitorar um tíquete de suporte

Depois de enviar a solicitação de suporte, a equipe de suporte do Azure entrará em contato com você. Para verificar o status e os detalhes da solicitação, clique em **Todas as solicitações de suporte** no painel.

![Verificar o status](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Outros recursos

Você também pode se conectar com a Comunidade do Azure Synapse Analytics em [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) ou por meio do [fórum do MSDN SQL data warehouse do Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

