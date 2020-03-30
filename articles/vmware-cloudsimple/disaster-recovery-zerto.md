---
title: Azure VMware Solution by CloudSimple - Use a Private Cloud como local de desastre para cargas de trabalho no local
description: Descreve como configurar sua Cloud Private Cloud cloud como um site de recuperação de desastres para cargas de trabalho VMware no local
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e019a9229b671be2fb73e758bd39f33657bc2d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083142"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>Configure o CloudSimple Private Cloud como um local de recuperação de desastres para cargas de trabalho vMware no local

O CloudSimple Private Cloud pode ser configurado como um local de recuperação para aplicativos locais para fornecer continuidade de negócios em caso de desastre. A solução de recuperação é baseada na Replicação Virtual zerto como a plataforma de replicação e orquestração. As máquinas virtuais de infra-estrutura crítica e aplicativos podem ser replicadas continuamente do seu vCenter no local para a sua Nuvem Privada. Você pode usar sua Nuvem Privada para testes de failover e para garantir a disponibilidade de seu aplicativo durante um desastre. Uma abordagem semelhante pode ser seguida para configurar a Nuvem Privada como um local principal que é protegido por um local de recuperação em um local diferente.

> [!NOTE]
> Consulte o documento Zerto [Dimensionando considerações para replicação virtual zerto](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) para obter orientações sobre o dimensionamento do ambiente de recuperação de desastres.

A solução CloudSimple:

* Elimina a necessidade de configurar um datacenter especificamente para recuperação de desastres (DR).
* Permite aproveitar os locais do Azure onde o CloudSimple é implantado para resiliência geográfica mundial.
* Dá-lhe uma opção para reduzir os custos de implantação e o custo total de propriedade para dr.

A solução exige que você:

* Instale, configure e gerencie zerto em sua Nuvem Privada.
* Forneça suas próprias licenças para zerto quando a Nuvem Privada for o site protegido. Você pode emparelhar zerto em execução no site do CloudSimple com seu site no local para licenciamento.

A figura a seguir mostra a arquitetura da solução Zerto.

![Arquitetura](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>Como implantar a solução

As seções a seguir descrevem como implantar uma solução DR usando a Replicação Virtual Zerto em sua Nuvem Privada.

1. [Pré-requisitos](#prerequisites)
2. [Configuração opcional no CloudSimple Private Cloud](#optional-configuration-on-your-private-cloud)
3. [Configure ZVM e VRA em CloudSimple Private Cloud](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Configurar o Zerto Virtual Protection Group](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Pré-requisitos

Para habilitar a replicação virtual zerto do seu ambiente local para a sua Nuvem Privada, complete os seguintes pré-requisitos.

1. [Configure uma conexão VPN site-to-site entre sua rede local e sua Nuvem Privada CloudSimple](set-up-vpn.md).
2. [Configure a procurar DNS para que seus componentes de gerenciamento de nuvem privada sejam encaminhados para servidores DNS privados em nuvem](on-premises-dns-setup.md).  Para habilitar o encaminhamento da busca dns, crie uma entrada de zona `*.cloudsimple.io` de encaminhamento em seu servidor DNS no local para servidores DNS cloudsimple.
3. Configure a busca do DNS para que os componentes do vCenter no local sejam encaminhados para servidores DNS no local.  Os servidores DNS devem ser acessíveis a partir de sua Cloud Simple Private Cloud sobre vpn site-to-site. Para assistência, envie uma [solicitação de suporte,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)fornecendo as seguintes informações.  

    * Nome de domínio DNS no local
    * Endereços IP do servidor DNS no local

4. Instale um servidor Windows em sua Nuvem Privada. O servidor é usado para instalar o Zerto Virtual Manager.
5. [Aumente seus privilégios CloudSimple](escalate-private-cloud-privileges.md).
6. Crie um novo usuário no seu vCenter privado da Nuvem com a função administrativa a ser usada como conta de serviço para zerto virtual manager.

### <a name="optional-configuration-on-your-private-cloud"></a>Configuração opcional em sua Nuvem Privada

1. Crie um ou mais pools de recursos no seu vCenter privado da Nuvem para usar como pools de recursos de destino para VMs do seu ambiente local.
2. Crie uma ou mais pastas no seu vCenter privado da Nuvem para usar como pastas de destino para VMs do seu ambiente local.
3. Crie VLANs para rede de failover e configure regras de firewall. Abra um [pedido de ajuda.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
4. Crie grupos de portas distribuídas para rede de failover e rede de teste para testar failover de VMs.
5. Instale [servidores DHCP e DNS](dns-dhcp-setup.md) ou use um controlador de domínio do Active Directory em seu ambiente Private Cloud.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>Configure ZVM e VRA em sua Nuvem Privada

1. Instale o Zerto Virtual Manager (ZVM) no servidor Windows em sua Nuvem Privada.
2. Faça login na ZVM usando a conta de serviço criada em etapas anteriores.
3. Configure o licenciamento do Zerto Virtual Manager.
4. Instale o Zerto Virtual Replication Appliance (VRA) nos hosts ESXi da sua Nuvem Privada.
5. Emparelhe seu Private Cloud ZVM com seu ZVM no local.

### <a name="set-up-zerto-virtual-protection-group"></a>Configurar o Zerto Virtual Protection Group

1. Crie um novo VPG (Virtual Protection Group, grupo de proteção virtual) e especifique a prioridade para o VPG.
2. Selecione as máquinas virtuais que requerem proteção para continuidade de negócios e personalize a ordem de inicialização, se necessário.
3. Selecione o site de recuperação como sua Nuvem Privada e o servidor de recuperação padrão como o cluster Private Cloud ou o grupo de recursos que você criou. Selecione **vsanDatastore** para o armazenamento de dados de recuperação em sua Nuvem Privada.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > Você pode personalizar a opção host para VMs individuais na opção Configurações de VM.

4. Personalize as opções de armazenamento conforme necessário.
5. Especifique as redes de recuperação a serem usadas para rede de failover e rede de teste failover como os grupos de portas distribuídas criados anteriormente e personalize os scripts de recuperação conforme necessário.
6. Personalize as configurações de rede para VMs individuais, se necessário, e crie o VPG.
7. Failover de teste assim que a replicação estiver concluída.

## <a name="reference"></a>Referência

[Documentação zerto](https://www.zerto.com/myzerto/technical-documentation/)
