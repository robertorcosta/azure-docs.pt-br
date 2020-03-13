---
title: Introdução ao SAP em VMs do Azure | Microsoft Docs
description: Saiba mais sobre as soluções SAP executadas em VMs (máquinas virtuais) no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 27fdf966f99ac2cba394515cb654ed74178d6673
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137638"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Usar o Azure para hospedar e executar cenários de carga de trabalho do SAP

Ao usar o Microsoft Azure, você pode executar de forma confiável suas cargas de trabalho e cenários de missão crítica do SAP em uma plataforma escalonável, em conformidade e comprovada pela empresa. Você Obtém a escalabilidade, a flexibilidade e a economia de custos do Azure. Com a parceria expandida entre a Microsoft e a SAP, você pode executar aplicativos SAP em cenários de desenvolvimento e teste e produção no Azure e ter total suporte. Do SAP NetWeaver ao SAP S/4HANA, SAP BI no Linux ao Windows e SAP HANA ao SQL, temos a você a você.

Além de hospedar cenários do SAP NetWeaver com o DBMS diferente no Azure, você pode hospedar outros cenários de carga de trabalho do SAP, como o SAP BI no Azure. 

A exclusividade do Azure para SAP HANA é uma oferta que define o Azure separadamente. Para habilitar a hospedagem de mais cenários SAP que exigem recursos de memória e CPU que envolvem SAP HANA, o Azure oferece o uso de hardware bare-metal dedicado ao cliente. Use esta solução para executar implantações SAP HANA que exigem até 24 TB (escala horizontal de 120 TB) de memória para S/4HANA ou outra carga de trabalho de SAP HANA. 

A hospedagem de cenários de carga de trabalho do SAP no Azure também pode criar requisitos de integração de identidade e logon único. Essa situação pode ocorrer quando você usa o Azure Active Directory (Azure AD) para conectar diferentes componentes SAP e ofertas de software como serviço (SaaS) ou plataforma como serviço (PaaS) do SAP. Uma lista desses cenários de integração e logon único com o Azure AD e as entidades do SAP é descrita e documentada na seção "integração de identidade e logon único do AAD SAP".

## <a name="changes-to-the-sap-workload-section"></a>Alterações na seção de carga de trabalho do SAP
Alterações em documentos na seção de carga de trabalho do SAP no Azure estão listadas no final deste artigo. As entradas no log de alterações são mantidas por cerca de 180 dias.

## <a name="you-want-to-know"></a>Você deseja saber
Se você tiver perguntas específicas, vamos apontar para documentos ou fluxos específicos nesta seção da página inicial. Você deseja saber:

