---
title: Auditoria para conta de armazenamento por trás da VNet e do firewall
description: Configurar a auditoria para gravar eventos de banco de dados em uma conta de armazenamento por trás da rede virtual e do firewall
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80387603"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>Gravar auditoria em uma conta de armazenamento por trás da VNet e do firewall

A auditoria do [banco de dados SQL do Azure](sql-database-technical-overview.md) e [do Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) dá suporte à gravação de eventos de banco de dados em uma [conta de armazenamento do Azure](../storage/common/storage-account-overview.md) atrás de uma rede virtual e firewall 

Este artigo explica duas maneiras de configurar o Azure SQL Server e a conta de armazenamento do Azure para essa opção. O primeiro usa o portal do Azure, o segundo usa REST.

### <a name="background"></a>Tela de fundo

A [VNet (rede virtual) do Azure](../virtual-network/virtual-networks-overview.md) é o bloco de construção fundamental para sua rede privada no Azure. Ela permite vários tipos de recursos do Azure, como VMs (Máquinas Virtuais) do Azure, a fim de se comunicar de forma segura com a Internet, com as redes locais e com outras VMs. A VNet é semelhante a uma rede tradicional em sua própria data center, mas traz benefícios adicionais da infraestrutura do Azure, como escala, disponibilidade e isolamento.

Para saber mais sobre os conceitos de VNet, práticas recomendadas e muito mais, consulte [o que é a rede virtual do Azure](../virtual-network/virtual-networks-overview.md).

Para saber mais sobre como criar uma rede virtual, consulte [início rápido: criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para que a auditoria grave em uma conta de armazenamento por trás de uma VNet ou um firewall, os seguintes pré-requisitos são necessários:

> [!div class="checklist"]
> * Uma conta de armazenamento de uso geral v2. Se você tiver uma conta de armazenamento de blob v1 ou de uso geral, [atualize para uma conta de armazenamento v2 de uso geral](../storage/common/storage-account-upgrade.md). Para obter mais informações, consulte [tipos de contas de armazenamento](../storage/common/storage-account-overview.md#types-of-storage-accounts).
> * A conta de armazenamento deve estar na mesma assinatura e no mesmo local que o servidor do banco de dados SQL do Azure. 
> * A conta de armazenamento do `Allow trusted Microsoft services to access this storage account`Azure requer. Defina isso na conta de armazenamento **firewalls e redes virtuais**.
> * Você deve ter `Microsoft.Authorization/roleAssignments/write` permissão na conta de armazenamento selecionada. Para obter mais informações, consulte [funções internas do Azure](../role-based-access-control/built-in-roles.md).

## <a name="configure-in-azure-portal"></a>Configurar no portal do Azure

Conecte-se a [portal do Azure](https://portal.azure.com) com sua assinatura. Navegue até o grupo de recursos e o servidor de banco de dados SQL do Azure.

1. Clique em **auditoria** no título segurança. Selecione **ativado**.

2. Selecione **Armazenamento**. Selecione a conta de armazenamento na qual os logs serão salvos. A conta de armazenamento deve estar em conformidade com os requisitos listados em [pré-requisitos](#prerequisites).

3. Abrir **detalhes do armazenamento** 

  > [!NOTE]
  > Se a conta de armazenamento selecionada estiver atrás da VNet, você verá a seguinte mensagem:
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage: requires an Active Directory admin on the server; enables 'Allow trusted Microsoft services to access this storage account' on the storage account; and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >Se você não vir essa mensagem, a conta de armazenamento não está atrás de uma VNet.

4. Selecione o número de dias para o período de retenção. Em seguida, clique em **OK**. Os logs anteriores ao período de retenção são excluídos.

5. Selecione **salvar** nas configurações de auditoria.

Você configurou com êxito a auditoria para gravar em uma conta de armazenamento por trás de uma VNet ou um firewall. 

## <a name="configure-with-rest-commands"></a>Configurar com comandos REST

Como alternativa ao uso do portal do Azure, você pode usar comandos REST para configurar a auditoria para gravar eventos de banco de dados em uma conta de armazenamento por trás de uma VNet e um firewall. 

Os scripts de exemplo nesta seção exigem que você atualize o script antes de executá-los. Substitua os seguintes valores nos scripts:

|Valor de exemplo|Descrição de exemplo|
|:-----|:-----|
|`<subscriptionId>`| ID de assinatura do Azure|
|`<resource group>`| Resource group|
|`<sql database server>`| Nome do servidor do banco de dados SQL do Azure|
|`<administrator login>`| Conta de administrador do banco de dados SQL |
|`<complex password>`| Senha complexa para a conta de administrador|

Para configurar a auditoria do SQL para gravar eventos em uma conta de armazenamento por trás de uma VNet ou um firewall:

1. Registre seu servidor de banco de dados SQL do Azure com o Azure Active Directory (Azure AD). Use o PowerShell ou a API REST.

   **PowerShell**
   
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

2. Abra o [portal do Azure](https://portal.azure.com). Navegue até sua conta de armazenamento. Localize o **controle de acesso (iam)** e clique em **Adicionar atribuição de função**. Atribua a função de RBAC de **colaborador de dados de blob de armazenamento** à sua SQL Server do Azure que hospeda o banco de dado SQL do Azure que você registrou com Azure Active Directory (Azure AD) como na etapa anterior.

   > [!NOTE]
   > Somente membros com o privilégio Proprietário podem executar essa etapa. Para várias funções internas para recursos do Azure, consulte [funções internas do Azure](../role-based-access-control/built-in-roles.md).

3. Configure a [política de auditoria de blob do Azure SQL Server](/rest/api/sql/server%20auditing%20settings/createorupdate), sem especificar um *storageAccountAccessKey*:

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

- [Use o PowerShell para criar um ponto de extremidade de serviço de rede virtual e uma regra de rede virtual para o banco de dados SQL do Azure.](sql-database-vnet-service-endpoint-rule-powershell.md)
- [Regras de rede virtual: operações com APIs REST](/rest/api/sql/virtualnetworkrules)
- [Use os pontos de extremidade e regras de serviço de rede virtual para os servidores do banco de dados](sql-database-vnet-service-endpoint-rule-overview.md)
