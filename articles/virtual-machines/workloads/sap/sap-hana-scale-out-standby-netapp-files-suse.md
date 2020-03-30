---
title: Scale-out SAP HANA com standby com arquivos Do Azure NetApp em SLES | Microsoft Docs
description: Guia de alta disponibilidade para SAP NetWeaver no SUSE Linux Enterprise Server com arquivos Azure NetApp para aplicativos SAP
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
ms.date: 01/10/2020
ms.author: radeltch
ms.openlocfilehash: c594ef3a62d45fb68002ec2b21fb89115f7a30af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565801"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-suse-linux-enterprise-server"></a>Implantar um sistema de scale-out SAP HANA com nó de espera em VMs do Azure usando arquivos Do Azure NetApp no SUSE Linux Enterprise Server 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
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
[1900823]:https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Este artigo descreve como implantar um sistema SAP HANA altamente disponível em uma configuração de escala com espera em máquinas virtuais (VMs) do Azure usando [arquivos Do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) para os volumes de armazenamento compartilhados.  

Na configuração de exemplo, comandos de instalação, e assim por diante, a ocorrência HANA é **03** e o ID do sistema HANA é **HN1**. Os exemplos são baseados no HANA 2.0 SP4 e no SUSE Linux Enterprise Server para SAP 12 SP4. 

Antes de começar, consulte as seguintes notas e papéis sap:

* [Documentação de arquivos do Azure NetApp][anf-azure-doc] 
* SAP Note [1928533] inclui:  
  * Uma lista de tamanhos de VM do Azure que são suportados para a implantação de software SAP
  * Informações importantes sobre capacidade para tamanhos de VM do Azure
  * Software SAP e combinações de SO (sistema operacional) e banco de dados com suporte
  * A versão necessária do kernel SAP para Windows e Linux no Microsoft Azure
