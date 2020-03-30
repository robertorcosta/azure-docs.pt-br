---
title: Instalação do SAP HANA em máquinas virtuais Do Azure | Microsoft Docs
description: Guia para instalação do SAP HANA em VMs Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: e017e082472e7a4a2fab6a2845e52d3dc7acc460
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123363"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Instalação do SAP HANA em máquinas virtuais Azure
## <a name="introduction"></a>Introdução
Este guia ajuda você a apontar os recursos certos para implantar hana em máquinas virtuais Azure com sucesso. Este guia vai apontar os recursos de documentação que você precisa verificar antes de instalar o SAP HANA em uma VM Azure. Assim, que você seja capaz de executar as etapas certas para terminar com uma configuração suportada de SAP HANA em VMs Azure.  

> [!NOTE]
> Esta guia descreve as implantações do SAP HANA em VMs do Azure. Para obter informações sobre como implantar o SAP HANA em grandes instâncias HANA, consulte [Como instalar e configurar o SAP HANA (Instâncias Grandes) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation).
 
## <a name="prerequisites"></a>Pré-requisitos
Este guia também assume que você está familiarizado com:
* SAP HANA e SAP NetWeaver e como instalá-los no local.
* Como instalar e operar instâncias de aplicativos SAP HANA e SAP no Azure.
* Os conceitos e procedimentos documentados em:
   * Planejamento para implantação de SAP no Azure, que inclui o planejamento da Rede Virtual do Azure e o uso do Armazenamento Azure. Ver [SAP NetWeaver em Máquinas Virtuais Azure - Guia de planejamento e implementação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
   * Princípios de implantação e maneiras de implantar VMs no Azure. Veja [a implantação do Azure Virtual Machines para SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
   * Conceitos de alta disponibilidade para SAP HANA documentados no [SAP HANA de alta disponibilidade para máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)

## <a name="step-by-step-before-deploying"></a>Passo a passo antes de implantar
Nesta seção, as diferentes etapas são listadas que você precisa executar antes de começar com a instalação do SAP HANA em uma máquina virtual Azure. A ordem é enumerada e, como tal, deve ser seguida como enumerada:

1. Nem todos os cenários de implantação possíveis são suportados no Azure. Portanto, você deve verificar a carga de trabalho SAP do documento [em cenários suportados por máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) para o cenário que você tem em mente com a sua implantação do SAP HANA. Se o cenário não estiver listado, você precisa assumir que ele não foi testado e, como resultado, não é suportado
2. Supondo que você tenha uma ideia aproximada sobre o seu requisito de memória para a sua implantação sap HANA, você precisa encontrar uma VM Azure adequada. Nem todas as VMs certificadas para SAP NetWeaver, como documentado na [nota de suporte SAP #1928533,](https://launchpad.support.sap.com/#/notes/1928533)são certificadas pela SAP HANA. A fonte da verdade para as VMs Azure certificadas pela SAP HANA é o diretório de [hardware SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) As unidades que começam com **S** são unidades [HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) e não VMs Azure.
3. Diferentes tipos de VM do Azure têm diferentes versões mínimas do sistema operacional para SUSE Linux ou Red Hat Linux. No diretório de [hardware SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)do site, você precisa clicar em uma entrada na lista de unidades certificadas SAP HANA para obter dados detalhados desta unidade. Além da carga de trabalho hana suportada, as versões do Sistema Operacional que são suportadas com essas unidades para SAP HANA são listadas
4. A partir das versões do sistema operacional, você precisa considerar certas versões mínimas do kernel. Essas versões mínimas estão documentadas nestas notas de suporte SAP:
    - [Nota de suporte sap #2814271 backup SAP HANA falha no Azure com erro de soma de verificação](https://launchpad.support.sap.com/#/notes/2814271)
    - [Nota de suporte sap #2753418 degradação potencial de desempenho devido ao recuo do temporizador](https://launchpad.support.sap.com/#/notes/2753418)
    - [Nota de suporte sap #2791572 degradação de desempenho devido à falta de suporte vdso para hiper-v no Azure](https://launchpad.support.sap.com/#/notes/2791572)
4. Com base na versão do Sistema Operacional que é suportada para o tipo de escolha da máquina virtual, você precisa verificar se a versão sap HANA desejada é suportada com essa versão do sistema operacional. Leia [a nota de suporte sap #2235581](https://launchpad.support.sap.com/#/notes/2235581) para uma matriz de suporte de versões SAP HANA com as diferentes versões do sistema operacional.
5. Como você pode ter encontrado uma combinação válida do tipo Azure VM, liberação do sistema operacional e versão SAP HANA, você precisa verificar na Matriz de Disponibilidade de Produtos SAP. Na Matriz de Disponibilidade SAP, você pode descobrir se o produto SAP que deseja executar contra seu banco de dados SAP HANA é suportado.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>Implantação passo-a-passo da VM e considerações do sistema operacional convidado
Nesta fase, você precisa passar pelas etapas de implantação dos VM(s) para instalar hana e eventualmente otimizar o sistema operacional escolhido após a instalação.

1. Escolheu a imagem base da galeria Azure. Se você quiser construir sua própria imagem do sistema operacional para O SAP HANA, você precisa conhecer todos os diferentes pacotes necessários para uma instalação sap hana bem sucedida. Caso contrário, recomenda-se o uso das imagens SUSE e Red Hat para SAP ou SAP HANA fora da galeria de imagens Azure. Essas imagens incluem os pacotes necessários para uma instalação HANA bem sucedida. Com base no seu contrato de suporte com o provedor do sistema operacional, você precisa escolher uma imagem onde você traz sua própria licença. Ou você escolhe uma imagem do sistema operacional que inclui suporte
2. Se você escolheu uma imagem do sistema operacional convidado que exige que você traga sua própria licença, você precisa registrar a imagem do SO com sua assinatura, para que você possa baixar e aplicar os patches mais recentes. Essa etapa vai exigir acesso público à internet. A menos que você configure sua instância privada de, por exemplo, um servidor SMT no Azure.
3. Decida a configuração de rede da VM. Você pode ler mais informações no documento [Configurações e operações de infra-estrutura SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations). Tenha em mente que não há cotas de throughput de rede que você pode atribuir a cartões de rede virtuais no Azure. Como resultado, o único propósito de direcionar o tráfego através de diferentes vNICs é baseado em considerações de segurança. Confiamos que você encontre um compromisso suportado entre a complexidade do roteamento de tráfego através de vários vNICs e os requisitos impostos pelos aspectos de segurança.
3. Aplique os patches mais recentes no sistema operacional assim que a VM for implantada e registrada. Registrado com sua própria assinatura. Ou no caso de você escolher uma imagem que inclua suporte ao sistema operacional, a VM já deve ter acesso aos patches. 
4. Aplique as músicas necessárias para SAP HANA. Essas músicas estão listadas nestas notas de suporte sap:

    - [Nota de suporte sap #2694118 - Red Hat Enterprise Linux HA Add-On no Azure](https://launchpad.support.sap.com/#/notes/2694118)
    - [Nota de suporte sap #1984787 - SUSE LINUX Enterprise Server 12: Notas de instalação](https://launchpad.support.sap.com/#/notes/1984787) 
    - [Nota de suporte sap #2578899 - SUSE Linux Enterprise Server 15: Nota de instalação](https://launchpad.support.sap.com/#/notes/2578899)
    - [Nota de suporte sap #2002167 - Red Hat Enterprise Linux 7.x: Instalação e Atualização](https://launchpad.support.sap.com/#/notes/0002002167)
    - [Nota de suporte SAP n° 2292690 – Banco de dados SAP HANA: configurações de sistema operacional recomendadas para RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690) 
    -  [Nota de suporte sap #2772999 - Red Hat Enterprise Linux 8.x: Instalação e Configuração](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [Nota de suporte sap #2777782 - SAP HANA DB: Configurações recomendadas do sistema operacional para RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
    -  [Nota de suporte sap #2455582 - Linux: Executando aplicativos SAP compilados com GCC 6.x](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [Nota de suporte sap #2382421 - Otimizando a configuração de rede em HANA e nível de osS](https://launchpad.support.sap.com/#/notes/2382421)

1. Selecione o tipo de armazenamento Azure para SAP HANA. Nesta etapa, você precisa decidir sobre o layout de armazenamento para a instalação do SAP HANA. Você vai usar discos Azure anexados ou ações nativas do Azure NFS. Os tipos de armazenamento Do Zure que ou suportados e combinações de diferentes tipos de armazenamento Azure que podem ser usados, estão documentados nas [configurações de armazenamento de máquinas virtuais SAP HANA Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) Tome as configurações documentadas como ponto de partida. Para sistemas não-produzidos, você pode ser capaz de configurar throughput inferior ou IOPS. Para fins de produção, você pode precisar configurar um pouco mais de throughput e IOPS.
2. Certifique-se de que você configurou [o Azure Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) para seus volumes que contêm os logs de transação DBMS ou registros de refazer quando estiver usando VMs da série M-Series ou Mv2. Esteja ciente das limitações do Write Accelerator conforme documentado.
2. Verifique se a rede acelerada do [Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está habilitada nas VM(s) implantadas.

> [!NOTE]
> Nem todos os comandos nos diferentes perfis de sap-tune ou como descrito nas notas podem ser executados com sucesso no Azure. Comandos que manipulariam o modo de alimentação das VMs geralmente retornam com um erro, já que o modo de alimentação do hardware host Azure subjacente não pode ser manipulado.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Preparações passo a passo específicas para máquinas virtuais Do Zure
Uma das especificidades do Azure é a instalação de uma extensão Azure VM que fornece dados de monitoramento para o Agente host SAP. Os detalhes sobre a instalação desta extensão de monitoramento estão documentados em:

-  [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) discute monitoramento aprimorado do SAP com VMs Linux no Azure 
-  [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) discute informações sobre SAPOSCOL no Linux 
-  [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) discute principais métricas de monitoramento para SAP no Microsoft Azure
-  [Implantação de Máquinas Virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>Instalação do SAP HANA
Com as máquinas virtuais Do Azure implantadas e os sistemas operacionais registrados e configurados, você pode instalar o SAP HANA de acordo com a instalação SAP. Como um bom começo para chegar a esta documentação, comece com este site SAP [recursos HANA](https://www.sap.com/products/hana/implementation/resources.html)

Para configurações de escala SAP HANA usando discos conectados diretos do Azure Premium Storage ou ultra disk, leia as especificidades no documento [Configurações e operações de infra-estrutura SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out)


## <a name="additional-resources-for-sap-hana-backup"></a>Recursos adicionais para backup SAP HANA
Para obter informações sobre como fazer backup de bancos de dados SAP HANA em VMs do Azure, consulte:
* [Guia de backup para SAP HANA nas Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [Backup do Azure do SAP HANA no nível do arquivo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)

## <a name="next-steps"></a>Próximas etapas
Leia a documentação:

- [Configurações e operações de infraestrutura do SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Configurações de armazenamento de máquina virtual do SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)





