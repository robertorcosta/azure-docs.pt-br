---
title: Azure VMs HA para SAP NW no Windows com Arquivos Azure NetApp (SMB)| Microsoft Docs
description: Alta disponibilidade para SAP NetWeaver em VMs Azure no Windows com Arquivos Azure NetApp (SMB) para aplicativos SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: radeltch
ms.openlocfilehash: b41db629c5308348f632b3dc51c75822ba361c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591346"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Alta disponibilidade para SAP NetWeaver em VMs Azure no Windows com Arquivos Azure NetApp (SMB) para aplicativos SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Este artigo descreve como implantar, configurar as máquinas virtuais, instalar a estrutura de cluster e instalar um sistema SAP NetWeaver 7.50 altamente disponível em VMs Windows, usando [SMB](https://docs.microsoft.com/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) em [Arquivos Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).  

A camada de banco de dados não é coberta em detalhes neste artigo. Assumimos que a [rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) Azure já foi criada.  

Primeiro, leia os seguintes documentos e Notas SAP:

* [Documentação de arquivos do Azure NetApp][anf-azure-doc] 
* SAP Note [1928533,][1928533]que contém:  
  * Uma lista de tamanhos de VM do Azure que são suportados para a implantação de software SAP
  * Informações importantes sobre capacidade para tamanhos de VM do Azure
  * Software SAP e combinações de SO (sistema operacional) e banco de dados com suporte
  * Versão do kernel SAP necessária para Windows no Microsoft Azure
