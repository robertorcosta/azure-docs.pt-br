---
title: Solicitar um aumento de cota
description: Esta página descreve como criar uma solicitação de suporte para aumentar as cotas para bancos de dados únicos do Azure SQL Database, servidores e instâncias gerenciadas.
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: ff2be6972bb4e8af266d0aa8a56d1879bc1b8b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586148"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Aumentos de cotas de solicitação para o Banco de Dados SQL do Azure

Este artigo explica como solicitar um aumento de cota para o Banco de Dados SQL do Azure para bancos de dados únicos, servidores e instâncias gerenciadas. Também explica como permitir o acesso por assinatura a uma região.

## <a name="create-a-new-support-request"></a><a id="newquota"></a>Crie uma nova solicitação de suporte

Use as etapas a seguir para criar uma nova solicitação de suporte do portal Azure para banco de dados SQL.

1. No menu do [portal Azure,](https://portal.azure.com) selecione **Ajuda + suporte**.

   ![O link ajuda + suporte](./media/quota-increase-request/help-plus-support.png)

1. Em **Ajuda + suporte,** selecione **Nova solicitação de suporte**.

    ![Crie uma nova solicitação de suporte](./media/quota-increase-request/new-support-request.png)

1. Para **tipo de problema**, selecione **Limites de serviço e assinatura (cotas)**.

   ![Selecione um tipo de problema](./media/quota-increase-request/select-quota-issue-type.png)

1. Para **Assinatura**, selecione a assinatura cuja cota você deseja aumentar.

   ![Selecione uma assinatura para uma cota aumentada](./media/quota-increase-request/select-subscription-support-request.png)

1. Para **o tipo Cota,** selecione um dos seguintes tipos de cotas:

   - **Banco de dados SQL** para banco de dados único e cotas de pool elástico.
   - **SQL Database Managed Instance** para instâncias gerenciadas.

   Em seguida, **selecione Next: Soluções >>**.

   ![Selecione um tipo de cota](./media/quota-increase-request/select-quota-type.png)

1. Na janela **Detalhes,** selecione **Fornecer detalhes** para inserir informações adicionais.

   ![O link "Fornecer detalhes"](./media/quota-increase-request/provide-details-link.png)

Clicar **Em Fornecer detalhes** exibe a janela **De detalhes de Cotas** que permite adicionar informações adicionais. As seções a seguir descrevem as diferentes opções para os tipos de cotas de instância gerenciada do banco de dados **SQL** e **sql.**

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a>Tipos de cotas de banco de dados SQL

As seções a seguir descrevem três opções de aumento de cotas para os tipos de cotas **do Banco de Dados SQL:**

- Unidades de transação de banco de dados (DTUs) por servidor
- Servidores por assinatura
- Habilite o acesso por assinatura a uma região

### <a name="database-transaction-units-dtus-per-server"></a>Unidades de transação de banco de dados (DTUs) por servidor

Use as seguintes etapas para solicitar um aumento nas DTUs por servidor.

1. Selecione as **unidades de transação de banco de dados (DTUs) por** tipo de cota de servidor.

1. Na lista **Recursos,** selecione o recurso a ser direcionado.

1. No campo **De Cota Nova,** insira o novo limite de DTU que você está solicitando.

   ![Detalhes da cota dtu](./media/quota-increase-request/quota-details-dtus.png)

Para obter mais informações, consulte [limites de recursos para bancos de dados únicos usando o modelo de compras dTU](sql-database-dtu-resource-limits-single-databases.md) e limites de recursos para [piscinas elásticas usando o modelo de compra do DTU](sql-database-dtu-resource-limits-elastic-pools.md).

### <a name="servers-per-subscription"></a>Servidores por assinatura

Use as seguintes etapas para solicitar um aumento no número de servidores por assinatura.

1. Selecione os Servidores por tipo de cota **de assinatura.**

1. Na lista **Localização,** selecione a região Azure a ser usada. A cota é por assinatura em cada região.

1. No campo **Nova cota,** insira sua solicitação para o número máximo de servidores naquela região.

   ![Detalhes da cota de servidores](./media/quota-increase-request/quota-details-servers.png)

Para obter mais informações, consulte [os limites de recursos do Banco de Dados SQL e a governança dos recursos](sql-database-resource-limits-database-server.md).

### <a name="enable-subscription-access-to-a-region"></a><a id="other"></a>Habilite o acesso por assinatura a uma região

Alguns tipos de ofertas não estão disponíveis em todas as regiões. Você pode ver um erro como o seguinte:

`This location is not available for subscription`

Se sua assinatura precisar de acesso em uma determinada região, use a opção **Outra solicitação de cota** para solicitar acesso. Em sua solicitação, especifique os detalhes de oferta e SKU que você deseja habilitar para a região. Para explorar as opções de oferta e SKU, consulte [os preços do Banco de Dados Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/single/).

![Outros detalhes da cota](./media/quota-increase-request/quota-details-whitelisting.png)

## <a name="managed-instance-quota-type"></a><a id="sqlmiquota"></a>Tipo de cota de instância gerenciada

Para o tipo de cota **de instância gerenciada do sql server,** use as seguintes etapas:

1. Na lista **Região,** selecione a região Azure para atingir.

1. Digite os novos limites que você está solicitando para **Subnet** e **vCore**.

   ![Detalhes de cotas de instância gerenciadas](./media/quota-increase-request/quota-details-managed-instance.png)

Para obter mais informações, consulte ['Visão geral' do Azure SQL Database's managed instance resource limits](sql-database-managed-instance-resource-limits.md).

## <a name="submit-your-request"></a>Enviar a solicitação

O passo final é preencher os detalhes restantes da sua solicitação de cota de banco de dados SQL. Em **seguida, **selecione Next: Review + create>>, e depois de revisar os detalhes da solicitação, clique **em Criar** para enviar a solicitação.

## <a name="next-steps"></a>Próximas etapas

Depois de enviar sua solicitação, ela será revisada. Você será contatado com uma resposta com base nas informações que você forneceu no formulário.

Para obter mais informações sobre outros limites do Azure, consulte [os limites de assinatura e serviço do Azure, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md).