- Quais VMs do Azure e as unidades de instância grande do HANA têm suporte para as versões de software SAP e quais versões do sistema operacional. Leia o documento [sobre qual software SAP tem suporte para a implantação do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) para obter respostas e o processo para encontrar as informações
- Quais cenários de implantação do SAP têm suporte com VMs do Azure e instâncias grandes do HANA. As informações sobre os cenários com suporte podem ser encontradas nos documentos:
    - [Carga de trabalho do SAP em cenários com suporte de máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [Cenários com suporte para o SAP HANA em instâncias grandes](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- Quais serviços do Azure, tipos de VM do Azure e serviços de armazenamento do Azure estão disponíveis nas diferentes regiões do Azure, verifique os produtos de site [disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) 

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA no Azure (Instâncias Grandes)

Uma série de documentos o conduz por SAP HANA no Azure (instâncias grandes) ou para instâncias grandes e HANA. Para obter informações sobre instâncias grandes do HANA, comece com a [visão geral do documento e arquitetura de SAP Hana no Azure (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) e percorra a documentação relacionada na seção de instância grande do Hana



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA em máquinas virtuais do Azure
Esta seção da documentação aborda diferentes aspectos do SAP HANA. Como pré-requisito, você deve estar familiarizado com os principais serviços do Azure que fornecem serviços elementares do Azure IaaS. Portanto, você precisa de conhecimento da computação, do armazenamento e da rede do Azure. Muitos desses assuntos são tratados no [Guia de planejamento do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)relacionado ao SAP NetWeaver. 

Para obter informações sobre o HANA no Azure, consulte os seguintes artigos e seus subartigos:

- [Configurações de infraestrutura do SAP HANA e operações no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Alta disponibilidade do SAP HANA para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Alta disponibilidade do SAP HANA em VMs (máquinas virtuais) do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Guia de backup para SAP HANA em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver implantado em máquinas virtuais do Azure
Esta seção lista a documentação de planejamento e implantação para o SAP NetWeaver e Business One no Azure. A documentação se concentra nas noções básicas e no uso de bancos de dados não HANA com uma carga de trabalho do SAP no Azure. Os documentos e artigos para alta disponibilidade também são a base para a alta disponibilidade do HANA no Azure, como:

- [Guia de planejamento do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 
- [SAP Business One em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Proteger uma implantação de aplicativo do SAP NetWeaver de várias camadas usando Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Conector SAP LaMa para o Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Para obter informações sobre bancos de dados não HANA em uma carga de trabalho do SAP no Azure, consulte:

- [Considerações para Implantação do DBMS de Máquinas Virtuais do Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Implantação do DBMS de Máquinas de Virtuais do SQL Server Azure para NetWeaver do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Implantação do DBMS de Máquinas Virtuais do Oracle Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Implantação do DBMS de Máquinas Virtuais do IBM DB2 Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Implantação do DBMS de Máquinas Virtuais do SAP ASE Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [SAP MaxDB, cache dinâmico e implantação de servidor de conteúdo em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Para obter informações sobre bancos de dados SAP HANA no Azure, consulte a seção "SAP HANA em máquinas virtuais do Azure".

Para obter informações sobre a alta disponibilidade de uma carga de trabalho do SAP no Azure, consulte:

- [Alta disponibilidade de Máquinas Virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Este documento aponta para vários outros documentos de arquitetura e cenário. Em documentos de cenário posterior, os links para documentos técnicos detalhados que explicam a implantação e a configuração dos diferentes métodos de alta disponibilidade são fornecidos. Os diferentes documentos que mostram como estabelecer e configurar a alta disponibilidade para uma carga de trabalho do SAP NetWeaver abrangem sistemas operacionais Linux e Windows.


Para obter informações sobre a integração entre Azure Active Directory (Azure AD) e serviços SAP e logon único, consulte:

- [Tutorial: integração do Azure Active Directory ao SAP Cloud for Customer](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Microsoft Azure Active Directory com a SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: integração do Azure Active Directory ao SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: integração do Azure Active Directory com o SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Seu ambiente S/4HANA: logon único SAML do Fiori Launchpad com o Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Para obter informações sobre a integração dos serviços do Azure em componentes SAP, consulte:

- [Usar SAP HANA no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery e SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Usar o Conector do SAP BW no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [O Azure Data Factory oferece integração de dados do SAP HANA e do Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Log de alterações
- 03/11/2020: alteração na [carga de trabalho do SAP em cenários de máquina virtual do Azure com suporte](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) para esclarecer vários bancos de dados por suporte de instância de DBMS
- 03/11/2020: alteração no [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) explicando VMs de geração 1 e geração 2
- 03/10/2020: alterar em [SAP Hana configurações de armazenamento de máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) para esclarecer os limites reais de taxa de transferência de seja
- 03/09/2020: alteração na [alta disponibilidade para SAP NetWeaver em VMs do Azure em SuSE Linux Enterprise Server para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [alta disponibilidade para SAP NetWeaver em VMs do Azure em SuSE Linux Enterprise Server com Azure NetApp Files para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [alta disponibilidade para NFS em VMs](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)do Azure no SUSE Linux Enterprise Server, [Configurando pacemaker em SuSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), [alta disponibilidade do IBM DB2 LUW em VMs do Azure no SUSE Linux Enterprise Server com pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm), [Alta disponibilidade de SAP Hana em VMs do Azure em SuSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) e [alta disponibilidade para SAP NetWeaver em VMs do Azure no guia de vários SID do RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid) para atualizar recursos de cluster com o agente de recursos Azure-lb 
- 03/05/2020: alterações de estrutura e alterações de conteúdo para regiões do Azure e máquinas virtuais do Azure em [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- 03/03/2020: alteração na [alta disponibilidade para o SAP NW em VMs do Azure no SLES com seja para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) para alterar para um layout de volume seja mais eficiente
- 03/01/2020: [Guia de backup retrabalhado para SAP Hana em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) para incluir o serviço de backup do Azure. Conteúdo reduzido e condensado em [SAP Hana backup do Azure em nível de arquivo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level) e excluiu um terceiro documento lidando com o backup por meio de instantâneo de disco. O conteúdo é tratado no guia de backup para SAP HANA em máquinas virtuais do Azure 
- 02/27/2020: alteração na [alta disponibilidade para o SAP NW em VMs do Azure no SLES para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [alta disponibilidade para SAP NW em VMs do Azure no SLES com seja para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) e [alta disponibilidade para SAP NetWeaver em VMs do Azure em um guia de vários SID do SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) para ajustar o parâmetro de cluster "on Fail"
- 02/26/2020: alterar em [SAP Hana configurações de armazenamento de máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) para esclarecer a opção do sistema de arquivos para o Hana no Azure
- 02/26/2020: alteração na [arquitetura e nos cenários de alta disponibilidade para o SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) incluir o link para a ha para SAP NetWeaver em VMs do Azure no guia de vários SID do RHEL
- 02/26/2020: alteração na [alta disponibilidade para o SAP NW em VMs do Azure no SLES para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [alta disponibilidade para SAP NW em VMs do Azure no SLES com seja para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), alta [disponibilidade de VMs do Azure para SAP NetWeaver em RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) e [VMs do Azure de alta disponibilidade para SAP NetWeaver no RHEL com Azure NetApp files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) para remover a instrução que o cluster ASCS/ers de vários SIDs não tem suporte
- 02/26/2020: lançamento de [alta disponibilidade para SAP NetWeaver em VMs do Azure no guia de vários SID do RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid) para adicionar um link para o guia de cluster do SUSE multi-Sid
- 02/25/2020: alteração na [arquitetura e nos cenários de alta disponibilidade para o SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) adicionar links para artigos mais recentes de ha
- 02/25/2020: alteração na [alta disponibilidade do IBM DB2 LUW em VMs do Azure em SuSE Linux Enterprise Server com pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) para apontar para o documento que descreve o acesso ao ponto de extremidade público com o balanceador de carga do Azure padrão
- 02/21/2020: concluir a revisão do artigo [implantação de DBMS de máquinas virtuais do Azure ase do SAP para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 02/21/2020: alterar em [SAP Hana configuração de armazenamento da máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) para representar uma nova recomendação no tamanho da distribuição para/Hana/data e adicionar a configuração do Agendador de e/s
- 02/21/2020: alterações nos documentos do SAP HANA em instâncias grandes para representar SKUs recém certificados de S224 e S224m
- 02/21/2020: alterar a [alta disponibilidade de VMs do Azure para SAP NetWeaver em RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) e a [alta disponibilidade de VMs do Azure para SAP NetWeaver no RHEL com Azure NetApp files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) para ajustar as restrições de cluster para ENSA2 (arquitetura de replicação de servidor de enfileiramento 2)
- 02/20/2020: alteração em [alta disponibilidade para o SAP NetWeaver em VMs do Azure no guia de vários SID do SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) para adicionar um link para o guia de cluster do SUSE multi-Sid
- 02/13/2020: alterações no [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) para implementar links para novos documentos
- 02/13/2020: foi adicionado um novo documento [de carga de trabalho SAP no cenário com suporte da máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
- 02/13/2020: novo documento adicionado [qual software SAP tem suporte para a implantação do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 02/13/2020: alteração na [alta disponibilidade do IBM DB2 LUW em VMs do Azure no Red Hat Enterprise Linux Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw) para apontar para um documento que descreve o acesso ao ponto de extremidade público com o balanceador de carga do Azure padrão
- 02/13/2020: adicionar os novos tipos de VM a [certificações SAP e configurações em execução no Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 02/13/2020: adicionar novas notas de suporte SAP [cargas de trabalho SAP no Azure: lista de verificação de planejamento e implantação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 02/13/2020: alterar a [alta disponibilidade de VMs do Azure para SAP NetWeaver em RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) e a [alta disponibilidade de VMs do Azure para SAP NetWeaver no RHEL com Azure NetApp files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) para alinhar os tempos limite dos recursos de cluster às recomendações de tempo limite do Red Hat
- 02/11/2020: lançamento de [SAP Hana na migração de instância grande do Azure para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)
- 02/07/2020: alteração na [conectividade de ponto de extremidade pública para VMs usando o ILB padrão do Azure em cenários de ha do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) para atualizar a captura de tela de NSG de exemplo
- 02/03/2020: alterar em [alta disponibilidade para o SAP NW em VMs do Azure no SLES para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) e [alta disponibilidade para SAP NW em VMs do Azure no SLES com seja para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) para remover o aviso sobre o uso de Dash nos nomes de host de nós de cluster no SLES
- 01/28/2020: alterar a [alta disponibilidade de SAP Hana em VMs do Azure no RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) para alinhar os SAP Hana de recursos de cluster para as recomendações de tempo limite do Red Hat
- 01/17/2020: alteração nos [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) para alterar a seção da movimentação de VMs existentes para um grupo de posicionamento de proximidade
- 01/17/2020: alteração nas [configurações de carga de trabalho do SAP com zonas de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) para apontar para um procedimento que automatiza medidas de latência entre zonas de disponibilidade
- 01/16/2020: alterar em [como instalar e configurar SAP Hana (instâncias grandes) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) para adaptar as versões do sistema operacional ao diretório de hardware de IaaS do Hana
- 01/16/2020: alterações em [alta disponibilidade para SAP NetWeaver em VMs do Azure no guia de vários SID do SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) para adicionar instruções para sistemas SAP, usando a arquitetura do enqueue Server 2 (ENSA2)
- 01/10/2020: alterações no [SAP Hana escalar horizontalmente com o nó em espera em VMs do Azure com Azure NetApp files no SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) e no [SAP Hana escalar horizontalmente com o nó em espera em VMs do Azure com Azure NetApp files no RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) para adicionar instruções sobre como tornar as alterações de `nfs4_disable_idmapping` permanentes.
- 01/10/2020: alterações na [alta disponibilidade para SAP NetWeaver em VMs do Azure no SLES com Azure NetApp Files para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) e em [máquinas virtuais do Azure alta disponibilidade para SAP NetWeaver no RHEL com Azure NetApp Files para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) para adicionar instruções sobre como montar volumes Azure NetApp files NFSv4.
- 12/23/2019: lançamento de [alta disponibilidade para SAP NetWeaver em VMs do Azure em guia de vários SID do SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 12/18/2019: versão do [SAP Hana escalar horizontalmente com o nó em espera em VMs do Azure com Azure NetApp files no RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 11/21/2019: alterações no [SAP Hana escalar horizontalmente com o nó em espera em VMs do Azure com Azure NetApp files no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) para simplificar a configuração do mapeamento de ID do NFS e alterar a interface de rede principal recomendada para simplificar o roteamento.
- 11/15/2019: pequenas alterações na [alta disponibilidade para SAP NetWeaver em SuSE Linux Enterprise Server com Azure NetApp Files para aplicativos SAP](high-availability-guide-suse-netapp-files.md) e [alta disponibilidade para sap NetWeaver no Red Hat Enterprise Linux com Azure NetApp Files para aplicativos SAP](high-availability-guide-rhel-netapp-files.md) para esclarecer as restrições de tamanho do pool de capacidade e remover a instrução que apenas a versão NFSv3 tem suporte.
- 11/12/2019: lançamento de [alta disponibilidade para SAP NetWeaver no Windows com Azure NetApp Files (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019: alterações na [alta disponibilidade de SAP Hana em VMs do Azure no SUSE Linux Enterprise Server](sap-hana-high-availability.md), [SAP Hana configurar a replicação do sistema em VMS (máquinas virtuais)](sap-hana-high-availability-rhel.md)do Azure, [a alta disponibilidade de máquinas virtuais do azure para SAP NetWeaver em SuSE Linux Enterprise Server para aplicativos SAP](high-availability-guide-suse.md), alta disponibilidade de máquinas virtuais do Azure [para SAP NetWeaver no SUSE Linux Enterprise Server com Azure NetApp files](high-availability-guide-suse-netapp-files.md), [alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver no Red Hat Enterprise Linux ](high-availability-guide-rhel.md), [Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver em Red Hat Enterprise Linux com Azure NetApp files](high-availability-guide-rhel-netapp-files.md), [alta disponibilidade para NFS em vms do Azure no SUSE Linux Enterprise Server](high-availability-guide-suse-nfs.md), [GlusterFS em VMs do Azure no Red Hat Enterprise Linux para o SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) para recomendar o Azure Standard Load Balancer  
- 11/08/2019: alterações na [lista de verificação de planejamento e implantação da carga de trabalho do SAP](sap-deployment-checklist.md) para esclarecer a recomendação de criptografia  
- 11/04/2019: alterações na [configuração de pacemaker no SUSE Linux Enterprise Server no Azure](high-availability-guide-suse-pacemaker.md) para criar o cluster diretamente com a configuração de unicast  
- 10/29/2019: liberação de [conectividade de ponto de extremidade pública para máquinas virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade do SAP](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 10/25/2019: alterações em [SAP Hana configurações de armazenamento de máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) e [SAP Hana expansão com o nó em espera em VMs do Azure com Azure NetApp files no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) para esclarecer o protocolo NFS para o volume/Hana/Shared
- 10/22/2019: alteração na [alta disponibilidade para SAP NetWeaver em VMs do Azure em SuSE Linux Enterprise Server para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [alta disponibilidade para SAP NetWeaver em VMs do Azure em SuSE Linux Enterprise Server com Azure NetApp Files para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [alta disponibilidade para NFS em VMs](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)do Azure no SUSE Linux Enterprise Server, [Configurando pacemaker em SuSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), [alta disponibilidade do IBM DB2 LUW em VMs do Azure no SUSE Linux Enterprise Server com pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm), e [alta disponibilidade de SAP Hana em VMs do Azure em SuSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) para proteção da detecção do balanceador de carga do Azure
- Altera a seção de seja e seção de cabeçalho em [SAP Hana configurações de armazenamento de máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/21/2019: versão do [SAP Hana escalar horizontalmente com o nó em espera em VMs do Azure com Azure NetApp files no SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 10/16/2019: corrigir links desfeitos em [backup e restauração](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)
- 10/16/2019: alterar o sistema operacional mínimo recomendado do SLES 12 SP3 para o SLES 12 SP4 em [alta disponibilidade do IBM DB2 LUW em VMs do Azure em SuSE Linux Enterprise Server com pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
- 10/11/2019: alterações nas configurações de armazenamento do ultra Disk e introdução do seja em [configurações de armazenamento de máquina virtual SAP Hana Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/01/2019: alteração nos elementos gráficos dos [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) para obter mais clareza
- 10/01/2019: alteração nas [configurações de infraestrutura SAP Hana e nas operações no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) para corrigir instruções sobre o compartilhamento NFS altamente disponível para/Hana/Shared. 
- 09/28/2019: alteração na [configuração de pacemaker em Red Hat Enterprise Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) para esclarecer SBD como um mecanismo de isolamento não tem suporte em CLUSTERs RHEL  
- 09/17/2019: alterar o guia de planejamento e implantação do NetWeaver para unificar os termos relacionados à extensão de VM para SAP  
- 08/22/2019: alterações na [configuração de pacemaker em SuSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) para atualizar as URLs para a criação de função personalizada  
- 08/16/2019: alterações na [configuração de pacemaker em Red Hat Enterprise Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) para lembrar os clientes de atualizar as ações na função personalizada, se atualizar para a nova versão do agente de limite do Azure  
- 08/15/2019: alterações em [SAP Hana configurações de armazenamento de máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) para refletir a disponibilidade geral do ultra Disk (anteriormente SSD ultra)
- 08/01/2019: alterações na [configuração de pacemaker no SUSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) para integrar alterações especificamente para SLES 15 


