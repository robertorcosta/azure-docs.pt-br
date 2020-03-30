---
title: Solicitar aumento sustais e obter suporte
description: Como criar uma solicitação de suporte no portal Azure para o Azure Synapse Analytics. Solicitar aumento de cota ou obter suporte de resolução de problemas.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: eaa6ceded9893b278b28d517cbddb303f8469e10
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350890"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>A cota de solicitação aumenta e obtenha suporte para o Azure Synapse Analytics

Este artigo descreve como enviar um bilhete de suporte no portal Azure para a Azure Synapse Analytics. Este processo permite que você solicite um aumento de cota ou envie uma solicitação de suporte técnico para a equipe de suporte de engenharia.

## <a name="create-a-support-ticket"></a>Criar um tíquete de suporte

Use as etapas a seguir para criar uma nova solicitação de suporte do portal Azure para o Azure Synapse Analytics.

1. No menu do [portal Azure,](https://portal.azure.com) selecione **Ajuda + suporte**.

   ![O link ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. Em **Ajuda + suporte,** selecione **Nova solicitação de suporte**.

    ![Crie uma nova solicitação de suporte](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Revise [seu plano de suporte do Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * **Gerenciamento de assinaturas, cobrança e cotas** está disponível em todos os níveis de suporte.
   * O suporte **a correção de quebra** é fornecido através do suporte ao [Desenvolvedor,](https://azure.microsoft.com/support/plans/developer/) [Padrão,](https://azure.microsoft.com/support/plans/standard/) [Profissional Direto](https://azure.microsoft.com/support/plans/prodirect/)ou [Premier.](https://azure.microsoft.com/support/plans/premier/) As questões que exigem reparos são problemas vivenciados pelo cliente ao usar o Azure, em que se espera que o problema tenha sido causado pela Microsoft.
   * O **aconselhamento de desenvolvedores** e os **serviços de consultoria** estão disponíveis nos níveis de suporte [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) e [Premier](https://azure.microsoft.com/support/plans/premier/).

   Se você tiver um plano de suporte Premier, você também pode relatar problemas do Azure Synapse Analytics no [portal online microsoft premier](https://premier.microsoft.com/). Consulte os planos de suporte do [Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) para saber mais sobre os vários planos de suporte, incluindo escopo, tempos de resposta, preços, etc.  Para perguntas frequentes sobre o suporte ao Azure, consulte [perguntas frequentes sobre o suporte ao Azure](https://azure.microsoft.com/support/faq/).

1. Para **o tipo De sumário,** selecione o tipo de problema apropriado. Para problemas de correção de quebra, selecione **Técnico**. Para solicitações de aumento de cotas, selecione **Limites de Serviço e assinatura (cotas)**.

   ![Selecione um tipo de problema](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > Este restante deste artigo se concentra em pedidos de aumento de cotas. Mas você também pode selecionar **Técnico** aqui para solicitações de suporte à resolução de problemas. Se você selecionar **Técnico,** você será solicitado a fornecer um resumo e, em seguida, identificar um tipo de problema selecionando **Selecionar tipo de problema**. Você pode ver soluções para ajudar a resolver seu problema. Se as soluções apresentadas não resolverem seu problema, selecione **Next:Details** e preencha o formulário para enviar o ticket de suporte.

1. Para solicitações de aumento de cotas, selecione **Azure Synapse Analytics** para o **tipo Quota**. Em seguida, **selecione Next: Soluções >>**.

   ![Selecione um tipo de cota](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. Na janela **Detalhes,** selecione **Fornecer detalhes** para inserir informações adicionais.

   ![O link "Fornecer detalhes"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Tipos de solicitação de cotas

Clicar **Em Fornecer detalhes** exibe a janela **De detalhes de Cotas** que permite adicionar informações adicionais. As seções a seguir descrevem as diferentes solicitações de cotas disponíveis para o Azure Synapse Analytics.

### <a name="data-warehouse-units-dwus-per-server"></a>Unidades de Data Warehouse (DWUs) por servidor

Use as seguintes etapas para solicitar um aumento nas DWUs por servidor.

1. Selecione as **DTUs (Data Warehouse Units, unidades** de armazenamento de dados) por tipo de cota de servidor.

1. Na lista **Recursos,** selecione o recurso a ser direcionado.

1. No campo **De cota de solicitação,** digite o novo limite DWU que você está solicitando.

   ![Detalhes da cota DWU](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Servidores por assinatura

Use as seguintes etapas para solicitar um aumento no número de servidores por assinatura.

1. Selecione os Servidores por tipo de cota **de assinatura.**

1. Na lista **Localização,** selecione a região Azure a ser usada. A cota é por assinatura em cada região.

1. No campo **Nova cota,** insira sua solicitação para o número máximo de servidores naquela região.

   ![Detalhes da cota de servidores](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Habilite o acesso por assinatura a uma região

Alguns tipos de ofertas não estão disponíveis em todas as regiões. Você pode ver um erro como o seguinte:

`This location is not available for subscription`

Se sua assinatura precisar de acesso em uma determinada região, use a opção **Outra solicitação de cota** para solicitar acesso. Em sua solicitação, especifique os detalhes de oferta e SKU que você deseja habilitar para a região. Para explorar as opções de oferta e SKU, consulte os preços do [Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

![Outros detalhes da cota](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Enviar a solicitação

O passo final é preencher os detalhes restantes da sua solicitação de suporte ao Banco de Dados SQL. Em **seguida, **selecione Next: Review + create>>, e depois de revisar os detalhes da solicitação, clique **em Criar** para enviar a solicitação.

## <a name="monitor-a-support-ticket"></a>Monitorar um tíquete de suporte

Depois de enviar a solicitação de suporte, a equipe de suporte do Azure entrará em contato com você. Para verificar o status e os detalhes da solicitação, clique em **Todas as solicitações de suporte** no painel.

![Verificar o status](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Outros recursos

Você também pode se conectar com a comunidade Azure Synapse Analytics no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) ou através do [fórum MSDN do Azure SQL Data Warehouse](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

