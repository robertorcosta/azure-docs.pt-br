---
title: Comece com o SAP nas VMs do Azure | Microsoft Docs
description: Conheça as soluções SAP que são executadas em máquinas virtuais (VMs) no Microsoft Azure
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
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0866db6b549bc940d2e73bccfc77b8fad19abd51
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273215"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Use o Azure para hospedar e executar cenários de carga de trabalho SAP

Quando você usa o Microsoft Azure, você pode executar de forma confiável suas cargas de trabalho e cenários SAP essenciais em uma plataforma escalável, compatível e comprovada pela empresa. Você tem a escalabilidade, flexibilidade e economia de custos do Azure. Com a parceria expandida entre a Microsoft e o SAP, você pode executar aplicativos SAP em cenários de desenvolvimento e teste e produção no Azure e ser totalmente suportado. De SAP NetWeaver a SAP S/4HANA, SAP BI no Linux para Windows e SAP HANA para SQL, temos você coberto.

Além de hospedar cenários SAP NetWeaver com os diferentes DBMS no Azure, você pode hospedar outros cenários de carga de trabalho SAP, como o SAP BI no Azure. 

A singularidade do Azure para o SAP HANA é uma oferta que diferencia o Azure. Para permitir a hospedagem de mais cenários SAP exigentes de memória e CPU que envolvem o SAP HANA, o Azure oferece o uso de hardware de metal nu dedicado ao cliente. Use esta solução para executar implantações SAP HANA que requerem até 24 TB (escala de 120 TB) de memória para S/4HANA ou outra carga de trabalho SAP HANA. 

Hospedar cenários de carga de trabalho SAP no Azure também pode criar requisitos de integração de identidade e de adesão única. Essa situação pode ocorrer quando você usa o Azure Active Directory (Azure AD) para conectar diferentes componentes SAP e sap software-as-a-service (SaaS) ou ofertas de plataforma como serviço (PaaS). Uma lista de tais cenários de integração e de login único com entidades Azure AD e SAP é descrita e documentada na seção "Integração de identidade AAD SAP e único login".

## <a name="changes-to-the-sap-workload-section"></a>Alterações na seção de carga de trabalho SAP
As alterações nos documentos na seção de carga de trabalho SAP on Azure são listadas no final deste artigo. As entradas no registro de alteração são mantidas por cerca de 180 dias.

## <a name="you-want-to-know"></a>Você quer saber
Se você tiver perguntas específicas, vamos apontá-lo para documentos ou fluxos específicos nesta seção da página inicial. Você quer saber:

