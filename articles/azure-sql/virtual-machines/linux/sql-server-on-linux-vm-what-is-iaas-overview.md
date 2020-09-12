---
title: Visão geral de SQL Server em máquinas virtuais do Azure para Linux | Microsoft Docs
description: Saiba mais sobre como executar as edições do SQL Server completas em máquinas virtuais do Azure para Linux. Obtenha links diretos para todas as imagens de VM Linux SQL Server e conteúdo relacionado.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6e6038e2c0aa4f6b41c4a4da9bde6e98555ceb31
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613561"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Visão geral do SQL Server nas Máquinas Virtuais do Azure (Linux)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

SQL Server em máquinas virtuais do Azure permite que você use versões completas do SQL Server na nuvem sem precisar gerenciar nenhum hardware local. As VMs do SQL Server também simplificam os custos de licenciamento quando são pré-pagas.

Máquinas virtuais do Azure executadas em várias [regiões geográficas](https://azure.microsoft.com/regions/) diferentes no mundo inteiro. Eles também oferecem uma variedade de [tamanhos de máquina](../../../virtual-machines/windows/sizes.md). A galeria de imagens de máquina virtual permite que você crie uma VM do SQL Server com a versão, a edição e o sistema operacional corretos. Isso torna a máquinas virtuais uma boa opção para uma das muitas cargas de trabalho diferentes do SQL Server. 

Se você for novo no Azure SQL, confira a *SQL Server no vídeo visão geral da VM do Azure* de nossa [série detalhada de vídeos SQL do Azure](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="get-started-with-sql-server-vms"></a><a id="create"></a> Introdução às VMs SQL Server

Para começar, escolha uma imagem de máquina virtual do SQL Server com a versão, a edição e o sistema operacional necessários. As seções a seguir fornecem links diretos para o Portal do Azure para as imagens da Galeria de máquina virtual do SQL Server.

> [!TIP]
> Para obter mais informações sobre como entender os preços de SQL Server imagens, consulte [a página de preços para VMs do Linux em execução SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Versão | Sistema operacional | Edition |
| --- | --- | --- |
| **SQL Server 2019** | Ubuntu 18.04 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804sqldev-ARM) | 
| **SQL Server 2019** | Red Hat Enterprise Linux (RHEL) 8 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8sqldev-ARM)|
| **SQL Server 2019** | SUSE Linux Enterprise Server (SLES) V12 SP5 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5sqldev-ARM)|
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Para ver as imagens de máquina virtual SQL Server disponíveis para Windows, consulte [visão geral de SQL Server em máquinas virtuais do Azure (Windows)](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

## <a name="installed-packages"></a><a id="packages"></a> Pacotes instalados

Ao configurar SQL Server em Linux, você instala o pacote de Mecanismo de Banco de Dados e, em seguida, vários pacotes opcionais, dependendo de seus requisitos. As imagens de máquina virtual Linux para SQL Server instalam automaticamente a maioria dos pacotes para você. A tabela a seguir mostra quais pacotes são instalados para cada distribuição.

| Distribuição | [Mecanismo de Banco de Dados](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Ferramentas](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [Agente de SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Pesquisa de texto completo](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Complemento HA](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![RHEL e mecanismo de banco de dados](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL e ferramentas](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL e agente de SQL Server](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL e pesquisa de texto completo](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL e SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Complemento RHEL e HA](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |
| SLES | ![SLES e mecanismo de banco de dados](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES e ferramentas](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Agente SLES e SQL Server](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES e pesquisa de texto completo](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES e SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![Complemento SLES e HA](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png)|
| Ubuntu | ![Ubuntu e mecanismo de banco de dados](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu e ferramentas](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Agente Ubuntu e SQL Server](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu e pesquisa de texto completo](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu e SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Complemento Ubuntu e HA](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>Produtos e serviços relacionados

### <a name="linux-virtual-machines"></a>Máquinas virtuais do Linux

* [Visão geral das máquinas virtuais do Azure](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>Armazenamento

* [Introdução ao Armazenamento do Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Rede

* [Visão geral da Rede Virtual](../../../virtual-network/virtual-networks-overview.md)
* [Endereços IP no Azure](../../../virtual-network/public-ip-addresses.md)
* [Criar um nome de domínio totalmente qualificado no portal do Azure](../../../virtual-machines/windows/portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [SQL Server na documentação do Linux](https://docs.microsoft.com/sql/linux)
* [Comparação de Banco de Dados SQL do Azure](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Próximas etapas

Introdução às máquinas virtuais SQL Server em Linux:

* [Criar uma VM do SQL Server no portal do Azure](sql-vm-create-portal-quickstart.md)

Obtenha respostas para perguntas frequentes sobre SQL Server VMs no Linux:

* [Perguntas frequentes sobre o SQL Server em Máquinas Virtuais do Azure](frequently-asked-questions-faq.md)
