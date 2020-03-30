---
title: Lista de verificação de planejamento e implantação de carga de trabalho do SAP | Microsoft Docs
description: Checklist para planejar implantações de carga de trabalho SAP no Azure e implantar as cargas de trabalho
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b8c1b0bcc74d73f1f869972488ba7c5dfe610d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060058"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Cargas de trabalho SAP no Azure: lista de verificação de planejamento e implantação

Esta lista de verificação foi projetada para clientes que movem aplicativos SAP NetWeaver, S/4HANA e Hybris para a infra-estrutura Azure como um serviço. Durante toda a duração do projeto, um cliente e/ou parceiro SAP deve revisar a lista de verificação. É importante notar que muitas das verificações são concluídas no início do projeto e durante a fase de planejamento. Depois que a implantação é feita, alterações diretas na infra-estrutura azure implantada ou versões de software SAP podem se tornar complexas.

Revise a lista de verificação em marcos importantes durante o seu projeto. Isso permitirá que você detecte pequenos problemas antes que eles se tornem grandes problemas. Você também terá tempo suficiente para reprojetar e testar quaisquer mudanças necessárias. Não considere esta lista completa. Dependendo da sua situação, você pode precisar realizar muito mais verificações.

A lista de verificação não inclui tarefas independentes do Azure. Por exemplo, as interfaces de aplicativos SAP mudam durante uma mudança para a plataforma Azure ou para um provedor de hospedagem.

Esta lista de verificação também pode ser usada para sistemas que já estão implantados. Novos recursos, como Write Accelerator e Availability Zones, e novos tipos de VM podem ter sido adicionados desde que você foi implantado. Portanto, é útil revisar a lista de verificação periodicamente para garantir que você esteja ciente dos novos recursos na plataforma Azure.

## <a name="project-preparation-and-planning-phase"></a>Fase de planejamento e preparação do projeto
Durante esta fase, você planeja a migração de sua carga de trabalho SAP para a plataforma Azure. No mínimo, durante esta fase você precisa criar os seguintes documentos e definir e discutir os seguintes elementos da migração:

