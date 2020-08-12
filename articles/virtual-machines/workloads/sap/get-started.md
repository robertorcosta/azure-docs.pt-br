---
title: Introdução às VMs SAP no Azure | Microsoft Docs
description: Aprenda mais sobre soluções SAP que são executadas em VMs (máquinas virtuais) no Microsoft Azure
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
ms.date: 08/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f13d0d2ba26f49daac49c4dd88f3bdf8621c4ad4
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121486"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Usar o Azure para hospedar e executar cenários de carga de trabalho do SAP

Quando você usa o Microsoft Azure, pode executar de modo confiável seus cenários e cargas de trabalho críticas do SAP em uma plataforma escalonável, em conformidade e de eficácia comprovada na empresa. Você obtém a escalabilidade, a flexibilidade e a economia de custos do Azure. Com a parceria expandida entre a Microsoft e a SAP, você pode executar aplicativos da SAP entre cenários de desenvolvimento e teste e produção no Azure, com suporte total. Do SAP NetWeaver ao SAP S/4HANA, do SAP BI no Linux para Windows e do SAP HANA para SQL, nós atendemos às suas necessidades.

Além de hospedar cenários do SAP NetWeaver com os diferentes DBMS no Azure, você pode hospedar outros cenários de carga de trabalho do SAP, assim como SAP BI no Azure. 

A exclusividade do Azure para SAP HANA é uma oferta exclusiva que diferencia o Azure. Para habilitar a hospedagem de mais cenários SAP que exigem recursos de memória e CPU que envolvem SAP HANA, o Azure oferece o uso de hardware bare-metal dedicado ao cliente. Use esta solução para executar implantações do SAP HANA que exigem até 24 TB (expansão de 120 TB) de memória para S/4HANA ou outra carga de trabalho do SAP HANA. 

A hospedagem de cenários de carga de trabalho do SAP no Azure também pode criar requisitos de integração de identidade e logon único. Essa situação pode ocorrer quando você usa o Azure AD (Azure Active Directory) para conectar diferentes componentes SAP e ofertas de SaaS (software como serviço) ou PaaS (plataforma como serviço) do SAP. Uma lista de tais cenários de integração e de logon único com entidades SAP e do Azure AD é descrita e documentada na seção "Integração de identidade e logon único do SAP do AAD".

## <a name="changes-to-the-sap-workload-section"></a>Alterações à seção de carga de trabalho do SAP
Alterações a documentos na seção de carga de trabalho do SAP no Azure estão listadas no final deste artigo. As entradas no log de alterações são mantidas por cerca de 180 dias.

## <a name="you-want-to-know"></a>Você quer saber
Se você tem tiver perguntas específicas, vamos encaminhá-lo a documentos ou fluxos específicos nesta seção da página inicial. Você quer saber:

- Quais VMs do Azure e as unidades de Instância Grande do HANA têm suporte para as versões de software SAP e quais versões do sistema operacional. Leia o documento [Qual software SAP tem suporte para a implantação do Azure](./sap-supported-product-on-azure.md) para obter respostas e o processo para encontrar as informações
- Quais cenários de implantação do SAP têm suporte com VMs do Azure e Instâncias Grandes do HANA. As informações sobre os cenários com suporte podem ser encontradas nos documentos:
    - [Carga de trabalho do SAP em cenários compatíveis com a máquina virtual do Azure](./sap-planning-supported-configurations.md)
    - [Cenários com suporte para Instâncias Grandes do HANA](./hana-supported-scenario.md)