- Quais unidades azure VMs e HANA Large Instance são suportadas para quais o software SAP lança e quais versões do sistema operacional. Leia o documento [Qual software SAP é suportado para a implantação do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) para obter respostas e o processo para encontrar as informações
- Quais cenários de implantação sap são suportados com VMs Azure e HANA Large Instances. Informações sobre os cenários suportados podem ser encontradas nos documentos:
    - [Carga de trabalho SAP em cenários suportados por máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [Cenários suportados para HANA Large Instance](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- Quais serviços de Azure Services, Azure VM e serviços de armazenamento Do Zure estão disponíveis nas diferentes regiões do Azure, verifique o site [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) 

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA no Azure (Instâncias Grandes)

Uma série de documentos leva você através do SAP HANA no Azure (Instâncias Grandes), ou para abreviar, HANA Large Instances. Para obter informações sobre hana grandes instâncias comece com a visão geral do documento [e a arquitetura do SAP HANA no Azure (Instâncias Grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) e passe pela documentação relacionada na seção HANA Large Instance



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA em máquinas virtuais Azure
Esta seção da documentação aborda diferentes aspectos do SAP HANA. Como pré-requisito, você deve estar familiarizado com os principais serviços do Azure que fornecem serviços elementares do Azure IaaS. Então, você precisa de conhecimento de computação, armazenamento e rede do Azure. Muitos desses assuntos são tratados no guia de [planejamento azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)relacionado ao SAP NetWeaver . 

Para obter informações sobre hana no Azure, consulte os seguintes artigos e seus subartigos:

- [Configurações e operações de infraestrutura do SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Alta disponibilidade do SAP HANA para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Alta disponibilidade do SAP HANA em VMs (máquinas virtuais) do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Guia de backup para SAP HANA em máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver implantado em máquinas virtuais Azure
Esta seção lista a documentação de planejamento e implantação do SAP NetWeaver e do Business One no Azure. A documentação se concentra no básico e no uso de bancos de dados não HANA com uma carga de trabalho SAP no Azure. Os documentos e artigos para alta disponibilidade também são a base para a alta disponibilidade do HANA no Azure, tais como:

- [Guia de planejamento azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 
- [SAP Business One em máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Proteja uma implantação de aplicativo SAP NetWeaver multinível usando a recuperação do site](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Conector SAP LaMa para o Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Para obter informações sobre bancos de dados não HANA sob uma carga de trabalho SAP no Azure, consulte:

- [Considerações para Implantação do DBMS de Máquinas Virtuais do Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Implantação do DBMS de Máquinas de Virtuais do SQL Server Azure para NetWeaver do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Implantação do DBMS de Máquinas Virtuais do Oracle Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Implantação do DBMS de Máquinas Virtuais do IBM DB2 Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Implantação do DBMS de Máquinas Virtuais do SAP ASE Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Implantação do SAP MaxDB, cache ao vivo e servidor de conteúdo em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Para obter informações sobre os bancos de dados SAP HANA no Azure, consulte a seção "SAP HANA em máquinas virtuais Do Azure".

Para obter informações sobre a alta disponibilidade de uma carga de trabalho SAP no Azure, consulte:

- [Alta disponibilidade de Máquinas Virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Este documento aponta para vários outros documentos de arquitetura e cenário. Em documentos de cenário posterior, são fornecidos links para documentos técnicos detalhados que explicam a implantação e configuração dos diferentes métodos de alta disponibilidade. Os diferentes documentos que mostram como estabelecer e configurar alta disponibilidade para uma carga de trabalho SAP NetWeaver cobrem sistemas operacionais Linux e Windows.


Para obter informações sobre a integração entre os serviços Azure Active Directory (Azure AD) e SAP e o login único, consulte:

- [Tutorial: integração do Azure Active Directory ao SAP Cloud for Customer](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Microsoft Azure Active Directory com a SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: integração do Azure Active Directory ao SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: integração do Azure Active Directory com o SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Seu ambiente S/4HANA: Fiori Launchpad SAML single sign-on com Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Para obter informações sobre a integração dos serviços do Azure em componentes SAP, consulte:

- [Usar SAP HANA no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery e SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Usar o Conector do SAP BW no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [O Azure Data Factory oferece integração de dados do SAP HANA e do Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Log de alterações
- 13/04/2020: Correto os números de lançamento do SAP ASE na [implantação do SAP ASE Azure Virtual Machines DBMS para carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 04/07/2020: Mudança na [configuração do Marcapasso no SLES no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) para esclarecer instruções cloud-netconfig-azure
- 04/06/2020: Alterações no [scale-out SAP HANA com nó de espera em VMs Azure com Arquivos Azure NetApp em SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) e em [escala SAP HANA com nó de espera em VMs Azure com Arquivos Azure NetApp no RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) para remover referências ao NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf) (substituído por [TR-4746](https://www.netapp.com/us/media/tr-4746.pdf))
- 31/03/2020: Mudança na [alta disponibilidade de SAP HANA em VMs Azure em SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) e [Alta disponibilidade de SAP HANA em VMs Azure no RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) para adicionar instruções de como especificar o tamanho da listra ao criar volumes listrados
- 27/03/2020: Alteração na [alta disponibilidade para SAP NW em VMs Azure em SLES com aplicações ANF para SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) para alinhar as opções de montagem do sistema de arquivos ao NetApp TR-4746 (remover a opção de montagem de sincronização)
- 26/03/2020: Mudança na [alta disponibilidade para SAP NetWeaver no Azure VMs no guia Multi-SID sles](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) para adicionar referência ao NetApp TR-4746
- 26/03/2020: Mudança na [alta disponibilidade para SAP NetWeaver em VMs Azure em SLES para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Alta disponibilidade para SAP NetWeaver em VMs Azure em SLES com Arquivos Azure NetApp para aplicativos SAP,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) [Alta disponibilidade para NFS em VMs Azure no SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [Alta disponibilidade para SAP NetWeaver no Azure VMs no guia Multi-SID SLES,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) [alta disponibilidade para SAP NetWeaver em VMs Azure em RHEL para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) e [alta disponibilidade para SAP NetWeaver no Azure VMs no RHEL com Azure NetApp Files para aplicativos SAP para](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) atualizar diagramas e esclarecer instruções para criação de backend pool do Azure Load Balancer
- 19/03/2020: Revisão principal do documento [Quickstart: Instalação manual de SAP HANA de instância única em Máquinas Virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started) para [instalação de SAP HANA em Máquinas Virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- 17/03/2020: Mudança na [configuração do Pacemaker no SUSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) para remover a configuração do SBD que não é mais necessária
- 16/03/2020: Esclarecimento do cenário de certificação de colunas na plataforma certificada SAP HANA IaaS em [que o software SAP é suportado para implantações do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 11/03/2020: Mudança na [carga de trabalho SAP em cenários suportados por máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) para esclarecer vários bancos de dados por suporte a instâncias DBMS
- 11/03/2020: Mudança no [planejamento e implementação de máquinas virtuais do Azure para O SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) explicando VMs de Geração 1 e Geração 2
- 03/10/2020: Mudança nas [configurações de armazenamento de máquinavirtual SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) para esclarecer os limites reais de throughput existentes da ANF
- 03/09/2020: Mudança na [alta disponibilidade para SAP NetWeaver no Azure VMs no SUSE Linux Enterprise Server para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Alta disponibilidade para SAP NetWeaver no Azure VMs no SUSE Linux Enterprise Server com Arquivos Azure NetApp para aplicativos SAP,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) [Alta disponibilidade para NFS no Azure VMs no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [Configuração Marcapasso no SUSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), [Alta disponibilidade do IBM Db2 LUW no Azure VMs no SUSE Linux Enterprise Server com pacemaker,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) [alta disponibilidade de SAP HANA no Azure VMs no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) e alta disponibilidade para [SAP NetWeaver no Azure VMs no guia Multi-SID do SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) para atualizar recursos de cluster com o agente de recursos azure-lb 
- 05/03/2020: Mudanças de estrutura e mudanças de conteúdo para regiões azure e máquinas virtuais do Azure no planejamento e implementação de [máquinas virtuais do Azure para o SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- 03/03/2020: Mudança na [alta disponibilidade para SAP NW em VMs Azure em SLES com aplicações ANF para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) para mudar para layout de volume ANF mais eficiente
- 03/01/2020: Guia de backup reformulado [para SAP HANA em Máquinas Virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) para incluir o serviço de backup do Azure. Conteúdo reduzido e condensado no [SAP HANA Azure Backup no nível do arquivo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level) e excluiu um terceiro documento que lida com backup através de instantâneo de disco. O conteúdo é manipulado no guia de backup do SAP HANA em Máquinas Virtuais do Azure 
- 27/02/2020: Mudança na [alta disponibilidade para SAP NW em VMs Azure em SLES para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Alta disponibilidade para SAP NW em VMs Azure em SLES com ANF para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) e [alta disponibilidade para SAP NetWeaver no Azure VMs no guia Multi-SID SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) para ajustar o parâmetro de cluster "on fail" para ajustar o parâmetro de cluster "on fail" para ajustar o parâmetro de cluster "on fail" para ajustar o parâmetro de cluster "on fail" para ajustar o parâmetro de cluster "on fail" para ajustar o parâmetro de cluster "on fail" para ajustar o parâmetro de cluster "on fail" para ajustar o parâmetro de cluster "on fail" para ajustar o parâmetro de cluster "on fail" para ajustar o parâmetro de cluster "on fail" para ajustar o parâmetro de cluster "on fail" para ajustar o parâmetro de cluster "on fail" para ajustar o parâmetro de cluster "on fail" para ajustar o parâmetro de cluster "on fail" para ajustar o parâmetro de cluster "on fail" para ajustar o parâmetro de cluster "on fail" para ajustar o parâmetro de cluster "on fail" para ajustar o parâmetro de cluster "on fail" para ajustar o parâmetro de cluster "on fail" para ajustar o
- 26/02/2020: Mudança nas [configurações de armazenamento de máquinavirtual SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) para esclarecer a escolha do sistema de arquivos para HANA no Azure
- 26/02/2020: Mudança na [arquitetura e cenários de alta disponibilidade para sap](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) para incluir o link para o HA para SAP NetWeaver no azure VMs no guia RHEL multi-SID
- 26/02/2020: Mudança na [alta disponibilidade para SAP NW em VMs Azure em SLES para aplicações SAP,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) [Alta disponibilidade para SAP NW em VMs Azure em SLES com ANF para aplicações SAP,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) [A Zure VMs alta disponibilidade para SAP NetWeaver em VMs RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) e [Azure alta disponibilidade para SAP NetWeaver no RHEL com Arquivos Azure NetApp](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) para remover a declaração de que o cluster MULTI-SID ASCS/ERS não é suportado
- 26/02/2020: Lançamento de [alta disponibilidade para SAP NetWeaver em VMs Azure no guia RHEL multi-SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid) para adicionar um link ao guia de cluster multiSID SUSE
- 25/02/2020: Mudança na [arquitetura e cenários de alta disponibilidade para SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) para adicionar links a artigos de HA mais novos
- 25/02/2020: Mudança na [alta disponibilidade do IBM Db2 LUW no Azure VMs no SUSE Linux Enterprise Server com pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) para apontar para documento que descreve o acesso ao ponto final público com balanceador standard azure Load
- 21/02/2020: Revisão completa do artigo [SAP ASE Azure Virtual Machines DBMS implantação para carga de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 21/02/2020: Alteração na [configuração de armazenamento da máquina virtual SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) para representar uma nova recomendação em tamanho de listra para /hana/dados e adição de configuração do agendador de I/O
- 21/02/2020: Alterações nos documentos hana de grande instância para representar SKUs recém-certificados de S224 e S224m
- 21/02/2020: Mudança nas [VMs do Azure alta disponibilidade para SAP NetWeaver em VMs RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) e [Azure alta disponibilidade para SAP NetWeaver no RHEL com Arquivos Azure NetApp](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) para ajustar as restrições de cluster para a arquitetura enqueue server replication 2 (ENSA2)
- 20/02/2020: Mudança na [alta disponibilidade do SAP NetWeaver no Azure VMs no guia Multi-SID do SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) para adicionar um link ao guia de cluster multiSID SUSE
- 13/02/2020: Alterações no [planejamento e implementação de máquinas virtuais do Azure para o SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) para implementar links para novos documentos
- 13/02/2020: Adicionada nova carga de trabalho SAP de documento [no cenário suportado por máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
- 13/02/2020: Novo documento [Adicionado Qual software SAP é suportado para implantação do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 13/02/2020: Mudança na [alta disponibilidade do IBM Db2 LUW no Azure VMs no Red Hat Enterprise Linux Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw) para apontar para documento que descreve o acesso ao ponto final público com balanceador Standard Azure Load
- 13/02/2020: Adicione os novos tipos de VM às [certificações e configurações SAP em execução no Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 13/02/2020: Adicione novas notas de suporte [sap cargas de trabalho SAP no Azure: lista de verificação de planejamento e implantação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 13/02/2020: Mudança nas [VMs do Azure alta disponibilidade para SAP NetWeaver no RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) e [Azure VMs alta disponibilidade para SAP NetWeaver no RHEL com Arquivos Azure NetApp](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) para alinhar os tempos de tempo dos recursos do cluster às recomendações de tempo out do Red Hat
- 11/02/2020: Lançamento do [SAP HANA na migração de grande instância do Azure para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)
- 02/07/2020: Mudança na [conectividade de ponto final público para VMs usando ILB Padrão Azure em cenários SAP HA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) para atualizar a captura de tela da amostra NSG
- 02/03/2020: Mudança na [alta disponibilidade para SAP NW em VMs Azure em SLES para aplicações SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) e [alta disponibilidade para SAP NW em VMs Azure em SLES com aplicações ANF para Aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) para remover o aviso sobre o uso de dash nos nomes host de nomes de cluster sles
- 28/01/2020: Mudança na [alta disponibilidade de SAP HANA em VMs Azure no RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) para alinhar os tempos de tempo dos recursos do cluster SAP HANA às recomendações de tempo out da Red Hat
- 17/01/2020: Mudança nos [grupos de colocação de proximidade do Azure para a latência ideal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) da rede com aplicativos SAP para alterar a seção de movimentação de VMs existentes em um grupo de colocação de proximidade
- 17/01/2020: Alteração nas [configurações de carga de trabalho SAP com zonas de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) para apontar para um procedimento que automatiza medições de latência entre Zonas de Disponibilidade
- 16/01/2020: Alteração em [como instalar e configurar SAP HANA (Instâncias Grandes) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) para adaptar as versões do Sistema Operacional ao diretório de hardware HANA IaaS
- 16/01/2020: Alterações na [alta disponibilidade do SAP NetWeaver no Azure VMs no guia Multi-SID sles](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) para adicionar instruções para sistemas SAP, usando a arquitetura enqueue server 2 (ENSA2)
- 01/10/2020: Alterações no [scale-out SAP HANA com nó de espera em VMs Azure com Arquivos Azure NetApp em SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) e em [escala SAP HANA com nó de espera em VMs Azure com Arquivos Azure NetApp no RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) para adicionar instruções sobre como tornar `nfs4_disable_idmapping` as alterações permanentes.
- 01/10/2020: Mudanças na [alta disponibilidade do SAP NetWeaver no Azure VMs no SLES com arquivos Azure NetApp para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) e em [Azure Virtual Machines alta disponibilidade para SAP NetWeaver no RHEL com azure NetApp Files para aplicativos SAP para](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) adicionar instruções de como montar os volumes do Azure NetApp Files NFSv4.
- 23/12/2019: Lançamento de [alta disponibilidade para SAP NetWeaver em VMs Azure no guia multiSID SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 18/12/2019: Lançamento do [scale-out SAP HANA com nó de espera em VMs Azure com Arquivos Azure NetApp no RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 21/11/2019: Alterações no [scale-out SAP HANA com nó de espera em VMs Azure com Arquivos Azure NetApp no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) para simplificar a configuração para mapeamento de ID NFS e alterar a interface de rede principal recomendada para simplificar o roteamento.
- 15/11/2019: Pequenas alterações na [alta disponibilidade do SAP NetWeaver no SUSE Linux Enterprise Server com o Azure NetApp Files para aplicativos SAP](high-availability-guide-suse-netapp-files.md) e [alta disponibilidade para SAP NetWeaver no Red Hat Enterprise Linux com a azure NetApp Files para aplicativos SAP para](high-availability-guide-rhel-netapp-files.md) esclarecer restrições de tamanho do pool de capacidade e remover a declaração de que apenas a versão NFSv3 é suportada.
- 12/11/2019: Lançamento de [alta disponibilidade para SAP NetWeaver no Windows com Arquivos Azure NetApp (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019: Mudanças na [alta disponibilidade do SAP HANA no Azure VMs no SUSE Linux Enterprise Server](sap-hana-high-availability.md), [Configure a replicação do sistema SAP HANA em máquinas virtuais (VMs) Azure,](sap-hana-high-availability-rhel.md) [azure Virtual Machines alta disponibilidade para SAP NetWeaver no SUSE Linux Enterprise Server para aplicativos SAP,](high-availability-guide-suse.md) [Azure Virtual Machines alta disponibilidade para SAP NetWeaver no SUSE Linux Enterprise Server com Azur e NetApp Files](high-availability-guide-suse-netapp-files.md), [Azure Virtual Machines alta disponibilidade para SAP NetWeaver no Red Hat Enterprise Linux](high-availability-guide-rhel.md), [Azure Virtual Machines alta disponibilidade para SAP NetWeaver no Red Hat Enterprise Linux com Arquivos Azure NetApp](high-availability-guide-rhel-netapp-files.md), [Alta disponibilidade para NFS no Azure VMs no SUSE Linux Enterprise Server,](high-availability-guide-suse-nfs.md) [GlusterFS no Azure Enterprise Linux para SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) para recomendar Azure standard loadr balance  
- 08/11/2019: Mudanças no [planejamento da carga de trabalho SAP e na lista de verificação de implantação](sap-deployment-checklist.md) para esclarecer a recomendação de criptografia  
- 04/11/2019: Mudanças na [configuração do Pacemaker no SUSE Linux Enterprise Server no Azure](high-availability-guide-suse-pacemaker.md) para criar o cluster diretamente com a configuração unicast  
- 29/10/2019: Lançamento da [conectividade de ponto final público para máquinas virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade SAP](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 25/10/2019: Mudanças nas [configurações de armazenamento de máquinas virtuais SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) e [scale-out SAP HANA com nó de espera em VMs Azure com Arquivos Azure NetApp no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) para esclarecer o protocolo NFS para /hana/volume compartilhado
- 22/10/2019: Mudança na [alta disponibilidade para SAP NetWeaver no Azure VMs no SUSE Linux Enterprise Server para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Alta disponibilidade para SAP NetWeaver no Azure VMs no SUSE Linux Enterprise Server com Arquivos Azure NetApp para aplicativos SAP,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) [alta disponibilidade para NFS no Azure V MMs no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [Configurando pacemaker no SUSE Linux Enterprise Server no Azure,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) [alta disponibilidade do IBM Db2 LUW no Azure VMs no SUSE Linux Enterprise Server com marcapasso](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm), e [alta disponibilidade de SAP HANA no Azure VMs no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) para azure Load-Balancer Detecção de Endurecimento
- Altera a seção ANF e a seção de cabeçalho nas [configurações de armazenamento de máquinavirtual SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 21/10/2019: Lançamento do [scale-out SAP HANA com nó de espera em VMs Azure com Arquivos Azure NetApp em SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 16/10/2019: Corrija links quebrados em [Backup e restauração](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)
- 16/10/2019: Mude o sistema operacional mínimo recomendado de SLES 12 SP3 para SLES 12 SP4 em [alta disponibilidade do IBM Db2 LUW no Azure VMs no SUSE Linux Enterprise Server com pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
- 11/10/2019: Alterações nas configurações de armazenamento em disco Ultra e introdução do ANF nas [configurações de armazenamento de máquina virtual SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 01/10/2019: Mudança nos gráficos dos grupos de colocação de proximidade do [Azure para a latência ideal da rede com aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) para obter mais clareza
- 01/10/2019: Alterações nas [configurações e operações de infra-estrutura SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) para corrigir instruções em torno do compartilhamento de NFS altamente disponível para /hana/compartilhado. 



