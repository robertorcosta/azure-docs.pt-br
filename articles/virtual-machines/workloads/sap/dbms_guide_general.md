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
ms.openlocfilehash: 581efde3128294a326bdfd08e622a8dcabe5784d
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.contentlocale: pt-BR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84232644"
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

Este guia faz parte da documentação sobre como implementar e implantar o software SAP no Microsoft Azure. Antes de ler este guia, leia o [guia de Planejamento e implementação][planning-guide]. Este documento aborda os aspectos de implantação genérica dos sistemas DBMS relacionados ao SAP em VMs (máquinas virtuais) do Microsoft Azure, usando os recursos de IaaS (infraestrutura como serviço) do Azure.

O documento complementa a documentação de instalação do SAP e as anotações do SAP, que representam os principais recursos para instalações e implantações de software SAP em determinadas plataformas.

Neste documento, são introduzidas considerações sobre a execução de sistemas DBMS relacionados ao SAP em VMs do Azure. Há algumas referências a sistemas DBMS específicos neste capítulo. Em vez disso, os sistemas DBMS específicos são abordados neste documento, após este documento.

## <a name="definitions"></a>Definições
Os termos a seguir são usados ao longo do documento:

* **IaaS**: infraestrutura como serviço.
* **PaaS**: plataforma como serviço.
* **SaaS**: software como serviço.
* **Componente do SAP**: um aplicativo SAP individual, como ECC (Componente Central ERP), BW (Business Warehouse), Solution Manager ou EP (Enterprise Portal). Os componentes SAP podem ser baseados em tecnologias ABAP ou Java tradicionais ou em um aplicativo não baseado em NetWeaver, como o Business Objects.
* **Ambiente SAP**: um ou mais componentes SAP agrupados logicamente para executar uma função de negócios, como desenvolvimento, garantia de qualidade, treinamento, recuperação de desastre ou produção.
* **Estrutura do SAP**: Este termo refere-se à totalidade dos ativos SAP na estrutura de TI de um cliente. A estrutura do SAP inclui todos os ambientes de produção e de não produção.
* **Sistema SAP**: a combinação de uma camada DBMS e uma camada de aplicativo de, por exemplo, um sistema de desenvolvimento SAP ERP, um sistema de teste SAP Business Warehouse ou um sistema de produção SAP CRM. Nas implantações do Azure não há suporte para a divisão dessas duas camadas entre local e Azure. Como resultado, um sistema SAP é implantado no local ou no Azure. Você pode implantar os diferentes sistemas de uma estrutura da SAP no Azure ou de forma local. Por exemplo, você poderia implantar os sistemas de desenvolvimento e teste SAP CRM no Azure, mas implantar o sistema de produção SAP CRM localmente.
* **Entre locais**: descreve um cenário em que as VMs são implantadas em uma assinatura do Azure com conectividade site a site, multissite ou de ExpressRoute entre os data centers locais e o Azure. Na documentação comum do Azure, esses tipos de implantações também são descritas como cenários entre instalações. 

    O motivo para a conexão é estender domínios locais, Active Directory local e DNS local para o Azure. A estrutura local é estendida para os ativos do Azure da assinatura. Com essa extensão, as VMs podem fazer parte do domínio local. Usuários de domínio do domínio local podem acessar os servidores e executar serviços nessas VMs como serviços DBMS. A comunicação e resolução de nomes entre VMs implantadas de forma local e VMs implantadas no Azure são possíveis. Este cenário é o mais comum em uso para implantar ativos SAP no Azure. Para obter mais informações, consulte [Planejamento e design para gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Implantações entre instalações de sistemas SAP em que máquinas virtuais do Azure que executam sistemas SAP são membros de um domínio local e têm suporte para sistemas SAP de produção. Configurações entre locais têm suporte para a implantação de partes ou estruturas da SAP completas no Azure. Até mesmo a execução da estrutura SAP completa no Azure exige que essas VMs façam parte de um domínio local e do Active Directory/LDAP. 
>
> Nas versões anteriores da documentação, os cenários de TI híbrida foram mencionados. O termo *híbrida* tem origem na conectividade entre o local e o Azure. Nesse caso, híbrida também significa que as VMs no Azure fazem parte do Active Directory local.
>
>

Algumas documentações da Microsoft descrevem cenários entre instalações de modo um pouco diferente, especialmente para configurações de alta disponibilidade do DBMS. No caso de documentos relacionados ao SAP, o cenário entre locais se resume a ter uma conectividade de site a site ou privada [ExpressRoute](https://azure.microsoft.com/services/expressroute/) e uma estrutura SAP distribuída entre os locais e o Azure.

## <a name="resources"></a>Recursos
Há outros artigos disponíveis sobre a carga de trabalho do SAP no Azure. Comece com [Carga de trabalho do SAP no Azure: Introdução](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) e escolha sua área de interesse.

As seguintes observações do SAP estão relacionadas ao SAP no Azure em relação à área abordada neste documento.

| Número da observação | Title |
| --- | --- |
| [1928533] |Aplicativos SAP no Azure: produtos com suporte e tipos de VM do Azure |
| [2015553] |SAP no Microsoft Azure: pré-requisitos de suporte |
| [1999351] |Solução de problemas de monitoramento aprimorado do Azure para SAP |
| [2178632] |Métricas-chave de monitoramento para SAP no Microsoft Azure |
| [1409604] |Virtualização no Windows: monitoramento aprimorado |
| [2191498] |SAP no Linux com o Azure: monitoramento aprimorado |
| [2039619] |Os aplicativos SAP no Microsoft Azure que usam o Oracle Database: produtos e versões com suporte |
| [2233094] |DB6: Aplicativos SAP no Azure usando IBM DB2 para Linux, UNIX e Windows: Informações adicionais |
| [2243692] |Linux na VM (IaaS) do Microsoft Azure: Problemas de licença do SAP |
| [1984787] |SUSE Linux Enterprise Server 12: Notas de instalação |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalação e atualização |
| [2069760] |Atualização e instalação do SAP do Oracle Linux 7.x |
| [1597355] |Recomendação de troca de espaço para Linux |
| [2171857] |Oracle Database 12c: suporte ao sistema de arquivos no Linux |
| [1114181] |Oracle Database 11g: suporte ao sistema de arquivos no Linux |


Para obter informações sobre todas as observações do SAP para Linux, confira a [wiki da comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Você precisa ter conhecimento prático sobre a arquitetura do Microsoft Azure e sobre como as máquinas virtuais do Microsoft Azure são implantadas e operadas. Para obter mais informações, confira [a documentação do Azure](https://docs.microsoft.com/azure/).

Em geral, as instalação e a configuração do Windows, do Linux e do DBMS são essencialmente as mesmas que em qualquer máquina virtual ou computador bare metal que você instala localmente. Há algumas decisões de implementação de arquitetura e gerenciamento do sistema que são diferentes ao utilizar a IaaS do Azure. Este documento explica as diferenças específicas de arquitetura e gerenciamento do sistema para as quais você deve estar preparado ao usar a IaaS do Azure.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Estrutura de armazenamento de uma VM para implantações de RDBMS
Para seguir este capítulo, leia e entenda as informações apresentadas [neste capítulo][deployment-guide-3] do [Guia de implantação][deployment-guide]. Você precisa entender e conhecer as diferentes séries de VM e as diferenças entre o armazenamento padrão e premium antes de ler este capítulo. 

Para saber mais sobre o Armazenamento do Microsoft Azure para VMs do Azure, confira:

- [Introdução aos discos gerenciados para VMs do Windows Azure](../../windows/managed-disks-overview.md).
- [Introdução aos discos gerenciados para VMs do Linux Azure](../../linux/managed-disks-overview.md).

Em uma configuração básica, nós normalmente recomendamos uma estrutura de implantação na qual o sistema operacional, o DBMS e eventuais binários SAP são separados dos arquivos de banco de dados. Recomendamos que os sistemas SAP em execução em máquinas virtuais do Azure tenham o VHD base ou disco instalado com o sistema operacional, os executáveis do sistema de gerenciamento de banco de dados e os executáveis do SAP. 

Os dados do DBMS e os arquivos de log são armazenados no armazenamento padrão ou premium. Eles são armazenados em discos separados e anexados como discos lógicos à VM de imagem do sistema operacional do Azure original. Para implantações do Linux, há diferentes recomendações documentadas, especificamente para SAP HANA.

Ao planejar o layout do disco, encontre o melhor equilíbrio entre estes itens:

* O número de arquivos de dados.
* O número de discos que contêm os arquivos.
* As cotas IOPS de um único disco.
* A taxa de transferência de dados por disco.
* O número de discos de dados adicionais possíveis por tamanho da VM.
* A taxa de transferência geral do armazenamento que uma VM pode fornecer.
* A latência que diferentes tipos de Armazenamento do Azure podem fornecer.
* SLAs de VM.

O Azure impõe uma cota de IOPS por disco de dados. Essas cotas são diferentes para discos hospedados no armazenamento padrão e no armazenamento premium do Azure. Latência de e/s também é diferente entre os dois tipos de armazenamento. O armazenamento Premium oferece uma latência de E/S melhor. 

Cada um dos diferentes tipos de VM tem um número limitado de discos de dados que podem ser anexados. Outra restrição é que somente determinados tipos de VM podem usar o armazenamento premium. Normalmente, você decide usar um determinado tipo de VM com base nos requisitos de CPU e memória. Você também pode considerar os requisitos de IOPS, latência e taxa de transferência de disco, que geralmente são dimensionados de acordo com o número de discos ou o tipo de discos do armazenamento premium. O número de IOPS e a taxa de transferência a ser obtida por cada disco podem determinar o tamanho do disco, especialmente com o armazenamento premium.

> [!NOTE]
> Para implantações de DBMS, recomendamos o uso do armazenamento premium para quaisquer dados, logs de transações ou arquivos de restauração. Não importa se você deseja implantar sistemas de produção ou não produção.

> [!NOTE]
> Para se beneficiar do [SLA de VM único](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) exclusivo do Azure, todos os discos anexados precisam ser do tipo de armazenamento premium, o que inclui o VHD base.

> [!NOTE]
> Não há suporte para a hospedagem de arquivos de banco de dados principal, como arquivos de log e de bancos de dados do SAP no hardware de armazenamento localizado em data centers de terceiros colocalizados adjacentes aos data centers do Azure. Para cargas de trabalho SAP, apenas armazenamento que é representado como nativo do serviço do Azure tem suporte para os arquivos de log de transações e dados de bancos de dados SAP.

O posicionamento de arquivos de banco de dados e arquivos de log e restauração, além do tipo de Armazenamento do Microsoft Azure usado devem ser definidos por requisitos de taxa de transferência, latência e IOPS. Para ter IOPS suficiente, você poderá ser forçado a usar vários discos ou usar um disco de armazenamento premium maior. Se você usar vários discos, crie uma distribuição de software nos discos que contêm os arquivos de dados ou os arquivos de log e restauração. Nesses casos, o IOPS e SLAs de discos do armazenamento premium subjacentes ou o máximo de discos de IOPS de armazenamento padrão que podem ser obtido da taxa de transferência de disco são cumulativos para o conjunto resultante de distribuição.

Como já foi mencionado, se seus requisitos de IOPS excedem o que pode fornecer um único VHD, equilibre o número de IOPS necessário para os arquivos de banco de dados entre um número de VHDs. A maneira mais fácil de distribuir a carga de IOPS em discos é criar uma distribuição de software nos diferentes discos e colocar um número de arquivos de dados do SAP DBMS nos LUNs retirados da distribuição de software. O número de discos na distribuição é orientado pelas demandas de IOPs, pelas demandas de taxa de transferência de disco e pelas demandas de volume.


---
> ![Windows][Logo_Windows] Windows
>
> Recomendamos usar espaços de armazenamento do Windows para criar conjuntos de distribuição entre vários VHDs do Azure. Use pelo menos o Windows Server 2012 R2 ou o Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Há suporte apenas para MDADM e LVM (Logical Volume Manager) para criar um RAID de software no Linux. Para obter mais informações, consulte:
>
> - [Configurar RAID de software no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) usando MDADM
> - [Configurar o LVM em uma VM Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) usando LVM
>
>

---

> [!NOTE]
> Como o Armazenamento do Microsoft Azure mantém três imagens dos VHDs, não faz sentido configurar uma redundância enquanto estiver distribuindo. Você só precisa configurar a distribuição para que as E/S sejam distribuídas nos diferentes VHDs.
>

### <a name="managed-or-nonmanaged-disks"></a>Discos gerenciados ou não gerenciados
Uma conta de armazenamento do Azure é uma construção administrativa e também está sujeita a limitações. As limitações são diferentes entre as contas de armazenamento padrão e as contas de armazenamento premium. Para obter informações sobre recursos e limitações, confira [Metas de desempenho e escalabilidade do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

Para o armazenamento padrão, lembre-se de que há um limite de IOPS por conta de armazenamento. Consulte a linha que contém a **Taxa de solicitação total** no artigo [Metas de desempenho e escalabilidade do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). Também há um limite inicial para o número de contas de armazenamento por assinatura do Azure. Balanceie VHDs para a estrutura SAP maior entre diferentes contas de armazenamento para evitar atingir os limites dessas contas de armazenamento. Esse é um trabalho tedioso quando você estiver falando de algumas centenas de computadores virtuais com mais de mil VHDs.

Como o uso do armazenamento padrão para implantações de DBMS em conjunto com uma carga de trabalho do SAP não é recomendado, as referências e recomendações para o armazenamento padrão são limitadas a este pequeno [artigo](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Para evitar o trabalho administrativo de planejamento e implantação de VHDs em diferentes contas de armazenamento do Azure, a Microsoft introduziu o [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) em 2017. Os discos gerenciados estão disponíveis para armazenamento padrão e premium. As principais vantagens dos discos gerenciados em comparação aos não gerenciados são:

- Para discos gerenciados, o Azure distribui os diferentes VHDs entre diferentes contas de armazenamento automaticamente no momento da implantação. Assim, os limites da conta de armazenamento para o volume de dados, a taxa de transferência de E/S e o IOPS não são afetados.
- Utilizando discos gerenciados, o Armazenamento do Microsoft Azure segue os conceitos dos conjuntos de disponibilidade do Azure. Se a VM fizer parte de um conjunto de disponibilidade do Azure, o VHD base e o disco anexado de uma VM serão implantados em diferentes domínios de falha e atualização.


> [!IMPORTANT]
> Considerando as vantagens dos discos gerenciados do Azure, é altamente recomendável usar os discos gerenciados do Azure para suas implantações de DBMS e implantações do SAP em geral.
>

Para converter de discos não gerenciados para gerenciados, confira:

- [Converter uma máquina virtual do Windows de discos não gerenciados para discos gerenciados](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Converter uma máquina virtual do Linux de discos não gerenciados para discos gerenciados](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Cache de VMs e discos de dados
Quando você monta discos carregados em VMs, é possível escolher se o tráfego de E/S entre a VM e os discos no armazenamento do Azure são armazenados em cache. Os armazenamentos padrão e premium usam duas tecnologias diferentes para esse tipo de cache.

As recomendações a seguir consideram essas características de E/S para o DBMS padrão:

- É principalmente uma carga de trabalho lida em arquivos de dados de um banco de dados. Essas leituras são críticas para o desempenho do sistema de DBMS.
- A gravação nos arquivos de dados ocorre em intermitências baseadas nos pontos de verificação ou num fluxo constante. Com média de um dia, há menos gravações do que leituras. Em vez de leituras de arquivos de dados, essas gravações são assíncronas e não obstruem nenhuma transação do usuário.
- Praticamente, não há nenhuma leitura dos arquivos de refazer ou log de transação. As exceções incluem E/Ss grandes ao executar backups de log de transações.
- A carga principal em relação a arquivos de log de transações ou refazer são gravações. Dependendo da natureza da carga de trabalho, você pode ter E/S pequenas de 4 KB ou, em outros casos, tamanhos de E/S iguais ou superiores a 1 MB.
- Todas as gravações devem ser persistidas em disco de forma confiável.

Para o armazenamento padrão, os possíveis tipos de cache são:

* Nenhum
* Ler
* Leitura/Gravação

Para obter um desempenho consistente e determinístico, defina o cache no armazenamento padrão para todos os discos que contêm arquivos de dados relacionados ao DBMS, arquivos de log e restauração e espaço de tabela para **NENHUM**. O caching de base VHD pode permanecer com o padrão.

Para o armazenamento premium, existem as seguintes opções de cache:

* Nenhum
* Ler
* Leitura/gravação
* Nenhum + Acelerador de Gravação, que é somente para VMs da série M do Azure
* Leitura + Acelerador de Gravação, que é apenas para VMs da série M do Azure

Para o armazenamento premium, recomendamos que você use **Cache de leitura para arquivos de dados** do banco de dados SAP e escolha **Sem cache para os discos dos arquivos de log**.

Para implantações da série M, recomendamos que você use o Acelerador de Gravação do Azure para sua implantação do DBMS. Para obter mais informações, restrições e implantar o Acelerador de Gravação do Azure, confira [Habilitar o Acelerador de Gravação](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Discos não persistentes do Azure
As VMs do Azure oferecem discos não persistentes depois que uma VM é implantada. Em caso de reinicialização da VM, todo o conteúdo dessas unidades é apagado. Portanto, consideramos que os arquivos de dados e arquivos de log e restauração de bancos de dados sob nenhuma circunstância devem estar nessas unidades não persistentes. Pode haver exceções para alguns bancos de dados, em que essas unidades não persistentes poderiam ser adequadas para espaços de tabela temporários e tempdb. Evite usar essas unidades VMs da série A, pois essas unidades não persistentes são limitadas na taxa de transferência dessa família VM. 

Para obter mais informações, confira [Como entender a unidade temporária em VMs do Windows no Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

---
> ![Windows][Logo_Windows] Windows
>
> A unidade D em uma VM do Azure é uma unidade não persistente que é apoiada por alguns discos locais no nó de computação do Azure. Como ela não é persistente, todas as alterações feitas no conteúdo da unidade D são perdidas quando a VM é reinicializada. As alterações incluem arquivos que foram armazenados, diretórios que foram criados e aplicativos que foram instalados.
>
> ![Linux][Logo_Linux] Linux
>
> As VMs do Azure do Linux montam automaticamente uma unidade em /mnt/resource, que é uma unidade não persistente apoiada por discos locais no nó de computação do Azure. Como ela não é persistente, todas as alterações feitas no conteúdo em /mnt/resource são perdidas quando a VM é reinicializada. As alterações incluem arquivos que foram armazenados, diretórios que foram criados e aplicativos que foram instalados.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Resiliência do Armazenamento do Microsoft Azure
O Armazenamento do Microsoft Azure armazena a VHD de base com o sistema operacional e os discos anexados ou blobs em, pelo menos, três nós de armazenamento separados. Esse tipo de armazenamento é chamado de LRS (armazenamento com redundância local). O LRS é o padrão para todos os tipos de armazenamento no Azure.

Há outros métodos de redundância. Para obter mais informações, consulte [Replicação do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>O armazenamento premium é o tipo recomendado para VMs de DBMS e discos que armazenam arquivos de banco de dados e arquivos de log e restauração. O único método de redundância disponível para o armazenamento premium é LRS. Como resultado, você precisa configurar os métodos de banco de dados para habilitar a replicação de banco de dados em outra região do Azure ou zona de disponibilidade. Os métodos de banco de dados incluem SQL Server AlwaysOn, Oracle Data Guard e replicação do sistema HANA.


> [!NOTE]
> Para implantações de DBMS, o uso de GRS (armazenamento com redundância geográfica) não é recomendado para o armazenamento padrão. O GRS afeta seriamente o desempenho e não respeita a ordem de gravação em diferentes VHDs anexados a uma VM. A falta de ordem de gravação em diferentes VHDs resulta potencialmente em bancos de dados inconsistentes no destino de replicação. Essa situação ocorre se os arquivos do banco de dados e arquivos de log e restauração estiverem espalhados por vários VHDs, como geralmente é o caso, no lado da VM de origem.



## <a name="vm-node-resiliency"></a>Resiliência de nó de VM
O Azure oferece vários SLAs diferentes para VMs. Para obter mais informações, veja a versão mais recente do [SLA de máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Como a camada DBMS é geralmente uma parte crítica da disponibilidade em um sistema SAP, você precisa entender os conjuntos de disponibilidade, as zonas de disponibilidade e os eventos de manutenção. Para obter mais informações sobre esses conceitos, confira [Gerenciar a disponibilidade de máquinas virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) e [Gerenciar a disponibilidade de máquinas virtuais do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

A recomendação mínima para cenários de DBMS de produção com carga de trabalho do SAP é:

- Implantar duas VMs em um conjunto de disponibilidade separado na mesma região do Azure.
- Executar essas duas VMs na mesma rede virtual do Azure e ter NICs anexadas nas mesmas sub-redes.
- Use os métodos de banco de dados para manter um modo de espera ativo com a segunda VM. Métodos podem ser SQL Server Always On, o Oracle Data Guard ou HANA System Replication.

Você também pode implantar uma terceira VM em outra região do Azure e usar os mesmos métodos de banco de dados para fornecer uma réplica assíncrona em outra região do Azure.

Para obter informações sobre como configurar conjuntos de disponibilidade do Azure, confira [este tutorial](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Considerações sobre a rede do Azure
Em implantações do SAP em larga escala, use o blueprint do [Datacenter virtual do Azure](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter). Use-o para sua configuração de rede virtual, bem como para atribuições de função e permissões para diferentes partes da sua organização.

Essas práticas recomendadas são o resultado de centenas de implantações do cliente:

- As redes virtuais nas quais o aplicativo SAP é implantado não têm acesso à Internet.
- As VMs do banco de dados são executadas na mesma rede virtual que a camada do aplicativo.
- As VMs na rede virtual têm uma alocação estática do endereço IP privado. Para obter mais informações, confira [Tipos de endereço IP e métodos de alocação no Azure](../../../virtual-network/public-ip-addresses.md).
- Restrições de roteamento de e para as VMs de DBMS *não* são definidas com firewalls instalados nas VMs DBMS locais. Em vez disso, o roteamento de tráfego é definido com [NSG (grupos de segurança de rede)](https://docs.microsoft.com/azure/virtual-network/security-overview).
- Para separar e isolar o tráfego para a VM do DBMS, atribua diferentes NICs à VM. Cada NIC recebe um endereço IP diferente e a cada uma é atribuída a uma sub-rede de rede virtual diferente. Cada sub-rede tem regras de NSG diferentes. O isolamento ou a separação do tráfego de rede é uma medida para o roteamento. Não é usado a fim de definir cotas para taxa de transferência de rede.

> [!NOTE]
> A atribuição de endereços IP estáticos por meio do Azure significa atribuí-los a NICs virtuais individuais. Não atribua endereços IP estáticos no sistema operacional convidado a uma NIC virtual. Alguns serviços do Azure, como o Backup do Azure, dependem de pelo menos o vNIC primário estar definido como DHCP e não para endereços IP estáticos. Para obter mais informações, confira [Solucionar problemas de backup da máquina virtual do Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Para atribuir vários endereços IP estáticos a uma VM, atribua várias NICs virtuais a uma VM.
>


> [!IMPORTANT]
> Não há suporte para a configuração de [dispositivos de virtuais de rede](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo SAP e a camada DBMS de um SAP NetWeaver, Hybris ou sistemas SAP baseados em S/4HANA. Essa restrição destina-se a questões de funcionalidade e desempenho. O caminho de comunicação entre a camada de aplicativo SAP e a camada DBMS deve ser direto. A restrição não inclui as [regras de ASG (grupo de segurança de aplicativo) e NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) se elas permitirem um caminho de comunicação direto. 
>
> Outros cenários em que as soluções de virtualização de rede não têm suporte estão em:
>
> * Caminhos de comunicação entre VMs do Azure que representam nós de cluster do Linux Pacemaker e dispositivos SBD, conforme descrito em [Alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> * Caminhos de comunicação entre as VMs do Azure e o SOFS (Servidor de Arquivos de Escalabilidade Horizontal) do Windows Server, conforme descrito em [Cluster de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um compartilhamento de arquivos no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Soluções de virtualização de rede em caminhos de comunicação podem facilmente dobrar a latência de rede entre dois parceiros de comunicação. Eles também podem restringir a taxa de transferência em caminhos críticos entre a camada do aplicativo SAP e a camada do DBMS. Em alguns cenários de clientes, as soluções de virtualização de rede podem resultar na falha dos clusters do Pacemaker Linux. Estes são casos em que as comunicações entre os nós de cluster do Pacemaker do Linux se comunicam com o dispositivo SBD por meio de uma solução de virtualização de rede.
>

> [!IMPORTANT]
> Outro design que *não* tem suporte é a segregação da camada de aplicativo SAP e da camada DBMS em diferentes redes virtuais do Azure que não estejam [emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre si. Recomendamos que você separe a camada de aplicativo SAP e a camada DBMS usando sub-redes em uma rede virtual do Azure, em vez de usar redes virtuais diferentes do Azure. 
>
> Se você decidir não seguir a recomendação e, em vez disso, segregar as duas camadas em redes virtuais diferentes, elas deverão ser [emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Esteja ciente de que o tráfego entre duas redes virtuais [emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) do Azure está sujeito a custos de transferência. Um grande volume de dados que consiste em muitos terabytes é trocado entre a camada do aplicativo SAP e a camada do DBMS. Você poderá acumular custos substanciais se a camada do aplicativo SAP e a camada do DBMS estiverem separadas entre duas redes virtuais emparelhadas do Azure.

Use duas VMs para sua implantação de DBMS de produção em um conjunto de disponibilidade do Azure. Use também o roteamento separado para a camada de aplicativo SAP e o tráfego de gerenciamento e operações para as duas VMs do DBMS. Veja a seguinte imagem:

![Diagrama de duas VMs em duas sub-redes](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Usar o Azure Load Balancer para redirecionar o tráfego
O uso de endereços IP privados virtuais usados em funcionalidades como a replicação do SQL Server AlwaysOn ou sistema HANA requer a configuração do balanceador de carga do Azure. O balanceador de carga usa portas de investigação determinar o nó ativo do DBMS e rotear o tráfego exclusivamente para esse nó do banco de dados ativo. 

Caso ocorra um failover de nó do banco de dados, o aplicativo SAP não precisa reconfigurar. Em vez disso, as arquiteturas de aplicativos SAP mais comuns se reconectarão ao endereço IP virtual. Enquanto isso, o balanceador de carga reage ao failover de nó redirecionando o tráfego do endereço IP virtual para o segundo nó.

O Azure oferece dois [SKUs de balanceador de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) diferentes: um SKU básico e um SKU padrão. A menos que você deseje implantar em zonas de disponibilidade do Azure, o SKU do balanceador de carga básico é adequado.

O tráfego entre as VMs de DBMS e a camada do aplicativo SAP é sempre roteado por meio do balanceador de carga o tempo todo? A resposta depende de como você configura o balanceador de carga. 

Neste momento, o tráfego de entrada para a VM do DBMS é sempre roteado pelo balanceador de carga. A rota de tráfego de saída da VM do DBMS para a VM da camada de aplicativo depende da configuração do balanceador de carga. 

O balanceador de carga oferece uma opção de DirectServerReturn. Se essa opção estiver configurada, o tráfego direcionado de VM de DBMS para a camada de aplicativo SAP *não* será roteado por meio do balanceador de carga do Azure. Em vez disso, ele vai diretamente para a camada do aplicativo. Se DirectServerReturn não estiver configurado, o tráfego de retorno para a camada de aplicativo SAP é roteado por meio do balanceador de carga.

Recomendamos que você configure o DirectServerReturn em combinação com balanceadores de carga posicionados entre a camada do aplicativo SAP e a camada de DBMS. Essa configuração reduz a latência de rede entre as duas camadas.

Para obter um exemplo de como definir essa configuração com o SQL Server AlwaysOn, confira [Configurar um ouvinte de ILB para grupos de disponibilidade AlwaysOn no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Se você usar modelos JSON do GitHub publicados como uma referência para suas implantações de infraestrutura SAP no Azure, estude este [modelo para um sistema de 3 camadas do SAP](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). Neste modelo, você também pode ver as configurações corretas do balanceador de carga.

### <a name="azure-accelerated-networking"></a>Rede Acelerada do Azure
Para reduzir ainda mais a latência de rede entre as VMs do Azure, recomendamos que você escolha [rede acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Use-o ao implantar VMs do Azure para uma carga de trabalho SAP, especialmente para a camada de aplicativo SAP e a camada do DBMS SAP.

> [!NOTE]
> Nem todos os tipos de VM oferecem suporte à rede acelerada. O artigo anterior lista os tipos de VM compatíveis com a rede acelerada.
>

---
> ![Windows][Logo_Windows] Windows
>
> Para saber como implantar VMs com rede acelerada para Windows, confira [Criar uma máquina virtual do Windows com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> Para obter mais informações sobre a distribuição do Linux, confira [Criar uma máquina virtual do Linux com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

---

> [!NOTE]
> No caso do SUSE, Red Hat e Oracle Linux, o serviço de Rede Acelerada de mensagens é compatível com versões recentes. Versões mais antigas, como SLES 12 SP2 ou RHEL 7.2, não oferecem suporte à rede acelerada do Azure.
>


## <a name="deployment-of-host-monitoring"></a>Implantação do monitoramento de host
Para o uso produtivo de aplicativos SAP nas máquinas virtuais do Azure, o SAP requer a capacidade de obter dados de monitoramento dos hosts físicos em execução nas máquinas virtuais do Azure. É necessário um nível de patch do Agente de Host SAP específico que habilite essa funcionalidade no SAPOSCOL e no Agente de Host SAP. O nível de patch exato está documentado na Nota SAP [1409604].

Para obter mais informações sobre a implantação de componentes que entregam dados do host ao SAPOSCOL e ao SAP Host Agent e o gerenciamento do ciclo de vida desses componentes, confira o [Guia de implantação][deployment-guide].


## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre um DBMS específico, confira:

- [Implantação do DBMS de Máquinas de Virtuais do SQL Server Azure para carga de trabalho do SAP](dbms_guide_sqlserver.md)
- [Implantação do DBMS de Máquinas Virtuais do Oracle Azure para carga de trabalho do SAP](dbms_guide_oracle.md)
- [Implantação do DBMS de Máquinas Virtuais do IBM DB2 Azure para carga de trabalho do SAP](dbms_guide_ibm.md)
- [Implantação do DBMS de Máquinas Virtuais do SAP ASE Azure para carga de trabalho do SAP](dbms_guide_sapase.md)
- [Implantação do SAP maxDB, Live Cache e Content Server no Azure](dbms_guide_maxdb.md)
- [SAP HANA no guia de operações do Azure](hana-vm-operations.md)
- [Alta disponibilidade do SAP HANA para máquinas virtuais do Azure](sap-hana-availability-overview.md)
- [Guia de backup para SAP HANA em máquinas virtuais do Azure](sap-hana-backup-guide.md)

