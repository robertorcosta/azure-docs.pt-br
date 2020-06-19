---
title: Soluções da Oracle no Microsoft Azure | Microsoft Docs
description: Saiba mais sobre as opções para implantar soluções e Aplicativos Oracle no Microsoft Azure, incluindo a execução completa na infraestrutura do Azure ou o uso da conectividade entre nuvem com o OCI (Oracle Cloud Infrastructure).
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/12/2020
ms.author: borisb
ms.openlocfilehash: 649d96a158682752e0d4a31bf7ec73eb7c442f0f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660551"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Visão geral de soluções e Aplicativos Oracle no Azure

Este artigo apresenta recursos para executar soluções Oracle usando a infraestrutura do Azure. Consulte também introduções detalhadas para [aplicativos Azure do WebLogic Server](oracle-weblogic.md) e [imagens de VM do Oracle](oracle-vm-solutions.md) disponíveis no Azure Marketplace e a funcionalidade de [interconexão do Azure com o OCI (Oracle Cloud Infrastructure)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Bancos de dados Oracle na infraestrutura do Azure

Execute bancos de dados Oracle na infraestrutura do Azure usando Oracle Database nas imagens Oracle Linux disponíveis no Azure Marketplace:

* Oracle Database 12.1, 12.2 e 18.3 Edição Enterprise 

* Oracle Database 12.1, 12.2 e 18.3 Edição Standard 

Você também pode optar por configurar o Oracle Database em uma imagem não Oracle Linux disponível no Azure, basear uma solução em uma imagem personalizada criada do zero no Azure ou carregar uma imagem personalizada do seu ambiente local.

Opcionalmente, configure com vários discos anexados e aprimore o desempenho do banco de dados instalando o ASM (Automated Storage Management) da Oracle.

## <a name="weblogic-server-with-azure-service-integrations"></a>WebLogic Server com integrações de serviço do Azure

Escolha entre uma variedade de aplicativos Azure do WebLogic Server para acelerar seu percurso de nuvem.  Várias integrações de serviço do Azure pré-configuradas estão disponíveis, incluindo banco de dados, Gateway de Aplicativo do Azure e Azure Active Directory.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Aplicativos no Oracle Linux e no WebLogic Server

Execute aplicativos empresariais no Azure em sistemas operacionais Oracle com suporte. As seguintes imagens de máquina virtual estão disponíveis no Azure Marketplace:

* Oracle WebLogic Server 12.1.2

* Oracle Linux com o UEK (Unbreakable Enterprise Kernel) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 e 7.6 

## <a name="high-availability-and-disaster-recovery-options"></a>Opções de alta disponibilidade e recuperação de desastre

* Configure o [Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), o [Active Data Guard com FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), a [Fragmentação](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) ou o [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) na infraestrutura do Azure em conjunto com as [Zonas de Disponibilidade](../../../availability-zones/az-overview.md) para alta disponibilidade na região. Você também pode definir essas configurações em várias regiões do Azure para aumentar a disponibilidade e a recuperação de desastres.

* Use o [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) para orquestrar e gerenciar a recuperação de desastre para suas VMs do Oracle Linux no Azure e seus servidores físicos ou locais. 

* Habilite o RAC (Clusters de Aplicativos Reais) da Oracle no Azure usando a [solução do Azure VMWare](https://docs.microsoft.com/azure/vmware-cloudsimple/oracle-real-application-clusters/) ou o [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="backup-oracle-workloads"></a>Fazer backup de cargas de trabalho do Oracle

* Faça backup de suas VMs Oracle usando o [Backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview)

* Faça backup de seu Oracle Database usando o Oracle RMAN e, opcionalmente, use o [Azure Blob Fuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux) para montar uma [conta de Armazenamento de Blobs do Azure altamente redundante](https://docs.microsoft.com/azure/storage/common/storage-redundancy) e grave seus backups do RMAN nela para obter resiliência adicional.

## <a name="integration-of-azure-with-oci"></a>Integração do Azure com o OCI

Execute aplicativos Oracle na infraestrutura do Azure, conectados a bancos de dados de back-end no OCI (Oracle Cloud Infrastructure). Essa solução usa as seguintes funcionalidades: 

* **Rede entre nuvens** – use a interconexão direta disponível entre o Azure ExpressRoute e o Oracle FastConnect para estabelecer conexões de alta largura de banda, privadas e de baixa latência entre o aplicativo e a camada de banco de dados.
* **Identidade integrada** – configure a identidade federada entre o Azure AD e o Oracle IDCS para criar uma fonte de identidade única para as soluções. Habilite o logon único para gerenciar recursos no OCI e no Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Implantar aplicativos Oracle no Azure

Use modelos Terraform para configurar a infraestrutura do Azure e instalar aplicativos Oracle. 

> [!IMPORTANT]
> A Oracle garantirá que esses aplicativos sejam executados no Azure ao usar a solução de interconexão do Azure/Oracle Cloud de maio de 2020.

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplicativos Oracle Retail
* Oracle Hyperion Financial Management

Implante também aplicativos personalizados no Azure que se conectam ao OCI e a outros serviços do Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Configurar bancos de dados Oracle em OCI

Use os serviços de nuvem do Oracle Database (Banco de Dados Autônomo, RAC, Exadata, DBaaS, Nó Único) em conjunto com os aplicativos Oracle em execução no Azure. Saiba mais sobre as [opções de banco de dados OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licenciamento

A implantação de aplicativos Oracle no Azure é baseada em um modelo "traga sua própria licença". Presume-se que você esteja devidamente licenciado para usar o software da Oracle e que tem um contrato de suporte em vigor com a Oracle. A Oracle tem garantia de mobilidade de licença do local para o Azure. Consulte as [perguntas frequentes](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html) sobre o Oracle-Azure.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os [aplicativos Azure do WebLogic Server](oracle-weblogic.md) e as integrações de serviço do Azure para as quais eles dão suporte.

* Saiba mais sobre como implantar [imagens de VM Oracle](oracle-vm-solutions.md) na infraestrutura do Azure.

* Saiba mais sobre como [interconectar o Azure com o OCI](oracle-oci-overview.md).

* Confira a [sessão de visão geral do Oracle no Azure](https://myignite.techcommunity.microsoft.com/sessions/82915) do Ignite 2019. 