- Quais Serviços do Azure, tipos de VM do Azure e serviços de armazenamento do Azure estão disponíveis nas diferentes regiões do Azure, confira o site [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) 
- Os quadros de HA de terceiros funcionam, além do Windows e do pacemaker, com suporte? Verifique a parte inferior da [Nota de suporte da SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- Qual armazenamento do Azure é melhor para o meu cenário? Ler [tipos de armazenamento do Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide-storage)

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA no Azure (Instâncias Grandes)

Uma série de documentos leva você por meio do SAP HANA no Azure (instâncias grandes) ou, para abreviar, em Instâncias Grandes do HANA. Para obter informações sobre Instâncias Grandes do HANA, comece com o documento [Visão geral e arquitetura do SAP HANA no Azure (Instâncias Grandes)](./hana-overview-architecture.md) e percorra a documentação relacionada na seção Instância Grande do HANA



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA em máquinas virtuais do Azure
Esta seção da documentação aborda diferentes aspectos do SAP HANA. Como pré-requisito, você deve estar familiarizado com os principais serviços do Azure que fornecem serviços elementares de IaaS do Azure. Portanto, você precisa de conhecimento da computação, do armazenamento e da rede do Azure. Muitos desses assuntos são tratados no [Guia de planejamento do Azure](./planning-guide.md) relacionado ao SAP NetWeaver. 

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver implantado em máquinas virtuais do Azure
Esta seção lista a documentação de planejamento e implantação do SAP NetWeaver, SAP LaMa e Business One no Azure. A documentação se concentra nas noções básicas e no uso de bancos de dados não HANA com uma carga de trabalho do SAP no Azure. Os documentos e artigos para alta disponibilidade também são a base para SAP HANA alta disponibilidade no Azure

Para obter informações sobre a alta disponibilidade de uma carga de trabalho do SAP no Azure, confira:

- [Alta disponibilidade de Máquinas Virtuais do Azure para SAP NetWeaver](./sap-high-availability-guide-start.md)



Para obter informações sobre a integração entre Azure AD (Azure Active Directory) e serviços SAP e logon único, confira:

- [Tutorial: Integração do Azure Active Directory com o SAP Cloud for Customer](../../../active-directory/saas-apps/sap-customer-cloud-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP Cloud Platform Identity Authentication](../../../active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP Cloud Platform](../../../active-directory/saas-apps/sap-hana-cloud-platform-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP NetWeaver](../../../active-directory/saas-apps/sap-netweaver-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP Business ByDesign](../../../active-directory/saas-apps/sapbusinessbydesign-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Integração do Azure Active Directory com o SAP HANA](../../../active-directory/saas-apps/saphana-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Seu ambiente S/4HANA: Logon Único do SAML do Fiori Launchpad com o Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Para obter informações sobre a integração dos serviços do Azure em componentes SAP, confira:

- [Usar SAP HANA no Power BI Desktop](/power-bi/desktop-sap-hana)
- [DirectQuery e SAP HANA](/power-bi/desktop-directquery-sap-hana)
- [Usar o Conector do SAP BW no Power BI Desktop](/power-bi/desktop-sap-bw-connector) 
- [O Azure Data Factory oferece integração de dados do SAP HANA e do Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Log de alterações

- 08/11/2020: adicionando RHEL 7,6 a [sistemas operacionais compatíveis para instâncias grandes do Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance) como sistema operacional disponível para unidades de HLI do tipo I
- 08/10/2020: introdução ao custo SAP HANA configuração de armazenamento em [SAP Hana configurações de armazenamento de máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) e fazer algumas atualizações [nas cargas de trabalho do SAP no Azure: lista de verificação de planejamento e implantação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 08/04/2020: alterar na [configuração do pacemaker no SLES no Azure](./high-availability-guide-suse-pacemaker.md) e [Configurar o pacemaker no RHEL no Azure](./high-availability-guide-rhel-pacemaker.md) para enfatizar a importância da resolução de nomes confiável para clusters do pacemaker
- 08/04/2020: alteração no [SAP NW ha no WFCS com compartilhamento de arquivos](./sap-high-availability-installation-wsfc-file-share.md), [SAP NW ha no WFCS com disco compartilhado](./sap-high-availability-installation-wsfc-shared-disk.md), [ha para SAP NW em VMs do Azure](./high-availability-guide.md), [ha para SAP NW em VMs do Azure no SLES](./high-availability-guide-suse.md), [ha para SAP NW em VMs do Azure no SLES com seja](./high-availability-guide-suse-netapp-files.md), [ha para SAP NW em VMs do Azure no guia de vários SID do SLES](./high-availability-guide-suse-multi-sid.md), [alta disponibilidade para SAP NetWeaver em VMs do Azure no RHEL](./high-availability-guide-rhel.md), [ha para SAP NW em VMs do Azure no RHEL com seja](./high-availability-guide-rhel-netapp-files.md) e [ha para SAP NW em VMs do Azure no RHEL multi-Sid](./high-availability-guide-rhel-multi-sid.md) para esclarecer o uso do parâmetro`enque/encni/set_so_keepalive`
- 07/23/2020: foi adicionado o artigo [salvar em SAP Hana em instâncias grandes com um Azure reserva](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md) explicando o que você precisa saber antes de comprar uma reserva de SAP Hana em instâncias grandes e como fazer a compra
- 07/16/2020: descrever como usar Azure PowerShell para instalar a nova extensão de VM para SAP no [Guia de implantação](deployment-guide.md)
- 7/04/2020: lançamento do [Azure monitor para soluções SAP (versão prévia)](./azure-monitor-overview.md)
- 07/01/2020: sugerindo uma configuração de armazenamento com menos custo com base na funcionalidade de intermitência de armazenamento Premium do Azure no documento [SAP Hana configurações de armazenamento de máquina virtual do Azure](./hana-vm-operations-storage.md) 
- 06/24/2020: alteração na [configuração de pacemaker no SLES no Azure](./high-availability-guide-suse-pacemaker.md) para liberar o novo agente de limite do Azure aprimorado e uma configuração de STONITH mais resiliente para dispositivos, com base no agente de limite do Azure 
- 06/24/2020: alteração na [configuração do pacemaker no RHEL no Azure](./high-availability-guide-rhel-pacemaker.md) para liberar uma configuração de STONITH mais resiliente
- 06/23/2020: alterações no [planejamento e implementação de máquinas virtuais do Azure para guia do SAP NetWeaver](./planning-guide.md) e introdução de [tipos de armazenamento do Azure para](./planning-guide-storage.md) guia de carga de trabalho do SAP
- 06/22/2020: adicionar etapas de instalação para a nova extensão de VM para SAP no [Guia de implantação](deployment-guide.md)
- 06/16/2020: alterar a [conectividade do ponto de extremidade público para VMs usando o ILB standard do Azure em cenários de ha do SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md) para adicionar um link para a documentação da infraestrutura de nuvem pública do SUSE 101 
- 06/10/2020: adicionando novas SKUs de HLI em [SKUs disponíveis para a arquitetura de armazenamento de HLI](./hana-available-skus.md) e [SAP Hana (instâncias grandes)](./hana-storage-architecture.md)
- 21/05/2020: alteração a [Configurar o Pacemaker no SLES no Azure](./high-availability-guide-suse-pacemaker.md) e [Configurar o Pacemaker no RHEL no Azure](./high-availability-guide-rhel-pacemaker.md) para adicionar um link para [Conectividade do ponto de extremidade público para VMs usando o ILB Standard do Azure em cenários de HA do SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)  
- 19/05/2020: adicionada uma mensagem importante para não usar o grupo de volumes raiz ao usar o LVM para volumes relacionados ao HANA em [Configurações de armazenamento de máquina virtual do Azure do SAP HANA](./hana-vm-operations-storage.md)
- 05/19/2020: Adicionar novo sistema operacional com suporte para o HANA grande instância de tipo II em [sistemas operacionais compatíveis para instâncias grandes do Hana](/- azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance)
- 12/05/2020: alteração na [Conectividade de ponto de extremidade público para VMs usando o ILB Standard do Azure em cenários de HA do SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md) para atualizar links e adicionar informações para configuração de firewall de terceiros
- 11/05/2020: alteração a [Alta disponibilidade do SAP HANA em VMs do Azure no SLES](./sap-hana-high-availability.md) para definir a adesão de recursos como 0 para o recurso netcat, pois isso leva a um failover mais simplificado 
- 05/05/2020: alterações a [Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver](./planning-guide.md) para expressar que as implantações Gen2 estão disponíveis para a família de VMs Mv1
- 24/04/2020: alterações a [Expansão do SAP HANA com o nó em espera em VMs do Azure com ANF em SLES](./sap-hana-scale-out-standby-netapp-files-suse.md), [Expansão do SAP HANA com o nó em espera em VMs do Azure com ANF em RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md), [Alta disponibilidade para SAP NetWeaver em VMs do Azure em SLES com ANF](./high-availability-guide-suse-netapp-files.md) e [Alta disponibilidade para SAP NetWeaver em VMs do Azure no RHEL com ANF](./high-availability-guide-rhel-netapp-files.md) para adicionar esclarecimentos de que os endereços IP para volumes ANF são atribuídos automaticamente
- 22/04/2020: alteração a [Alta disponibilidade de SAP HANA em VMs do Azure no SLES](./sap-hana-high-availability.md) para remover o meta-atributo `is-managed` das instruções, pois ele entra em conflito com colocar ou tirar o cluster do modo de manutenção
- 21/04/2020: adicionado DB SQL do Azure como DBMS com suporte para a Hybris (SAP) Commerce Platform 1811 e posteriores em artigos [Qual software SAP tem suporte para implantações do Azure](./sap-supported-product-on-azure.md) e [Configurações e certificações do SAP em execução no Microsoft Azure](./sap-certifications.md)
- 16/04/2020: adicionado SAP HANA como DBMS com suporte para a Hybris (SAP) Commerce Platform em artigos [Qual software SAP tem suporte para implantações do Azure](./sap-supported-product-on-azure.md) e [Configurações e certificações do SAP em execução no Microsoft Azure](./sap-certifications.md)
- 13/04/2020: corrigidos os números de versão do SAP ASE exatos em [Implantação de DBMS de máquinas virtuais do Azure SAP ASE para carga de trabalho do SAP](./dbms_guide_sapase.md)
- 07/04/2020: alteração a [Configurar o Pacemaker no SLES no Azure](./high-availability-guide-suse-pacemaker.md) para esclarecer as instruções cloud-netconfig-azure
- 06/04/2020: alterações a [Expansão do SAP HANA com o nó em espera em VMs do Azure com Azure NetApp Files em SLES](./sap-hana-scale-out-standby-netapp-files-suse.md) e [Expansão do SAP Hana com o nó em espera em VMs do Azure com Azure NetApp Files em RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md) para remover referências ao NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf) (substituído por [TR-4746](https://www.netapp.com/us/media/tr-4746.pdf))
- 31 de março de 2020: alterar em [alta disponibilidade de SAP Hana em VMs do Azure no SLES](./sap-hana-high-availability.md) e [alta disponibilidade de SAP Hana em VMs do Azure no RHEL](./sap-hana-high-availability-rhel.md) para adicionar instruções sobre como especificar o tamanho da distribuição ao criar volumes distribuídos
- 27 de março de 2020: alterar em [alta disponibilidade para SAP NW em VMs do Azure no SLES com seja para aplicativos SAP](./high-availability-guide-suse-netapp-files.md) para alinhar as opções de montagem do sistema de arquivos ao NetApp TR-4746 (remover a opção de montagem de sincronização)
- 26 de março de 2020: alterar em [alta disponibilidade para SAP NetWeaver em VMs do Azure no guia de vários SID do SLES](./high-availability-guide-suse-multi-sid.md) para Adicionar referência ao NetApp TR-4746
- 26 de março de 2020: alterar em [alta disponibilidade para SAP NetWeaver em VMs do Azure no SLES para aplicativos SAP](./high-availability-guide-suse.md), [alta disponibilidade para SAP NetWeaver em VMs do Azure no SLES com Azure NetApp Files para aplicativos SAP](./high-availability-guide-suse-netapp-files.md), [alta disponibilidade para NFS em VMs do Azure no SLES](./high-availability-guide-suse-nfs.md), [alta disponibilidade para SAP NetWeaver em VMs do Azure no guia de vários SID do RHEL](./high-availability-guide-suse-multi-sid.md), [alta disponibilidade para SAP NetWeaver em VMs do Azure em RHEL para aplicativos SAP](./high-availability-guide-rhel.md) e [alta disponibilidade para SAP NetWeaver em VMs do Azure em RHEL com Azure NetApp Files para](./high-availability-guide-rhel-netapp-files.md) aplicações do Microsoft SAP para atualizar diagramas e esclarecer instruções para a criação de pool de back-end Azure Load Balancer
- 19 de março de 2020: revisão principal do [início rápido do documento: instalação manual de SAP Hana de instância única em máquinas virtuais do Azure](./hana-get-started.md) para [instalação de SAP Hana em máquinas virtuais do Azure](./hana-get-started.md)
- 17 de março de 2020: alterar na [configuração de pacemaker em SuSE Linux Enterprise Server no Azure](./high-availability-guide-suse-pacemaker.md) para remover a configuração de SBD que não é mais necessária
- Março de 16 2020: esclarecimento do cenário de certificação de coluna em SAP HANA plataforma certificada IaaS em [qual software SAP tem suporte para implantações do Azure](./sap-supported-product-on-azure.md)
- 11/03/2020: alteração a [Carga de trabalho do SAP em cenários com suporte de máquina virtual do Azure](./sap-planning-supported-configurations.md) para esclarecer vários bancos de dados por suporte de instância de DBMS
- 11 de março de 2020: alteração no [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver](./planning-guide.md) explicando VMs de geração 1 e geração 2
- 10 de março de 2020: alterar em [SAP Hana configurações de armazenamento de máquina virtual do Azure](./hana-vm-operations-storage.md) para esclarecer os limites reais de taxa de transferência de seja
- 09 de março de 2020: alterar em [alta disponibilidade para SAP NetWeaver em VMs do Azure em SuSE Linux Enterprise Server para aplicativos SAP](./high-availability-guide-suse.md), [alta disponibilidade para SAP NetWeaver em VMs do Azure em SuSE Linux Enterprise Server com Azure NetApp Files para aplicativos SAP](./high-availability-guide-suse-netapp-files.md), [alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md), [Configurando o pacemaker no SUSE Linux Enterprise Server no Azure](./high-availability-guide-suse-pacemaker.md), [alta disponibilidade do IBM DB2 LUW em VMs do azure no SUSE Linux Enterprise Server com pacemaker](./dbms-guide-ha-ibm.md), [alta disponibilidade de SAP Hana em VMs do Azure no SUSE Linux Enterprise Server](./sap-hana-high-availability.md) e [alta disponibilidade para SAP NetWeaver em VMs do Azure em um guia de vários SID do SLES](./high-availability-guide-suse-multi-sid.md) para atualizar recursos de cluster com o agente de recursos Azure-lb 
- 5 de março de 2020: alterações de estrutura e alterações de conteúdo para regiões do Azure e máquinas virtuais do Azure em [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver](./planning-guide.md)
- 03/03/2020: alteração a [Alta disponibilidade para o SAP NW em VMs do Azure no SLES com ANF para aplicativos SAP](./high-availability-guide-suse-netapp-files.md) para alterar para um layout de volume ANF mais eficiente
- 1º de março de 2020: [Guia de backup retrabalhado para SAP Hana em máquinas virtuais do Azure](./sap-hana-backup-guide.md) para incluir o serviço de backup do Azure. Reduzido e condensado o conteúdo em [Backup do Azure do SAP HANA no nível de arquivo](./sap-hana-backup-file-level.md) e excluído um terceiro documento que tratava do backup por meio de instantâneo de disco. O conteúdo é tratado no guia de backup para SAP HANA em máquinas virtuais do Azure 
- 27 de fevereiro de 2020: alterar em [alta disponibilidade para o SAP NW em VMs do Azure no SLES para aplicativos SAP](./high-availability-guide-suse.md), [alta disponibilidade para SAP NW em VMs do Azure no SLES com seja para aplicativos SAP](./high-availability-guide-suse-netapp-files.md) e alta disponibilidade para SAP NetWeaver em VMs do Azure em clusters de SLES de [vários SIDs](./high-availability-guide-suse-multi-sid.md) para ajustar o parâmetro de cluster "on Fail"
- 26 de fevereiro de 2020: alterar em [SAP Hana configurações de armazenamento de máquina virtual do Azure](./hana-vm-operations-storage.md) para esclarecer a opção do sistema de arquivos para o Hana no Azure
- 26 de fevereiro de 2020: alterar em [arquitetura e cenários de alta disponibilidade para o SAP](./sap-high-availability-architecture-scenarios.md) incluir o link para a ha para SAP NetWeaver em VMs do Azure no guia de vários SID do RHEL
- 26 de fevereiro de 2020: alterar em [alta disponibilidade para o SAP NW em VMs do Azure no SLES para aplicativos SAP](./high-availability-guide-suse.md), [alta disponibilidade para SAP NW em VMs do Azure no SLES com seja para aplicativos SAP](./high-availability-guide-suse-netapp-files.md), alta disponibilidade de [VMs do Azure para SAP NetWeaver no RHEL](./high-availability-guide-rhel.md) e a [alta disponibilidade de VMs do azure para SAP NetWeaver no RHEL com Azure NetApp files](./high-availability-guide-rhel-netapp-files.md) para remover a instrução que o cluster ASCS/ers de vários SIDs
- 26 de fevereiro de 2020: lançamento de [alta disponibilidade para SAP NetWeaver em VMs do Azure no guia de vários SID do RHEL](./high-availability-guide-rhel-multi-sid.md) para adicionar um link ao guia de cluster do SUSE multi-Sid
- 25/02/2020: alteração a [Arquitetura e cenários de alta disponibilidade para o SAP](./sap-high-availability-architecture-scenarios.md) para adicionar links para artigos mais recentes de HA
- 25 de fevereiro de 2020: alteração em [alta disponibilidade do IBM DB2 LUW em VMs do Azure em SuSE Linux Enterprise Server com pacemaker](./dbms-guide-ha-ibm.md) para apontar para o documento que descreve o acesso ao ponto de extremidade público com o Azure Load Balancer padrão
- 21 de fevereiro de 2020: revisão completa do artigo [implantação de DBMS de máquinas virtuais do Azure ase do SAP para carga de trabalho do SAP](./dbms_guide_sapase.md)
- 21 de fevereiro de 2020: alterar em [SAP Hana configuração de armazenamento da máquina virtual do Azure](./hana-vm-operations-storage.md) para representar uma nova recomendação no tamanho da distribuição para/Hana/data e adicionar a configuração do Agendador de e/s
- 21 de fevereiro de 2020: alterações em documentos do SAP HANA em instâncias grandes para representar SKUs recém certificados de S224 e S224m
- 21 de fevereiro de 2020: alteração em [VMs do Azure alta disponibilidade para SAP NetWeaver em RHEL](./high-availability-guide-rhel.md) e [a alta disponibilidade de VMs do Azure para SAP NetWeaver no RHEL com Azure NetApp files](./high-availability-guide-rhel-netapp-files.md) para ajustar as restrições de cluster para ENSA2 (arquitetura de replicação de servidor de enfileiramento 2)
- 20 de fevereiro de 2020: alterar em [alta disponibilidade para SAP NetWeaver em VMs do Azure no guia de vários SID do SLES](./high-availability-guide-suse-multi-sid.md) para adicionar um link ao guia de cluster do SUSE multi-Sid
- 13 de fevereiro de 2020: alterações no [planejamento e implementação de máquinas virtuais do Azure para o SAP NetWeaver](./planning-guide.md) implementar links para novos documentos
- 13 de fevereiro de 2020: Adicionado novo documento [carga de trabalho SAP no cenário com suporte da máquina virtual do Azure](./sap-planning-supported-configurations.md)
- 13 de fevereiro de 2020: Adicionado novo documento [que software SAP tem suporte para a implantação do Azure](./sap-supported-product-on-azure.md)
- 13 de fevereiro de 2020: alteração em [alta disponibilidade do IBM DB2 LUW em VMs do Azure no Red Hat Enterprise Linux Server](./high-availability-guide-rhel-ibm-db2-luw.md) para apontar para o documento que descreve o acesso ao ponto de extremidade público com o Azure Load Balancer padrão
- 13 de fevereiro de 2020: adicionar os novos tipos de VM a [certificações SAP e configurações em execução no Microsoft Azure](./sap-certifications.md)
- 13 de fevereiro de 2020: adicionar novas notas de suporte SAP [cargas de trabalho SAP no Azure: lista de verificação de planejamento e implantação](./sap-deployment-checklist.md)
- 13 de fevereiro de 2020: alteração nas [VMs do Azure alta disponibilidade para SAP NetWeaver em RHEL](./high-availability-guide-rhel.md) e [a alta disponibilidade de VMs do Azure para SAP NetWeaver no RHEL com Azure NetApp files](./high-availability-guide-rhel-netapp-files.md) para alinhar os tempos limite dos recursos de cluster às recomendações de tempo limite do Red Hat
- 11 de fevereiro de 2020: lançamento de [SAP Hana na migração de instância grande do Azure para máquinas virtuais do Azure](./hana-large-instance-virtual-machine-migration.md)
- 7 de fevereiro de 2020: alteração na [conectividade de ponto de extremidade pública para VMs usando o ILB padrão do Azure em cenários de ha do SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md) para atualizar captura de tela de NSG de exemplo
- Fevereiro de 03, 2020: alterar em [alta disponibilidade para o SAP NW em VMs do Azure no SLES para aplicativos SAP](./high-availability-guide-suse.md) e [alta disponibilidade para SAP NW em VMs do Azure no SLES com seja para aplicativos SAP](./high-availability-guide-suse-netapp-files.md) para remover o aviso sobre o uso de Dash nos nomes de host de nós de cluster no SLES
- 28 de janeiro de 2020: alterar em [alta disponibilidade de SAP Hana em VMs do Azure no RHEL](./sap-hana-high-availability-rhel.md) para alinhar os SAP Hana de recursos de cluster para as recomendações de tempo limite do Red Hat
- 17 de janeiro de 2020: alteração nos [grupos de posicionamento de proximidade do Azure para latência de rede ideal com aplicativos SAP](./sap-proximity-placement-scenarios.md) para alterar a seção da movimentação de VMs existentes para um grupo de posicionamento de proximidade
- 17 de janeiro de 2020: alteração nas [configurações de carga de trabalho do SAP com zonas de disponibilidade do Azure](./sap-ha-availability-zones.md) para apontar para um procedimento que automatiza medidas de latência entre zonas de disponibilidade
- 16 de janeiro de 2020: alterar em [como instalar e configurar SAP Hana (instâncias grandes) no Azure](./hana-installation.md) para adaptar as versões do sistema operacional ao diretório de hardware de IaaS do Hana
- 16 de janeiro de 2020: alterações em [alta disponibilidade para SAP NetWeaver em VMs do Azure no guia de vários SID do SLES](./high-availability-guide-suse-multi-sid.md) para adicionar instruções para sistemas SAP, usando a arquitetura do enqueue Server 2 (ENSA2)
- 10 de janeiro de 2020: alterações no [SAP Hana escalar horizontalmente com o nó em espera em VMs do Azure com Azure NetApp files no SLES](./sap-hana-scale-out-standby-netapp-files-suse.md) e no [SAP Hana escalar horizontalmente com o nó em espera em VMs do Azure com Azure NetApp files no RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md) para adicionar instruções sobre como fazer `nfs4_disable_idmapping` alterações permanentes.
- 10 de janeiro de 2020: alterações na [alta disponibilidade para SAP NetWeaver em VMs do Azure no SLES com Azure NetApp Files para aplicativos SAP](./high-availability-guide-suse-netapp-files.md) e em [máquinas virtuais do Azure alta disponibilidade para SAP NetWeaver no RHEL com Azure NetApp Files para aplicativos SAP](./high-availability-guide-rhel-netapp-files.md) para adicionar instruções sobre como montar volumes Azure NetApp files NFSv4.
- 23 de dezembro de 2019: Lançamento de [Alta disponibilidade para SAP NetWeaver em VMs do Azure no guia de vários SID em SLES](./high-availability-guide-suse-multi-sid.md)
- 18 de dezembro de 2019: Lançamento de [Expansão do SAP HANA com nó em espera em VMs do Azure com Azure NetApp Files em RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- 21 de novembro de 2019: alterações na [Expansão do SAP HANA com o nó em espera em VMs do Azure com Azure NetApp Files em SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md) para simplificar a configuração do mapeamento de ID do NFS e alterar o adaptador de rede principal recomendado para simplificar o roteamento.
- 15 de novembro de 2019: alterações secundárias a [Alta disponibilidade para SAP NetWeaver em SUSE Linux Enterprise Server com Azure NetApp Files para aplicativos SAP](high-availability-guide-suse-netapp-files.md) e [Alta disponibilidade para SAP NetWeaver no Red Hat Enterprise Linux com Azure NetApp Files para aplicativos SAP](high-availability-guide-rhel-netapp-files.md) para esclarecer as restrições de tamanho do pool de capacidade e remover a instrução que apenas a versão NFSv3 tem suporte.
- 12 de novembro de 2019: lançamento de [Alta disponibilidade para SAP NetWeaver no Windows com Azure NetApp Files (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 8 de novembro de 2019: alterações a [Alta disponibilidade do SAP HANA em VMs do Azure no SUSE Linux Enterprise Server](sap-hana-high-availability.md), [Configurar a replicação de sistema de SAP HANA em VMs (máquinas virtuais) do Azure](sap-hana-high-availability-rhel.md), [Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver em SUSE Linux Enterprise Server para aplicativos SAP](high-availability-guide-suse.md), [Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver no SUSE Linux Enterprise Server com Azure NetApp files](high-availability-guide-suse-netapp-files.md), [Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver no Red Hat Enterprise Linux](high-availability-guide-rhel.md), [Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver em Red Hat Enterprise Linux  com Azure NetApp Files](high-availability-guide-rhel-netapp-files.md), [Alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server](high-availability-guide-suse-nfs.md), [GlusterFS em VMs do Azure no Red Hat Enterprise Linux para o SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) para recomendar o Azure Standard Load Balancer  
- 8 de novembro de 2019: alterações a [Lista de verificação de planejamento e implantação de carga de trabalho SAP](sap-deployment-checklist.md) para esclarecer a recomendação de criptografia  
- 4 de novembro de 2019: alterações a [Configuração do Pacemaker no SUSE Linux Enterprise Server no Azure](high-availability-guide-suse-pacemaker.md) para criar o cluster diretamente com a configuração de unicast  
