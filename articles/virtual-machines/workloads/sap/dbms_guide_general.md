---
title: Considerações para Implantação do DBMS de Máquinas de Virtuais do Azure para carga de trabalho do SAP | Microsoft Docs
description: Considerações para Implantação do DBMS de Máquinas de Virtuais do Azure para carga de trabalho do SAP
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
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a852ddc68a6f51e677e5ff2e641ada25f4bf0105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101357"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Considerações para Implantação do DBMS de Máquinas de Virtuais do Azure para carga de trabalho do SAP
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Este guia faz parte da documentação sobre como implementar e implantar software SAP no Microsoft Azure. Antes de ler este guia, leia o [guia de planejamento e implementação][planning-guide]. Este documento abrange os aspectos genéricos de implantação dos sistemas DBMS relacionados ao SAP em máquinas virtuais (VMs) do Microsoft Azure usando os recursos de infra-estrutura do Azure como serviço (IaaS).

O papel complementa a documentação de instalação SAP e notas SAP, que representam os recursos primários para instalações e implantações de software SAP em determinadas plataformas.

Neste documento, são introduzidas considerações sobre a execução de sistemas DBMS relacionados ao SAP em VMs do Azure. Há algumas referências a sistemas DBMS específicos neste capítulo. Em vez disso, os sistemas DBMS específicos são tratados dentro deste artigo, após este documento.

## <a name="definitions"></a>Definições
Ao longo do documento, estes termos são utilizados:

