---
title: Visão geral do SQL Server nas Máquinas Virtuais do Windows do Azure | Microsoft Docs
description: Saiba mais sobre como executar as edições completas do SQL Server nas Máquinas Virtuais do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f1786123808371601467926c2a114c676e898330
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668593"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>O que é SQL Server nas máquinas virtuais do Azure (Windows)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)

[SQL Server em máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) permite que você use versões completas do SQL Server na nuvem sem precisar gerenciar nenhum hardware local. SQL Server VMs (máquinas virtuais) também simplificam os custos de licenciamento quando você paga conforme o uso.

Máquinas virtuais do Azure executadas em várias [regiões geográficas](https://azure.microsoft.com/regions/) diferentes no mundo inteiro. Eles também oferecem uma variedade de [tamanhos de máquina](../../../virtual-machines/windows/sizes.md). A galeria de imagens de máquina virtual permite que você crie uma VM do SQL Server com a versão, a edição e o sistema operacional corretos. Isso torna as máquinas virtuais uma boa opção para muitas cargas de trabalho de SQL Server diferentes.

## <a name="automated-updates"></a>Atualizações automáticas

SQL Server nas máquinas virtuais do Azure podem usar a [aplicação de patch automatizada](automated-patching.md) para agendar uma janela de manutenção para instalar atualizações importantes do Windows e do SQL Server automaticamente.

## <a name="automated-backups"></a>Backups automatizados

O SQL Server em máquinas virtuais do Azure pode aproveitar o [backup automatizado](automated-backup.md), que cria regularmente backups de seu banco de dados para o armazenamento de BLOBs. Essa técnica também pode ser usada manualmente. Para obter mais informações, consulte [Usar o Armazenamento do Azure para o Backup e a Restauração do SQL Server](azure-storage-sql-server-backup-restore-use.md).

O Azure também fornece uma solução de backup de classe empresarial para SQL Server em execução em VMs do Azure. Uma solução de backup totalmente gerenciada, compatível com grupos de disponibilidade Always On, retenção de longo prazo, recuperação pontual e gerenciamento e monitoramento central. Para obter mais informações, consulte [backup do Azure para SQL Server em VMs do Azure](https://docs.microsoft.com/azure/backup/backup-azure-sql-database).
  

## <a name="high-availability"></a>Alta disponibilidade

Se você precisar de alta disponibilidade, considere configurar grupos de disponibilidade do SQL Server. Isso envolve várias instâncias de SQL Server em máquinas virtuais do Azure em uma rede virtual. Você pode configurar sua solução de alta disponibilidade manualmente ou pode usar modelos no portal do Azure para configuração automática. Para obter uma visão geral de todas as opções de alta disponibilidade, consulte [alta disponibilidade e recuperação de desastre para SQL Server em máquinas virtuais do Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="performance"></a>Desempenho

Máquinas virtuais do Azure oferecem tamanhos de máquinas diferentes para atender às demandas de várias cargas de trabalho. SQL Server VMs também fornecem configuração de armazenamento automatizada, otimizada para seus requisitos de desempenho. Para obter mais informações sobre como configurar o armazenamento para VMs SQL Server, consulte [configuração de armazenamento para vms SQL Server](storage-configuration.md). Para ajustar o desempenho, consulte as [práticas recomendadas de desempenho para SQL Server em máquinas virtuais do Azure](performance-guidelines-best-practices.md).

## <a name="get-started-with-sql-server-vms"></a>Introdução às VMs SQL Server

Para começar, escolha uma imagem de máquina virtual do SQL Server com a versão, a edição e o sistema operacional necessários. As seções a seguir fornecem links diretos para o Portal do Azure para as imagens da Galeria de máquina virtual do SQL Server.

> [!TIP]
> Para obter mais informações sobre como entender os preços de SQL Server imagens, consulte [diretrizes de preços para SQL Server em máquinas virtuais do Azure](pricing-guidance.md). 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a> Pré-pagas
A tabela a seguir fornece uma matriz de imagens do SQL Server pré-pagas.

| Versão | Sistema operacional | Edition |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Para ver as imagens de máquina virtual SQL Server em Linux disponíveis, consulte [visão geral de SQL Server em máquinas virtuais do Azure (Linux)](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md).

> [!NOTE]
> Agora, é possível alterar o modelo de licenciamento de uma VM do SQL Server paga por uso para usar sua própria licença. Para obter mais informações, confira [Como alterar o modelo de licenciamento para uma VM do SQL Server](licensing-model-azure-hybrid-benefit-ahb-change.md). 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> Traga sua própria licença
Também é possível usar sua própria licença (BYOL). Nesse cenário, você paga apenas pela VM sem encargos adicionais para o licenciamento do SQL Server.  Colocar sua própria licença pode economizar dinheiro ao longo do tempo para cargas de trabalho de produção contínua. Para obter os requisitos para usar essa opção, veja [Diretrizes de preços para VMs do SQL Server do Azure](pricing-guidance.md#byol).

Para trazer sua própria licença, você pode converter uma VM de SQL Server de pagamento por uso existente ou pode implantar uma imagem com o prefixo **{BYOL}**. Para obter mais informações sobre como alternar o modelo de licenciamento entre pagamento por uso e BYOL, consulte [como alterar o modelo de licenciamento para uma VM SQL Server](licensing-model-azure-hybrid-benefit-ahb-change.md). 

| Versão | Sistema operacional | Edition |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise), [Standard  BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard  BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

É possível implantar uma imagem mais antiga do SQL Server que não esteja disponível no portal do Azure usando o PowerShell. Para exibir todas as imagens disponíveis usando o PowerShell, use o seguinte comando:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

Para obter mais informações sobre como implantar VMs do SQL Server usando o PowerShell, confira [Como provisionar máquinas virtuais do SQL Server com o Azure PowerShell](create-sql-vm-powershell.md).


### <a name="connect-to-the-vm"></a>Conectar-se à VM
Depois de criar sua VM do SQL Server, conecte-se a ela de aplicativos ou ferramentas, como o SQL Server Management Studio (SSMS). Para obter instruções, consulte [conectar-se a uma máquina virtual SQL Server no Azure](ways-to-connect-to-sql.md).

### <a name="migrate-your-data"></a>Migrar seus dados
Se você tiver um banco de dados existente, convém movê-lo para a VM SQL Server recentemente provisionada. Para obter uma lista das opções de migração e diretrizes, consulte [Migrar um Banco de Dados para o SQL Server em uma VM do Azure](migrate-to-vm-from-sql-server.md).

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Criar e gerenciar recursos de SQL do Azure com o portal do Azure

O portal do Azure fornece uma página em que você pode gerenciar [todos os seus recursos SQL do Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql) incluindo suas máquinas virtuais do SQL.

Para acessar a página de **recursos do SQL do Azure**, selecione **SQL do Azure** no menu do portal do Azure ou pesquise e selecione **SQL do Azure** em qualquer página.

![Pesquisar SQL do Azure](./media/sql-server-on-azure-vm-iaas-what-is-overview/search-for-azure-sql.png)

> [!NOTE]
> O SQL do Azure oferece uma forma rápida e fácil de acessar todos os seus bancos de dados SQL do Azure, pools elásticos, servidores lógicos, instâncias gerenciadas e máquinas virtuais. O SQL do Azure não é um serviço nem um recurso. 

Para gerenciar os recursos existentes, selecione o item desejado na lista. Para criar recursos de SQL do Azure, selecione **+ Adicionar**. 

![Criar recurso de SQL do Azure](./media/sql-server-on-azure-vm-iaas-what-is-overview/create-azure-sql-resource.png)

Depois de selecionar **+ Adicionar**, veja informações adicionais sobre as diferentes opções selecionando **Mostrar detalhes** em qualquer bloco.

![detalhes do bloco bancos de dados](./media/sql-server-on-azure-vm-iaas-what-is-overview/sql-vm-details.png)

Para obter detalhes, confira:

- [Criar um banco de dados individual](../../database/single-database-create-quickstart.md)
- [Criar um pool elástico](../../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Criar uma instância gerenciada](../../managed-instance/instance-create-quickstart.md)
- [Criar uma máquina virtual do SQL Server](sql-vm-create-portal-quickstart.md)

## <a name="sql-server-vm-image-refresh-policy"></a><a id="lifecycle"></a>Política de atualização de imagem de VM SQL Server
O Azure mantém apenas uma imagem de máquina virtual para cada combinação de sistema operacional, versão e edição com suporte. Isso significa que, ao longo do tempo, as imagens são atualizadas e as imagens mais antigas são removidas. Para saber mais, confira a seção **Imagens** das [Perguntas frequentes sobre VMs SQL Server](frequently-asked-questions-faq.md#images).

## <a name="customer-experience-improvement-program-ceip"></a>Programa de aperfeiçoamento da experiência do usuário (CEIP)
O CEIP (Programa de Aperfeiçoamento da Experiência do Usuário) está habilitado por padrão. Isso envia relatórios periodicamente à Microsoft a fim de ajudar a aprimorar o SQL Server. Nenhuma tarefa de gerenciamento é necessária com o CEIP, a menos que você queira desabilitá-lo após o provisionamento. Você pode personalizar ou desabilitar o CEIP conectando-se à VM com área de trabalho remota. Em seguida, execute o utilitário **Erro do SQL Server e o Relatório de Uso** . Siga as instruções para desabilitar o relatório. Para saber mais sobre coleta de dados, veja a [Declaração de privacidade do SQL Server](https://docs.microsoft.com/sql/getting-started/microsoft-sql-server-privacy-statement).

## <a name="related-products-and-services"></a>Produtos e serviços relacionados
### <a name="windows-virtual-machines"></a>Máquinas virtuais do Windows
* [Visão geral das máquinas virtuais do Azure](../../../virtual-machines/windows/overview.md)

### <a name="storage"></a>Armazenamento
* [Introdução ao Armazenamento do Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Rede
* [Visão geral da Rede Virtual](../../../virtual-network/virtual-networks-overview.md)
* [Endereços IP no Azure](../../../virtual-network/public-ip-addresses.md)
* [Criar um nome de domínio totalmente qualificado no portal do Azure](../../../virtual-machines/linux/portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [Documentação do SQL Server](https://docs.microsoft.com/sql/index)
* [Comparação de Banco de Dados SQL do Azure](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Próximas etapas

Introdução ao SQL Server em Máquinas Virtuais do Azure:

* [Criar uma VM do SQL Server no portal do Azure](sql-vm-create-portal-quickstart.md)

Obtenha respostas para perguntas frequentes sobre máquinas virtuais do SQL Server:

* [Perguntas frequentes sobre o SQL Server em Máquinas Virtuais do Azure](frequently-asked-questions-faq.md)

Ver arquiteturas de referência para executar aplicativos de N camadas no SQL Server em IaaS

* [Aplicativo de N camadas do Windows no Azure com SQL Server](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Executar aplicativo de N camadas em várias regiões do Azure para ter alta disponibilidade](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
