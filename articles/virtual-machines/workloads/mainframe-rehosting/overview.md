---
title: Rehospedagem do mainframe em máquinas virtuais do Azure
description: Rehospede suas cargas de trabalho de mainframe, como sistemas baseados em IBM Z usando máquinas virtuais (VMs) no Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: ccdeefabeedfca4959239696361ccce0bc6c1c78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76289791"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Rehospedagem do mainframe em máquinas virtuais do Azure

Migrar cargas de trabalho de ambientes de mainframe para a nuvem permite que você modernize sua infra-estrutura e, muitas vezes, economize custos. Muitas cargas de trabalho podem ser transferidas para o Azure com apenas pequenas alterações de código, como atualizar os nomes dos bancos de dados.

De um modo geral, o termo *mainframe* significa um grande sistema de computador. Especificamente, a grande maioria atualmente em uso são servidores IBM System Z ou sistemas compatíveis com plug-jato ibm que executam MVS, DOS, VSE, OS/390 ou z/OS.

Uma máquina virtual Azure (VM) é usada para isolar e gerenciar os recursos para um aplicativo específico em uma única instância. Mainframes como ibm z/OS usam Partições Lógicas (LPARS) para este fim. Um mainframe pode usar um LPAR para uma região CICS com programas COBOL associados e um LPAR separado para o banco de dados IBM Db2. Um aplicativo típico [de nível n no Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) implanta As VMs do Azure em uma rede virtual que pode ser segmentada em sub-redes para cada nível.

As VMs do Azure podem executar ambientes de emulação de mainframe e compiladores que suportam cenários de elevação e mudança. Desenvolvimento e teste estão frequentemente entre as primeiras cargas de trabalho a migrar de um mainframe para um ambiente de desenvolvimento/teste do Azure. Os componentes comuns do servidor que você pode emular incluem sistemas de transação on-line (OLTP), lote e de ingestão de dados, como mostra a figura a seguir.

![Os ambientes de emulação no Azure permitem que você execute sistemas baseados em z/OS.](media/01-overview.png)

