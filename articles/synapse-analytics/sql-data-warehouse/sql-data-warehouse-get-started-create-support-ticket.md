---
title: Solicite aumentos de cota e obtenha suporte
description: Como criar uma solicitação de suporte para o Azure Synapse Analytics no portal do Azure. Solicitar aumentos de cota ou obter suporte para resoluções de problemas.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: e0788f978fd25356b230a7923def6cbbea3dc305
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835454"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Solicitar aumentos de cota ou obter suporte para o Azure Synapse Analytics

Este artigo descreve como solicitar um tíquete de suporte para o Azure Synapse Analytics no portal do Azure. Esse processo permite que você solicite um aumento de cota ou envie uma solicitação de suporte técnico para a equipe de suporte de engenharia.

## <a name="create-a-support-ticket"></a>Criar um tíquete de suporte

Siga as etapas a seguir para criar uma nova solicitação de suporte para o Azure Synapse Analytics no portal do Azure.

1. No menu do [Portal do Azure](https://portal.azure.com), selecione **Ajuda + suporte**.

   ![Link de ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. Em **Ajuda + suporte**, selecione **Nova solicitação de suporte**.

    ![Criar uma solicitação de suporte](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Examinar seu [plano de suporte do Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * **Gerenciamento de assinaturas, cobrança e cotas** está disponível em todos os níveis de suporte.
   * O suporte de **reparo** é fornecido por meio do suporte para o [Desenvolvedor](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) ou [Premier](https://azure.microsoft.com/support/plans/premier/). As questões que exigem reparos são problemas vivenciados pelo cliente ao usar o Azure, em que se espera que o problema tenha sido causado pela Microsoft.
   * O **aconselhamento de desenvolvedores** e os **serviços de consultoria** estão disponíveis nos níveis de suporte [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) e [Premier](https://azure.microsoft.com/support/plans/premier/).

   Se você tem um plano de suporte Premier, também pode relatar problemas do Azure Synapse Analytics no portal do [Microsoft Premier Online](https://premier.microsoft.com/). Confira os [planos de suporte do Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) para saber mais sobre os vários planos de suporte, incluindo escopos, tempos de resposta, preços etc.  Para ver as perguntas frequentes sobre esse tipo de suporte, confira [Perguntas frequentes do suporte do Azure](https://azure.microsoft.com/support/faq/).

1. Em **Tipo de problema**, selecione o tipo apropriado. Caso o problema seja relacionado a consertos e correções, selecione **Problemas técnicos**. Para solicitações de aumento de cota, selecione **Limites de serviço e assinatura (cotas)** .

   ![Selecione um tipo de problema](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > O restante deste artigo aborda as solicitações de aumento de cota. Mas você também pode selecionar **Problemas técnicos** aqui para solicitações de suporte à resolução de problemas. Se você selecionar **Problemas técnicos**, precisará fornecer um resumo e identificar o tipo de problema escolhendo **Selecionar tipo de problema**. Você verá as opções de tipos de problemas e poderá escolher o seu. Se as soluções apresentadas não se relacionam com seu problema, selecione **Avançar:Detalhes** e preencha o formulário para enviar o tíquete de suporte.

1. Para solicitações de aumento de cota, selecione **Azure Synapse Analytics** para ver o **Tipo de cota**. Em seguida, selecione **Avançar: Soluções >>** .

   ![Selecionar um tipo de cota](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. Na janela **Detalhes**, selecione **Inserir detalhes** para incluir informações adicionais.

   ![Link "fornecer detalhes"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Tipos de solicitação de cota

Clicar em **Fornecer detalhes** exibirá a janela **Detalhes da cota**, que permite que você inclua informações adicionais. As seções a seguir descrevem as diferentes solicitações de cota disponíveis para o Azure Synapse Analytics.

### <a name="data-warehouse-units-dwus-per-server"></a>Unidades de Data Warehouse (DWUs) por servidor

Siga as etapas a seguir para solicitar um aumento de DWUs por servidor.

1. Selecione o tipo de cota de **DWUs (Unidades de Data Warehouse) por servidor**.

1. Na lista de **Recursos**, selecione o recurso a de destino.

1. No campo **Solicitar cota**, insira o novo limite de DWU que você está solicitando.

   ![Detalhes da cota de DWU](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Servidores por assinatura

Siga as etapas a seguir para solicitar um aumento no número de servidores por assinatura.

1. Selecione o tipo de cota de **Servidores por assinatura**.

1. Na lista de **Locais**, selecione a região do Azure a ser usada. A cota é relativa a cada região e por assinatura.

1. No campo **Nova cota**, insira sua solicitação até o número máximo de servidores nessa região.

   ![Detalhes de cota de servidores](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Habilitar o acesso à assinatura para uma região

Nem todos os tipos de oferta estão disponíveis em todas as regiões. Nesse caso, é possível que você veja o seguinte erro:

`This location is not available for subscription`

Se sua assinatura precisar de acesso em uma região específica, opte por **Outra solicitação de cota** para solicitar acesso. Em sua solicitação, especifique a oferta e os detalhes de SKU que você deseja habilitar para a região. Para explorar as opções de oferta e SKU, confira [Preços do Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

![Outros detalhes da cota](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Enviar a solicitação

A etapa final é preencher os detalhes restantes da sua solicitação de suporte do Banco de Dados SQL do Microsoft Azure. Em seguida, selecione **Avançar: Examinar + criar>>** e, depois de revisar os detalhes da solicitação, clique em **Criar** para enviar a solicitação.

## <a name="monitor-a-support-ticket"></a>Monitorar um tíquete de suporte

Depois que você enviar a solicitação de suporte, a equipe de suporte do Azure entrará em contato com você. Para verificar o status e os detalhes da solicitação, clique em **Todas as solicitações de suporte** no painel.

![Verificar o status](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Outros recursos

Você também pode se conectar à comunidade do Azure Synapse Analytics no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) ou na página de [Perguntas frequentes sobre o SQL Data Warehouse do Microsoft Azure](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html).