* Nota SAP [2015553]: Lista pré-requisitos para implantações de software SAP suportadas pelo SAP no Azure
* Nota SAP [2205917]: Contém configurações recomendadas do Sistema Operacional para SUSE Linux Enterprise Server para aplicações SAP
* Nota SAP [1944799]: Contém diretrizes SAP para o SUSE Linux Enterprise Server para aplicações SAP
* Nota SAP [2178632]: Contém informações detalhadas sobre todas as métricas de monitoramento relatadas para SAP no Azure
* Nota SAP [2191498]: Contém a versão necessária do SAP Host Agent para Linux no Azure
* Nota SAP [2243692]: Contém informações sobre licenciamento SAP no Linux no Azure
* Nota SAP [1984787]: Contém informações gerais sobre o SUSE Linux Enterprise Server 12
* Nota SAP [1999351]: Contém informações adicionais de solução de problemas para a extensão de monitoramento aprimorada do Azure para SAP
* Nota SAP [1900823]: Contém informações sobre os requisitos de armazenamento SAP HANA
* [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Contém todas as notas SAP necessárias para Linux
* [Planejamento e implementação de Máquinas Virtuais do Azure para SAP no Linux][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para no Linux][deployment-guide]
* [Implantação de Máquinas Virtuais do Azure do DBMS para SAP no Linux][dbms-guide]
* [Guias de práticas recomendadas do SUSE SAP HA][suse-ha-guide]: Contém todas as informações necessárias para configurar a replicação do sistema NetWeaver High Availability e SAP HANA no local (para ser usada como uma linha de base geral; eles fornecem informações muito mais detalhadas)
* [Notas de versão do SP3 com a extensão de alta disponibilidade do SUSE 12][suse-ha-12sp3-relnotes]
* [Aplicativos SAP da NetApp no Microsoft Azure usando arquivos do Azure NetApp][anf-sap-applications-azure]
* [SAP HANA em Sistemas NetApp com Sistema de Arquivos de Rede (NFS)](https://www.netapp.com/us/media/tr-4435.pdf): Um guia de configuração que contém informações sobre como configurar o SAP HANA usando o Azure NFS pela NetApp


## <a name="overview"></a>Visão geral

Um método para alcançar a alta disponibilidade do HANA é configurar o failover automático do host. Para configurar o failover automático do host, adicione uma ou mais máquinas virtuais ao sistema HANA e configure-as como nós de espera. Quando o nó ativo falha, um nó de espera assume automaticamente. Na configuração apresentada com máquinas virtuais Azure, você consegue failover automático usando [NFS em Arquivos Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).  

> [!NOTE]
> O nó de espera precisa de acesso a todos os volumes de banco de dados. Os volumes HANA devem ser montados como volumes NFSv4. O mecanismo de bloqueio aprimorado baseado em locação de `I/O` arquivos no protocolo NFSv4 é usado para esgrima. 

> [!IMPORTANT]
> Para construir a configuração suportada, você deve implantar os dados HANA e os volumes de registro como volumes NFSv4.1 e montá-los usando o protocolo NFSv4.1. A configuração de failover automático do host HANA com nó de espera não é suportada com NFSv3.

![Visão geral da Alta Disponibilidade do SAP NetWeaver](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

No diagrama anterior, que segue as recomendações da rede SAP HANA, três sub-redes são representadas em uma rede virtual Azure: 
* Para comunicação com o cliente
* Para comunicação com o sistema de armazenamento
* Para comunicação interna de dois nós HANA

Os volumes do Azure NetApp estão em uma sub-rede separada, [delegada aos Arquivos Do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

Para esta configuração de exemplo, as sub-redes são:  

  - `client`10.23.0.0/24  
  - `storage`10.23.2.0/24  
  - `hana`10.23.3.0/24  
  - `anf`10.23.1.0/26  

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Configure a infra-estrutura de arquivos do Azure NetApp 

Antes de prosseguir com a configuração da infra-estrutura de Arquivos Do Azure NetApp, familiarize-se com a [documentação do Azure NetApp Files][anf-azure-doc]. 

O Azure NetApp Files está disponível em várias [regiões do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Verifique se sua região selecionada do Azure oferece arquivos do Azure NetApp.  

Para obter informações sobre a disponibilidade de Arquivos Azure NetApp pela região do Azure, consulte [a disponibilidade de arquivos do Azure NetApp por região do Azure][anf-avail-matrix].  

Antes de implantar os arquivos do Azure NetApp, solicite o onboarding aos arquivos do Azure NetApp indo para [registrar as instruções do Azure NetApp Files][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Implantar recursos de arquivos do Azure NetApp  

As instruções a seguir supõem que você já implantou sua [rede virtual Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Os recursos do Azure NetApp Files e VMs, onde os recursos do Azure NetApp Files serão montados, devem ser implantados na mesma rede virtual do Azure ou em redes virtuais azure peered.  

1. Se você ainda não implantou os recursos, solicite o [onboarding para arquivos do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  

2. Crie uma conta netapp em sua região Azure selecionada seguindo as instruções em [Criar uma conta NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  

3. Configure um pool de capacidade do Azure NetApp Files seguindo as instruções em [Configurar um pool de capacidade do Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  

   A arquitetura HANA apresentada neste artigo usa um único pool de capacidade de Arquivos Azure NetApp no nível *ultra service.* Para cargas de trabalho HANA no Azure, recomendamos o uso de um Nível de serviço *Ultra* ou *Premium* do Azure NetApp [Files.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)  

4. Delegue uma sub-rede para arquivos do Azure NetApp, conforme descrito nas instruções no [Delegate a Subnet to Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Implantar volumes de arquivos do Azure NetApp seguindo as instruções em [Criar um volume NFS para arquivos do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  

   À medida que você estiver implantando os volumes, certifique-se de selecionar a versão **NFSv4.1.** Atualmente, o acesso ao NFSv4.1 requer uma listagem adicional em branco. Implante os volumes na [sub-rede](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)azure NetApp Files . 
   
   Tenha em mente que os recursos do Azure NetApp Files e as VMs do Azure devem estar na mesma rede virtual do Azure ou em redes virtuais azure. Por exemplo, **HN1**-data-mnt00001, **HN1**-log-mnt00001, e assim por diante, são os nomes de volume e nfs://10.23.1.5/**HN1**-data-mnt00001, nfs://10.23.1.4/**HN1**-log-mnt00001, e assim por diante, são os caminhos de arquivo para os volumes de Arquivos do Azure NetApp.  

   * volume **HN1**-data-mnt00001 (nfs://10.23.1.5/**HN1**-data-mnt00001)
   * volume **HN1**-data-mnt0002 (nfs://10.23.1.6/**HN1**-data-mnt00002)
   * volume **HN1**-log-mnt00001 (nfs://10.23.1.4/**HN1**-log-mnt00001)
   * volume **HN1**-log-mnt0002 (nfs://10.23.1.6/**HN1**-log-mnt00002)
   * volume **HN1**compartilhado (nfs://10.23.1.4/**HN1**-compartilhado)
   
   Neste exemplo, usamos um volume separado de Arquivos Do Azure NetApp para cada dados HANA e volume de log. Para uma configuração mais otimizada de custo em sistemas menores ou não produtivos, é possível colocar todas as montagens de dados e todas as montagens de logs em um único volume.  

### <a name="important-considerations"></a>Considerações importantes

Como você está criando seus arquivos Do Azure NetApp para SAP NetWeaver na arquitetura SUSE de alta disponibilidade, esteja ciente das seguintes considerações importantes:

- O pool de capacidade mínima é de 4 tebibytes (TiB).  
- O tamanho mínimo do volume é de 100 gibibytes (GiB).
- Os Arquivos Do Azure NetApp e todas as máquinas virtuais onde os volumes de Arquivos do Azure NetApp serão montados devem estar na mesma rede virtual do Azure ou em [redes virtuais peered](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) na mesma região.  
- A rede virtual selecionada deve ter uma sub-rede delegada aos Arquivos do Azure NetApp.
- O throughput de um volume de Arquivos Do Azure NetApp é uma função da cota de volume e nível de serviço, conforme documentado no [nível de serviço para arquivos Do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Quando estiver dimensionando os volumes do HANA Azure NetApp, certifique-se de que o throughput resultante atenda aos requisitos do sistema HANA.  
- Com a política de [exportação](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)do Azure NetApp Files, você pode controlar os clientes permitidos, o tipo de acesso (ler-gravação, ler apenas e assim por diante). 
- O recurso Azure NetApp Files ainda não está disponível na região. Atualmente, o recurso não está implantado em todas as zonas de disponibilidade em uma região do Azure. Esteja ciente das possíveis implicações de latência em algumas regiões do Azure.  
-  

> [!IMPORTANT]
> Para as cargas de trabalho SAP HANA, a baixa latência é crítica. Trabalhe com seu representante da Microsoft para garantir que as máquinas virtuais e os volumes de arquivos do Azure NetApp sejam implantados de perto.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionamento para banco de dados HANA em arquivos Do Azure NetApp

O throughput de um volume de Arquivos Do Azure NetApp é uma função do tamanho do volume e do nível de serviço, conforme documentado no [nível de serviço para arquivos Do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Ao projetar a infra-estrutura para SAP no Azure, esteja ciente de alguns requisitos mínimos de armazenamento pelo SAP, que se traduzem em características mínimas de throughput:

- Habilite a leitura/gravação em /hana/log de 250 megabytes por segundo (MB/s) com tamanhos de I/O de 1 MB.  
- Habilite a atividade de leitura de pelo menos 400 MB/s para tamanhos de /hana/data para tamanhos de I/O de 16 MB e 64 MB.  
- Habilite a atividade de gravação de pelo menos 250 MB/s para /hana/data com tamanhos de I/O de 16 MB e 64 MB. 

Os [limites de throughput do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) por 1 TiB de cota de volume são:
- Nível de armazenamento premium - 64 MiB/s  
- Ultra Nível de armazenamento - 128 MiB/s  

Para atender aos requisitos mínimos de throughput sap para dados e log, e as diretrizes para /hana/compartilhado, os tamanhos recomendados seriam:

| Volume | Tamanho de<br>Nível de armazenamento premium | Tamanho de<br>Ultra nível de armazenamento | Protocolo NFS suportado |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared | Max (512 GB, 1xRAM) por 4 nódulos de trabalhador | Max (512 GB, 1xRAM) por 4 nódulos de trabalhador | v3 ou v4.1 |

A configuração SAP HANA para o layout apresentado neste artigo, usando o nível Azure NetApp Files Ultra Storage, seria:

| Volume | Tamanho de<br>Ultra nível de armazenamento | Protocolo NFS suportado |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | v4.1 |
| /hana/log/mnt00002 | 2 TiB | v4.1 |
| /hana/data/mnt00001 | 3.2 TiB | v4.1 |
| /hana/data/mnt00002 | 3.2 TiB | v4.1 |
| /hana/shared | 2 TiB | v3 ou v4.1 |

> [!NOTE]
> As recomendações de dimensionamento de arquivos do Azure NetApp declaradas aqui são direcionadas para atender aos requisitos mínimos que a SAP recomenda para seus provedores de infra-estrutura. Em implantações reais de clientes e cenários de carga de trabalho, esses tamanhos podem não ser suficientes. Use essas recomendações como ponto de partida e adapte-se, com base nos requisitos de sua carga de trabalho específica.  

> [!TIP]
> Você pode redimensionar os volumes do Azure NetApp Files dinamicamente, sem ter que *desmontar* os volumes, parar as máquinas virtuais ou parar o SAP HANA. Essa abordagem permite flexibilidade para atender tanto às demandas de throughput esperadas quanto imprevistos de sua aplicação.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Implantar máquinas virtuais Linux através do portal Azure

Primeiro você precisa criar os volumes de Arquivos do Azure NetApp. Em seguida, faça as seguintes etapas:
1. Crie as [sub-redes virtuais do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) em sua [rede virtual Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). 
1. Implante as VMs. 
1. Crie as interfaces de rede adicionais e conecte as interfaces de rede às VMs correspondentes.  

   Cada máquina virtual possui três interfaces de rede, que correspondem`client` `storage` às `hana`três sub-redes virtuais do Azure ( e ). 

   Para obter mais informações, consulte [Criar uma máquina virtual Linux no Azure com várias placas de interface de rede](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics).  

> [!IMPORTANT]
> Para as cargas de trabalho SAP HANA, a baixa latência é crítica. Para obter baixa latência, trabalhe com seu representante da Microsoft para garantir que as máquinas virtuais e os volumes de arquivos do Azure NetApp sejam implantados de perto. Quando você estiver [embarcando no novo sistema SAP HANA](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) que está usando arquivos SAP HANA Azure NetApp, envie as informações necessárias. 
 
As próximas instruções supõem que você já criou o grupo de recursos, a rede `client`virtual `storage` `hana`Azure e as três sub-redes virtuais do Azure: , e . Ao implantar as VMs, selecione a sub-rede do cliente, para que a interface de rede cliente seja a interface principal nas VMs. Você também precisará configurar uma rota explícita para a sub-rede delegada Azure NetApp Files através do gateway de sub-rede de armazenamento. 

> [!IMPORTANT]
> Certifique-se de que o Sistema Operacional selecionado é certificado sap para SAP HANA nos tipos específicos de VM que você está usando. Para obter uma lista de tipos de VM certificados pela SAP HANA e versões do SO para esses tipos, acesse o site [de plataformas IaaS certificadas pela SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Clique nos detalhes do tipo VM listado para obter a lista completa de versões do Sistema Operacional suportadas pelo SAP HANA para esse tipo.  

1. Crie um conjunto de disponibilidade para SAP HANA. Certifique-se de definir o domínio de atualização máxima.  

2. Crie três máquinas virtuais **(hanadb1,** **hanadb2,** **hanadb3)** fazendo as seguintes etapas:  

   a. Use uma imagem SLES4SAP na galeria Azure suportada para SAP HANA. Usamos uma imagem SLES4SAP 12 SP4 neste exemplo.  

   b. Selecione o conjunto de disponibilidade que você criou anteriormente para SAP HANA.  

   c. Selecione a sub-rede virtual azure do cliente. Selecione [Rede Acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).  

   Quando você implanta as máquinas virtuais, o nome da interface de rede é gerado automaticamente. Nestas instruções de simplicidade, nos referiremos às interfaces de rede geradas automaticamente, que são anexadas à sub-rede virtual azure cliente, como **hanadb1-client,** **hanadb2-client**e **hanadb3-client**. 

3. Crie três interfaces de rede, uma `storage` para cada máquina virtual, para a sub-rede virtual (neste exemplo, **hanadb1-storage,** **hanadb2-storage**e **hanadb3-storage**).  

4. Crie três interfaces de rede, uma `hana` para cada máquina virtual, para a sub-rede virtual (neste exemplo, **hanadb1-hana**, **hanadb2-hana**e **hanadb3-hana**).  

5. Conecte as interfaces de rede virtuais recém-criadas às máquinas virtuais correspondentes, fazendo as seguintes etapas:  

    a. Vá para a máquina virtual no [portal Azure](https://portal.azure.com/#home).  

    b. No painel esquerdo, selecione **Máquinas Virtuais**. Filtrar o nome da máquina virtual (por exemplo, **hanadb1),** e depois selecionar a máquina virtual.  

    c. No **painel Visão Geral,** selecione **Parar** para desalocar a máquina virtual.  

    d. Selecione **Rede**e, em seguida, conecte a interface de rede. Na lista de paradas de interface de **rede Anexar,** `storage` selecione as interfaces de rede já criadas para as sub-redes. `hana`  
    
    e. Selecione **Salvar**. 
 
    f. Repita os passos b a e para as demais máquinas virtuais (em nosso exemplo, **hanadb2** e **hanadb3**).
 
    g. Deixe as máquinas virtuais em estado parado por enquanto. Em seguida, habilitaremos [a rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) para todas as interfaces de rede recém-conectadas.  

6. Habilite a rede acelerada para `storage` as `hana` interfaces de rede adicionais para as sub-redes e as seguintes etapas:  

    a. Abra [a Cloud Shell do Azure](https://azure.microsoft.com/features/cloud-shell/) no portal [Azure](https://portal.azure.com/#home).  

    b. Execute os seguintes comandos para permitir a rede acelerada para as `storage` `hana` interfaces de rede adicionais, que são anexadas às sub-redes.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Inicie as máquinas virtuais fazendo as seguintes etapas:  

    a. No painel esquerdo, selecione **Máquinas Virtuais**. Filtrar o nome da máquina virtual (por exemplo, **hanadb1),** e depois selecioná-lo.  

    b. No **painel Visão geral,** selecione **Iniciar**.  

## <a name="operating-system-configuration-and-preparation"></a>Configuração e preparação do sistema operacional

As instruções nas próximas seções são prefixadas com uma das seguintes:
* **[A]**: Aplicável a todos os nós
* **[1]**: Aplicável apenas ao nó 1
* **[2]**: Aplicável apenas ao nó 2
* **[3]**: Aplicável apenas ao nó 3

Configure e prepare seu sistema operacional fazendo as seguintes etapas:

1. **[A] Manter** os arquivos host nas máquinas virtuais. Inclua entradas para todas as sub-redes. As seguintes entradas `/etc/hosts` foram adicionadas para este exemplo.  

    <pre><code>
    # Storage
    10.23.2.4   hanadb1-storage
    10.23.2.5   hanadb2-storage
    10.23.2.6   hanadb3-storage
    # Client
    10.23.0.5   hanadb1
    10.23.0.6   hanadb2
    10.23.0.7   hanadb3
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** Alterar as configurações de configuração de Configuração de DHCP e nuvem para a interface de rede para armazenamento para evitar alterações não intencionais do nome do host.  

    As instruções a seguir assumem que a interface da rede de armazenamento é `eth1`. 

    <pre><code>
    vi /etc/sysconfig/network/dhcp
    # Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-<b>eth1</b>
    # Edit ifcfg-eth1 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

2. **[A]** Adicione uma rota de rede, de modo que a comunicação aos Arquivos Do Azure NetApp vá através da interface da rede de armazenamento.  

    As instruções a seguir assumem que a interface da rede de armazenamento é `eth1`.  

    <pre><code>
    vi /etc/sysconfig/network/ifroute-<b>eth1</b>
    # Add the following routes 
    # RouterIPforStorageNetwork - - -
    # ANFNetwork/cidr RouterIPforStorageNetwork - -
    <b>10.23.2.1</b> - - -
    <b>10.23.1.0/26</b> <b>10.23.2.1</b> - -
    </code></pre>

    Reinicie a VM para ativar as alterações.  

3. **[A]** Prepare o SISTEMA OPERACIONAL para executar o SAP HANA em Sistemas NetApp com NFS, conforme descrito no [SAP HANA na NetApp AFF Systems com guia de configuração NFS](https://www.netapp.com/us/media/tr-4435.pdf). Crie arquivo de configuração */etc/sysctl.d/netapp-hana.conf* para as configurações do NetApp.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000
    net.ipv4.tcp_slow_start_after_idle=0
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** Crie arquivo de configuração */etc/sysctl.d/ms-az.conf* com a Microsoft para configurações do Azure.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

4. **[A]** Ajuste as configurações do sunrpc, conforme recomendado no [SAP HANA no NetApp AFF Systems com o guia de configuração NFS](https://www.netapp.com/us/media/tr-4435.pdf).  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>Monte os volumes de arquivos do Azure NetApp

1. **[A] Criar** pontos de montagem para os volumes de banco de dados HANA.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** Crie diretórios específicos do nó para /usr/sap no **HN1**-compartilhado.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A] Verifique** a configuração do domínio NFS. Certifique-se de que o domínio está configurado como o domínio **`defaultv4iddomain.com`** Padrão Azure NetApp Files, ou seja, e o mapeamento não está definido para **ninguém**.  

    > [!IMPORTANT]
    > Certifique-se de definir o `/etc/idmapd.conf` domínio NFS na VM para corresponder à configuração de domínio padrão em Arquivos Azure NetApp: **`defaultv4iddomain.com`**. Se houver uma incompatibilidade entre a configuração de domínio no cliente NFS (ou seja, o VM) e o servidor NFS, ou seja, a configuração do Azure NetApp, então as `nobody`permissões para arquivos nos volumes do Azure NetApp que são montados nas VMs serão exibidas como .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`Verificar . Deve ser definido como **Y**. Para criar a estrutura `nfs4_disable_idmapping` do diretório onde está localizado, execute o comando mount. Você não poderá criar manualmente o diretório em /sys/modules, porque o acesso é reservado para o kernel/drivers.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

5. **[A]** Crie o grupo SAP HANA e o usuário manualmente. Os IDs para sapsys de grupo e adm do usuário **hn1**devem ser definidos para os mesmos IDs, que são fornecidos durante o onboarding. (Neste exemplo, os IDs são definidos como **1001**.) Se os IDs não forem definidos corretamente, você não poderá acessar os volumes. Os IDs para sapsys de grupo e contas de usuário **hn1**adm e sapadm devem ser os mesmos em todas as máquinas virtuais.  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A] Monte** os volumes compartilhados de arquivos do Azure NetApp.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** Monte os volumes específicos do nó em **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** Monte os volumes específicos do nó em **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** Monte os volumes específicos do nó em **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Verifique se todos os volumes HANA estão montados com a versão do protocolo **NFS NFV4**.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>Instalação  

Neste exemplo para implantar o SAP HANA na configuração de escala com nó de espera com o Azure, usamos o HANA 2.0 SP4.  

### <a name="prepare-for-hana-installation"></a>Prepare-se para a instalação do HANA

1. **[A]** Antes da instalação do HANA, defina a senha raiz. Você pode desativar a senha raiz depois que a instalação estiver concluída. Execute `root` como `passwd`comando .  

2. **[1]** Verifique se você pode fazer login via SSH para **hanadb2** e **hanadb3**, sem ser solicitado para uma senha.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A] Instale** pacotes adicionais, que são necessários para hana 2.0 SP4. Para obter mais informações, consulte SAP Nota [2593824](https://launchpad.support.sap.com/#/notes/2593824). 

    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2], [3]** Mude a propriedade do `data` `log` SAP HANA e diretórios para **hn1**adm.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>Instalação hana

1. **[1]** Instale o SAP HANA seguindo as instruções no [guia de instalação e atualização SAP HANA 2.0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). Neste exemplo, instalamos o scale-out SAP HANA com master, um trabalhador e um nó de espera.  

   a. Inicie o programa **hdblcm** a partir do diretório de software de instalação HANA. Use `internal_network` o parâmetro e passe o espaço de endereço para a sub-rede, que é usado para a comunicação interna de nó HANA.  

    <pre><code>
    ./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   b. No prompt, digite os seguintes valores:

     * Para **Escolher uma ação**: digite **1** (para instalar)
     * Para **componentes adicionais para instalação**: digite **2, 3**
     * Para o caminho de instalação: pressione Enter (padrões para /hana/compartilhado)
     * Para **nome local do host**: pressione Enter para aceitar o padrão
     * Em **Você deseja adicionar hosts ao sistema?** **y**
     * Para **obter nomes de host separados por comma para adicionar**: insira **hanadb2, hanadb3**
     * Para **nome de usuário raiz** [raiz]: pressione Enter para aceitar o padrão
     * Para **senha de usuário raiz**: digite a senha do usuário raiz
     * Para funções para host hanadb2: insira **1** (para trabalhador)
     * Para **host Failover Group** para host hanadb2 [default]: pressione Enter para aceitar o padrão
     * Para **o número de partição de armazenamento** para host hanadb2 [<<assign automatically>>]: pressione Enter para aceitar o padrão
     * Para **grupo de trabalhadores** para host hanadb2 [default]: pressione Enter para aceitar o padrão
     * Para **selecionar funções** para host hanadb3: insira **2** (para espera)
     * Para **host Failover Group** para host hanadb3 [default]: pressione Enter para aceitar o padrão
     * Para **grupo de trabalhadores** para host hanadb3 [default]: pressione Enter para aceitar o padrão
     * Para **SAP HANA System ID**: insira **HN1**
     * Para **o número da instância** [00]: digite **03**
     * Para **o Grupo de Trabalhadores de Host Local** [padrão]: pressione Enter para aceitar o padrão
     * Para **selecionar o uso do sistema / Inserir índice [4]**: digite **4** (para personalizado)
     * Para **Localização de Volumes de Dados** [/hana/data/HN1]: pressione Enter para aceitar o padrão
     * Para **Localização de Volumes de log** [/hana/log/HN1]: pressione Enter para aceitar o padrão
     * Para **restringir a alocação máxima de memória?** [n]: enter **n**
     * Para **nome do host de certificado para host hanadb1** [hanadb1]: pressione Enter para aceitar o padrão
     * Para **nome do host de certificado para host hanadb2** [hanadb2]: pressione Enter para aceitar o padrão
     * Para **nome do host de certificado para host hanadb3** [hanadb3]: pressione Enter para aceitar o padrão
     * Para **administrador do sistema (hn1adm) Senha:** digite a senha
     * Para **o sistema de banco de dados usuário (sistema) Senha**: digite a senha do sistema
     * Para **confirmar o sistema de banco de dados usuário (sistema) Senha**: digite a senha do sistema
     * Para **reiniciar sistema após reinicialização da máquina?** [n]: enter **n** 
     * Pois **você quer continuar (y/n)**: validar o resumo e se tudo estiver bem, digite **y**


2. **[1]** Verifique global.ini  

   Exiba global.ini e certifique-se de que a configuração para a comunicação interna do nó SAP HANA esteja em vigor. Verifique a seção **de comunicação.** Ele deve ter o `hana` espaço de `listeninterface` endereço para `.internal`a sub-rede, e deve ser definido como . Verifique a seção **internal_hostname_resolution.** Ele deve ter os endereços IP para as `hana` máquinas virtuais HANA que pertencem à sub-rede.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** Adicione o mapeamento do host para garantir que os endereços IP do cliente sejam usados para a comunicação do cliente. Adicione `public_host_resolution`seção e adicione os endereços IP correspondentes da sub-rede do cliente.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** Reinicie o SAP HANA para ativar as alterações.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Verifique se a interface do cliente `client` estará usando os endereços IP da sub-rede para comunicação.  

   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   Para obter informações sobre como verificar a configuração, consulte SAP Nota [2183363 - Configuração da rede interna SAP HANA](https://launchpad.support.sap.com/#/notes/2183363).  

6. Para otimizar o SAP HANA para o armazenamento subjacente de Arquivos Azure NetApp, defina os seguintes parâmetros sap HANA:

   - `max_parallel_io_requests`**128**
   - `async_read_submit` **on**
   - `async_write_submit_active` **on**
   - `async_write_submit_blocks`**todos os**

   Para obter mais informações, consulte [SAP HANA no NetApp AFF Systems com o NFS Configuration Guide](https://www.netapp.com/us/media/tr-4435.pdf). 

   Começando com os sistemas SAP HANA 2.0, você pode definir os parâmetros em `global.ini`. Para obter mais informações, consulte SAP Note [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   
   Para as versões sps12 e anteriores dos sistemas SAP HANA 1.0, esses parâmetros podem ser definidos durante a instalação, conforme descrito no SAP Note [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. O armazenamento usado pelo Azure NetApp Files tem uma limitação de tamanho de arquivo de 16 terabytes (TB). O SAP HANA não está implicitamente ciente da limitação de armazenamento e não criará automaticamente um novo arquivo de dados quando o limite de tamanho do arquivo de 16 TB for atingido. À medida que o SAP HANA tenta aumentar o arquivo para além de 16 TB, essa tentativa resultará em erros e, eventualmente, em uma falha no servidor de índice. 

   > [!IMPORTANT]
   > Para evitar que o SAP HANA tentar crescer arquivos de dados além do limite `global.ini`de [16 TB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) do subsistema de armazenamento, defina os seguintes parâmetros em .  
   > - datavolume_striping = verdadeiro
   > - datavolume_striping_size_gb = 15000 Para obter mais informações, consulte nota SAP [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Esteja atento ao SAP Note [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Failover SAP HANA de teste 

1. Simule uma falha de nó em um nó de trabalhador SAP HANA. Faça o seguinte: 

   a. Antes de simular a falha do nó, execute os seguintes comandos como **hn1**adm para capturar o status do ambiente:  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. Para simular uma falha de nó, execute o seguinte comando como raiz no nó do trabalhador, que é **hanadb2** neste caso:  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. Monitore o sistema para conclusão de failover. Quando o failover tiver sido concluído, capture o status, que deve parecer o seguinte:  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Quando um nó experimenta pânico no kernel, evite atrasos `kernel.panic` com failover SAP HANA definindo para 20 segundos em *todas as* máquinas virtuais HANA. A configuração `/etc/sysctl`é feita em . Reinicie as máquinas virtuais para ativar a mudança. Se essa alteração não for realizada, o failover pode levar 10 ou mais minutos quando um nó está experimentando pânico no kernel.  

2. Mate o servidor de nomes fazendo o seguinte:

   a. Antes do teste, verifique o status do ambiente executando os seguintes comandos como **hn1**adm:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   b. Execute os seguintes comandos como **hn1**adm no nó mestre ativo, que é **hanadb1** neste caso:  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    O nó de espera **hanadb3** assumirá como nó mestre. Aqui está o estado de recurso após o teste de failover ser concluído:  

    <pre><code>
        # Check the instance status
        sapcontrol -nr 03 -function GetSystemInstanceList
        GetSystemInstanceList
        OK
        hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
        hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
        hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
        hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
        # Check the landscape status
        python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
        | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
        |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
        |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
        | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
        | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
        | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
        | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. Reinicie a instância HANA no **hanadb1** (ou seja, na mesma máquina virtual, onde o servidor de nome foi morto). O **nó hanadb1** se juntará ao meio ambiente e manterá seu papel de espera.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Depois que o SAP HANA tiver começado no **hanadb1,** espere o seguinte status:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. Novamente, mate o servidor de nomes no nó mestre atualmente ativo (ou seja, no nó **hanadb3**).  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   O nó **hanadb1** retomará o papel de nó mestre. Depois que o teste de failover tiver sido concluído, o status ficará assim:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. Inicie o SAP HANA no **hanadb3**, que estará pronto para servir como um nó de espera.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Depois que o SAP HANA tiver começado em **hanadb3,** o status parece o seguinte:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>Próximas etapas

* [Planejamento e implementação de Máquinas Virtuais do Azure para o SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação do DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastres do SAP HANA no Azure (instâncias grandes), confira [Alta disponibilidade e recuperação de desastres do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastres do SAP HANA em VMs Azure, consulte [Alta disponibilidade de SAP HANA em VMs (Azure Virtual Machines)][sap-hana-ha].