Algumas cargas de trabalho de mainframe podem ser migradas para o Azure com relativa facilidade, enquanto outras podem ser rehospedadas no Azure usando uma solução de parceiro. Para obter orientações detalhadas sobre a escolha de uma solução de parceiro, o [centro de migração do Mainframe Do Azure](https://azure.microsoft.com/migration/mainframe/) pode ajudar.

## <a name="mainframe-migration"></a>Migração de mainframe

Realugar, reconstruir, substituir ou aposentar? IaaS ou PaaS? Para determinar a estratégia de migração certa para o aplicativo de mainframe, consulte o guia [de migração do Mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) no Centro de Arquitetura Azure.

## <a name="micro-focus-rehosting-platform"></a>Plataforma de rehospedagem Micro Focus

O Micro Focus Enterprise Server é uma das maiores plataformas de rehospedagem de mainframe disponíveis. Você pode usá-lo executar suas cargas de trabalho z/OS em uma plataforma x86 mais barata no Azure.

Introdução:

- [Instale o Enterprise Server e o Enterprise Developer no Azure](./microfocus/set-up-micro-focus-azure.md)
- [Configure o CICS BankDemo para Desenvolvedor Corporativo no Azure](./microfocus/demo.md)
- [Execute o Enterprise Server em um contêiner Docker no Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame no Azure

TmaxSoft OpenFrame é uma solução popular de rehospedagem de mainframe usada em cenários de elevação e mudança. Um ambiente OpenFrame no Azure é adequado para desenvolvimento, demonstrações, testes ou cargas de trabalho de produção.

Introdução:

- [Comece com o TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Baixe o ebook](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

O IBM Z Development and Test Environment (IBM zD&T) configura um ambiente de não produção no Azure que você pode usar para desenvolvimento, teste e demonstrações de aplicativos baseados em z/OS.

O ambiente de emulação no Azure pode hospedar diferentes tipos de instâncias Z através de ADCDs (Application Developers Controlled Distributions, distribuições controladas por desenvolvedores de aplicativos). Você pode executar zD&T Personal Edition, zD&T Parallel Sysplex e zD&T Enterprise Edition no Azure e No Azure Stack.

Introdução:

- [Configure o IBM zD&T 12.0 no Azure](./ibm/install-ibm-z-environment.md)
- [Configurar ADCD em zD&T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale no Azure

O ambiente IBM DB2 pureScale fornece um cluster de banco de dados para o Azure. Não é idêntico ao ambiente original, mas oferece disponibilidade e escala semelhantes ao IBM DB2 para z/OS em execução em uma configuração Sysplex Paralela.

Para começar, consulte [IBM DB2 pureScale no Azure](/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Considerações

Quando você migra cargas de trabalho de mainframe para a infra-estrutura do Azure como um serviço (IaaS), você pode escolher entre vários tipos de recursos de computação escaláveis demanda, incluindo VMs do Azure. O Azure oferece uma gama de VMs [Linux](/azure/virtual-machines/linux/overview) e [Windows.](/azure/virtual-machines/windows/overview)

### <a name="compute"></a>Computação

O poder computacional do Azure se compara favoravelmente à capacidade de um mainframe. Se você está pensando em mover uma carga de trabalho de mainframe para o Azure, compare a métrica do mainframe de um milhão de instruções por segundo (MIPS) com AS CPUs virtuais. 

Saiba como mover a [computação de mainframe para o Azure](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Failover e alta disponibilidade

O Azure oferece contratos de nível de serviço (SLAs) baseados em compromisso. A disponibilidade de vários noves é o padrão, e os SLAs podem ser otimizados com a replicação local ou geobaseada de serviços. O [SLA completo do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explica a disponibilidade garantida do Azure como um todo.

Com o Azure IaaS, como uma VM, funções específicas do sistema fornecem suporte de failover — por exemplo, instâncias de cluster de failover e conjuntos de disponibilidade. Quando você usa a plataforma Azure como um serviço (PaaS) recursos, a plataforma lida com failover automaticamente. Exemplos incluem [banco de dados Azure SQL](/azure/sql-database/sql-database-technical-overview) e [Azure Cosmos DB](/azure/cosmos-db/introduction).

### <a name="scalability"></a>Escalabilidade

Os mainframes normalmente se ampliam, enquanto os ambientes de nuvem se escoram. O Azure oferece uma gama de tamanhos [de Linux](/azure/virtual-machines/linux/sizes) e [Windows](/azure/virtual-machines/windows/sizes) para atender às suas necessidades. A nuvem também se dimensiona para cima ou para baixo para corresponder às especificações exatas do usuário. A escala de poder, armazenamento e [serviços](/azure/architecture/best-practices/auto-scaling) de computação demanda um modelo de faturamento baseado em uso.

### <a name="storage"></a>Armazenamento

Na nuvem, você tem uma gama de opções de armazenamento flexíveis e escaláveis, e você paga apenas pelo que precisa. O [Armazenamento do Azure](/azure/storage/common/storage-introduction) oferece um armazenamento de objetos altamente escalonável para objetos de dados, um serviço de sistema de arquivos para a nuvem, armazenamento de mensagens confiáveis e armazenamento de NoSQL. Para VMs, discos gerenciados e discos não gerenciados fornecem armazenamento em disco persistente e seguro.

Saiba como [mover o armazenamento do mainframe para o Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Backup e recuperação

Manter seu próprio local de recuperação de desastres pode ser uma proposta cara. O Azure tem opções fáceis de implementar e econômicas para [backup,](/azure/backup/backup-introduction-to-azure-backup) [recuperação](/azure/site-recovery/site-recovery-overview)e [redundância](/azure/storage/common/storage-redundancy) em níveis locais ou regionais, ou via geo-redundância.

## <a name="azure-government-for-mainframe-migrations"></a>Governo azure para migrações de mainframe

Muitas entidades do setor público adorariam mudar suas aplicações de mainframe para uma plataforma mais moderna e flexível. O Governo Microsoft Azure é uma instância fisicamente separada da plataforma global Microsoft Azure — empacotada para sistemas governamentais federais, estaduais e locais. Fornece serviços de segurança, proteção e conformidade de classe mundial especificamente para agências governamentais dos Estados Unidos e seus parceiros.

O Governo do Azure ganhou uma Autoridade Provisória para Operar (P-ATO) para o FedRAMP De Alto Impacto para sistemas que precisam desse tipo de ambiente.

Para começar, baixe [a nuvem do Governo do Microsoft Para aplicativos de mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Próximas etapas

Peça aos [nossos parceiros](partner-workloads.md) para ajudá-lo a migrar ou rehospedar seus aplicativos de mainframe. 

Consulte também:

- [White papers sobre tópicos de mainframe](mainframe-white-papers.md)
- [Migração de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Solução de problemas](/azure/virtual-machines/troubleshooting/)
- [Desmistificando mainframe para migração do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
