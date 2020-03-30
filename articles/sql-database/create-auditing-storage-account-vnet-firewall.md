---
title: Auditoria para conta de armazenamento por trás do VNet e firewall
description: Configure auditoria para gravar eventos de banco de dados em uma conta de armazenamento por trás de rede virtual e firewall
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/19/2020
ms.custom: azure-synapse
ms.openlocfilehash: 6345d210e26747f921595039a2a3c8e11be11fda
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387603"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Escreva auditoria em uma conta de armazenamento por trás do VNet e firewall

A auditoria para [o Banco de Dados SQL do Azure](sql-database-technical-overview.md) e o [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) suporta a gravação de eventos de banco de dados em uma [conta do Azure Storage](../storage/common/storage-account-overview.md) por trás de uma rede virtual e firewall. 

Este artigo explica duas maneiras de configurar a conta de armazenamento Azure SQL server e Azure para essa opção. O primeiro usa o portal Azure, o segundo usa REST.

### <a name="background"></a>Segundo plano

[A Rede Virtual (VNet)](../virtual-network/virtual-networks-overview.md) é o bloco de construção fundamental para sua rede privada no Azure. Ela permite vários tipos de recursos do Azure, como VMs (Máquinas Virtuais) do Azure, a fim de se comunicar de forma segura com a Internet, com as redes locais e com outras VMs. O VNet é semelhante a uma rede tradicional em seu próprio data center, mas traz consigo benefícios adicionais da infra-estrutura do Azure, como escala, disponibilidade e isolamento.

Para saber mais sobre os conceitos de VNet, Melhores Práticas e muito mais, consulte [O que é a Rede Virtual Azure](../virtual-network/virtual-networks-overview.md).

Para saber mais sobre como criar uma rede virtual, consulte [Quickstart: Crie uma rede virtual usando o portal Azure](../virtual-network/quick-create-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para que a auditoria seja feita para gravar em uma conta de armazenamento atrás de um VNet ou firewall, são necessários os seguintes pré-requisitos:

> [!div class="checklist"]
> * Uma conta de armazenamento v2 de uso geral. Se você tiver uma conta de armazenamento v1 ou blob de uso geral, [atualize para uma conta de armazenamento v2 de uso geral.](../storage/common/storage-account-upgrade.md) Para obter mais informações, consulte [Tipos de contas de armazenamento](../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * A conta de armazenamento deve estar na mesma assinatura e no mesmo local que o servidor Azure SQL Database. 
> * A conta Azure `Allow trusted Microsoft services to access this storage account`Storage requer . Defina isso nos firewalls de contas **de armazenamento e redes virtuais.**
> * Você deve `Microsoft.Authorization/roleAssignments/write` ter permissão na conta de armazenamento selecionada. Para obter mais informações, consulte [funções incorporadas do Azure](../role-based-access-control/built-in-roles.md).

## <a name="configure-in-azure-portal"></a>Configurar no portal do Azure

Conecte-se [ao portal Azure](https://portal.azure.com) com sua assinatura. Navegue até o grupo de recursos e o servidor de banco de dados Azure SQL.

1. Clique em **Auditoria** o título Segurança. Selecione **Em**.

2. Selecione **Armazenamento**. Selecione a conta de armazenamento onde os logs serão salvos. A conta de armazenamento deve estar em conformidade com os requisitos listados nos [Pré-requisitos](#prerequisites).

3. **Detalhes de armazenamento aberto** 

  > [!NOTE]
  > Se a conta de armazenamento selecionada estiver por trás do VNet, você verá a seguinte mensagem:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage: requires an Active Directory admin on the server; enables 'Allow trusted Microsoft services to access this storage account' on the storage account; and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Se você não ver esta mensagem, então a conta de armazenamento não está atrás de um VNet.

4. Selecione o número de dias para o período de retenção. Em seguida, clique em **OK**. Os registros mais antigos do que o período de retenção são excluídos.

5. Selecione **Salvar** em suas configurações de auditoria.

Você configurou com sucesso a auditoria para gravar em uma conta de armazenamento atrás de um VNet ou firewall. 

## <a name="configure-with-rest-commands"></a>Configurar com comandos REST

Como alternativa ao uso do portal Azure, você pode usar os comandos REST para configurar auditoria para gravar eventos de banco de dados em uma conta de armazenamento atrás de um VNet e firewall. 

Os scripts de exemplo nesta seção exigem que você atualize o script antes de executá-los. Substitua os seguintes valores nos scripts:

|Valor de exemplo|Descrição da amostra|
|:-----|:-----|
|`<subscriptionId>`| ID de assinatura do Azure|
|`<resource group>`| Resource group|
|`<sql database server>`| Nome do servidor do banco de dados SQL do Azure|
|`<administrator login>`| Conta de administrador de banco de dados SQL |
|`<complex password>`| Senha complexa para a conta do administrador|

Para configurar a Auditoria SQL para gravar eventos em uma conta de armazenamento por trás de um VNet ou Firewall:

1. Registre seu servidor de banco de dados Azure SQL com o Azure Active Directory (Azure AD). Use powershell ou API REST.

   **Powershell**
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```
   
   [**API REST**](https://docs.microsoft.com/rest/api/sql/servers/createorupdate):

   Solicitação de exemplo

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   Corpo da solicitação

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
   }
   ```

2. Abra o [portal do Azure](https://portal.azure.com). Navegue até sua conta de armazenamento. Localizar **controle de acesso (IAM)** e clicar em Adicionar **atribuição de função**. Atribuir **o RBAC do Contribuinte de Dados Blob** de Armazenamento ao seu Azure SQL Server hospedando seu banco de dados Azure SQL que você registrou no Azure Active Directory (Azure AD) como na etapa anterior.

   > [!NOTE]
   > Somente membros com o privilégio Proprietário podem executar essa etapa. Para várias funções incorporadas para os recursos do Azure, consulte [as funções incorporadas do Azure](../role-based-access-control/built-in-roles.md).

3. Configure [a política de auditoria blob do servidor Azure SQL,](/rest/api/sql/server%20auditing%20settings/createorupdate)sem especificar um *armazenamentoAccountAccessKey*:

   Solicitação de exemplo

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2017-03-01-preview
   ```

   Corpo da solicitação

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="next-steps"></a>Próximas etapas

- [Use o PowerShell para criar um ponto final de serviço de rede virtual e, em seguida, uma regra de rede virtual para o Banco de Dados SQL do Azure.](sql-database-vnet-service-endpoint-rule-powershell.md)
- [Regras da rede virtual: operações com APIs REST](/rest/api/sql/virtualnetworkrules)
- [Use os pontos de extremidade e regras de serviço de rede virtual para os servidores do banco de dados](sql-database-vnet-service-endpoint-rule-overview.md)
