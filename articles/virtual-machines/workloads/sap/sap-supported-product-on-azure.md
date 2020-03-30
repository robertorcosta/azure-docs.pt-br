---
title: 'SAP no Azure: o que o software SAP é suportado no Azure'
description: Explica o suporte do software SAP para ser implantado no Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0aaa13ff4d3331378cc17cd0cde29be43822397
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460782"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>O que o software SAP é suportado para implantações do Azure
Este artigo descreve como você pode descobrir qual software SAP é suportado para implantações do Azure e quais são as versões necessárias do sistema operacional ou lançamentos DBMS.

Avaliando se o software SAP atual é suportado e quais versões do SE e DBMS são suportadas com seu software SAP no Azure, você precisará de acesso a:

- Notas de suporte SAP
- Matriz de disponibilidade de produtos SAP



## <a name="general-restrictions-for-sap-workload"></a>Restrições gerais para carga de trabalho SAP
Os serviços Azure IaaS que podem ser usados para carga de trabalho SAP estão limitados ao hardware x86-64 ou x64. Não há ofertas baseadas em CPU Sparc ou Power que se apliquem à carga de trabalho SAP. Clientes que executam em seus aplicativos em sistemas operacionais proprietários de arquiteturas de hardware como o mainframe ibm ou AS400, ou onde os sistemas operacionais HP-UX, Solaris ou AIX estão em uso, precisam alterar seus aplicativos SAP, incluindo DBMS, para um dos seguintes sistemas operacionais:

- Servidor Windows 64bit para a plataforma x86-64
- SUSE linux 64bit para a plataforma x86-64
- Red hat Linux 64Bit para a plataforma x86-64
- Oracle Linux 64bit para a plataforma x86-64

Em combinação com o software SAP, nenhuma outra versão do Sistema Operacional ou distribuições Linux são suportadas. Detalhes exatos sobre versões e casos específicos são documentados posteriormente no documento.