1. Documento de design de alto nível. Este documento deve conter:
    - O inventário atual de componentes e aplicativos SAP e um inventário de aplicativo de destino para o Azure.
    - Uma matriz de atribuição de responsabilidades (RACI) que define as responsabilidades e atribuições das partes envolvidas. Comece em um alto nível, e trabalhe para níveis mais granulares ao longo do planejamento e das primeiras implantações.
    - Uma arquitetura de solução de alto nível.
    - Uma decisão sobre para quais regiões do Azure se implantar. Veja a [lista das regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/). Para saber quais serviços estão disponíveis em cada região, consulte [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/)
    - Uma arquitetura de rede para conectar-se do local ao Azure. Comece a se familiarizar com o projeto do [Virtual Datacenter para o Azure](https://docs.microsoft.com/azure/architecture/vdc/).
    - Princípios de segurança para executar dados de negócios de alto impacto no Azure. Para saber mais sobre segurança de dados, comece com a documentação de segurança do [Azure](https://docs.microsoft.com/azure/security/).
2.  Documento de projeto técnico. Este documento deve conter:
    - Um diagrama de bloco para a solução.
    - O dimensionamento de componentes de computação, armazenamento e rede no Azure. Para obter o dimensionamento SAP de VMs Azure, consulte nota de [suporte sap #1928533](https://launchpad.support.sap.com/#/notes/1928533).
    - Continuidade de negócios e arquitetura de recuperação de desastres.
    - Informações detalhadas sobre as versões do pacote de suporte ao SISTEMA OPERACIONAL, DB, kernel e SAP. Não é necessariamente verdade que todas as versões do Sistema Operacional suportadas pelo SAP NetWeaver ou S/4HANA são suportadas em VMs azure. O mesmo vale para as versões DBMS. Verifique as seguintes fontes para alinhar e, se necessário, atualizar as versões SAP, as versões Do DBMS e as versões do Sistema Operacional para garantir o suporte ao SAP e ao Azure. Você precisa ter combinações de lançamento suportadas por SAP e Azure para obter suporte total do SAP e da Microsoft. Se necessário, você precisa planejar para atualizar alguns componentes do software. Mais detalhes sobre o software SAP, S E SO e DBMS suportados estão documentados aqui:
        - [Nota de suporte sap #1928533](https://launchpad.support.sap.com/#/notes/1928533). Esta nota define as versões mínimas do SISTEMA OPERACIONAL suportadas em VMs azure. Também define as liberações mínimas de banco de dados necessárias para a maioria dos bancos de dados não HANA. Finalmente, ele fornece o dimensionamento SAP para tipos de VM Azure suportados pelo SAP.
        - [Nota de suporte SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553). Esta nota define políticas de suporte em torno do relacionamento de armazenamento e suporte do Azure necessário com a Microsoft.
        - [Nota de suporte SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619). Esta nota define a matriz de suporte Oracle para o Azure. A Oracle suporta apenas o Windows e o Oracle Linux como sistemas operacionais convidados no Azure para cargas de trabalho SAP. Essa declaração de suporte também se aplica à camada de aplicativo SAP que executa instâncias SAP. No entanto, a Oracle não suporta alta disponibilidade para Serviços Centrais SAP no Oracle Linux através do Pacemaker. Se você precisa de alta disponibilidade para ASCS no Oracle Linux, você precisa usar o SIOS Protection Suite para Linux. Para obter dados detalhados de certificação SAP, consulte nota de suporte sap [#1662610 - Detalhes de suporte para o SIOS Protection Suite para Linux](https://launchpad.support.sap.com/#/notes/1662610). Para o Windows, a solução de clustering de failover do Servidor Windows suportada pelo SAP para serviços centrais SAP é suportada em conjunto com a Oracle como a camada DBMS.
        - [Nota de suporte SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581). Esta nota fornece a matriz de suporte para SAP HANA em diferentes versões do Sistema Operacional.
        - VMs Azure e [hana grandes instâncias](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) suportadas pelo SAP HANA estão listados no site da [SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
        - [Matriz de disponibilidade de produtos SAP](https://support.sap.com/en/).
        - [Nota de suporte sap #2555629 - Tiering dinâmico SAP HANA 2.0 - Suporte a hipervisor e nuvem](https://launchpad.support.sap.com/#/notes/2555629)
        - [Nota de suporte sap #1662610 - Detalhes de suporte para SIOS Protection Suite para Linux](https://launchpad.support.sap.com/#/notes/1662610)
        - Notas SAP para outros produtos específicos do SAP.     
    - Recomendamos projetos rigorosos de três níveis para sistemas de produção SAP. Não recomendamos combinar ASCS e/ou Servidores DBMS e/ou aplicativos em uma VM. O uso de configurações de cluster multi-SID para Serviços Centrais SAP é suportado em sistemas operacionais convidados do Windows no Azure. Mas essa configuração não é suportada para sap central services em sistemas operacionais Linux no Azure. Você pode encontrar documentação para o cenário do sistema operacional convidado do Windows nestes artigos:
        - [Alta disponibilidade de vários SIDs da instância do SAP ASCS/SCS com clustering de failover do Windows Server e disco compartilhado no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        - [Alta disponibilidade de vários SIDs da instância do SAP ASCS com clustering de failover do Windows Server e compartilhamento de arquivos no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    - Arquitetura de alta disponibilidade e recuperação de desastres.
        - Com base no RTO e no RPO, defina como é a arquitetura de alta disponibilidade e recuperação de desastres.
        - Para alta disponibilidade dentro de uma região, verifique o que o DBMS desejado tem a oferecer no Azure. A maioria dos pacotes DBMS oferece métodos síncronos de uma espera síncrona, que recomendamos para sistemas de produção. Verifique também a documentação relacionada ao SAP para diferentes bancos de dados, começando [com considerações para a implantação do Azure Virtual Machines DBMS para cargas de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) e documentos relacionados.
           O uso do Clusterde failover do Windows Server com uma configuração de disco compartilhado para a camada DBMS como, por exemplo, [descrito para o SQL Server,](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017)não é suportado. Em vez disso, use soluções como:
           - [SQL Server Always On](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
           - [Replicação de sistema do HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - Para recuperação de desastres em todas as regiões do Azure, revise as soluções oferecidas por diferentes fornecedores de DBMS. A maioria deles suporta replicação assíncrona ou envio de log.
        - Para a camada de aplicativo SAP, determine se você executará seus sistemas de teste de regressão de negócios, que idealmente são réplicas de suas implantações de produção, na mesma região do Azure ou na sua região DR. No segundo caso, você pode direcionar esse sistema de regressão de negócios como o alvo dr para suas implantações de produção.
        - Se você decidir não colocar os sistemas de não-produção no site dr, procure o Azure Site Recovery como um método para replicar a camada de aplicação SAP na região do Azure DR. Para obter mais informações, consulte uma [recuperação de desastres configurada para uma implantação de aplicativo SAP NetWeaver de vários níveis](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap).
        - Se você decidir usar uma configuração HADR combinada usando [as Zonas de Disponibilidade do Azure,](https://docs.microsoft.com/azure/availability-zones/az-overview)familiarize-se com as regiões do Azure onde as Zonas de Disponibilidade estão disponíveis. Também leve em conta as restrições que podem ser introduzidas pelo aumento das latências de rede entre duas Zonas de Disponibilidade.  
3.  Um inventário de todas as interfaces SAP (SAP e não-SAP).
4.  Projeto de serviços de fundação. Este design deve incluir os seguintes itens:
    - Design de Diretório Ativo e DNS.
    - Topologia de rede dentro do Azure e atribuição de diferentes sistemas SAP.
    - Estrutura de acesso baseada em função para equipes que gerenciam aplicativos de [infra-estrutura](https://docs.microsoft.com/azure/role-based-access-control/overview) e SAP no Azure.
    - Topologia de grupo de recursos.
    - [Estratégia de marcação](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing).
    - Convenções de nomeação para VMs e outros componentes de infra-estrutura e/ou nomes lógicos.
5.  Contrato de suporte do Microsoft Premier. Identifique seu Microsoft Technical Account Manager (TAM). Para os requisitos de suporte ao SAP, consulte [nota de suporte sap #2015553](https://launchpad.support.sap.com/#/notes/2015553).
6.  O número de assinaturas do Azure e a cota principal para as assinaturas. [Pedidos de suporte abertos para aumentar as cotas de assinaturas do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) conforme necessário.
7.  Plano de redução de dados e migração de dados para migração de dados SAP para o Azure. Para os sistemas SAP NetWeaver, o SAP tem orientações sobre como limitar o volume de grandes quantidades de dados. Consulte [este guia SAP](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) sobre gerenciamento de dados em sistemas ErP SAP. Parte do conteúdo também se aplica aos sistemas NetWeaver e S/4HANA em geral.
8.  Uma abordagem de implantação automatizada. O objetivo da automação de implantações de infra-estrutura no Azure é implantar de forma determinista e obter resultados determinísticos. Muitos clientes usam scripts baseados em PowerShell ou CLI. Mas existem várias tecnologias de código aberto que você pode usar para implantar a infra-estrutura Do Zure para SAP e até mesmo instalar software SAP. Você pode encontrar exemplos no GitHub:
    - [Implantações automatizadas do SAP na nuvem do Azure](https://github.com/Azure/sap-hana)
    - [Instalação do SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Defina uma cadência regular de revisão de design e implantação entre você como cliente, o integrador do sistema, a Microsoft e outras partes envolvidas.

 
## <a name="pilot-phase-strongly-recommended"></a>Fase piloto (fortemente recomendada)
 
Você pode executar um piloto antes ou durante o planejamento e preparação do projeto. Você também pode usar a fase piloto para testar abordagens e projetos feitos durante a fase de planejamento e preparação. E você pode expandir a fase piloto para torná-la uma prova real de conceito.

Recomendamos que você configure e valide uma solução HADR completa e um projeto de segurança durante uma implantação piloto. Alguns clientes realizam testes de escalabilidade durante esta fase. Outros clientes usam implantações de sistemas sap sandbox como uma fase piloto. Presumimos que você já identificou um sistema que você quer migrar para a Azure para o piloto.

1. Otimizar a transferência de dados para o Azure. A escolha ideal é altamente dependente do cenário específico. A transferência do local através do [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) é mais rápida se o circuito ExpressRoute tiver largura de banda suficiente. Em outros cenários, a transferência pela internet é mais rápida.
2. Para uma migração heterogênea da plataforma SAP que envolva uma exportação e importação de dados, teste e otimize as fases de exportação e importação. Para grandes migrações nas quais o SQL Server é a plataforma de destino, você pode encontrar [recomendações](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070). Você pode usar o Migration Monitor/SWPM se não precisar de uma atualização de versão combinada. Você pode usar o processo [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) quando combinar a migração com uma atualização de versão SAP. Para isso, você precisa atender a certos requisitos para a combinação de plataforma DBMS de origem e destino. Este processo está documentado na [Opção de Migração de Banco de Dados (DMO) da Soma 2.0 SP03](https://launchpad.support.sap.com/#/notes/2631152).
   1.  Exportar para origem, exportar o upload de arquivo para o Azure e importar o desempenho. Maximizar a sobreposição entre exportação e importação.
   2.  Avalie o volume do banco de dados nas plataformas de destino e destino para fins de dimensionamento de infra-estrutura.
   3.  Validar e otimizar o tempo.
1. Validação técnica.
   1. Tipos de VM.
        - Revise os recursos nas notas de suporte sap, no diretório de hardware SAP HANA e no SAP PAM novamente. Certifique-se de que não há alterações nas VMs suportadas para o Azure, versões de SO suportadas para esses tipos de VM e versões SAP e DBMS suportadas.
        - Valide novamente o dimensionamento do seu aplicativo e a infraestrutura que você implanta no Azure. Se você estiver movendo aplicativos existentes, muitas vezes você pode obter o SAPS necessário da infra-estrutura que você usa e da [página de benchmark SAP](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) e compará-lo com os números SAPS listados na [nota de suporte SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Também tenha [este artigo sobre classificações SAPS](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) em mente.
        - Avalie e teste o dimensionamento de suas VMs azure no que diz respeito ao máximo de throughput de armazenamento e throughput de rede dos tipos de VM que você escolheu durante a fase de planejamento. Você pode encontrar os dados aqui:
           -  [Tamanhos das Máquinas Virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). É importante considerar o *throughput de disco sem cache máximo* para dimensionamento.
           -  [Tamanhos para máquinas virtuais Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). É importante considerar o *throughput de disco sem cache máximo* para dimensionamento.
   2. Armazenamento.
        - No mínimo, use [o armazenamento SSD Padrão Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) para VMs que representam camadas de aplicativos SAP e para a implantação de DBMSs que não sejam sensíveis ao desempenho.
        - Em geral, não recomendamos o uso de [discos HDD padrão Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd).
        - Use [o Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) para quaisquer VMs DBMS que sejam remotamente sensíveis ao desempenho.
        - Use [discos gerenciados pelo Azure](https://azure.microsoft.com/services/managed-disks/).
        - Use o Acelerador de Gravação do Azure para unidades de log do DBMS com a série M. Esteja ciente dos limites e uso do Write Accelerator, conforme documentado no [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).
        - Para os diferentes tipos de DBMS, verifique a [documentação DBMS genérica relacionada ao SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) e a documentação específica do DBMS que o documento genérico aponta.
        - Para obter mais informações sobre o SAP HANA, consulte [as configurações e operações de infra-estrutura SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
        - Nunca monte discos de dados do Azure em uma VM Linux do Azure usando a ID do dispositivo. Em vez disso, use o identificador universal exclusivo (UUID). Tenha cuidado ao usar, por exemplo, ferramentas gráficas para montar discos de dados do Azure. Verifique duas vezes as entradas em /etc/fstab para certificar-se de que o UUID está usado para montar os discos. Você pode encontrar mais detalhes [neste artigo](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk).
   3. Rede.
        - Teste e avalie sua infra-estrutura de rede virtual e a distribuição de seus aplicativos SAP entre ou dentro das diferentes redes virtuais do Azure.
        -  Avalie a abordagem da arquitetura de rede virtual hub-and-spoke ou a abordagem de microsegmentação em uma única rede virtual Do Zure. Baseie esta avaliação em:
               1. Custos de troca de dados entre [redes virtuais Azure peered](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Para obter informações sobre custos, consulte [preços da Rede Virtual](https://azure.microsoft.com/pricing/details/virtual-network/).
               2. Vantagens de uma rápida desconexão do peering entre redes virtuais Azure em vez de alterar o grupo de segurança da rede para isolar uma sub-rede dentro de uma rede virtual. Essa avaliação é para casos em que aplicativos ou VMs hospedados em uma sub-rede da rede virtual se tornaram um risco de segurança.
                3. Registro central e auditoria do tráfego de rede entre o local, o mundo exterior e o datacenter virtual que você construiu no Azure.
        - Avalie e teste o caminho dos dados entre a camada de aplicativo SAP e a camada SAP DBMS.
            -  A colocação de dispositivos virtuais de [rede Azure](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo SAP e a camada DBMS de sistemas SAP baseados em SAP NetWeaver, Hybris ou S/4HANA não é suportada.
            -  A colocação da camada de aplicativos SAP e do SAP DBMS em diferentes redes virtuais do Azure que não são acompanhadas não é suportada.
            -  Você pode usar [as regras do grupo de segurança de aplicativos e do grupo de segurança da rede](https://docs.microsoft.com/azure/virtual-network/security-overview) para definir rotas entre a camada de aplicativo SAP e a camada SAP DBMS.
        - Certifique-se de que [a rede acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) esteja ativada nas VMs usadas na camada de aplicativo SAP e na camada SAP DBMS. Lembre-se de que diferentes níveis de sistema operacional são necessários para dar suporte à Rede Acelerada no Azure:
            - Windows Server 2012 R2 ou posterior.
            - SUSE Linux 12 SP3 ou posterior.
            - RHEL 7.4 ou posterior.
            - Oracle Linux 7.5. Se estiver usando o kernel RHCKL, é necessário liberar 3.10.0-862.13.1.el7. Se você estiver usando o kernel Oracle UEK, a versão 5 é necessária.
        - Teste e avalie a latência da rede entre as VMs de camada de aplicação SAP e as VMs DBMS de acordo com notas de suporte SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avalie os resultados com a orientação de latência da rede na [nota de suporte sap #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A latência da rede deve estar na faixa moderada ou boa. Exceções se aplicam ao tráfego entre vMs e unidades HANA Large Instance, conforme documentado [neste artigo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance).
        - Certifique-se de que as implantações do ILB estão configuradas para usar o Direct Server Return. Essa configuração reduzirá a latência quando os ILBs do Azure forem usados para configurações de alta disponibilidade na camada DBMS.
        - Se você estiver usando o Azure Load Balancer juntamente com os sistemas operacionais convidados do Linux, verifique se o parâmetro de rede Linux **net.ipv4.tcp_timestamps** está definido como **0**. Esta recomendação conflita com as recomendações em versões mais antigas da [nota SAP #2382421](https://launchpad.support.sap.com/#/notes/2382421). A nota SAP agora é atualizada para afirmar que este parâmetro precisa ser definido como **0** para trabalhar com balanceadores de carga do Azure.
        - Considere usar [grupos de colocação de proximidade do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) para obter a latência ideal da rede. Para obter mais informações, consulte [os grupos de colocação de proximidade do Azure para obter uma latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md).
   4. Implantações de alta disponibilidade e recuperação de desastres.
        - Se você implantar a camada de aplicativo SAP sem definir uma zona de disponibilidade específica do Azure, certifique-se de que todas as VMs que executam instâncias de diálogo SAP ou instâncias de middleware de um único sistema SAP sejam implantadas em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).
        - Se você não precisar de alta disponibilidade para serviços centrais SAP e o DBMS, você pode implantar esses VMs no mesmo conjunto de disponibilidade que a camada de aplicativo SAP.
        - Se você proteger os Serviços Centrais SAP e a camada DBMS para alta disponibilidade usando replicação passiva, coloque os dois nós para Serviços Centrais SAP em um conjunto de disponibilidade separado e os dois álos DBMS em outro conjunto de disponibilidade.
        - Se você implantar em Zonas de Disponibilidade do Azure, não poderá usar conjuntos de disponibilidade. Mas você precisa ter certeza de implantar os nós de Serviços Centrais ativos e passivos em duas Zonas de Disponibilidade diferentes. Use Zonas de disponibilidade que tenham a menor latência entre elas.
          Tenha em mente que você precisa usar o [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) para o caso de usar clusters failover do Windows ou Pacemaker para a camada De Serviços Centrais DBMS e SAP em Zonas de Disponibilidade. Você não pode usar [o Balanceador de Carga Básica](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) para implantações zonais.
   5. Configurações de tempo.
        - Verifique os traços do desenvolvedor SAP NetWeaver das instâncias SAP para ter certeza de que não há quebras de conexão entre o servidor enqueue e os processos de trabalho SAP. Você pode evitar essas quebras de conexão definindo esses dois parâmetros de registro:
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000. Para obter mais informações, consulte [KeepAliveTime](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)).
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000. Para obter mais informações, consulte [KeepAliveInterval](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)).
        - Para evitar tempos de GUI entre interfaces de GUI SAP e camadas de aplicativos SAP implantadas no Azure, verifique se esses parâmetros estão definidos no perfil padrão.pfl ou no perfil de instância:
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - Para evitar a interrupção das conexões estabelecidas entre o processo de enqueue SAP e os processos de trabalho SAP, você precisa definir o parâmetro **enque/encni/set_so_keepalive** **como verdadeiro**. Veja também [a nota sap #2743751](https://launchpad.support.sap.com/#/notes/2743751).  
        - Se você usar uma configuração de cluster failover do Windows, certifique-se de que o tempo de reação em nomes não responsivos esteja definido corretamente para o Azure. O artigo [Sintonia limites de rede de cluster failover](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) lista parâmetros e como eles afetam sensibilidades de failover. Supondo que os nós de cluster estejam na mesma sub-rede, você deve alterar esses parâmetros:
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistóricode comprimento = 30
    6. Configurações ou patches do sistema operacional
        - Para executar o HANA no SAP, leia estas notas e documentações:
            -   [Nota de suporte SAP #2814271 - SAP HANA Backup falha no Azure com erro de soma de verificação](https://launchpad.support.sap.com/#/notes/2814271)
            -   [Nota de suporte SAP #2753418 - Degradação potencial de desempenho devido ao retorno do temporizador](https://launchpad.support.sap.com/#/notes/2753418)
            -   [Nota de suporte sap #2791572 - Degradação de desempenho devido à falta de suporte VDSO para hyper-V no Azure](https://launchpad.support.sap.com/#/notes/2791572)
            -   [Nota de suporte sap #2382421 - Otimizando a configuração de rede em HANA e nível de osS](https://launchpad.support.sap.com/#/notes/2382421)
            -   [Nota de suporte sap #2694118 - Red Hat Enterprise Linux HA Add-On no Azure](https://launchpad.support.sap.com/#/notes/2694118)
            -   [Nota de suporte sap #1984787 - SUSE LINUX Enterprise Server 12: Notas de instalação](https://launchpad.support.sap.com/#/notes/1984787)
            -   [Nota de suporte sap #2002167 - Red Hat Enterprise Linux 7.x: Instalação e Atualização](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [Nota de suporte SAP n° 2292690 – Banco de dados SAP HANA: configurações de sistema operacional recomendadas para RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [Nota de suporte sap #2772999 - Red Hat Enterprise Linux 8.x: Instalação e Configuração](https://launchpad.support.sap.com/#/notes/2772999)
            -   [Nota de suporte sap #2777782 - SAP HANA DB: Configurações recomendadas do sistema operacional para RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
            -   [Nota de suporte sap #2578899 - SUSE Linux Enterprise Server 15: Nota de instalação](https://launchpad.support.sap.com/#/notes/2578899)
            -   [Nota de suporte sap #2455582 - Linux: Executando aplicativos SAP compilados com GCC 6.x](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [Nota de suporte sap #2729475 - Falha do HWCCT com erro "O hypervisor não é suportado" em VMs Azure certificados para SAP HANA](https://launchpad.support.sap.com/#/notes/2729475)
1. Teste seus procedimentos de alta disponibilidade e recuperação de desastres.
   1. Simule situações de failover desligando VMs (sistemas operacionais convidados do Windows) ou colocando sistemas operacionais em modo de pânico (sistemas operacionais convidados Linux). Esta etapa ajudará você a descobrir se suas configurações de failover funcionam conforme projetada.
   1. Meça quanto tempo leva para executar um failover. Se os tempos forem muito longos, considere:
        - Para o SUSE Linux, use dispositivos SBD em vez do agente Azure Fence para acelerar o failover.
        - Para o SAP HANA, se a recarga de dados demorar muito, considere provisionar mais largura de banda de armazenamento.
   3. Teste sua seqüência de backup/restauração e cronometragem e faça correções se precisar. Certifique-se de que os tempos de backup são suficientes. Você também precisa testar as atividades de restauração e restauração de tempo. Certifique-se de que os tempos de restauração estão dentro de suas SLAs RTO onde o RTO depende de um banco de dados ou processo de restauração de VM.
   4. Teste a funcionalidade e a arquitetura de DR entre regiões.
1. Verificações de segurança.
   1. Teste a validade da arquitetura RBAC (Role-Based Access Control, controle de acesso baseado em função do Azure). O objetivo é separar e limitar o acesso e as permissões de diferentes equipes. Por exemplo, os membros da equipe Base SAP devem ser capazes de implantar VMs e atribuir discos do Azure Storage a uma determinada rede virtual Azure. Mas a equipe do SAP Base não deve ser capaz de criar suas próprias redes virtuais ou alterar as configurações das redes virtuais existentes. Os membros da equipe de rede não devem ser capazes de implantar VMs em redes virtuais nas quais o aplicativo SAP e as VMs DBMS estão sendo executados. Os membros desta equipe também não devem ser capazes de alterar atributos de VMs ou mesmo excluir VMs ou discos.  
   1.  Verifique se as regras [do grupo de segurança de rede e as ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) funcionam conforme o esperado e proteja os recursos protegidos.
   1.  Verifique se todos os recursos que precisam ser criptografados foram criptografados. Defina e implemente processos para fazer backup de certificados, armazenar e acessar esses certificados e restaurar as entidades criptografadas.
   1.  Use [a criptografia de disco Do Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) para discos de sO, sempre que possível, do ponto de vista do suporte ao sistema operacional.
   1.  Certifique-se de que você não está usando muitas camadas de criptografia. Em alguns casos, faz sentido usar o Azure Disk Encryption juntamente com um dos métodos de criptografia de dados transparentes do DBMS para proteger diferentes discos ou componentes no mesmo servidor.  Por exemplo, em um servidor SAP DBMS, o Ade (ADE) a Azure Disk Encryption (ADE) pode ser habilitada no disco de inicialização do sistema operacional (se o SISTEMA OPERACIONAL suportar ADE) e aqueles discos de dados não usados pelos arquivos de persistência de dados DBMS.  Um exemplo é usar o ADE no disco que segura as chaves de criptografia DBMS TDE.
1. Teste de desempenho. No SAP, com base no rastreamento e medições sap, faça essas comparações:
   - Quando aplicável, compare os 10 principais relatórios on-line com sua implementação atual.
   - Quando aplicável, compare os 10 melhores trabalhos em lote com a sua implementação atual.
   - Compare as transferências de dados através de interfaces para o sistema SAP. Concentre-se em interfaces onde você sabe que a transferência está indo entre diferentes locais, como de no local para o Azure.


## <a name="non-production-phase"></a>Fase de não produção 
Nesta fase, assumimos que após um piloto bem-sucedido ou prova de conceito (POC), você está começando a implantar sistemas SAP não-produzidos para o Azure. Incorpore tudo o que você aprendeu e experimentou durante a POC para esta implantação. Todos os critérios e etapas listados para POCs também se aplicam a esta implantação.

Durante esta fase, você geralmente implanta sistemas de desenvolvimento, sistemas de teste de unidades e sistemas de teste de regressão de negócios para o Azure. Recomendamos que pelo menos um sistema de não-produção em uma linha de aplicativos SAP tenha a configuração de alta disponibilidade que o futuro sistema de produção terá. Aqui estão algumas etapas adicionais que você precisa concluir durante esta fase:  

1.  Antes de mover sistemas da plataforma antiga para o Azure, colete dados de consumo de recursos, como uso de CPU, throughput de armazenamento e dados IOPS. Especialmente coletar esses dados das unidades de camada DBMS, mas também recolhê-los das unidades de camada de aplicação. Também meça a latência de rede e de armazenamento.
2.  Registre os padrões de tempo de uso de disponibilidade de seus sistemas. O objetivo é descobrir se os sistemas não-produtivos precisam estar disponíveis o dia todo, todos os dias ou se existem sistemas não-produtivos que podem ser desligados durante determinadas fases de uma semana ou mês.
3.  Teste e determine se deseja criar suas próprias imagens do Sistema Operacional para suas VMs no Azure ou se deseja usar uma imagem da Galeria de Imagens Compartilhadas do Azure. Se você estiver usando uma imagem da Galeria de Imagens Compartilhadas, certifique-se de usar uma imagem que reflita o contrato de suporte com o fornecedor do so. Para alguns vendedores de SO, a Galeria de Imagens Compartilhadas permite que você traga suas próprias imagens de licença. Para outras imagens do Sistema Operacional, o suporte está incluído no preço citado pelo Azure. Se você decidir criar suas próprias imagens de sistema operacional, poderá encontrar documentação nestes artigos:
    -   [Construa uma imagem generalizada de um VM do Windows implantado no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    -   [Construa uma imagem generalizada de um VM Linux implantado no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Se você usar imagens SUSE e Red Hat Linux da Galeria de Imagens Compartilhadas, você precisa usar as imagens para SAP fornecidas pelos fornecedores do Linux na Galeria de Imagens Compartilhadas.
4.  Certifique-se de cumprir os requisitos de suporte sap para contratos de suporte da Microsoft. Consulte [a nota de suporte sap #2015553](https://launchpad.support.sap.com/#/notes/2015553). Para hana instâncias grandes, consulte [requisitos de onboarding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements).
4.  Certifique-se de que as pessoas certas recebem [notificações de manutenção planejadas](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) para que você possa escolher os melhores horários de inatividade.
5.  Verifique frequentemente se há apresentações do Azure em canais como [o Canal 9](https://channel9.msdn.com/) para obter novas funcionalidades que possam se aplicar às suas implantações.
6.  Verifique as notas SAP relacionadas ao Azure, como [nota de suporte #1928533,](https://launchpad.support.sap.com/#/notes/1928533)para novas SKUs VM e versões recém-suportadas de OS e DBMS. Compare os preços dos novos tipos de VM com os dos tipos mais antigos de VM, para que você possa implantar VMs com a melhor relação preço/desempenho.
7.  Verifique novamente as notas de suporte sap, o diretório de hardware SAP HANA e o SAP PAM. Certifique-se de que não houve alterações nas VMs suportadas para o Azure, lançamentos de SO suportados nesses VMs e lançamentos SAP e DBMS suportados.
8.  Verifique [no site da SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) as novas SKUs certificadas pela HANA no Azure. Compare os preços das novas SKUs com as que você planejou usar. Eventualmente, faça as alterações necessárias para usar as que possuem a melhor relação preço/desempenho.
9.  Adapte seus scripts de implantação para usar novos tipos de VM e incorpore novos recursos do Azure que você deseja usar.
10. Após a implantação da infra-estrutura, teste e avalie a latência da rede entre VMs de camada de aplicação SAP e VMs DBMS, de acordo com notas de suporte SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avalie os resultados com a orientação de latência da rede na [nota de suporte sap #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A latência da rede deve estar na faixa moderada ou boa. Exceções se aplicam ao tráfego entre vMs e unidades HANA Large Instance, conforme documentado [neste artigo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Certifique-se de que nenhuma das restrições mencionadas em [Considerações para a implantação do Azure Virtual Machines DBMS para cargas de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) e [configurações e operações de infra-estrutura SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) se aplicam à sua implantação.
11. Certifique-se de que suas VMs estão implantadas no grupo correto [de colocação de proximidade do Azure,](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)conforme descrito nos [grupos de colocação de proximidade do Azure para uma latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md).
11. Realize todas as outras verificações listadas para a fase de prova de conceito antes de aplicar a carga de trabalho.
12. À medida que a carga de trabalho se aplica, registre o consumo de recursos dos sistemas no Azure. Compare esse consumo com registros de sua plataforma antiga. Ajuste o dimensionamento de VM de implantações futuras se você ver que você tem grandes diferenças. Tenha em mente que quando você reduzir o tamanho, o armazenamento e as larguras de banda de rede das VMs também serão reduzidas.
    - [Tamanhos para máquinas virtuais windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Tamanhos das máquinas virtuais do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Experimente com funcionalidade e processos de cópia do sistema. O objetivo é facilitar a cópia de um sistema de desenvolvimento ou de um sistema de teste, para que as equipes de projeto possam obter novos sistemas rapidamente. Considere usar [O SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) para essas tarefas.
14. Otimize e aprimore o acesso, permissões e processos baseados em função do Azure da sua equipe para garantir que você tenha separação de deveres. Ao mesmo tempo, certifique-se de que todas as equipes possam executar suas tarefas na infra-estrutura do Azure.
15. Exercite, teste e documente procedimentos de alta disponibilidade e recuperação de desastres para permitir que sua equipe execute essas tarefas. Identifique as deficiências e adapte a nova funcionalidade do Azure que você está integrando em suas implantações.

 
## <a name="production-preparation-phase"></a>Fase de preparação da produção 
Nesta fase, colete o que você experimentou e aprendeu durante suas implantações não-de-produção e aplique-o em futuras implantações de produção. Você também precisa preparar o trabalho da transferência de dados entre sua localização de hospedagem atual e o Azure.

1.  Complete as atualizações necessárias de versão SAP de seus sistemas de produção antes de se mudar para o Azure.
1.  Concordo com os empresários em testes funcionais e de negócios que precisam ser realizados após a migração do sistema de produção.
1.  Certifique-se de que esses testes sejam concluídos com os sistemas de origem no local de hospedagem atual. Evite realizar testes pela primeira vez depois que o sistema for movido para o Azure.
1.  Teste o processo de migração de sistemas de produção para o Azure. Se você não estiver movendo todos os sistemas de produção para o Azure durante o mesmo período de tempo, construa grupos de sistemas de produção que precisam estar no mesmo local de hospedagem. Testar a migração de dados. Aqui estão alguns métodos comuns:
    - Use métodos DBMS como backup/restauração em combinação com o SQL Server Always On, HANA System Replication ou Log shipping to seed and synchronsynchron database content in Azure.
    - Use backup/restauração para bancos de dados menores.
    - Use o SAP Migration Monitor, integrado ao SAP SWPM, para realizar migrações heterogêneas.
    - Use o processo [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) se você precisar combinar sua migração com uma atualização de versão SAP. Tenha em mente que nem todas as combinações de DBMS de origem e DBMS de destino são suportadas. Você pode encontrar mais informações nas notas de suporte SAP específicas para as diferentes versões do DMO. Por exemplo, [Opção de Migração de Banco de Dados (DMO) da Soma 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872).
    - Teste se o throughput de transferência de dados é melhor através da internet ou através do ExpressRoute, caso você precise mover backups ou arquivos de exportação SAP. Se você estiver movendo dados através da internet, talvez seja necessário alterar algumas das regras do grupo de segurança de segurança de rede/grupo de segurança de aplicativos que você precisará ter em vigor para futuros sistemas de produção.
1.  Antes de mover sistemas de sua plataforma antiga para o Azure, colete dados de consumo de recursos. Os dados úteis incluem uso da CPU, throughput de armazenamento e dados IOPS. Especialmente coletar esses dados das unidades de camada DBMS, mas também recolhê-los das unidades de camada de aplicação. Também meça a latência de rede e de armazenamento.
1.  Verifique novamente as notas de suporte sap e as configurações necessárias do sistema operacional, o diretório de hardware SAP HANA e o SAP PAM. Certifique-se de que não houve alterações nas VMs suportadas para o Azure, lançamentos de SO suportados nessas VMs e lançamentos SAP e DBMS suportados.
1.  Atualize os scripts de implantação para levar em conta as últimas decisões que você tomou sobre tipos de VM e funcionalidade do Azure.
1.  Depois de implantar infra-estrutura e aplicativos, valide que:
    - Os tipos corretos de VM foram implantados, com os atributos corretos e tamanhos de armazenamento.
    - As VMs estão nas versões e patches corretos e desejados do SO e são uniformes.
    - As VMs são endurecidas conforme necessário e de forma uniforme.
    - As versões e patches corretos do aplicativo foram instalados e implantados.
    - As VMs foram implantadas em conjuntos de disponibilidade do Azure como planejado.
    - O Azure Premium Storage é usado para discos sensíveis à latência ou onde o [SLA único-VM de 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) é necessário.
    - O Azure Write Accelerator é implantado corretamente.
        - Certifique-se de que, dentro das VMs, espaços de armazenamento ou conjuntos de listras foram construídos corretamente em discos que precisam do Write Accelerator.
        - Verifique a [configuração do software RAID no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid).
        - Verifique a [configuração de LVM em VMs Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm).
    - [Os discos gerenciados do Azure](https://azure.microsoft.com/services/managed-disks/) são usados exclusivamente.
    - As VMs foram implantadas nos conjuntos de disponibilidade e zonas de disponibilidade corretas.
    - [O Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está habilitado nas VMs usadas na camada de aplicativo SAP e na camada SAP DBMS.
    - Nenhum [aparelho virtual de rede Azure](https://azure.microsoft.com/solutions/network-appliances/) está no caminho de comunicação entre o aplicativo SAP e a camada DBMS de sistemas SAP baseados em SAP NetWeaver, Hybris ou S/4HANA.
    - As regras do grupo de segurança de aplicativos e do grupo de segurança da rede permitem a comunicação conforme desejado e planejado e bloqueiam a comunicação quando necessário.
    - As configurações de tempo estão definidas corretamente, como descrito anteriormente.
    - As VMs são implantadas no grupo correto [de colocação de proximidade do Azure,](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)conforme descrito nos [grupos de colocação de proximidade do Azure para uma latência de rede ideal com aplicativos SAP](sap-proximity-placement-scenarios.md).
    - A latência de rede entre VMs de camada de aplicação SAP e VMs DBMS é testada e validada conforme descrito nas notas de suporte sap [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Avalie os resultados com a orientação de latência da rede na [nota de suporte sap #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A latência da rede deve estar na faixa moderada ou boa. Exceções se aplicam ao tráfego entre vMs e unidades HANA Large Instance, conforme documentado [neste artigo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance).
    - A criptografia foi implementada quando necessário e com o método de criptografia adequado.
    - Interfaces e outros aplicativos podem conectar a infra-estrutura recém-implantada.
1.  Crie um manual para reagir à manutenção planejada do Azure. Determine a ordem em que sistemas e VMs devem ser reiniciados para manutenção planejada.
    

## <a name="go-live-phase"></a>Fase go-live
Durante a fase go-live, certifique-se de seguir os livros de jogos que você desenvolveu durante as fases anteriores. Execute as etapas que você testou e praticou. Não aceite mudanças de última hora nas configurações e processos. Também complete estas etapas:

1. Verifique se o monitoramento do portal do Azure e outras ferramentas de monitoramento estão funcionando. Recomendamos o Windows Performance Monitor (perfmon) para Windows e SAR para Linux.
    - Contadores de CPU.
        - Tempo médio da CPU, total (todas as CPUs)
        - Tempo médio da CPU, cada processador individual (128 processadores em VMs M128)
        - Tempo do kernel da CPU, cada processador individual
        - Tempo do usuário da CPU, cada processador individual
    - Memória.
        - Memória livre
        - Página de memória em/segundo
        - Página de memória fora/segundo
    - Disk.
        - Leitura de disco em KBps, por disco individual
        - Leituras de disco/segundo, por disco individual
        - Leitura de disco em microssegundos/leitura, por disco individual
        - Gravação de disco em KBps, por disco individual
        - Gravação de disco/segundo, por disco individual
        - Gravação de disco em microssegundos/leitura, por disco individual
    - Network.
        - Pacotes de rede em/segundo
        - Pacotes de rede fora/segundo
        - Rede KB em/segundo
        - Rede KB out/second
1.  Após a migração de dados, realize todos os testes de validação acordados com os proprietários de empresas. Aceite os resultados do teste de validação somente quando tiver resultados para os sistemas de origem originais.
1.  Verifique se as interfaces estão funcionando e se outros aplicativos podem se comunicar com os sistemas de produção recém-implantados.
1.  Verifique o sistema de transporte e correção através de STMS transação SAP.
1.  Realize backups de banco de dados depois que o sistema for liberado para produção.
1.  Execute backups em VM para as VMs de camada de aplicativo SAP depois que o sistema for liberado para produção.
1.  Para sistemas SAP que não fizeram parte da fase atual do go-live, mas que se comunicam com os sistemas SAP que você moveu para o Azure durante esta fase go-live, você precisa redefinir o buffer de nome de host no SM51. Isso removerá os antigos endereços IP armazenados em cache associados aos nomes das instâncias de aplicativo que você moveu para o Azure.  


## <a name="post-production"></a>Após a produção
Esta fase é sobre monitoramento, operação e administração do sistema. Do ponto de vista SAP, as tarefas usuais que você foi obrigado a completar em seu antigo local de hospedagem se aplicam. Complete essas tarefas específicas do Azure também:

1. Revise as faturas do Azure para sistemas de alto carregamento.
2. Otimize a eficiência de preço/desempenho no lado VM e no lado de armazenamento.
3. Otimize os tempos em que você pode desligar os sistemas.  


## <a name="next-steps"></a>Próximas etapas
 Consulte estes artigos:

- [Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Implantação de Máquinas Virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Considerações para a implantação do Azure Virtual Machines DBMS para cargas de trabalho SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