* **IaaS**: Infraestrutura como serviço.
* **PaaS**: Plataforma como serviço.
* **SaaS**: Software como serviço.
* **Componente SAP**: Um aplicativo SAP individual, como ERP Central Component (ECC), Business Warehouse (BW), Solution Manager ou Enterprise Portal (EP). Os componentes SAP podem ser baseados em tecnologias TRADICIONAIS ABAP ou Java ou em um aplicativo não baseado no NetWeaver, como Objetos de Negócios.
* **Ambiente SAP**: Um ou mais componentes SAP logicamente agrupados para executar uma função de negócios, como desenvolvimento, garantia de qualidade, treinamento, recuperação de desastres ou produção.
* **Cenário SAP**: Este termo refere-se a todos os ativos SAP no cenário de TI de um cliente. A paisagem SAP inclui todos os ambientes de produção e não produção.
* **Sistema SAP**: A combinação de uma camada DBMS e uma camada de aplicação de, por exemplo, um sistema de desenvolvimento SAP ERP, um sistema de teste SAP Business Warehouse ou um sistema de produção de CRM SAP. Nas implantações do Azure, a divisão dessas duas camadas entre o local e o Azure não é suportada. Como resultado, um sistema SAP é implantado no local ou é implantado no Azure. Você pode implantar os diferentes sistemas de uma paisagem SAP no Azure ou no local. Por exemplo, você pode implantar os sistemas de desenvolvimento e teste de CRM SAP no Azure, mas implantar o sistema de produção de CRM SAP no local.
* **Cross-premises**: Descreve um cenário onde as VMs são implantadas em uma assinatura do Azure que tem conectividade site-to-site, multisite ou Azure ExpressRoute entre os data centers locais e o Azure. Na documentação comum do Azure, esses tipos de implantações também são descritas como cenários entre instalações. 

    O motivo para a conexão é estender domínios locais, Active Directory local e DNS local para o Azure. A estrutura local é estendida para os ativos do Azure da assinatura. Com essa extensão, as VMs podem fazer parte do domínio local. Os usuários de domínio do domínio on-premises podem acessar os servidores e executar serviços nessas VMs, como serviços DBMS. A comunicação e resolução de nomes entre VMs implantadas de forma local e VMs implantadas no Azure são possíveis. Este cenário é o cenário mais comum em uso para implantar ativos SAP no Azure. Para obter mais informações, consulte [Planejamento e design para gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> As implantações entre premissas dos sistemas SAP são onde as máquinas virtuais Azure que executam sistemas SAP são membros de um domínio local e são suportadas para sistemas SAP de produção. Configurações entre locais têm suporte para a implantação de partes ou estruturas da SAP completas no Azure. Mesmo executando o cenário SAP completo no Azure requer que essas VMs sejam parte de um domínio local e Active Directory/LDAP. 
>
> Nas versões anteriores da documentação, foram mencionados cenários híbridos-TI. O termo *híbrido* está enraizado no fato de que há uma conectividade entre o local e o Azure. Neste caso, híbrido também significa que as VMs no Azure fazem parte do Active Directory no local.
>
>

Algumas documentações da Microsoft descrevem cenários entre premissas um pouco diferente, especialmente para configurações de alta disponibilidade do DBMS. No caso dos documentos relacionados ao SAP, o cenário de cross-premises se resume à conectividade local-para-local ou privada [expressRoute](https://azure.microsoft.com/services/expressroute/) e a uma paisagem SAP distribuída entre o local e o Azure.

## <a name="resources"></a>Recursos
Existem outros artigos disponíveis sobre a carga de trabalho SAP no Azure. Comece com [a carga de trabalho SAP no Azure: Comece](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) e escolha sua área de interesse.

As seguintes Notas SAP estão relacionadas ao SAP no Azure em relação à área abrangida neste documento.

| Número da observação | Title |
| --- | --- |
| [1928533] |Aplicações SAP no Azure: produtos suportados e tipos de VM do Azure |
| [2015553] |SAP no Microsoft Azure: pré-requisitos de suporte |
| [1999351] |Solução de problemas de monitoramento aprimorado do Azure para SAP |
| [2178632] |Métricas-chave de monitoramento para SAP no Microsoft Azure |
| [1409604] |Virtualização no Windows: monitoramento aprimorado |
| [2191498] |SAP no Linux com Azure: Monitoramento aprimorado |
| [2039619] |Aplicativos SAP no Microsoft Azure usando o banco de dados Oracle: produtos e versões suportados |
| [2233094] |DB6: Aplicativos SAP no Azure usando O IBM DB2 para Linux, UNIX e Windows: Informações adicionais |
| [2243692] |Linux na VM do Microsoft Azure (IaaS): problemas de licença SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: notas de instalação |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalação e atualização |
| [2069760] |Atualização e instalação do SAP do Oracle Linux 7.x |
| [1597355] |Recomendação de troca de espaço para Linux |
| [2171857] |Banco de dados Oracle 12c: Suporte ao sistema de arquivos no Linux |
| [1114181] |Banco de dados Oracle 11g: suporte ao sistema de arquivos no Linux |


Para obter informações sobre todas as Notas SAP para Linux, consulte a wiki da [comunidade SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Você precisa de um conhecimento de trabalho da arquitetura Microsoft Azure e de como as máquinas virtuais do Microsoft Azure são implantadas e operadas. Para obter mais informações, consulte [a documentação do Azure](https://docs.microsoft.com/azure/).

Em geral, a instalação e configuração do Windows, Linux e DBMS são essencialmente as mesmas que qualquer máquina virtual ou máquina de metal nu que você instala no local. Existem algumas decisões de implementação de arquitetura e gerenciamento de sistemas que são diferentes quando você usa o Azure IaaS. Este documento explica as diferenças específicas de gestão arquitetônica e de sistemas a serem preparadas para quando você usa o Azure IaaS.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Estrutura de armazenamento de uma VM para implantações RDBMS
Para acompanhar este capítulo, leia e entenda as informações apresentadas [neste capítulo][deployment-guide-3] do Guia de [Implantação][deployment-guide]. Você precisa entender e saber sobre as diferentes Séries VM e as diferenças entre o armazenamento padrão e premium antes de ler este capítulo. 

Para saber mais sobre o Azure Storage for Azure VMs, consulte:

- [Introdução aos discos gerenciados para VMs do Windows Azure](../../windows/managed-disks-overview.md).
- [Introdução a discos gerenciados para VMs Azure Linux](../../linux/managed-disks-overview.md).

Em uma configuração básica, geralmente recomendamos uma estrutura de implantação onde o sistema operacional, o DBMS e eventuais binários SAP são separados dos arquivos do banco de dados. Recomendamos que os sistemas SAP que são executados em máquinas virtuais Azure tenham o VHD base, ou disco, instalado com o sistema operacional, executáveis do sistema de gerenciamento de banco de dados e executáveis SAP. 

Os dados DBMS e os arquivos de registro são armazenados em armazenamento padrão ou armazenamento premium. Eles são armazenados em discos separados e anexados como discos lógicos à VM de imagem do sistema operacional Original Azure. Para implantações linux, diferentes recomendações são documentadas, especialmente para SAP HANA.

Ao planejar o layout do disco, encontre o melhor equilíbrio entre esses itens:

* O número de arquivos de dados.
* O número de discos que contêm os arquivos.
* As cotas IOPS de um único disco.
* A taxa de transferência de dados por disco.
* O número de discos de dados adicionais possíveis por tamanho da VM.
* A taxa de transferência geral do armazenamento que uma VM pode fornecer.
* A latência que diferentes tipos de Armazenamento do Azure podem fornecer.
* VM SLAs.

O Azure impõe uma cota de IOPS por disco de dados. Essas cotas são diferentes para discos hospedados no armazenamento padrão e no armazenamento premium. Latência de e/s também é diferente entre os dois tipos de armazenamento. O armazenamento premium oferece melhor latência de I/O. 

Cada um dos diferentes tipos de VM tem um número limitado de discos de dados que você pode anexar. Outra restrição é que apenas certos tipos de VM podem usar armazenamento premium. Normalmente, você decide usar um certo tipo de VM com base nos requisitos de CPU e memória. Você também pode considerar os requisitos de iOPS, latência e throughput de disco que geralmente são dimensionados com o número de discos ou o tipo de discos de armazenamento premium. O número de IOPS e o throughput a ser alcançado por cada disco podem ditar o tamanho do disco, especialmente com o armazenamento premium.

> [!NOTE]
> Para implantações dbms, recomendamos o uso de armazenamento premium para quaisquer arquivos de dados, log de transações ou refazer. Não importa se você quer implantar sistemas de produção ou não de produção.

> [!NOTE]
> Para se beneficiar do Exclusivo [SLA VM único](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)do Azure, todos os discos conectados devem ser do tipo de armazenamento premium, que inclui o VHD base.

> [!NOTE]
> A hospedagem de arquivos principais de banco de dados, como dados e arquivos de registro, de bancos de dados SAP em hardware de armazenamento localizado em data centers de terceiros co-localizados adjacentes aos data centers do Azure não é suportada. Para cargas de trabalho SAP, apenas o armazenamento representado como serviço nativo do Azure é suportado para os arquivos de registro de dados e transações de bancos de dados SAP.

A colocação dos arquivos do banco de dados e dos arquivos de log e redo e o tipo de Armazenamento Azure que você usa é definido pelos requisitos de IOPS, latência e throughput. Para ter IOPS suficiente, você pode ser forçado a usar vários discos ou usar um disco de armazenamento premium maior. Se você usar vários discos, construa uma faixa de software nos discos que contenham os arquivos de dados ou os arquivos log e refazer. Nesses casos, os SLAs de throughput de disco e o SLAs de throughput de disco dos discos de armazenamento premium subjacentes ou o IOPS máximo alcançável dos discos de armazenamento padrão são acumulativos para o conjunto de listras resultante.

Como já dito, se o seu requisito de IOPS exceder o que um único VHD pode fornecer, equilibre o número de IOPS necessários para os arquivos de banco de dados em um número de VHDs. A maneira mais fácil de distribuir a carga IOPS entre os discos é construir uma faixa de software sobre os diferentes discos. Em seguida, coloque uma série de arquivos de dados do SAP DBMS nas LUNs esculpidas na faixa de software. O número de discos na faixa é impulsionado por demandas de IOPs, demandas de throughput de disco e demandas de volume.


---
> ![Windows][Logo_Windows] Windows
>
> Recomendamos que você use o Windows Storage Spaces para criar conjuntos de listras em vários VHDs do Azure. Use pelo menos o Windows Server 2012 R2 ou o Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Apenas o MDADM e o Logical Volume Manager (LVM) são suportados para construir um RAID de software no Linux. Para obter mais informações, consulte:
>
> - [Configurar o software RAID no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) usando o MDADM
> - [Configurar o LVM em uma VM Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) usando LVM
>
>

---

> [!NOTE]
> Como o Azure Storage mantém três imagens dos VHDs, não faz sentido configurar uma redundância quando você listra. Você só precisa configurar striping para que os I/Os sejam distribuídos nos diferentes VHDs.
>

### <a name="managed-or-nonmanaged-disks"></a>Discos gerenciados ou não gerenciados
Uma conta de armazenamento Azure é uma construção administrativa e também um assunto de limitações. As limitações diferem entre contas de armazenamento padrão e contas de armazenamento premium. Para obter informações sobre recursos e limitações, consulte [as metas de escalabilidade e desempenho do Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

Para armazenamento padrão, lembre-se de que há um limite no IOPS por conta de armazenamento. Consulte a linha que contém **taxa de solicitação total** no artigo [Aescalabilidade de armazenamento e metas de desempenho do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). Há também um limite inicial no número de contas de armazenamento por assinatura do Azure. Equilibrar VHDs para o cenário SAP maior em diferentes contas de armazenamento para evitar atingir os limites dessas contas de armazenamento. Este é um trabalho tedioso quando você está falando de algumas centenas de máquinas virtuais com mais de mil VHDs.

Como o uso do armazenamento padrão para implantações DBMS em conjunto com uma carga de trabalho SAP não é recomendado, as referências e recomendações para o armazenamento padrão estão limitadas a este [pequeno artigo](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Para evitar o trabalho administrativo de planejamento e implantação de VHDs em diferentes contas de armazenamento do Azure, a Microsoft introduziu o [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) em 2017. Discos gerenciados estão disponíveis para armazenamento padrão e armazenamento premium. As principais vantagens dos discos gerenciados em comparação com discos não gerenciados são:

- Para discos gerenciados, o Azure distribui os diferentes VHDs em diferentes contas de armazenamento automaticamente no momento da implantação. Desta forma, os limites da conta de armazenamento para volume de dados, throughput de I/O e IOPS não são atingidos.
- Usando discos gerenciados, o Azure Storage honra os conceitos de conjuntos de disponibilidade do Azure. Se a VM fizer parte de um conjunto de disponibilidade do Azure, o VHD base e o disco conectado de uma VM serão implantados em diferentes domínios de falha e atualização.


> [!IMPORTANT]
> Dadas as vantagens dos Discos Gerenciados do Azure, recomendamos que você use discos gerenciados do Azure para suas implantações DBMS e Implantações SAP em geral.
>

Para converter de discos não gerenciados para discos gerenciados, consulte:

- [Converta uma máquina virtual do Windows de discos não gerenciados em discos gerenciados](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Converta uma máquina virtual Linux de discos não gerenciados para discos gerenciados](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Cache de VMs e discos de dados
Ao montar discos em VMs, você pode escolher se o tráfego de I/O entre a VM e os discos localizados no armazenamento do Azure é armazenado em cache. O armazenamento padrão e premium usa duas tecnologias diferentes para este tipo de cache.

As seguintes recomendações assumem essas características de I/O para DBMS padrão:

- É principalmente uma carga de trabalho de leitura contra arquivos de dados de um banco de dados. Essas leituras são críticas para o sistema DBMS.
- A gravação contra os arquivos de dados ocorre em rajadas com base em pontos de verificação ou em um fluxo constante. Em média ao longo de um dia, há menos escritos do que leituras. Em oposição às leituras dos arquivos de dados, essas gravações são assíncronas e não sustentam nenhuma transação de usuário.
- Praticamente, não há nenhuma leitura dos arquivos de refazer ou log de transação. Exceções são grandes I/Os quando você executa backups de log de transação.
- A carga principal contra arquivos de log de transação ou refazer é a gravação. Dependendo da natureza da carga de trabalho, você pode ter I/Os tão pequenos quanto 4 KB ou, em outros casos, tamanhos de I/O de 1 MB ou mais.
- Todas as gravações devem ser persistidas no disco de forma confiável.

Para armazenamento padrão, os possíveis tipos de cache são:

* Nenhum
* Ler
* Leitura/Gravação

Para obter um desempenho consistente e determinista, defina o cache no armazenamento padrão para todos os discos que contenham arquivos de dados relacionados ao DBMS, arquivos de log e redo e espaço de tabela para **NONE**. O caching de base VHD pode permanecer com o padrão.

Para armazenamento premium, existem as seguintes opções de cache:

* Nenhum
* Ler
* Leitura/gravação
* Nenhum + Write Accelerator, que é apenas para VMs Da Série M do Azure
* Leia + Write Accelerator, que é apenas para VMs Da Série M do Azure

Para armazenamento premium, recomendamos que você use **'Gemching' para arquivos** de dados do banco de dados SAP e escolha **Não fazer cache para os discos de arquivos log.**

Para implantações da Série M, recomendamos que você use o Azure Write Accelerator para sua implantação de DBMS. Para obter detalhes, restrições e implantação do Azure Write Accelerator, consulte [Ativar o acelerador de gravação](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Discos não persistentes do Azure
As VMs do Azure oferecem discos não persistentes depois que uma VM é implantada. No caso de uma reinicialização em VM, todo o conteúdo nessas unidades é apagado. É dado que arquivos de dados e arquivos de registro e refazer de bancos de dados não devem, nenhuma circunstância, ser localizados nessas unidades não persistidas. Pode haver exceções para alguns bancos de dados, onde essas unidades não persistidas podem ser adequadas para espaços de tabela sem temperatura e temperatura. Evite usar essas unidades para VMs da série A porque essas unidades não persistidas são limitadas em throughput com essa família VM. 

Para obter mais informações, consulte [Entenda a unidade temporária em VMs windows no Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

---
> ![Windows][Logo_Windows] Windows
>
> A unidade D em um Azure VM é uma unidade não persistida, que é apoiada por alguns discos locais no nó de computação do Azure. Por não persistir, todas as alterações feitas no conteúdo da unidade D são perdidas quando a VM é reiniciada. As alterações incluem arquivos armazenados, diretórios criados e aplicativos que foram instalados.
>
> ![Linux][Logo_Linux] Linux
>
> As VMs do Linux Azure montam automaticamente uma unidade em /mnt/resource que é uma unidade não persistida apoiada por discos locais no nó de computação do Azure. Por não persistir, todas as alterações feitas no conteúdo em /mnt/resource são perdidas quando a VM é reiniciada. As alterações incluem arquivos armazenados, diretórios criados e aplicativos que foram instalados.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Resiliência do Armazenamento do Microsoft Azure
O Microsoft Azure Storage armazena o VHD base, com sO e discos ou blobs conectados, em pelo menos três nós de armazenamento separados. Esse tipo de armazenamento é chamado de armazenamento localmente redundante (LRS). LRS é o padrão para todos os tipos de armazenamento no Azure.

Existem outros métodos de redundância. Para obter mais informações, consulte [a replicação do Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>O armazenamento premium é o tipo recomendado de armazenamento para VMs e discos DBMS que armazenam banco de dados e registram e refazem arquivos. O único método de redundância disponível para armazenamento premium é o LRS. Como resultado, você precisa configurar métodos de banco de dados para permitir a replicação de dados de banco de dados em outra região ou zona de disponibilidade do Azure. Os métodos de banco de dados incluem SQL Server Always On, Oracle Data Guard e HANA System Replication.


> [!NOTE]
> Para implantações DBMS, o uso de GRS (Geo-redundante Storage, armazenamento geo-redundante) não é recomendado para armazenamento padrão. O GRS afeta severamente o desempenho e não honra a ordem de gravação em diferentes VHDs que são anexados a uma VM. Não honrar a ordem de gravação em diferentes VHDs potencialmente leva a bancos de dados inconsistentes no lado do destino de replicação. Essa situação ocorre se os arquivos de banco de dados e log e redo estiverem espalhados por vários VHDs, como geralmente é o caso, no lado VM de origem.



## <a name="vm-node-resiliency"></a>Resiliência de nó de VM
O Azure oferece vários SLAs diferentes para VMs. Para obter mais informações, consulte o lançamento mais recente do [SLA para Máquinas Virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Como a camada DBMS geralmente é fundamental para a disponibilidade em um sistema SAP, você precisa entender conjuntos de disponibilidade, zonas de disponibilidade e eventos de manutenção. Para obter mais informações sobre esses conceitos, consulte [Gerenciar a disponibilidade de máquinas virtuais windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) e gerenciar a disponibilidade de [máquinas virtuais Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

A recomendação mínima para cenários DBMS de produção com uma carga de trabalho SAP é:

- Implante duas VMs em um conjunto de disponibilidade separado na mesma região do Azure.
- Execute essas duas VMs na mesma rede virtual Do Azure e tenha NICs anexados das mesmas sub-redes.
- Use os métodos de banco de dados para manter um modo de espera ativo com a segunda VM. Métodos podem ser SQL Server Always On, o Oracle Data Guard ou HANA System Replication.

Você também pode implantar uma terceira VM em outra região do Azure e usar os mesmos métodos de banco de dados para fornecer uma réplica assíncrona em outra região do Azure.

Para obter informações sobre como configurar conjuntos de disponibilidade do Azure, consulte [este tutorial](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Considerações da rede Azure
Em implantações SAP em larga escala, use o projeto do [Azure Virtual Datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter). Use-o para sua configuração de rede virtual e permissões e atribuições de função para diferentes partes da sua organização.

Essas práticas recomendadas são o resultado de centenas de implantações de clientes:

- As redes virtuais em que o aplicativo SAP é implantado não têm acesso à internet.
- As VMs do banco de dados são executadas na mesma rede virtual que a camada do aplicativo.
- As VMs dentro da rede virtual têm uma alocação estática do endereço IP privado. Para obter mais informações, consulte [os tipos de endereço IP e os métodos de alocação no Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).
- As restrições de roteamento de e para as VMs DBMS *não* são definidas com firewalls instalados nas VMs DBMS locais. Em vez disso, o roteamento de tráfego é definido com [os NSGs (Network Security Groups, grupos de segurança da rede).](https://docs.microsoft.com/azure/virtual-network/security-overview)
- Para separar e isolar o tráfego para o VM DBMS, atribua diferentes NICs à VM. Cada NIC obtém um endereço IP diferente, e cada NIC é atribuído a uma sub-rede virtual diferente. Cada sub-rede tem regras diferentes do NSG. O isolamento ou separação do tráfego de rede é uma medida para o roteamento. Não é usado para definir cotas para o throughput de rede.

> [!NOTE]
> Atribuir endereços IP estáticos através do Azure significa atribuí-los a NICs virtuais individuais. Não atribua endereços IP estáticos dentro do sistema operacional convidado a uma NIC virtual. Alguns serviços do Azure, como o Azure Backup, dependem do fato de que pelo menos a NIC virtual principal está definida como DHCP e não para endereços IP estáticos. Para obter mais informações, consulte [Backup de máquina virtual Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Para atribuir vários endereços IP estáticos a uma VM, atribua várias NICs virtuais a uma VM.
>


> [!IMPORTANT]
> A configuração de [dispositivos virtuais](https://azure.microsoft.com/solutions/network-appliances/) de rede no caminho de comunicação entre o aplicativo SAP e a camada DBMS de um sistema SAP NetWeaver-, Hybris-ou S/4HANA não é suportada. Essa restrição é por razões de funcionalidade e desempenho. O caminho de comunicação entre a camada de aplicação SAP e a camada DBMS deve ser direto. A restrição não inclui [as regras do grupo de segurança de aplicativos (ASG) e do NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) se essas regras ASG e NSG permitirem um caminho de comunicação direta. 
>
> Outros cenários em que os dispositivos virtuais de rede não são suportados estão em:
>
> * Caminhos de comunicação entre VMs Azure que representam nós de cluster Do Marcapasso Linux e dispositivos SBD conforme descrito em [alta disponibilidade para SAP NetWeaver no Azure VMs no SUSE Linux Enterprise Server para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> * Caminhos de comunicação entre o Azure VMs e o SOFS (Windows Server Scale-Out File Server, servidor de arquivos de escala do Windows) configurados conforme descrito no [Cluster uma instância SAP ASCS/SCS em um cluster de failover do Windows usando um compartilhamento de arquivos no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Os aparelhos virtuais de rede em vias de comunicação podem facilmente dobrar a latência da rede entre dois parceiros de comunicação. Eles também podem restringir o throughput em caminhos críticos entre a camada de aplicativo SAP e a camada DBMS. Em alguns cenários de clientes, os dispositivos virtuais de rede podem fazer com que os clusters Pacemaker Linux falhem. Estes são os casos em que as comunicações entre os nós de cluster do Linux Pacemaker se comunicam com seu dispositivo SBD através de um dispositivo virtual de rede.
>

> [!IMPORTANT]
> Outro design que *não* é suportado é a segregação da camada de aplicativo SAP e da camada DBMS em diferentes redes virtuais Do Zure que não são [acompanhadas umas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) das outras. Recomendamos que você separe a camada de aplicativo SAP e a camada DBMS usando sub-redes dentro de uma rede virtual Azure em vez de usar diferentes redes virtuais do Azure. 
>
> Se você decidir não seguir a recomendação e, em vez disso, segregar as duas camadas em diferentes redes virtuais, as duas redes virtuais devem ser [espiadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Esteja ciente de que o tráfego de rede entre duas redes virtuais Azure [peered](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) está sujeito a custos de transferência. Um enorme volume de dados que consiste em muitos terabytes é trocado entre a camada de aplicativo SAP e a camada DBMS. Você pode acumular custos substanciais se a camada de aplicativo SAP e a camada DBMS forem segregadas entre duas redes virtuais Azure peered.

Use duas VMs para sua implantação de DBMS de produção dentro de um conjunto de disponibilidade do Azure. Use também roteamento separado para a camada de aplicativo SAP e o tráfego de gerenciamento e operações para as duas VMs DBMS. Veja a seguinte imagem:

![Diagrama de duas VMs em duas sub-redes](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Use o Balanceador de carga do Azure para redirecionar o tráfego
O uso de endereços IP virtuais privados usados em funcionalidades como SQL Server Always On ou HANA System Replication requer a configuração de um balanceador de carga Do Zure. O balanceador de carga usa portas de teste para determinar o nó DBMS ativo e direcionar o tráfego exclusivamente para esse nó de banco de dados ativo. 

Se houver um failover do nó do banco de dados, não há necessidade de o aplicativo SAP reconfigurar. Em vez disso, as arquiteturas de aplicativos SAP mais comuns se reconectam contra o endereço IP virtual privado. Enquanto isso, o balanceador de carga reage ao failover do nó redirecionando o tráfego contra o endereço IP virtual privado para o segundo nó.

O Azure oferece duas [SKUs balanceador de carga diferentes:](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)um SKU básico e um SKU padrão. A menos que você queira implantar em zonas de disponibilidade do Azure, o Balanceador de carga básico SKU se sai bem.

O tráfego entre as VMs DBMS e a camada de aplicativo SAP é sempre roteado através do balanceador de carga o tempo todo? A resposta depende de como você configura o balanceador de carga. 

Neste momento, o tráfego de entrada para o VM DBMS é sempre roteado através do balanceador de carga. A rota de tráfego de saída do VM DBMS para a vm de camada de aplicativo depende da configuração do balanceador de carga. 

O balanceador de carga oferece uma opção de DirectServerReturn. Se essa opção estiver configurada, o tráfego direcionado da VM DBMS para a camada de aplicativo SAP *não* será roteado através do balanceador de carga. Em vez disso, ele vai diretamente para a camada de aplicação. Se o DirectServerReturn não estiver configurado, o tráfego de retorno para a camada de aplicativo SAP será roteado através do balanceador de carga.

Recomendamos que você configure directServerReturn em combinação com balanceadores de carga posicionados entre a camada de aplicativo SAP e a camada DBMS. Essa configuração reduz a latência da rede entre as duas camadas.

Para um exemplo de como configurar essa configuração com o SQL Server Always On, consulte [Configurar um ouvinte ILB para grupos de disponibilidade Always On no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Se você usar modelos GitHub JSON publicados como referência para suas implantações de infra-estrutura SAP no Azure, estude este [modelo para um sistema SAP 3-Tier](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). Neste modelo, você também pode ver as configurações corretas para o balanceador de carga.

### <a name="azure-accelerated-networking"></a>Rede Acelerada do Azure
Para reduzir ainda mais a latência da rede entre as VMs do Azure, recomendamos que você escolha [a Rede Acelerada Do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Use-o quando implantar VMs do Azure para uma carga de trabalho SAP, especialmente para a camada de aplicativo SAP e a camada SAP DBMS.

> [!NOTE]
> Nem todos os tipos de VM suportam rede acelerada. O artigo anterior lista os tipos de VM que suportam rede acelerada.
>

---
> ![Windows][Logo_Windows] Windows
>
> Para aprender como implantar VMs com rede acelerada para Windows, consulte [Criar uma máquina virtual do Windows com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> Para obter mais informações sobre a distribuição Linux, consulte [Criar uma máquina virtual Linux com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

---

> [!NOTE]
> No caso do SUSE, Red Hat e Oracle Linux, o serviço de Rede Acelerada de mensagens é compatível com versões recentes. Versões mais antigas como SLES 12 SP2 ou RHEL 7.2 não suportam rede acelerada do Azure.
>


## <a name="deployment-of-host-monitoring"></a>Implantação do monitoramento de host
Para o uso de produção de aplicativos SAP em máquinas virtuais Azure, o SAP requer a capacidade de obter dados de monitoramento de host dos hosts físicos que executam as máquinas virtuais do Azure. É necessário um nível de patch do Agente de Host SAP específico que habilite essa funcionalidade no SAPOSCOL e no Agente de Host SAP. O nível de patch exato está documentado na Nota SAP [1409604].

Para obter mais informações sobre a implantação de componentes que fornecem dados host para saposcol e SAP Host Agent e o gerenciamento do ciclo de vida desses componentes, consulte o [guia de implantação][deployment-guide].


## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre um DBMS específico, consulte:

- [Implantação do DBMS de Máquinas de Virtuais do SQL Server Azure para carga de trabalho do SAP](dbms_guide_sqlserver.md)
- [Implantação do DBMS de Máquinas Virtuais do Oracle Azure para carga de trabalho do SAP](dbms_guide_oracle.md)
- [Implantação do DBMS de Máquinas Virtuais do IBM DB2 Azure para carga de trabalho do SAP](dbms_guide_ibm.md)
- [Implantação do DBMS de Máquinas Virtuais do SAP ASE Azure para carga de trabalho do SAP](dbms_guide_sapase.md)
- [Implantação do SAP maxDB, Live Cache e Content Server no Azure](dbms_guide_maxdb.md)
- [SAP HANA no guia de operações do Azure](hana-vm-operations.md)
- [Alta disponibilidade do SAP HANA para máquinas virtuais do Azure](sap-hana-availability-overview.md)
- [Guia de backup para SAP HANA em máquinas virtuais Azure](sap-hana-backup-guide.md)