* A Nota SAP [2015553][2015553] lista pré-requisitos para implantações de software SAP com suporte do SAP no Azure.
* A Nota SAP [2178632][2178632] contém informações detalhadas sobre todas as métricas de monitoramentos relatadas para o SAP no Azure.
* A Nota SAP [1999351][1999351] tem informações de solução de problemas adicionais para a Extensão de Monitoramento Avançado do Azure para SAP.
* O SAP Note [2287140](https://launchpad.support.sap.com/#/notes/2287140) lista pré-requisitos para o recurso CA suportado pelo SAP do protocolo SMB 3.x.
* O SAP Note [2802770](https://launchpad.support.sap.com/#/notes/2802770) tem informações de solução de problemas para a transação SAP de execução lenta AL11 no Windows 2012 e 2016.
* O SAP Note [1911507](https://launchpad.support.sap.com/#/notes/1911507) tem informações sobre o recurso de failover transparente para um compartilhamento de arquivos no Windows Server com o protocolo SMB 3.0.
* O SAP Note [662452](https://launchpad.support.sap.com/#/notes/662452) tem recomendação (desativar a geração de nomes 8.3) para lidar com o desempenho/erros do sistema de arquivos ruins durante os acessos aos dados.
* [Instalar alta disponibilidade do SAP NetWeaver em um cluster de failover do Windows e compartilhamento de arquivos para instâncias ASCS/SCS do SAP no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-file-share) 
* [Arquitetura e Cenários de Alta disponibilidade de Máquinas Virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
* [Adicionar a porta do teste na configuração de cluster ASCS](sap-high-availability-installation-wsfc-file-share.md)
* [Instalação de uma (A)SCS Instance em um cluster failover](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Criar um volume SMB para o Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)
* [Aplicativos SAP da NetApp no Microsoft Azure usando arquivos do Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Visão geral

SAP desenvolveu uma nova abordagem e alternativa para o cluster de discos compartilhados para fazedr cluster de instância SAP ASCS/SCS no Windows Failover Cluster. Em vez de usar discos compartilhados de cluster, pode-se usar um compartilhamento de arquivos SMB para implantar arquivos host globais SAP. O Azure NetApp Files suporta SMBv3 (juntamente com o NFS) com o NTFS ACL usando o Active Directory. O Azure NetApp Files está automaticamente disponível (por ser um serviço PaaS). Esses recursos tornam o Azure NetApp Files uma ótima opção para hospedar o compartilhamento de arquivos SMB para o SAP global.  
Tanto [os Serviços de Domínio do Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory-domain-services/overview) quanto os Serviços de [Domínio de Diretório Ativo (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) são suportados. Você pode usar os controladores de domínio do Active Directory existentes com arquivos do Azure NetApp. Os controladores de domínio podem estar no Azure como máquinas virtuais ou em locais via ExpressRoute ou S2S VPN. Neste artigo, usaremos controlador de domínio em uma VM Azure.  
A alta disponibilidade (HA) para serviços centrais SAP Netweaver requer armazenamento compartilhado. Para conseguir isso no Windows, até agora foi necessário construir um cluster SOFS ou usar disco compartilhado de cluster s/w como O SIOS. Agora é possível alcançar o SAP Netweaver HA usando armazenamento compartilhado, implantado no Azure NetApp Files. O uso do Azure NetApp Files para o armazenamento compartilhado elimina a necessidade de SOFS ou SIOS.  

> [!NOTE]
> Clustering de instâncias SAP ASCS/SCS com compartilhamento de arquivos tem suporte para produtos SAP NetWeaver 7.40 (e superior) com SAP Kernel 7.49 (e superior).  

![Arquitetura SAP ASCS/SCS HA com compartilhamento de SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

Os pré-requisitos para um compartilhamento de arquivos SMB são:
* Protocolo SMB 3.0 (ou posterior).
* Capacidade de definir listas de controle de acesso do Active Directory (ACLs) para grupos de usuários do Active Directory e o objeto do computador$ computador.
* O compartilhamento de arquivos deve ser habilitado para HA.

A ação dos serviços SAP Central nesta arquitetura de referência é oferecida pelo Azure NetApp Files:

![Arquitetura SAP ASCS/SCS HA com compartilhamento de SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Criar e montar o volume de SMB para arquivos Do Azure NetApp

Execute as seguintes etapas, como preparação para o uso de Arquivos Azure NetApp.  

1. Siga as etapas para [registrar os arquivos do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)  
2. Crie a conta do Azure NetApp, seguindo as etapas descritas em [Criar uma conta netapp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)  
3. Configure o pool de capacidade, seguindo as instruções em [Configurar um pool de capacidade](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)
4. Os recursos do Azure NetApp Files devem residir na sub-rede delegada. Siga as instruções em [Delegar uma sub-rede para Arquivos Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) para criar uma sub-rede delegada.  

> [!IMPORTANT]
> Você precisa criar conexões do Active Directory antes de criar um volume smb. Revise os [requisitos para conexões do Active Directory](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections).  

5. Criar conexão active diretório, conforme descrito em [Criar uma conexão Active Directory](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#create-an-active-directory-connection)  
6. Criar o volume de SMB Azure NetApp Files SMB, seguindo as instruções em [Adicionar um volume de SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#add-an-smb-volume)  
7. Monte o volume de SMB na máquina virtual do Windows.

> [!TIP]
> Você pode encontrar as instruções sobre como montar o volume de arquivos do Azure NetApp, se você navegar no [Portal Azure](https://portal.azure.com/#home) até o objeto Azure NetApp Files, clicar na lâmina **Volumes** e, em seguida, **montar instruções**.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Prepare a infra-estrutura para SAP HA usando um cluster de failover do Windows 

1. [Defina os endereços IP DNS necessários](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#b22d7b3b-4343-40ff-a319-097e13f62f9e)  
2. [Defina endereços IP estáticos para as máquinas virtuais SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#84c019fe-8c58-4dac-9e54-173efd4b2c30).
3. [Defina um endereço IP estático para o balanceador de carga interno do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#7a8f3e9b-0624-4051-9e41-b73fff816a9e).
4. [Defina regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#f19bd997-154d-4583-a46e-7f5a69d0153c).
5. [Altere as regras de balanceamento de carga padrão ASCS/SCS para o balanceador de carga interna do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#fe0bd8b5-2b43-45e3-8295-80bee5415716).
6. [Adicione máquinas virtuais do Windows ao domínio](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#e69e9a34-4601-47a3-a41c-d2e11c626c0c).
7. [Adicionar entradas de registro em ambos os nós de cluster da instância SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#661035b2-4d0f-4d31-86f8-dc0a50d78158)
8. [Configure um cluster de failover do Windows Server para uma instância SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#0d67f090-7928-43e0-8772-5ccbf8f59aab)
9. Se você estiver usando o Windows Server 2016, recomendamos que você configure [o Azure Cloud Witness](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).


## <a name="install-sap-ascs-instance-on-both-nodes"></a>Instale a instância SAP ASCS em ambos os nós

Você precisa do seguinte software do SAP:
   * SAP Software Provisioning Manager (SWPM) versão da ferramenta de instalação SPS25 ou posterior.
   * Kernel SAP 7.49 ou posterior
   * Crie um nome de host virtual (nome de rede de cluster) para a instância SAP ASCS/SCS agrupada, conforme descrito em [Criar um nome de host virtual para a instância SAP ASCS/SCS agrupada](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#a97ad604-9094-44fe-a364-f89cb39bf097).

> [!NOTE]
> Clustering de instâncias SAP ASCS/SCS com compartilhamento de arquivos tem suporte para produtos SAP NetWeaver 7.40 (e superior) com SAP Kernel 7.49 (e superior).  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>Instale uma instância ASCS/SCS no primeiro nó de cluster ASCS/SCS

1. Instale uma ocorrência SAP ASCS/SCS no primeiro nó de cluster. Inicie a ferramenta de instalação SAP SWPM e navegue até: **Produto** > **DBMS** > Instalação > Servidor de Aplicativos ABAP (ou Java) > Sistema de Alta Disponibilidade > instância ASCS/SCS > primeiro nó de cluster.  

2. Selecione **Cluster de compartilhamento de arquivos** como a configuração de compartilhamento de cluster no SWPM.  
3. Quando solicitado na etapa **SAP System Cluster Parameters**, digite o nome do host para o compartilhamento SMB do Azure NetApp Files que você já criou como **Nome de Host de Compartilhamento de Compartilhamento de Arquivos**.  Neste exemplo, o nome de host de compartilhamento SMB é **anfsmb-9562**. 

> [!IMPORTANT]
> Se o verificador pré-requisito Resultados no SWPM mostra condição de recurso de disponibilidade contínua não atendida, ele pode ser endereçado seguindo as instruções na [mensagem de erro Retardada quando você tenta acessar uma pasta compartilhada que não existe mais no Windows](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l).  

> [!TIP]
> Se o verificador pré-requisito Resultados no SWPM mostra a condição de tamanho de swap não atendida, você pode ajustar o tamanho swap navegando para as configurações de desempenho>do sistema do meu computador>> memória> virtual avançada> alterar.  

4. Configure um recurso de `SAP-SID-IP` cluster SAP, a porta de teste, usando o PowerShell. Execute esta configuração em um dos nós de cluster SAP ASCS/SCS, conforme descrito na [porta de teste Configure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#10822f4f-32e7-4871-b63a-9b86c76ce761).

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>Instale uma instância ASCS/SCS no segundo nó de cluster ASCS/SCS

1. Instale uma ocorrência SAP ASCS/SCS no segundo nó de cluster. Inicie a ferramenta de instalação SAP SWPM e navegue até **o Produto** > **DBMS** > Instalação > servidor de aplicativos ABAP (ou Java) > de alta disponibilidade do sistema > instância ASCS/SCS > nó de cluster adicional.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>Instalar instância DBMS e servidores de aplicativos SAP

Complete sua instalação SAP, instalando:

   * Uma instância DBMS  
   * Um servidor de aplicativo SAP principal  
   * Um servidor de aplicativo SAP adicional  

## <a name="test-the-sap-ascsscs-instance-failover"></a>Teste o failover de instância SAP ASCS/SCS 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Falha no nó de cluster A para o nó de cluster B e para trás
Neste cenário de teste, nos referiremos ao nó de cluster sapascs1 como nó A, e ao nó de cluster sapascs2 como nó B.

1. Verifique se os recursos do cluster ![estão sendo executados no nó A. Figura 1: Recursos de cluster failover do Windows Server em execução no nó A anterior estocem antes do teste de failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Reiniciar o nó de cluster A. Os recursos de cluster SAP serão ![mover-se para o nó de cluster B. Figura 2: Recursos de cluster de failover do Windows Server em execução no nó B após o teste de failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Teste de entrada de bloqueio

1.Verifique se o ERS (SAP Enqueue Replication Server, servidor de replicação enqueue) está ativo  
2. Faça login no sistema SAP, execute a transação SU01 e abra um ID do usuário no modo de alteração. Isso gerará entrada de bloqueio SAP.  
3. Como você está conectado no sistema SAP, exiba a entrada de bloqueio, navegando para a transação ST12.  
4. Falha sobre os recursos ASCS do nó de cluster A ao nó de cluster B.  
5. Verifique se a entrada de bloqueio, gerada antes do failover de recursos do cluster SAP ASCS/SCS, é retida.  

![Figura 3: A entrada de bloqueio é retida após o teste de failover](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

Para obter mais informações, consulte [Solução de problemas para failover enqueue no ASCS com ERS](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>Próximas etapas

* [Planejamento e implementação de Máquinas Virtuais do Azure para o SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação do DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastres do SAP 
* HANA no Azure (grandes instâncias), ver [SAP HANA (grandes instâncias) alta disponibilidade e recuperação de desastres no Azure](hana-overview-high-availability-disaster-recovery.md).
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastre do SAP HANA em VMs do Azure, confira [Alta disponibilidade do SAP HANA em VMs (Máquinas Virtuais) do Azure][sap-hana-ha]
