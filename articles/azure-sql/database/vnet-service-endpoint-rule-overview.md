---
title: Pontos de extremidade de rede virtual e regras para bancos de dados no Azure SQL Database
description: Marque uma sub-rede como um ponto de extremidade de serviço de Rede virtual. Em seguida, o ponto de extremidade como uma regra de rede virtual para a ACL do seu banco de dados. Em seguida, seu banco de dados aceita comunicação de todas as máquinas virtuais e de outros nós na sub-rede.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: c5839589c35ea5a9c52303801a8767fc598434fc
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905869"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Usar pontos de extremidade de serviço de rede virtual e regras para servidores no banco de dados SQL do Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*Regras de rede virtual* são um recurso de segurança de firewall que controla se o servidor para seus bancos de dados e pools elásticos no [banco de dados SQL do Azure](sql-database-paas-overview.md) ou em seus bancos de dados no [Azure Synapse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) aceita comunicações enviadas de sub-redes específicas em redes virtuais. Este artigo explica por que o recurso de regra de rede virtual às vezes é a melhor opção para permitir a comunicação com segurança com seu banco de dados no banco de dados SQL do Azure e no Azure Synapse Analytics.

> [!NOTE]
> Este artigo aplica-se ao Banco de Dados SQL do Azure e ao Azure Synapse Analytics. Para simplificar, o termo "banco de dados" se refere aos bancos de dados no Banco de Dados SQL do Azure e no Azure Synapse Analytics. Da mesma forma, as referências a "servidor" se referem ao [servidor SQL lógico](logical-servers.md) que hospeda o Banco de Dados SQL do Azure e o Azure Synapse Analytics.

Para criar uma regra da rede virtual, primeiro, é preciso que haja um [ponto de extremidade de serviço de rede virtual][vm-virtual-network-service-endpoints-overview-649d] para a regra de referência.

## <a name="how-to-create-a-virtual-network-rule"></a>Como criar uma regra da rede virtual