## <a name="you-start-here"></a>Você começa aqui
O ponto de partida para você é [a nota de suporte sap #1928533](https://launchpad.support.sap.com/#/notes/1928533). À medida que você passa por esta nota SAP de cima para baixo, várias áreas de software suportado e VMs são mostrados

A primeira seção lista os requisitos mínimos para versões operacionais suportadas com software SAP em VMs Azure em geral. Se você não estiver atingindo esses requisitos mínimos e executar versões mais antigas desses sistemas operacionais, você precisa atualizar sua versão do SO para uma versão tão mínima ou até mesmo versões mais recentes. É correto que o Azure em geral suportaria versões mais antigas de alguns desses sistemas operacionais. Mas as restrições ou liberações mínimas listadas são baseadas em testes e qualificações executadas e não serão estendidas mais para trás. 


> [!NOTE]
>Existem alguns tipos específicos de VM, HANA Large Instances ou cargas de trabalho SAP que vão exigir versões mais recentes do Sistema Operacional. Casos como esse serão mencionados ao longo do documento. Casos como esse são claramente documentados em notas SAP ou outras publicações SAP.

A seção a seguir lista plataformas SAP gerais que são suportadas com as versões suportadas e mais importantes os kernels SAP que são suportados. Ele lista pilhas NetWeaver/ABAP ou Java suportadas e que precisam de versões mínimas do kernel. Pilhas ABAP mais recentes são suportadas no Azure, mas não precisam de versões mínimas do kernel, uma vez que as alterações para o Azure foram implementadas desde o início do desenvolvimento das pilhas mais recentes

Você precisa verificar:

- Se os aplicativos SAP que você está executando, estão cobertos pelas versões mínimas indicadas. Caso assim, você precisa definir uma nova versão de destino, verifique na Matriz de Disponibilidade de Produtos SAP, quais compilações do sistema operacional e combinações de DBMS são suportadas com a nova versão-alvo. Assim, que você pode escolher a versão certa do sistema operacional e a versão DBMS
- Se você precisa atualizar seus kernels SAP em uma mudança para o Azure
- Se você precisa atualizar pacotes de suporte SAP. Especialmente pacotes de suporte base que podem ser necessários para os casos em que você é obrigado a mudar para uma versão DBMS mais recente


A próxima seção entra em mais detalhes sobre outros produtos SAP e lançamentos DBMS que são suportados pelo SAP no Azure para Windows e Linux. 

> [!NOTE]
> As versões mínimas dos diferentes DBMS são cuidadosamente escolhidas e podem nem sempre refletir todo o espectro de lançamentos DBMS que os diferentes fornecedores de DBMS suportam no Azure em geral. Muitas considerações relacionadas à carga de trabalho SAP foram levadas em conta para definir essas liberações mínimas. Não há nenhum esforço para testar e qualificar versões DBMS mais antigas. 

> [!NOTE]
> As versões mínimas listadas representam a versão mais antiga dos sistemas operacionais e das versões do banco de dados. Nós altamente encorajamos a usar as versões mais recentes do sistema operacional e lançamentos de banco de dados. Em muitos casos, lançamentos mais recentes do sistema operacional e banco de dados levaram em consideração o caso de uso de execução em nuvem pública e código adaptado para otimizar para execução em nuvem pública ou mais especificamente Azure

## <a name="oracle-dbms-support"></a>Suporte ao Oracle DBMS
O sistema operacional, as versões Oracle DBMS e a funcionalidade Oracle suportadas no Azure estão listadas especificamente na [nota de suporte ao SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619). A essência dessa nota pode ser resumida como:

- A versão mínima oracle suportada em VMs Azure certificados para netweaver é Oracle 11g Release 2 Patchset 3 (11.2.0.4)
- Como sistemas operacionais convidados, apenas o Windows e o Oracle Linux se qualificam. As versões exatas do Sistema Operacional e das versões dBMS mínimas relacionadas estão listadas na nota
- O suporte do Oracle Linux também se estende ao cliente Oracle DBMS. Isso significa que todos os componentes SAP, como as instâncias de diálogo do ABAP ou Java Stack, também precisam ser executados no Oracle Linux. Apenas componentes SAP dentro de um sistema SAP que não se conectaria ao Oracle DBMS seriam autorizados a executar um sistema operacional Linux diferente
- Oracle RAC não é suportada 
- O Oracle ASM é suportado para alguns dos casos. Os detalhes estão listados na nota
- Os sistemas SAP não-Unicode só são suportados com servidores de aplicativos em execução com o sistema operacional convidado do Windows. O sistema operacional convidado do DBMS pode ser oracle Linux ou Windows. A razão para essa restrição é evidente ao verificar a Matriz de Disponibilidade de Produtos SAP (PAM). Para oracle Linux, sap nunca lançou kernels SAP não-Unicode

Conhecendo as versões DBMS que são suportadas com a infra-estrutura Azure direcionada, você precisa verificar a Matriz de Disponibilidade de Produtos SAP sobre se as versões do Sistema Operacional e DBMS necessárias são suportadas com as versões do produto SAP que você pretendia executar. 


## <a name="sap-hana-support"></a>Suporte ao SAP HANA
No Azure existem dois serviços, que podem ser usados para executar o banco de dados HANA:

- Máquinas Virtuais do Azure
- [HANA Grandes Instâncias](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

Para executar o SAP HANA, o SAP tem condições cada vez mais fortes que a infra-estrutura precisa atender do que para executar o NetWeaver ou outros aplicativos SAP e DBMS. Como resultado, um número menor de VMs Azure se qualifica para executar o SAP HANA DBMS. A lista de infra-estrutura azure suportada para O SAP HANA pode ser encontrada no chamado diretório de [hardware SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). 

> [!NOTE]
> As unidades que começam com a letra 'S' são unidades [HANA Large Instances.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 

> [!NOTE]
> A SAP não possui certificação específica dependente das principais versões do SAP HANA. Ao contrário da opinião comum, o **cenário de certificação** da coluna nas [plataformas iaas certificadas hana,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)a coluna não faz **nenhuma declaração sobre o HANA maior ou menor versão certificada**. Você precisa assumir que todas as unidades listadas que podem ser usadas para HANA 1.0 e HANA 2.0, desde que as versões certificadas do sistema operacional para as unidades específicas também sejam suportadas por versões HANA 1.0. 

Para o uso do SAP HANA, diferentes versões mínimas do SISTEMA OPERACIONAL podem ser aplicadas do que para os casos gerais do NetWeaver. Você precisa verificar os sistemas operacionais suportados para cada unidade individualmente, pois esses podem variar. Você faz isso clicando em cada unidade. Mais detalhes aparecerão. Um dos detalhes listados são os diferentes sistemas operacionais suportados para esta unidade específica.

> [!NOTE]
> As unidades Azure HANA Large Instance são mais restritivas com sistemas operacionais suportados em comparação com as VMs do Azure. Por outro lado, as VMs do Azure podem impor versões operacionais mais recentes como versões mínimas. Isso é especialmente verdade para algumas das unidades vm maiores que exigiram mudanças nos kernels Linux

Conhecendo o sistema operacional suportado para a infra-estrutura Azure, você precisa verificar a [nota de suporte sap #2235581](https://launchpad.support.sap.com/#/notes/2235581) para as versões exatas do SAP HANA e níveis de patch que são suportados com as unidades do Azure que você está mirando. 

> [!IMPORTANT]
> A etapa de verificação das versões exatas do SAP HANA e dos níveis de patch suportados é muito importante. Em muitos casos, o suporte a uma determinada versão do SO depende de um nível específico de patch dos executáveis SAP HANA.

Como você sabe as versões hana específicas que você pode executar na infra-estrutura Azure direcionada, você precisa verificar na Matriz de Disponibilidade de Produtos SAP para descobrir se há restrições com as versões do produto SAP que suportam as versões HANA que você filtrado


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>Unidades certificadas de VMs azure e HANA Large Instance e throughput de transações comerciais
Além de avaliar as liberações suportadas do sistema operacional, as versões do DBMS e as liberações de software SAP de suporte dependente para unidades de infra-estrutura do Azure, você tem a necessidade de qualificar essas unidades por throughput de transação comercial, que é expressa na unidade 'SAP' por Sap. Todo o dimensionamento SAP depende dos cálculos SAPS. Avaliando os sistemas SAP existentes, você geralmente pode, com a ajuda do seu provedor de infra-estrutura, calcular o SAPS das unidades. Para a camada DBMS, bem como para a camada de aplicação. Em outros casos em que novas funcionalidades são criadas, um exercício de dimensionamento com SAP pode revelar os números SAPS necessários para a camada de aplicativo e a camada DBMS. Como provedor de infra-estrutura, a Microsoft é obrigada a fornecer a caracterização de throughput SAP das diferentes unidades que são certificadas pela NetWeaver e/ou HANA.

Para VMs azure, esses números de throughput SAPS são documentados na [nota de suporte sap #1928533](https://launchpad.support.sap.com/#/notes/1928533). Para unidades Azure HANA Large Instance, os números de throughput do SAPS são documentados na [nota de suporte sap #2316233](https://launchpad.support.sap.com/#/notes/2316233)

Olhando para [a nota de suporte sap #1928533](https://launchpad.support.sap.com/#/notes/1928533), as seguintes observações se aplicam:

- **Para VMs Azure da Série M e VMs Azure da Série MV2, diferentes versões mínimas do SO se aplicam do que para outros tipos de VM azure**. A exigência de versões mais recentes do SO é baseada em mudanças que os diferentes fornecedores de sistemas operacionais tiveram que fornecer em suas versões do sistema operacional para permitir que seus sistemas operacionais funcionassem nos tipos específicos de VM do Azure ou otimizassem o desempenho e throughput de carga de trabalho SAP nesses tipos de VM
- Existem duas tabelas que especificam diferentes tipos de VM. A segunda tabela especifica o throughput SAPS para tipos de VM do Azure que suportam apenas o armazenamento padrão Do Zure. A implantação do DBMS nas unidades especificadas na segunda tabela da nota não é suportada


## <a name="other-sap-products-supported-on-azure"></a>Outros produtos SAP suportados no Azure
Em geral, a suposição é que, com o estado de nuvens de hiperescala como o Azure, a maioria do software SAP deve ser executado sem problemas funcionais no Azure. No entanto, e oposto à visualização em nuvem privada, o SAP ainda expressa suporte para os diferentes produtos SAP explicitamente para os diferentes provedores de nuvem hyerpscale. Como resultado, existem diferentes notas de suporte SAP indicando suporte ao Azure para diferentes produtos SAP. 

Para a plataforma Business Objects BI, a nota de [suporte sap #2145537](https://launchpad.support.sap.com/#/notes/2145537) fornece uma lista de produtos SAP Business Objects suportados no Azure. Se houver perguntas sobre componentes ou combinações de versões de software e sO que parecem não estar listadas ou suportadas e que são mais recentes do que as versões mínimas listadas, você precisa abrir uma solicitação de suporte SAP contra o componente que você consulta apoio para.

Para serviços de dados de objetos de negócios, [a nota de suporte sap #22288344](https://launchpad.support.sap.com/#/notes/2288344) explica o suporte mínimo dos Serviços de Dados SAP em execução no Azure. 

> [!NOTE]
> Como indicado na nota de suporte sap, você precisa verificar no SAP PAM para identificar o nível correto do pacote de suporte a ser suportado no Azure

O suporte ao SAP Datahub/Vora no Azure Kubernetes Services (AKS) é detalhado na [nota de suporte sap #2464722](https://launchpad.support.sap.com/#/notes/2464722)

O suporte ao SAP BPC 10.1 SP08 é descrito na [nota de suporte sap #2451795](https://launchpad.support.sap.com/#/notes/2451795)

O suporte para sap Hybris Commerce Platform 5.x e 6.x no Azure é detalhado no [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud)


## <a name="next-steps"></a>Próximas etapas
Leia os próximos passos no planejamento e implementação do [Azure Virtual Machines para o SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)

