---
title: Solicitar um aumento de cota
description: Esta página descreve como criar uma solicitação de suporte para aumentar as cotas para bancos de dados únicos, servidores e instâncias gerenciadas do banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: ff2be6972bb4e8af266d0aa8a56d1879bc1b8b78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77586148"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Aumentos de cota de solicitação para o banco de dados SQL do Azure

Este artigo explica como solicitar um aumento de cota para o banco de dados SQL do Azure para bancos de dados individuais, servidores e instâncias gerenciadas. Ele também explica como habilitar o acesso à assinatura para uma região.

## <a name="create-a-new-support-request"></a><a id="newquota"></a>Criar uma nova solicitação de suporte

Use as etapas a seguir para criar uma nova solicitação de suporte do portal do Azure para o banco de dados SQL.

1. No menu [portal do Azure](https://portal.azure.com) , selecione **ajuda + suporte**.

   ![O link ajuda + suporte](./media/quota-increase-request/help-plus-support.png)

1. Em **ajuda + suporte**, selecione **nova solicitação de suporte**.

    ![Criar uma nova solicitação de suporte](./media/quota-increase-request/new-support-request.png)

1. Para **tipo de problema**, selecione **Limites de serviço e assinatura (cotas)**.

   ![Selecione um tipo de problema](./media/quota-increase-request/select-quota-issue-type.png)

1. Para **assinatura**, selecione a assinatura cuja cota você deseja aumentar.

   ![Selecione uma assinatura para uma cota maior](./media/quota-increase-request/select-subscription-support-request.png)

1. Para **tipo de cota**, selecione um dos seguintes tipos de cota:

   - **Banco de dados SQL** para cotas de banco de dados individual e pool elástico.
   - **Instância gerenciada do banco de dados SQL** para instâncias gerenciadas.

   Em seguida, selecione **Avançar: soluções >>**.

   ![Selecionar um tipo de cota](./media/quota-increase-request/select-quota-type.png)

1. Na janela **detalhes** , selecione **fornecer detalhes** para inserir informações adicionais.

   ![O link "fornecer detalhes"](./media/quota-increase-request/provide-details-link.png)

Clicar em **fornecer detalhes** exibe a janela **detalhes da cota** que permite que você adicione informações adicionais. As seções a seguir descrevem as diferentes opções para o **banco de dados SQL** e **instância gerenciada do banco de dados SQL** tipos de cota.

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a>Tipos de cota do banco de dados SQL

As seções a seguir descrevem três opções de aumento de cota para os tipos de cota do **banco de dados SQL** :

- DTUs (unidades de transação de banco de dados) por servidor
- Servidores por assinatura
- Habilitar o acesso à assinatura para uma região

### <a name="database-transaction-units-dtus-per-server"></a>DTUs (unidades de transação de banco de dados) por servidor

Use as etapas a seguir para solicitar um aumento nas DTUs por servidor.

1. Selecione o tipo de cota **unidades de transação de banco de dados (DTUs) por servidor** .

1. Na lista de **recursos** , selecione o recurso para o destino.

1. No campo **nova cota** , insira o novo limite de DTU que você está solicitando.

   ![Detalhes da cota de DTU](./media/quota-increase-request/quota-details-dtus.png)

Para obter mais informações, consulte [limites de recursos para bancos de dados individuais usando o modelo de compra de DTU](sql-database-dtu-resource-limits-single-databases.md) e [os limites de recursos para pools elásticos usando o modelo de compra de DTU](sql-database-dtu-resource-limits-elastic-pools.md).

### <a name="servers-per-subscription"></a>Servidores por assinatura

Use as etapas a seguir para solicitar um aumento no número de servidores por assinatura.

1. Selecione o tipo de cota **servidores por assinatura** .

1. Na lista **local** , selecione a região do Azure a ser usada. A cota é por assinatura em cada região.

1. No campo **nova cota** , insira sua solicitação para o número máximo de servidores nessa região.

   ![Detalhes de cota de servidores](./media/quota-increase-request/quota-details-servers.png)

Para obter mais informações, consulte [limites de recursos do banco de dados SQL e governança de recursos](sql-database-resource-limits-database-server.md).

### <a name="enable-subscription-access-to-a-region"></a><a id="other"></a>Habilitar o acesso à assinatura para uma região

Alguns tipos de oferta não estão disponíveis em todas as regiões. Você pode ver um erro como o seguinte:

`This location is not available for subscription`

Se sua assinatura precisar de acesso em uma região específica, use a **outra** opção de solicitação de cota para solicitar acesso. Em sua solicitação, especifique a oferta e os detalhes de SKU que você deseja habilitar para a região. Para explorar as opções de oferta e SKU, consulte [preços do banco de dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

![Outros detalhes da cota](./media/quota-increase-request/quota-details-whitelisting.png)

## <a name="managed-instance-quota-type"></a><a id="sqlmiquota"></a>Tipo de cota de instância gerenciada

Para o tipo de cota de **SQL Server instância gerenciada** , use as seguintes etapas:

1. Na lista **região** , selecione a região do Azure para o destino.

1. Insira os novos limites que você está solicitando para a **sub-rede** e **vCore**.

   ![Detalhes da cota de instância gerenciada](./media/quota-increase-request/quota-details-managed-instance.png)

Para obter mais informações, consulte [visão geral limites de recursos de instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-resource-limits.md).

## <a name="submit-your-request"></a>Enviar a solicitação

A etapa final é preencher os detalhes restantes de sua solicitação de cota do banco de dados SQL. Em seguida, selecione **Avançar: revisar + criar>>** e, depois de examinar os detalhes da solicitação, clique em **criar** para enviar a solicitação.

## <a name="next-steps"></a>Próximas etapas

Depois de enviar sua solicitação, ela será revisada. Você será contatado com uma resposta com base nas informações fornecidas no formulário.

Para obter mais informações sobre outros limites do Azure, consulte [assinatura e limites de serviço, cotas e restrições do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).
