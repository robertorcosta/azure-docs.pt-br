---
title: Alta disponibilidade de VMs do Azure para SAP NW no RHEL com Azure NetApp Files | Microsoft Docs
description: Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver no Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/24/2020
ms.author: radeltch
ms.openlocfilehash: 601194d3a8cc789c51b8e127001ab2367dceeee7
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148212"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver em Red Hat Enterprise Linux com Azure NetApp Files para aplicativos SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Este artigo descreve como implantar as máquinas virtuais, configurar as máquinas virtuais, instalar a estrutura de cluster e instalar um sistema SAP NetWeaver 7,50 altamente disponível, usando [Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).
Nas configurações de exemplo, comandos de instalação etc. A instância de ASCS é o número 00, a instância ERS é o número 01, a instância de aplicativo principal (PAS) é 02 e a instância do aplicativo (AAS) é 03. A ID do sistema SAP QAS é usada. 

A camada de banco de dados não é abordada em detalhes neste artigo.  

Primeiro, leia os seguintes documentos e Notas SAP:

* [Documentação do Azure NetApp Files][anf-azure-doc] 
* A Nota SAP [1928533], que tem:
  * Lista de tamanhos de VM do Azure que têm suporte para a implantação de software SAP
  * Informações importantes sobre capacidade para tamanhos de VM do Azure
  * Software SAP e combinações de SO (sistema operacional) e banco de dados com suporte
  * A versão do kernel do SAP necessária para Windows e para Linux no Microsoft Azure