Se você só criar uma regra da rede virtual, poderá pular para as etapas e explicação [posteriores neste artigo](#anchor-how-to-by-using-firewall-portal-59j).

## <a name="details-about-virtual-network-rules"></a>Detalhes sobre as regras da rede virtual

Esta seção descreve vários detalhes sobre as regras da rede virtual.

### <a name="only-one-geographic-region"></a>Apenas uma região geográfica

Cada ponto de extremidade de serviço de rede virtual se aplica a apenas uma região do Azure. O ponto de extremidade não permite que outras regiões aceitem a comunicação da sub-rede.

Qualquer regra da rede virtual é limitada à região à qual seu ponto de extremidade subjacente se aplica.

### <a name="server-level-not-database-level"></a>Nível de servidor, não o nível de banco de dados

Cada regra de rede virtual se aplica a todo o servidor, não apenas a um banco de dados específico no servidor. Em outras palavras, a regra da rede virtual se aplica no nível de servidor, não no nível do banco de dados.

- Por outro lado, as regras de IP podem ser aplicadas em qualquer nível.

### <a name="security-administration-roles"></a>Funções de administração de segurança

Há uma separação de funções de segurança na administração de pontos de extremidade de serviço de rede virtual. A ação é necessária em cada uma das seguintes funções:

- **Administrador de rede:** &nbsp; ativar o ponto de extremidade.
- **Administrador do banco de dados:** &nbsp; Atualize a lista de controle de acesso (ACL) para adicionar a sub-rede fornecida ao servidor.

*Alternativa do RBAC do Azure:*

As funções de Administrador de banco de dados e Administrador de rede têm mais recursos do que o necessário para gerenciar regras da rede virtual. É necessário apenas um subconjunto de seus recursos.

Você tem a opção de usar o [controle de acesso baseado em função do Azure (RBAC do Azure)][rbac-what-is-813s] no Azure para criar uma única função personalizada que tem apenas o subconjunto necessário de recursos. A função personalizada pode ser usada em vez de envolver o administrador de rede ou o administrador de banco de dados. A área da superfície de sua exposição de segurança será menor se você adicionar um usuário a uma função personalizada, em vez de adicionar o usuário às outras duas principais funções de administrador.

> [!NOTE]
> Em alguns casos, o banco de dados no banco de dados SQL do Azure e a sub-rede VNet estão em assinaturas diferentes. Nesses casos, você deve garantir as seguintes configurações:
>
> - Ambas as assinaturas devem estar associadas ao mesmo locatário do Azure Active Directory.
> - O usuário tem as permissões necessárias para iniciar operações, como habilitar pontos de extremidade de serviço e adicionar uma sub-rede da VNet ao servidor.
> - Ambas as assinaturas devem ter o provedor Microsoft.Sql registrado.

## <a name="limitations"></a>Limitações

Para o Banco de Dados SQL do Azure, o recurso de regras da rede virtual tem as seguintes limitações:

- No firewall para seu banco de dados no banco de dados SQL do Azure, cada regra de rede virtual faz referência a uma sub-rede. Todas essas sub-redes referenciadas devem ser hospedadas na mesma região geográfica que hospeda o banco de dados.

- Cada servidor pode ter até 128 entradas de ACL para qualquer rede virtual específica.

- As regras da rede virtual se aplicam somente a redes virtuais do Azure Resource Manager; e não a redes do [modelo de implantação clássico][arm-deployment-model-568f].

- A ativação de pontos de extremidade de serviço de rede virtual no Banco de Dados SQL do Azure também habilita os pontos de extremidade para os serviços MySQL e PostgreSQL do Azure. No entanto, com os pontos de extremidade ativados, as tentativas de conexão dos pontos de extremidade com as instâncias do MySQL ou PostgreSQL provavelmente falharão.
  - O motivo subjacente é que o MySQL e o PostgreSQL provavelmente não têm uma regra da rede virtual configurada. Você precisará configurar uma regra da rede virtual para o Banco de Dados do Azure para MySQL e PostgreSQL e então a conexão será bem-sucedida.
  - Para definir regras de firewall de VNet em um servidor lógico do SQL que já está configurado com pontos de extremidade privados, defina **negar acesso à rede pública** como **não**.

- No firewall, os intervalos de endereços IP se aplicam aos seguintes itens de rede, mas as regras da rede virtual não:
  - [Rede privada virtual (VPN) de site a site (S2S)][vpn-gateway-indexmd-608y]
  - Local via [ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-using-service-endpoints"></a>Considerações ao usar pontos de extremidade de serviço

Ao usar pontos de extremidade de serviço para o Banco de Dados SQL do Azure, veja as considerações a seguir:

- **Saída para IPs públicos do Banco de Dados SQL do Azure é necessária**: NSGs (Grupos de Segurança de Rede) devem ser abertos para IPs do Banco de Dados SQL do Azure para permitir a conectividade. Você pode fazer isso usando o NSG [Marcas de Serviço](../../virtual-network/network-security-groups-overview.md#service-tags) para o Banco de Dados SQL do Azure.

### <a name="expressroute"></a>ExpressRoute

Se você estiver usando [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de suas instalações, para emparelhamento público ou emparelhamento da Microsoft, será necessário identificar os endereços IP NAT usados. Para emparelhamento público, cada circuito do ExpressRoute usará dois endereços IP de NAT, que serão aplicados ao tráfego do serviço do Azure quando o tráfego entrar no backbone da rede do Microsoft Azure. Para emparelhamento da Microsoft, os endereços IP de NAT usados são fornecidos pelo cliente ou são fornecidos pelo provedor de serviço. Para permitir o acesso aos recursos do serviço, você deve permitir estes endereços IP públicos na configuração do firewall de IP do recurso. Para localizar os endereços IP do circuito do ExpressRoute de emparelhamento público, [abra um tíquete de suporte com o ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) por meio do Portal do Azure. Saiba mais sobre [NAT para emparelhamento público de ExpressRoute e emparelhamento da Microsoft.](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)

Para permitir a comunicação do seu circuito com o Banco de Dados SQL do Azure, você deve criar regras de rede IP para os endereços IP públicos do seu NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Impacto de usar pontos de extremidade de serviço de VNet com Armazenamento do Azure

O Armazenamento do Azure implementou o mesmo recurso que permite que você limite a conectividade à sua conta de Armazenamento do Azure. Se optar por usar esse recurso com uma conta de Armazenamento do Azure que está sendo usada pelo Banco de Dados SQL do Azure, você poderá encontrar problemas. Veja a seguir uma lista e uma discussão sobre os recursos do banco de dados SQL do Azure e do Azure Synapse Analytics afetados por isso.

### <a name="azure-synapse-polybase-and-copy-statement"></a>Azure Synapse polybase e instrução de cópia

O polybase e a instrução de cópia costumam ser usados para carregar dados no Azure Synapse Analytics de contas de armazenamento do Azure para a ingestão de dados de alta taxa de transferência. Se a conta de armazenamento do Azure para a qual você está carregando dados limita o acesso somente a um conjunto de sub-redes de VNet, a conectividade ao usar o polybase e a instrução de cópia para a conta de armazenamento será interrompida. Para habilitar cenários de importação e exportação usando o COPY e o polybase com o Azure Synapse Analytics conectando-se ao armazenamento do Azure que é protegido para VNet, siga as etapas indicadas abaixo:

#### <a name="prerequisites"></a>Pré-requisitos

- Instalar o Azure PowerShell usando este [guia](/powershell/azure/install-az-ps).
- Se você tiver uma conta de armazenamento de blobs ou de uso geral v1, será necessário primeiro atualizar para uso geral v2 usando este [guia](../../storage/common/storage-account-upgrade.md).
- É necessário ativar **Permitir que os serviços confiáveis da Microsoft acessem essa conta de armazenamento** no menu de configurações **Firewalls e redes virtuais** da conta do Armazenamento do Azure. Habilitar essa configuração permitirá que o polybase e a instrução de cópia se conectem à conta de armazenamento usando autenticação forte em que o tráfego de rede permanece no backbone do Azure. Confira este [guia](../../storage/common/storage-network-security.md#exceptions) para saber mais.

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber as correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para saber mais sobre a compatibilidade entre eles, confira [Apresentação do novo módulo Az do Azure PowerShell](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Etapas

1. Se você tiver um pool SQL dedicado autônomo, Registre seu SQL Server com Azure Active Directory (AAD) usando o PowerShell: 

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Esta etapa não é necessária para pools SQL dedicados em um espaço de trabalho Synapse.

1. Se você tiver um espaço de trabalho Synapse, registre a identidade gerenciada pelo sistema do seu espaço de trabalho:

   1. Vá para o espaço de trabalho Synapse na portal do Azure
   2. Ir para a folha identidades gerenciadas 
   3. Verificar se a opção "permitir pipelines" está habilitada
   
1. Crie uma **Conta de armazenamento de uso geral v2** usando este [guia](../../storage/common/storage-account-create.md).

   > [!NOTE]
   >
   > - Se você tiver uma conta de armazenamento de blobs ou de uso geral v1, será necessário **primeiro atualizar para v2** usando este [guia](../../storage/common/storage-account-upgrade.md).
   > - Para problemas conhecidos com o Azure Data Lake Storage Gen2, confira este [guia](../../storage/blobs/data-lake-storage-known-issues.md).

1. Na conta de armazenamento, navegue até o **Controle de Acesso (IAM)** e selecione **Adicionar atribuição de função**. Atribua a função de **colaborador de dados do armazenamento de BLOBs** ao servidor ou ao espaço de trabalho que hospeda seu pool SQL dedicado que você registrou com o Azure Active Directory (AAD).

   > [!NOTE]
   > Somente membros com privilégio de proprietário na conta de armazenamento podem executar esta etapa. Para obter várias funções internas do Azure, confira este [guia](../../role-based-access-control/built-in-roles.md).
  
1. **Conectividade do Polybase com a conta de Armazenamento do Azure:**

   1. Crie uma **[chave mestra](/sql/t-sql/statements/create-master-key-transact-sql)** de banco de dados se ainda não tiver criado uma anteriormente:

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Criar credencial com escopo de banco de dados com **IDENTITY = 'Managed Service Identity'**:

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Não é necessário especificar SECRET com a chave de acesso de Armazenamento do Azure, porque esse mecanismo usa [Identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) nos bastidores.
       > - O nome IDENTITY deve ser **'Managed Service Identity'** para que a conectividade do PolyBase funcione com a conta de armazenamento do Azure protegida para VNet.

   1. Crie uma fonte de dados externa com `abfss://` o esquema para se conectar à sua conta de armazenamento de uso geral v2 usando o polybase:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Se você já tiver tabelas externas associadas à conta de armazenamento de blobs ou de uso geral v1, primeiro remova essas tabelas externas e, em seguida, remova a fonte de dados externa correspondente. Em seguida, crie uma fonte de dados externa com `abfss://` o esquema conectando-se à conta de armazenamento de uso geral v2 como acima e recrie todas as tabelas externas usando essa nova fonte de dados externa. Use o [Assistente para gerar e publicar scripts](/sql/ssms/scripting/generate-and-publish-scripts-wizard) para gerar scripts de criação para todas as tabelas externas com facilidade.
       > - Para obter mais informações sobre `abfss://` o esquema, consulte este [guia](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md).
       > - Para saber mais sobre CREATE EXTERNAL DATA SOURCE, confira este [guia](/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Consulte normalmente usando [tabelas externas](/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Auditoria de Blobs do Banco de Dados SQL do Azure

A auditoria de blob envia por push logs de auditoria para sua própria conta de armazenamento. Se essa conta de armazenamento usar o recurso de pontos de extremidade do serviço VNet, a conectividade do Banco de Dados SQL do Azure para a conta de armazenamento será interrompida.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Adicionando uma regra do Firewall VNet ao seu servidor sem ativar os pontos de extremidade do serviço da VNet

Há muito tempo antes que esse recurso fosse aprimorado, era necessário ativar os pontos de extremidade de serviço da VNet antes de implementar uma regra dinâmica da VNet no Firewall. Os pontos de extremidade relacionados a uma determinada sub-rede de VNet a um banco de dados no banco de dados SQL do Azure. No entanto, a partir de janeiro de 2018, você pode contornar esse requisito definindo o sinalizador **IgnoreMissingVNetServiceEndpoint**.

A simples configuração de uma regra de Firewall não ajuda a proteger o servidor. Você também precisa ativar os pontos de extremidade de serviço da VNET para que a segurança entre em vigor. Quando você ativa os pontos de extremidade de serviço, a sub-rede da VNET passa por um tempo de inatividade até concluir a transição de desativado para ativado. Isso é especialmente verdadeiro no contexto de VNETs grandes. Use o sinalizador **IgnoreMissingVNetServiceEndpoint** para reduzir ou eliminar o tempo de inatividade durante a transição.

Defina o sinalizador **IgnoreMissingVNetServiceEndpoint** usando o PowerShell. Para obter detalhes, consulte [PowerShell para criar uma regra e um ponto de extremidade de serviço de Rede Virtual para o Banco de Dados SQL do Azure][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Erros 40914 e 40615

O erro de conexão 40914 está relacionado a *regras da rede virtual*, conforme especificado no painel Firewall no Portal do Azure. O erro 40615 é semelhante, exceto pelo fato de que ele se relaciona com *regras de endereço IP* no Firewall.

### <a name="error-40914"></a>Erro 40914

*Texto da mensagem:* não é possível abrir o servidor '*[nome-do-servidor]*' solicitado pelo logon. O cliente não tem permissão para acessar o servidor.

*Descrição do erro:* o cliente está em uma sub-rede que tem pontos de extremidade de servidor de rede virtual. Mas o servidor não tem nenhuma regra de rede virtual que conceda à sub-rede o direito de se comunicar com o banco de dados.

*Resolução de erro:* no painel Firewall do Portal do Azure, use o controle de regras de rede virtual para [adicionar uma regra da rede virtual](#anchor-how-to-by-using-firewall-portal-59j) à sub-rede.

### <a name="error-40615"></a>Erro 40615

*Texto da mensagem:* não é possível abrir o servidor '{0}' solicitado pelo logon. O cliente com o endereço IP '{1}' não tem permissão para acessar o servidor.

*Descrição do erro:* O cliente está tentando se conectar de um endereço IP que não está autorizado a se conectar ao servidor. O Firewall do servidor não tem nenhuma regra de endereço IP que permita que um cliente se comunique do endereço IP fornecido ao banco de dados.

*Resolução de erro:* digite o endereço IP do cliente como uma regra de IP. Faça isso usando o painel Firewall no Portal do Azure.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="portal-can-create-a-virtual-network-rule"></a>O Portal pode criar uma regra da rede virtual

Esta seção ilustra como você pode usar o [portal do Azure][http-azure-portal-link-ref-477t] para criar uma *regra de rede virtual* em seu banco de dados no banco de dados SQL do Azure. A regra diz ao banco de dados para aceitar a comunicação de uma sub-rede específica que foi marcada como um *ponto de extremidade de serviço de rede virtual*.

> [!NOTE]
> Se você pretende adicionar um ponto de extremidade de serviço às regras de firewall de rede virtual do seu servidor, primeiro verifique se os pontos de extremidade de serviço estão ativados para a sub-rede.
>
> Se os pontos de extremidade de serviço não estão ativados para a sub-rede, o portal pede para você habilitá-los. Clique no botão **Habilitar** na mesma folha em que você adicionou a regra.

## <a name="powershell-alternative"></a>Alternativa do PowerShell

Um script também pode criar regras de rede virtual usando o cmdlet do PowerShell **New-AzSqlServerVirtualNetworkRule** ou [AZ Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create). Se tiver interesse, consulte [PowerShell para criar uma regra e um ponto de extremidade de serviço de rede virtual para o banco de dados SQL do Azure][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>Alternativa de API REST

Internamente, os cmdlets do PowerShell para ações de VNet do SQL chamam APIs REST. É possível chamar as APIs REST diretamente.

- [Regras de Rede Virtual do Microsoft Azure: Operações][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Pré-requisitos

Você já deve ter uma sub-rede que esteja marcada com o ponto de extremidade de serviço de rede virtual específico *nome do tipo* relevante para o banco de dados SQL do Azure.

- O nome do tipo de ponto de extremidade relevante é **Microsoft.Sql**.
- Se a sua sub-rede não pode ser marcada com o nome do tipo, consulte [Verificar se sua sub-rede é um ponto de extremidade][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>etapas do portal do Azure

1. Entre no [portal do Azure][http-azure-portal-link-ref-477t].

2. Procure e selecione **servidores SQL** e, em seguida, selecione o servidor. Em **segurança**, selecione **firewalls e redes virtuais**.

3. Defina o controle **Permitir acesso aos serviços do Azure** como OFF.

    > [!IMPORTANT]
    > Se você deixar o controle definido como ativado, o servidor aceitará a comunicação de qualquer sub-rede dentro do limite do Azure, ou seja, proveniente de um dos endereços IP que são reconhecidos como aqueles nos intervalos definidos para data centers do Azure. Deixar o controle definido como ON pode ocasionar acesso excessivo de um ponto de vista de segurança. O recurso de ponto de extremidade de serviço de rede virtual do Microsoft Azure, em conjunto com o recurso de regra da rede virtual do Banco de dados SQL do Microsoft Azure, pode reduzir a área de superfície de segurança.

4. Clique no controle **+ Adicionar existente**, na seção **Redes virtuais**.

    ![Clique em Adicionar existente (ponto de extremidade de sub-rede, como uma regra SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. No novo painel **Criar/Atualizar**, preencha os controles com os nomes dos recursos do Azure.

    > [!TIP]
    > Você deve incluir o **prefixo de endereço** correto para a sua sub-rede. É possível encontrar o valor no portal.
    > Navegue até **Todos os recursos** &gt; **Todos os tipos** &gt; **Redes virtuais**. O filtro exibe suas redes virtuais. Clique em sua rede virtual e, em seguida, clique em **Sub-redes**. A coluna **INTERVALO DE ENDEREÇOS** tem o prefixo de endereço de que você precisa.

    ![Preencha os campos para a nova regra.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Clique no botão **OK** perto da parte inferior do painel.

7. Consulte a regra da rede virtual resultante no painel de firewall.

    ![Consulte a nova regra, no painel de firewall.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> O status ou os estados a seguir se aplicam às regras:
>
> - **Pronto:** indica que a operação que você iniciou foi bem-sucedida.
> - **Falha:** indica que a operação que você iniciou falhou.
> - **Excluído:** só se aplica à operação de exclusão e indica que a regra foi excluída e não se aplica mais.
> - **Em andamento:** indica que a operação está em andamento. A regra antiga é aplicável enquanto a operação está nesse estado.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Artigos relacionados

- [Pontos de extremidade de serviço de rede virtual do Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Regras de firewall no nível de servidor e banco de dados][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Próximas etapas

- [Use o PowerShell para criar um ponto de extremidade de serviço de rede virtual e, em seguida, uma regra da rede virtual para o Banco de Dados SQL do Azure.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Regras de rede virtual: operações][rest-api-virtual-network-rules-operations-862r] com APIs REST

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]:../index.yml
[rbac-what-is-813s]:../../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]:firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]:scripts/vnet-service-endpoint-rule-powershell-create.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]:scripts/vnet-service-endpoint-rule-powershell-create.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[vpn-gateway-indexmd-608y]: ../../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: /rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
