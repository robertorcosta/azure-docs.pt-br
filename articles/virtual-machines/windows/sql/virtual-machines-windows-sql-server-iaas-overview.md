---
title: Visão geral do SQL Server nas Máquinas Virtuais do Windows do Azure | Microsoft Docs
description: Saiba mais sobre como executar as edições completas do SQL Server nas Máquinas Virtuais do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7d8d1505a268976161636abd0ed2d24398978284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75374279"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>O que é o SQL Server nas Máquinas Virtuais do Azure? (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

[O SQL Server em máquinas virtuais Do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) permite que você use versões completas do SQL Server na Nuvem sem ter que gerenciar qualquer hardware no local. As VMs do SQL Server também simplificam os custos de licenciamento quando são pré-pagas.

Máquinas virtuais do Azure executadas em várias [regiões geográficas](https://azure.microsoft.com/regions/) diferentes no mundo inteiro. Eles também oferecem uma variedade de [tamanhos de máquina](../sizes.md). A galeria de imagens de máquina virtual permite que você crie uma VM do SQL Server com a versão, a edição e o sistema operacional corretos. Isso torna a máquinas virtuais uma boa opção para uma das muitas cargas de trabalho diferentes do SQL Server.

## <a name="automated-updates"></a>Atualizações automáticas

As VMs do Azure do SQL Server podem usar a [Aplicação de Patch Automatizada](virtual-machines-windows-sql-automated-patching.md) para agendar uma janela de manutenção para instalar automaticamente janelas importantes e atualizações do SQL Server.

## <a name="automated-backups"></a>Backups automatizados

As VMs do Azure do SQL Server podem aproveitar o [Backup Automatizado](virtual-machines-windows-sql-automated-backup-v2.md), que cria regularmente backups do banco de dados no armazenamento de blobs. Essa técnica também pode ser usada manualmente. Para obter mais informações, consulte [Usar o Armazenamento do Azure para o Backup e a Restauração do SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md).

## <a name="high-availability"></a>Alta disponibilidade

Se você precisar de alta disponibilidade, considere configurar grupos de disponibilidade do SQL Server. Isso envolve várias VMs do Azure do SQL Server em uma rede virtual. Você pode configurar manualmente sua solução de alta disponibilidade ou pode usar modelos no portal do Azure para a configuração automática. Para obter uma visão geral de todas as opções de alta disponibilidade, veja [Alta disponibilidade e recuperação de desastre para SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="performance"></a>Desempenho

Máquinas virtuais do Azure oferecem tamanhos de máquinas diferentes para atender às demandas de várias cargas de trabalho. VMs do SQL também fornecem a configuração automatizada de armazenamento, que é otimizada para seus requisitos de desempenho. Para saber mais sobre como configurar o armazenamento para VMs do SQL, consulte [Configuração de armazenamento para VMs do SQL Server](virtual-machines-windows-sql-server-storage-configuration.md). Para ajustar o desempenho, confira as [Práticas recomendadas de desempenho para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-performance.md).

## <a name="get-started-with-sql-vms"></a>Introdução a VMs do SQL

Para começar, escolha uma imagem de máquina virtual do SQL Server com a versão, a edição e o sistema operacional necessários. As seções a seguir fornecem links diretos para o Portal do Azure para as imagens da Galeria de máquina virtual do SQL Server.

> [!TIP]
> Para saber mais sobre como compreender o preço para imagens do SQL, confira [Diretrizes de preços para VMs SQL Server do Azure](virtual-machines-windows-sql-server-pricing-guidance.md). 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a>Pague como você vai
A tabela a seguir fornece uma matriz de imagens do SQL Server pré-pagas.

| Versão | Sistema operacional | Edition |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Empresa,](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise) [Padrão,](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard) [Web,](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web) [Desenvolvedor](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **Microsoft SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Para ver as imagens de máquina virtual do SQL Server do Linux disponíveis, confira [Visão geral do SQL Server em máquinas virtuais do Azure (Linux)](../../linux/sql/sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Agora, é possível alterar o modelo de licenciamento de uma VM do SQL Server paga por uso para usar sua própria licença. Para obter mais informações, consulte [Como alterar o modelo de licenciamento de um VM SQL](virtual-machines-windows-sql-ahb.md). 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> Traga sua própria licença
Também é possível usar sua própria licença (BYOL). Nesse cenário, você paga apenas pela VM sem encargos adicionais para o licenciamento do SQL Server.  Colocar sua própria licença pode economizar dinheiro ao longo do tempo para cargas de trabalho de produção contínua. Para obter os requisitos para usar essa opção, veja [Diretrizes de preços para VMs do SQL Server do Azure](virtual-machines-windows-sql-server-pricing-guidance.md#byol).

Para usar sua própria licença, converta uma VM existente do SQL de pagamento por uso, ou implante uma imagem com **{BYOL}** prefixado. Para saber mais sobre como alternar seu modelo de licenciamento entre o pagamento por uso e BYOL, confira [Como alterar o modelo de licenciamento para uma VM do SQL](virtual-machines-windows-sql-ahb.md). 

| Versão | Sistema operacional | Edition |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise), [Padrão BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **Microsoft SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Padrão BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

É possível implantar uma imagem mais antiga do SQL Server que não está disponível no portal Azure usando o PowerShell. Para exibir todas as imagens disponíveis usando o PowerShell, use o seguinte comando:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

Para obter mais informações sobre como implantar VMs do SQL Server usando o PowerShell, confira [Como provisionar máquinas virtuais do SQL Server com o Azure PowerShell](virtual-machines-windows-ps-sql-create.md).


### <a name="connect-to-the-vm"></a>Conectar-se à VM
Depois de criar sua VM do SQL Server, conecte-se a ela de aplicativos ou ferramentas, como o SQL Server Management Studio (SSMS). Para obter instruções, veja [Conectar-se a uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migrar seus dados
Se você tiver um banco de dados existente, deverá movê-lo para a VM do SQL recentemente provisionada. Para obter uma lista das opções de migração e diretrizes, consulte [Migrar um Banco de Dados para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md).

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Crie e gerencie recursos SQL do Azure com o portal Azure

O portal Azure fornece uma única página onde você pode gerenciar [todos os seus recursos SQL do Azure,](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql) incluindo suas máquinas virtuais SQL.

Para acessar a página **de recursos do Azure SQL,** selecione **O Azure SQL** no menu do portal Azure ou procure e selecione **o Azure SQL** em qualquer página.

![Procure por Azure SQL](./media/quickstart-sql-vm-create-portal/search-for-azure-sql.png)

> [!NOTE]
> **O Azure SQL** fornece uma maneira rápida e fácil de acessar todos os seus bancos de dados SQL, pools elásticos, servidores de banco de dados, instâncias gerenciadas por SQL e máquinas virtuais SQL. O Azure SQL não é um serviço ou recurso. 

Para gerenciar os recursos existentes, selecione o item desejado na lista. Para criar novos recursos Do Azure SQL, selecione **+ Adicionar**. 

![Criar recurso SQL do Azure](./media/quickstart-sql-vm-create-portal/create-azure-sql-resource.png)

Depois de selecionar **+ Adicionar,** visualize informações adicionais sobre as diferentes opções selecionando **Mostrar detalhes** em qualquer azulejo.

![dados de dados detalhes de ladrilhos](./media/quickstart-sql-vm-create-portal/sql-vm-details.png)

Para obter detalhes, confira:

- [Criar um banco de dados individual](../../../sql-database/sql-database-single-database-get-started.md)
- [Criar um pool elástico](../../../sql-database/sql-database-elastic-pool.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Criar uma instância gerenciada](../../../sql-database/sql-database-managed-instance-get-started.md)
- [Crie uma máquina virtual SQL](quickstart-sql-vm-create-portal.md)

## <a name="sql-vm-image-refresh-policy"></a><a id="lifecycle"></a> Política de atualização de imagem de VM SQL
O Azure mantém apenas uma imagem de máquina virtual para cada combinação de sistema operacional, versão e edição com suporte. Isso significa que, ao longo do tempo, as imagens são atualizadas e as imagens mais antigas são removidas. Para saber mais, confira a seção **Imagens** das [Perguntas frequentes sobre VMs SQL Server](virtual-machines-windows-sql-server-iaas-faq.md#images).

## <a name="customer-experience-improvement-program-ceip"></a>Programa de aperfeiçoamento da experiência do usuário (CEIP)
O CEIP (Programa de Aperfeiçoamento da Experiência do Usuário) está habilitado por padrão. Isso envia relatórios periodicamente à Microsoft a fim de ajudar a aprimorar o SQL Server. Nenhuma tarefa de gerenciamento é necessária com o CEIP, a menos que você queira desabilitá-lo após o provisionamento. Você pode personalizar ou desabilitar o CEIP conectando-se à VM com área de trabalho remota. Em seguida, execute o utilitário **Erro do SQL Server e o Relatório de Uso** . Siga as instruções para desabilitar o relatório. Para saber mais sobre coleta de dados, veja a [Declaração de privacidade do SQL Server](https://docs.microsoft.com/sql/getting-started/microsoft-sql-server-privacy-statement).

## <a name="related-products-and-services"></a>Produtos e serviços relacionados
### <a name="windows-virtual-machines"></a>Máquinas Virtuais do Windows
* [Visão geral de Máquinas Virtuais](../overview.md)

### <a name="storage"></a>Armazenamento
* [Introdução ao Armazenamento do Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Rede
* [Visão geral da Rede Virtual](../../../virtual-network/virtual-networks-overview.md)
* [Endereços IP no Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Criar um nome de domínio totalmente qualificado no portal do Azure](../portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [Documentação do SQL Server](https://docs.microsoft.com/sql/index)
* [Comparação de Banco de Dados SQL do Azure](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Próximas etapas

Introdução ao SQL Server em Máquinas Virtuais do Azure:

* [Criar uma VM do SQL Server no portal do Azure](quickstart-sql-vm-create-portal.md)

Obtenha respostas para perguntas frequentes sobre máquinas virtuais do SQL:

* [Perguntas frequentes sobre o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-faq.md)

Exibir arquiteturas de referência para executar aplicativos de nível N no SQL Server no IaaS

* [Aplicativo de N camadas do Windows no Azure com SQL Server](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Executar aplicativo de N camadas em várias regiões do Azure para ter alta disponibilidade](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