* A Nota SAP [2015553] lista pré-requisitos para implantações de software SAP com suporte do SAP no Azure.
* Nota SAP [2002167] recomendou configurações do sistema operacional Red Hat Enterprise Linux
* Nota SAP [2009879] tem diretrizes SAP HANA para Red Hat Enterprise Linux
* A Nota SAP [2178632] contém informações detalhadas sobre todas as métricas de monitoramentos relatadas para o SAP no Azure.
* A Nota SAP [2191498] tem a versão necessária do SAP Host Agent para Linux no Azure.
* A Nota SAP [2243692] tem informações sobre o licenciamento do SAP no Linux no Azure.
* A Nota SAP [1999351] tem informações de solução de problemas adicionais para a Extensão de Monitoramento Avançado do Azure para SAP.
* [WIKI da comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as Notas SAP necessárias para Linux.
* [Planejamento e implementação de Máquinas Virtuais do Azure para SAP no Linux][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para no Linux][deployment-guide]
* [Implantação de Máquinas Virtuais do Azure do DBMS para SAP no Linux][dbms-guide]
* [SAP Netweaver no cluster do pacemaker](https://access.redhat.com/articles/3150081)
* Documentação geral do RHEL
  * [Visão geral do complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administração de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Configurando o ASCS/ERS do SAP Netweaver com recursos autônomos no RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Configurar o SAP S/4HANA ASCS/ERS com o servidor de enfileiramento autônomo 2 (ENSA2) no pacemaker no RHEL](https://access.redhat.com/articles/3974941)
* Documentação do RHEL específica do Azure:
  * [Políticas de suporte para clusters de alta disponibilidade do RHEL - máquinas virtuais do Microsoft Azure como membros de cluster](https://access.redhat.com/articles/3131341)
  * [Instalando e configurando um Cluster de alta disponibilidade do Red Hat Enterprise Linux 7.4 (e posterior) no Microsoft Azure](https://access.redhat.com/articles/3252491)
* [Aplicativos SAP da NetApp em Microsoft Azure usando Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Visão geral

HA (alta disponibilidade) para serviços centrais do SAP NetWeaver requer armazenamento compartilhado.
Para conseguir isso no Red Hat Linux até o momento, era necessário criar um cluster GlusterFS de alta disponibilidade separado. 

Agora é possível obter a alta disponibilidade do SAP NetWeaver usando o armazenamento compartilhado, implantado em Azure NetApp Files. O uso de Azure NetApp Files para o armazenamento compartilhado elimina a necessidade de um [cluster GlusterFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)adicional. O pacemaker ainda é necessário para HA dos serviços centrais do SAP NetWeaver (ASCS/SCS).

![Visão geral da Alta Disponibilidade do SAP NetWeaver](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

O SAP NetWeaver ASCS, o SAP NetWeaver SCS, o SAP NetWeaver ERS e o banco de dados SAP HANA usam o nome do host virtual e os endereços IP virtuais. No Azure, um balanceador de carga é necessário para usar um endereço IP virtual. É recomendável usar o [balanceador de carga padrão](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal). A lista a seguir mostra a configuração do balanceador de carga com IPs de front-end separados para (A) SCS e ERS.

### <a name="ascs"></a>(A)SCS

* Configuração de front-end
  * 192.168.14.9 de endereço IP
* Porta de Investigação
  * Porta 620<strong>&lt;NR&gt;</strong>
* Regras de balanceamento de carga
  * Se estiver usando Standard Load Balancer, selecione **portas de alta disponibilidade**
  * 32<strong>&lt;NR&gt; </strong> TCP
  * 36<strong>&lt;NR&gt; </strong> TCP
  * 39<strong>&lt;NR&gt; </strong> TCP
  * 81<strong>&lt;NR&gt; </strong> TCP
  * 5<strong>&lt;NR&gt;</strong>13 TCP
  * 5<strong>&lt;NR&gt;</strong>14 TCP
  * 5<strong>&lt;NR&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Configuração de front-end
  * 192.168.14.10 de endereço IP
* Porta de Investigação
  * Porta 621<strong>&lt;NR&gt;</strong>
* Regras de balanceamento de carga
  * Se estiver usando Standard Load Balancer, selecione **portas de alta disponibilidade**
  * 32<strong>&lt;NR&gt; </strong> TCP
  * 33<strong>&lt;NR&gt; </strong> TCP
  * 5<strong>&lt;NR&gt;</strong>13 TCP
  * 5<strong>&lt;NR&gt;</strong>14 TCP
  * 5<strong>&lt;NR&gt;</strong>16 TCP

* Configuração de back-end
  * Conectado aos adaptadores de rede primários de todas as máquinas virtuais que devem ser parte do cluster (A)SCS/ERS

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Configurando a infraestrutura de Azure NetApp Files 

O SAP NetWeaver requer um armazenamento compartilhado para o diretório de perfil e transporte.  Antes de prosseguir com a configuração da infraestrutura de arquivos do Azure NetApp, familiarize-se com a [documentação do Azure NetApp files][anf-azure-doc]. Verifique se a região do Azure selecionada oferece Azure NetApp Files. O link a seguir mostra a disponibilidade de Azure NetApp Files pela região do Azure: [Azure NetApp files disponibilidade pela região do Azure][anf-avail-matrix].

Os arquivos do Azure NetApp estão disponíveis em várias [regiões do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Antes de implantar Azure NetApp Files, solicite a integração ao Azure NetApp Files, seguindo as [instruções registrar para arquivos do Azure NetApp][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Implantar Azure NetApp Files recursos  

As etapas pressupõem que você já tenha implantado a [rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Os recursos de Azure NetApp Files e as VMs, em que os recursos de Azure NetApp Files serão montados devem ser implantados na mesma rede virtual do Azure ou em redes virtuais emparelhadas do Azure.  

1. Se você ainda não fez isso, solicite [a integração ao Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  
2. Crie a conta do NetApp na região do Azure selecionada, seguindo as [instruções para criar uma conta do NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Configure Azure NetApp Files pool de capacidade, seguindo as [instruções sobre como configurar o pool de capacidade Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
A arquitetura do SAP NetWeaver apresentada neste artigo usa um único pool de capacidade de Azure NetApp Files, SKU Premium. É recomendável Azure NetApp Files SKU Premium para carga de trabalho do aplicativo SAP NetWeaver no Azure.  
4. Delegue uma sub-rede para os arquivos do Azure NetApp conforme descrito nas [instruções delegar uma sub-rede para Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Implante Azure NetApp Files volumes, seguindo as [instruções para criar um volume para Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Implante os volumes na [sub-rede](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)Azure NetApp files designada. Os endereços IP dos volumes do Azure NetApp são atribuídos automaticamente. Tenha em mente que os recursos Azure NetApp Files e as VMs do Azure devem estar na mesma rede virtual do Azure ou em redes virtuais emparelhadas do Azure. Neste exemplo, usamos dois volumes Azure NetApp Files: SAP<b>QAS</b> e transSAP. Os caminhos de arquivo montados nos pontos de montagem correspondentes são/usrsap<b>QAS</b>/sapmnt<b>QAS</b>,/usrsap<b>QAS</b>/usrsap<b>QAS sys,</b>etc.  

   1. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/sapmnt<b>QAS</b>)
   2. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ASCs)
   3. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys)
   4. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ERS)
   5. volume transSAP (nfs://192.168.24.4/transSAP)
   6. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>Pas)
   7. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>AAS)
  
Neste exemplo, usamos Azure NetApp Files para todos os sistemas de arquivos do SAP NetWeaver para demonstrar como Azure NetApp Files pode ser usado. Os sistemas de arquivos SAP que não precisam ser montados via NFS também podem ser implantados como [armazenamento em disco do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) . Neste exemplo, <b>a-e</b> deve estar em Azure NetApp Files e <b>f-g</b> (ou seja,/usr/SAP/<b>QAS</b>/d<b>02</b>,/usr/SAP/<b>QAS</b>/d<b>03</b>) pode ser implantado como armazenamento em disco do Azure. 

### <a name="important-considerations"></a>Considerações importantes

Ao considerar Azure NetApp Files para o SAP NetWeaver na arquitetura de alta disponibilidade do SUSE, esteja ciente das seguintes considerações importantes:

- O pool de capacidade mínima é 4 TiB. O tamanho do pool de capacidade pode ser aumentado em incrementos de 1 TiB.
- O volume mínimo é 100 GiB
- Azure NetApp Files e todas as máquinas virtuais, nas quais Azure NetApp Files volumes serão montados, devem estar na mesma rede virtual do Azure ou em [redes virtuais emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) na mesma região. Agora há suporte para o acesso Azure NetApp Files sobre o emparelhamento VNET na mesma região. Ainda não há suporte para o acesso do Azure NetApp sobre o emparelhamento global.
- A rede virtual selecionada deve ter uma sub-rede, delegada para Azure NetApp Files.
- O Azure NetApp Files oferece [política de exportação](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): você pode controlar os clientes permitidos, o tipo de acesso (leitura&gravação, somente leitura, etc.). 
- O recurso de Azure NetApp Files ainda não reconhece a zona. Atualmente Azure NetApp Files recurso não está implantado em todas as zonas de disponibilidade em uma região do Azure. Esteja atento às possíveis implicações de latência em algumas regiões do Azure. 
- Azure NetApp Files volumes podem ser implantados como volumes NFSv3 ou NFSv 4.1. Os dois protocolos têm suporte para a camada de aplicativo SAP (ASCS/ERS, servidores de aplicativos SAP). 

## <a name="setting-up-ascs"></a>Configuração do (A)SCS

Neste exemplo, os recursos foram implantados manualmente por meio do [portal do Azure](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Implantar o Linux manualmente por meio do portal do Azure

Primeiro, você precisa criar os volumes de Azure NetApp Files. Implante as VMs. Posteriormente, você criará um balanceador de carga e usará as máquinas virtuais no pool de back-end.

1. Criar balanceador de carga (interno, padrão):  
   1. Criar os endereços IP de front-end
      1. Endereço IP 192.168.14.9 para o ASCS
         1. Abra o balanceador de carga, selecione o pool de IPs de front-end e clique em Adicionar
         1. Insira o nome do novo pool de IPS de front-end (por exemplo, **frontend. QAS. ASCS**)
         1. Defina a atribuição como estática e insira o endereço IP (por exemplo **192.168.14.9**)
         1. Clique em OK
      1. Endereço IP 192.168.14.10 para ASCS ERS
         * Repita as etapas acima em "a" para criar um endereço IP para o ERS (por exemplo, **192.168.14.10** e **frontend. QAS. ERS**)
   1. Criar o pool de back-end
      1. Abra o balanceador de carga, selecione os pools de back-end e clique em Adicionar
      1. Insira o nome do novo pool de back-end (por exemplo, o **back-end. QAS**)
      1. Clique em Adicionar uma máquina virtual.
      1. Selecione Máquina virtual. 
      1. Selecione as máquinas virtuais do cluster (A) SCS e seus endereços IP.
      1. Clique em Adicionar
   1. Crie as investigações de integridade
      1. Porta 620**00** para ASCS
         1. Abra o balanceador de carga, selecione as investigações de integridade e clique em Adicionar
         1. Insira o nome da nova investigação de integridade (por exemplo, **integridade. QAS. ASCS**)
         1. Selecione TCP como protocolo, porta 620**00**, mantenha o Intervalo 5 e o limite Não Íntegro 2
         1. Clique em OK
      1. Porta 621**01** para ASCS ers
            * Repita as etapas acima em "c" para criar uma investigação de integridade para o ERS (por exemplo, 621**01** e a **integridade. QAS. ERS**)
   1. Regras de balanceamento de carga
      1. Regras de balanceamento de carga para ASCS
         1. Abra o balanceador de carga, selecione regras de balanceamento de carga e clique em Adicionar
         1. Insira o nome da nova regra do balanceador de carga (por exemplo, **lb. QAS. ASCS**)
         1. Selecione o endereço IP de front-end para ASCS, pool de back-end e investigação de integridade que você criou anteriormente (por exemplo, **frontend. QAS. ASCS**, **back-end. QAS** e **integridade. QAS. ASCS**)
         1. Selecionar **portas de alta disponibilidade**
         1. Aumente o tempo limite de ociosidade para 30 minutos
         1. **Certifique-se de habilitar o IP flutuante**
         1. Clique em OK
         * Repita as etapas acima para criar regras de balanceamento de carga para ERS (por exemplo, **lb. QAS. ERS**)
1. Como alternativa, se seu cenário exigir o Load Balancer básico (interno), siga estas etapas:  
   1. Criar os endereços IP de front-end
      1. Endereço IP 192.168.14.9 para o ASCS
         1. Abra o balanceador de carga, selecione o pool de IPs de front-end e clique em Adicionar
         1. Insira o nome do novo pool de IPS de front-end (por exemplo, **frontend. QAS. ASCS**)
         1. Defina a atribuição como estática e insira o endereço IP (por exemplo **192.168.14.9**)
         1. Clique em OK
      1. Endereço IP 192.168.14.10 para ASCS ERS
         * Repita as etapas acima em "a" para criar um endereço IP para o ERS (por exemplo, **192.168.14.10** e **frontend. QAS. ERS**)
   1. Criar o pool de back-end
      1. Abra o balanceador de carga, selecione os pools de back-end e clique em Adicionar
      1. Insira o nome do novo pool de back-end (por exemplo, o **back-end. QAS**)
      1. Clique em Adicionar uma máquina virtual.
      1. Selecione o conjunto de disponibilidade que você criou anteriormente para ASCS 
      1. Selecione as máquinas virtuais do cluster (A)SCS
      1. Clique em OK
   1. Crie as investigações de integridade
      1. Porta 620**00** para ASCS
         1. Abra o balanceador de carga, selecione as investigações de integridade e clique em Adicionar
         1. Insira o nome da nova investigação de integridade (por exemplo, **integridade. QAS. ASCS**)
         1. Selecione TCP como protocolo, porta 620**00**, mantenha o Intervalo 5 e o limite Não Íntegro 2
         1. Clique em OK
      1. Porta 621**01** para ASCS ers
            * Repita as etapas acima em "c" para criar uma investigação de integridade para o ERS (por exemplo, 621**01** e a **integridade. QAS. ERS**)
   1. Regras de balanceamento de carga
      1. 32**00** TCP para ASCS
         1. Abra o balanceador de carga, selecione regras de balanceamento de carga e clique em Adicionar
         1. Insira o nome da nova regra do balanceador de carga (por exemplo, **lb. QAS. ASCS. 3200**)
         1. Selecione o endereço IP de front-end para ASCS, pool de back-end e investigação de integridade que você criou anteriormente (por exemplo, **frontend. QAS. ASCS**)
         1. Mantenha o protocolo **TCP**, insira a porta **3200**
         1. Aumente o tempo limite de ociosidade para 30 minutos
         1. **Certifique-se de habilitar o IP flutuante**
         1. Clique em OK
      1. Portas adicionais para ASCS
         * Repita as etapas acima em "d" para as portas**36 00**,**39 00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 e TCP para o ASCS
      1. Portas adicionais para ERS do ASCS
         * Repita as etapas acima em "d" para as portas 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 e TCP para o ASCS ers

      > [!Note]
      > Quando as VMs sem endereços IP públicos forem colocadas no pool de back-end do Azure Load Balancer padrão (sem endereço IP público), não haverá nenhuma conectividade com a Internet de saída, a menos que a configuração adicional seja executada para permitir o roteamento para pontos de extremidade públicos. Para obter detalhes sobre como obter conectividade de saída, consulte [conectividade de ponto de extremidade pública para máquinas virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

      > [!IMPORTANT]
      > Não habilite carimbos de data/hora TCP em VMs do Azure colocadas por trás Azure Load Balancer. Habilitar carimbos de data/hora TCP fará com que as investigações de integridade falhem. Defina o parâmetro **net. IPv4. tcp_timestamps** como **0**. Para obter detalhes, consulte [Load Balancer investigações de integridade](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

## <a name="disable-id-mapping-if-using-nfsv41"></a>Desabilitar mapeamento de ID (se estiver usando NFSv 4.1)

As instruções nesta seção só serão aplicáveis se você estiver usando Azure NetApp Files volumes com o protocolo NFSv 4.1. Execute a configuração em todas as VMs, onde Azure NetApp Files os volumes NFSv 4.1 serão montados.  

1. Verifique a configuração de domínio do NFS. Verifique se o domínio está configurado como o domínio de Azure NetApp Files padrão, ou seja **`defaultv4iddomain.com`** , e o mapeamento está definido como **ninguém**.  

    > [!IMPORTANT]
    > Certifique-se de definir o domínio NFS `/etc/idmapd.conf` em na VM para corresponder à configuração de domínio padrão no Azure NetApp files **`defaultv4iddomain.com`**:. Se houver uma incompatibilidade entre a configuração de domínio no cliente NFS (ou seja, a VM) e o servidor NFS, ou seja, a configuração da Azure NetApp, as permissões para arquivos nos volumes do Azure NetApp montados nas VMs serão exibidas como `nobody`.  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** verificar `nfs4_disable_idmapping`. Ele deve ser definido como **Y**. Para criar a estrutura de diretório `nfs4_disable_idmapping` onde está localizado, execute o comando Mount. Você não poderá criar o diretório manualmente em/sys/modules, pois o acesso é reservado para o kernel/drivers.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Para obter mais detalhes sobre como alterar `nfs4_disable_idmapping` o parâmetro https://access.redhat.com/solutions/1749883, consulte.

### <a name="create-pacemaker-cluster"></a>Criar cluster do Pacemaker

Siga as etapas em [Configurando o Pacemaker no Red Hat Enterprise Linux no Azure](high-availability-guide-rhel-pacemaker.md) para criar um cluster básico do Pacemaker para esse servidor (A) SCS.

### <a name="prepare-for-sap-netweaver-installation"></a>Preparar para a instalação do SAP NetWeaver

Os itens a seguir são prefixados com **[A]** -aplicável a todos os nós **[1]** -aplicável somente ao nó 1 ou **[2]** – aplicável somente ao nó 2.

1. **[A]** Configurar a resolução de nome do host

   Você pode usar um servidor DNS ou modificar /etc/hosts em todos os nós. Este exemplo mostra como usar o arquivo /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir

    ```
    sudo vi /etc/hosts
    ```

   Insira as seguintes linhas para /etc/hosts. Altere o endereço IP e o nome do host para corresponder ao seu ambiente

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]** crie diretórios SAP no volume Azure NetApp files.  
   Monte temporariamente o volume Azure NetApp Files em uma das VMs e crie os diretórios SAP (caminhos de arquivo).  

    ```
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp
    ``` 

1. **[A]** Criar os diretórios compartilhados

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** instalar cliente NFS e outros requisitos

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Verificar versão de resource-agents-sap

   Certifique-se de que a versão do pacote resource-agents-sap instalado é pelo menos 3.9.5-124.el7
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A] ** Adicionar entradas de montagem

   Se estiver usando NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Se estiver usando o NFSv 4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > Certifique-se de corresponder à versão do protocolo NFS dos volumes de Azure NetApp Files, ao montar os volumes. Se os volumes de Azure NetApp Files forem criados como volumes NFSv3, use a configuração de NFSv3 correspondente. Se os volumes de Azure NetApp Files forem criados como volumes NFSv 4.1, siga as instruções para desabilitar o mapeamento de ID e certifique-se de usar a configuração correspondente do NFSv 4.1. Neste exemplo, os volumes de Azure NetApp Files foram criados como volumes NFSv3.  

   Montar os novos compartilhamentos

   ```
   sudo mount -a  
   ```

1. **[A]** Configurar arquivo de permuta

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Reiniciar o agente para ativar a alteração

   ```
   sudo service waagent restart
   ```

1. **[A] ** Configuração RHEL

   Configurar RHEL, conforme descrito na nota SAP [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Instalação do ASCS/ERS do SAP NetWeaver

1. **[1]** Criar um recurso de IP virtual e uma investigação de integridade para a instância do ASCS

   ```
   sudo pcs node standby anftstsapcl2
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Verifique se o status do cluster é ok e se todos os recursos estão iniciados. Não importa em qual nó os recursos estão sendo executados.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** Instalar o ASCS do SAP NetWeaver  

   Instale o ASCS do SAP NetWeaver como raiz no primeiro nó usando um nome de host virtual que mapeia para o endereço IP da configuração de front-end do balanceador de carga para o ASCS, por exemplo, <b>anftstsapvh</b>, <b>192.168.14.9</b> e o número da instância que você usou para a investigação do balanceador de carga, por exemplo <b>00</b>.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Se a instalação não conseguir criar uma subpasta no/usr/SAP/**QAS**/ASCS**00**, tente definir o proprietário e o grupo da pasta ASCS**00** e tente novamente.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Criar um recurso de IP virtual e uma investigação de integridade para a instância do ERS

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Verifique se o status do cluster é ok e se todos os recursos estão iniciados. Não importa em qual nó os recursos estão sendo executados.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** Instalar o ERS do SAP NetWeaver  

   Instale o ERS do SAP NetWeaver como raiz no segundo nó usando um nome de host virtual que mapeia para o endereço IP da configuração de front-end do balanceador de carga para o ERS, por exemplo, <b>anftstsapers</b>, <b>192.168.14.10</b> e o número da instância que você usou para a investigação do balanceador de carga, por exemplo <b>01</b>.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Se a instalação não conseguir criar uma subpasta no/usr/SAP/**QAS**/ers**01**, tente definir o proprietário e o grupo da pasta ers**01** e tente novamente.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Adaptar os perfis de instância do ASCS/SCS e do ERS

   * Perfil do ASCS/SCS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

   * Perfil do ERS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** Configurar Keep Alive

   A comunicação entre o servidor de aplicativos do SAP NetWeaver e o ASCS/SCS é roteada por meio de um balanceador de carga de software. O balanceador de carga desconecta conexões inativas após um tempo limite configurável. Para evitar isso, você precisa definir um parâmetro no perfil do SAP NetWeaver ASCS/SCS e alterar as configurações do sistema Linux. Leia a [Nota SAP 1410736][1410736] para obter mais informações.

   O parâmetro enque/encni/set_so_keepalive do perfil do ASCS/SCS já foi adicionado na última etapa.

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Atualize o arquivo / usr / sap / sapservices

   Para evitar o início das instâncias pelo script de inicialização do sapinit, todas as instâncias gerenciadas pelo Pacemaker devem ser comentadas no arquivo / usr / sap / sapservices. Não comente a instância do SAP HANA se ela for usada com o HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** Criar os recursos de cluster do SAP

   Se estiver usando a arquitetura do enqueue Server 1 (ENSA1), defina os recursos da seguinte maneira:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   O SAP introduziu o suporte para o enqueue Server 2, incluindo a replicação, a partir do SAP NW 7,52. A partir do ABAP Platform 1809, o enqueue Server 2 é instalado por padrão. Consulte o SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) para suporte ao servidor de enfileiramento 2.
   Se estiver usando[ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)(Enqueue Server 2 Architecture), instale o agente de recurso Resource-Agents-SAP-4.1.1 12. el7. x86_64 ou mais recente e defina os recursos da seguinte maneira:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Se você estiver atualizando de uma versão mais antiga e alternando para o servidor de enfileiramento 2, consulte a observação do SAP [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > Os tempos limite na configuração acima são apenas exemplos e talvez precisem ser adaptados para a configuração específica do SAP. 

   Verifique se o status do cluster é ok e se todos os recursos estão iniciados. Não importa em qual nó os recursos estão sendo executados.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** adicionar regras de firewall para ASCS e ers em ambos os nós adicione as regras de firewall para ASCS e ers em ambos os nós.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Preparação do servidor de aplicativos do SAP NetWeaver

   Alguns bancos de dados exigem que a instalação da instância do banco de dados seja executada em um servidor de aplicativos. Prepare as máquinas virtuais do servidor de aplicativos para poder usá-las nesses casos.  

   As etapas abaixo pressupõem que você instale o servidor de aplicativos em um servidor diferente dos servidores do ASCS/SCS e do HANA. Caso contrário, algumas das etapas abaixo (como configurar a resolução de nome do host) não são necessárias.  

   Os seguintes itens são prefixados com **[A]** -aplicável ao pas e ao AAS, **[P]** – aplicável somente ao pas ou **[S]** – aplicável somente ao AAS.  

1. **[A]** configurar resolução de nome de host você pode usar um servidor DNS ou modificar o/etc/hosts em todos os nós. Este exemplo mostra como usar o arquivo /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir:  

   ```
   sudo vi /etc/hosts
   ```

   Insira as seguintes linhas para /etc/hosts. Altere o endereço IP e o nome do host para corresponder ao seu ambiente.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** criar o diretório sapmnt crie o diretório sapmnt.
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** instalar cliente NFS e outros requisitos  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A] ** Adicionar entradas de montagem  
   Se estiver usando NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Se estiver usando o NFSv 4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   Montar os novos compartilhamentos

   ```
   sudo mount -a
   ```

1. **[P]** criar e montar o diretório do Pas  
   Se estiver usando NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Se estiver usando o NFSv 4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** criar e montar o diretório AAS  
   Se estiver usando NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Se estiver usando o NFSv 4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A]** Configurar arquivo de permuta
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Reiniciar o agente para ativar a alteração

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Instalar banco de dados

Neste exemplo, o SAP NetWeaver está instalado no SAP HANA. Você pode usar todos os bancos de dados com suporte para esta instalação. Para obter mais informações sobre como instalar SAP HANA no Azure, consulte [alta disponibilidade de SAP Hana em VMs do Azure no Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Executar a instalação da instância do banco de dados SAP

   Instale a instância de banco de dados do SAP NetWeaver como raiz usando um nome de host virtual que mapeia para o endereço IP da configuração de front-end do balanceador de carga para o banco de dados.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>Instalação de servidor de aplicativos do SAP NetWeaver

Siga estas etapas para instalar um servidor de aplicativos SAP.

1. Preparar o servidor de aplicativos

   Siga as etapas no capítulo [Preparação do servidor de aplicativos SAP NetWeaver](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) acima para preparar o servidor de aplicativos.

1. Instalar o servidor de aplicativos do SAP NetWeaver

   Instale um servidor de aplicativos do SAP NetWeaver primário ou adicional.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Atualizar o repositório seguro do SAP HANA

   Atualize o repositório seguro do SAP HANA para apontar para o nome virtual do programa de instalação da replicação de sistema do SAP HANA.

   Execute o seguinte comando para listar as entradas como \< sapsid> adm

   ```
   hdbuserstore List
   ```

   Isso deve listar todas as entradas e deve ser semelhante a
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   A saída mostra que o endereço IP da entrada padrão está apontando para a máquina virtual e não para o endereço IP do balanceador de carga. Essa entrada precisa ser alterada para apontar para o nome do host virtual do balanceador de carga. Certifique-se de usar a mesma porta (**30313** na saída acima) e o nome do banco de dados (**QAS** na saída acima)!

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

1. Migrar manualmente a instância do ASCS

   Estado do recurso antes de iniciar o teste:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Execute os seguintes comandos como raiz para migrar a instância do ASCS.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado do recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Simular falhas de nó

   Estado do recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Execute o comando a seguir como raiz no nó onde a instância do ASCS está em execução

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   O status depois que o nó for iniciado novamente deve ser assim.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Use o seguinte comando para limpar os recursos com falha.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado do recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Encerrar o processo do servidor de mensagens

   Estado do recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   Execute os seguintes comandos como raiz para identificar o processo de servidor de mensagens e encerrá-lo.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   Se você eliminar apenas o servidor de mensagens uma vez, ele será reiniciado pelo `sapstart`. Se você encerrá-lo com frequência suficiente, o Pacemaker finalmente moverá a instância do ASCS para o outro nó. Execute os seguintes comandos como raiz para limpar o estado do recurso da instância do ASCS e ERS após o teste.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado do recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Encerrar processo do servidor de enfileiramento

   Estado do recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Execute o comando a seguir como raiz no nó onde a instância do ASCS está em execução para encerrar o servidor de enfileiramento.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   A instância do ASCS deve imediatamente executar failover para o outro nó. A instância do ERS também deverá executar failover depois que a instância do ASCS for iniciada. Execute os seguintes comandos como raiz para limpar o estado do recurso da instância do ASCS e ERS após o teste.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado do recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Interromper o processo de servidor de replicação de enfileiramento

   Estado do recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Execute o comando a seguir como raiz no nó onde a instância do ERS está em execução para encerrar o processo do servidor de replicação de enfileiramento.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   Se você executar o comando apenas uma vez `sapstart` , o reiniciará o processo. Se você executá-lo com frequência `sapstart` suficiente, o não reiniciará o processo e o recurso estará em um estado parado. Execute os seguintes comandos como raiz para limpar o estado do recurso da instância do ERS após o teste.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Estado do recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Interromper o processo sapstartsrv de enfileiramento

   Estado do recurso antes de iniciar o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Execute os comandos a seguir como raiz no nó onde o ASCS está em execução.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   O processo sapstartsrv sempre deve ser reiniciado pelo agente de recursos do Pacemaker como parte do monitoramento. Estado do recurso após o teste:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>Próximas etapas

* [HA para SAP NW em VMs do Azure no RHEL para aplicativos SAP guia de vários SIDs](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [Planejamento e implementação de Máquinas Virtuais do Azure para o SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação do DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastres do SAP HANA no Azure (instâncias grandes), confira [Alta disponibilidade e recuperação de desastres do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastre do SAP HANA em VMs do Azure, confira [Alta disponibilidade do SAP HANA em VMs (Máquinas Virtuais) do Azure][sap-hana-ha]
