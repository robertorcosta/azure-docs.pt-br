---
title: Soluções da Oracle no Microsoft Azure | Microsoft Docs
description: Conheça as opções para implantar aplicativos e soluções Oracle no Microsoft Azure, incluindo a execução inteira mente na infra-estrutura do Azure ou o uso de conectividade entre nuvens com o Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: borisb
ms.openlocfilehash: e6a2e7c646782f5ad874124e4a86809ed1d79eea
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687394"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Visão geral dos aplicativos e soluções Oracle no Azure

Este artigo introduz recursos para executar soluções Oracle usando a infra-estrutura Do Zure. Veja também introduções detalhadas às [imagens VM oracle](oracle-vm-solutions.md) disponíveis no Azure Marketplace e a capacidade de [interconectar o Azure com o Oracle Cloud Infrastructure (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Bancos de dados Oracle sobre infra-estrutura do Azure

Execute bancos de dados Oracle na infra-estrutura do Azure usando o Oracle Database em imagens Oracle Linux disponíveis no Azure Marketplace:

* Banco de dados Oracle 12.1, 12.2 e 18.3 Enterprise Edition 

* Banco de dados Oracle 12.1, 12.2 e 18.3 Standard Edition 

Você também pode optar por configurar o Oracle Database em uma imagem Linux não-Oracle disponível no Azure, basear uma solução em uma imagem personalizada que você cria do zero no Azure ou carregar uma imagem personalizada do seu ambiente local.

Configurar opcionalmente com vários discos conectados e melhorar o desempenho do banco de dados instalando o ASM (Oracle Automated Storage Management, gerenciamento automatizado de armazenamento) oracle.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Aplicativos no Oracle Linux e No WebLogic Server

Execute aplicativos corporativos no Azure em sistemas operacionais Oracle suportados. As seguintes imagens estão disponíveis no Azure Marketplace:

* Servidor WebLogic Oracle 12.1.2

* Oracle Linux com o Núcleo Corporativo Inquebrável (UEK) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 e 7.6 

## <a name="high-availability-and-disaster-recovery-options"></a>Alta disponibilidade e opções de recuperação de desastres

* Configure [o Oracle Data Guard,](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956)active Data Guard com [FSFO,](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html) [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) ou [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) na infra-estrutura Do Zure em conjunto com as Zonas de [Disponibilidade](../../../availability-zones/az-overview.md) para alta disponibilidade na região. Você também pode configurar essas configurações em várias regiões do Azure para obter disponibilidade adicional e recuperação de desastres.

* Use [o Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) para orquestrar e gerenciar a recuperação de desastres para seus VMs Oracle Linux no Azure e seus servidores físicos ou locais. 

* Habilite o Oracle Real Application Clusters (RAC) no Azure usando [a Solução VMWare do Azure](https://docs.azure.cloudsimple.com/oracle-rac/) ou [o FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="backup-oracle-workloads"></a>Backup de cargas de trabalho oracle

* Faça backup de suas VMs Oracle usando [o Backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview)

* Faça backup do seu banco de dados Oracle usando o Oracle RMAN e, opcionalmente, use [o Azure Blob Fuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux) para montar uma [conta altamente redudant Azure Blob Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) e escreva seus backups RMAN para maior resiliência.

## <a name="integration-of-azure-with-oci"></a>Integração do Azure com o OCI

Execute os Aplicativos Oracle na infra-estrutura do Azure, conectados a bancos de dados back-end na Oracle Cloud Infrastructure (OCI). Esta solução usa os seguintes recursos: 

* **Rede entre nuvens** - Use a interconexão direta disponível entre o Azure ExpressRoute e o Oracle FastConnect para estabelecer conexões de alta largura de banda, privadas e de baixa latência entre o aplicativo e a camada de banco de dados.
* **Identidade integrada** - Configure a identidade federada entre o Azure AD e o Oracle IDCS para criar uma única fonte de identidade para as soluções. Habilite o login único para gerenciar recursos em OCI e Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Implantar aplicativos Oracle no Azure

Use modelos Terraform para configurar a infra-estrutura do Azure e instalar aplicativos Oracle. 

> [!IMPORTANT]
> A Oracle certificará esses aplicativos para serem executados no Azure ao usar a solução de interconexão Azure/Oracle Cloud até maio de 2020.

* Suíte E-Business
* JD Edwards EnterpriseOne
* Peoplesoft
* Aplicativos oracle de varejo
* Oracle Hyperion Gestão Financeira

Também implante aplicativos personalizados no Azure que se conectam com o OCI e outros serviços do Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Configurar bancos de dados Oracle em OCI

Use o Oracle Database Cloud Services (Banco de Dados Autônomo, RAC, Exadata, DBaaS, Nó Único) em conjunto com os Aplicativos Oracle em execução no Azure. Saiba mais sobre [as opções de banco de dados OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licenciamento

A implantação de aplicativos Oracle no Azure é baseada em um modelo de "traga sua própria licença". Presume-se que você está devidamente licenciado para usar o software Oracle e que você tem um contrato de suporte atual em vigor com a Oracle. A Oracle tem garantia de mobilidade de licença do local para o Azure. Consulte o [FAQ](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)Oracle-Azure .

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a implantação de [imagens Oracle VM](oracle-vm-solutions.md) na infra-estrutura do Azure.

* Saiba mais sobre como [interligar o Azure com o OCI](oracle-oci-overview.md).

* Confira a [sessão de visão geral oracle on Azure](https://myignite.techcommunity.microsoft.com/sessions/82915) do Ignite 2019. 
