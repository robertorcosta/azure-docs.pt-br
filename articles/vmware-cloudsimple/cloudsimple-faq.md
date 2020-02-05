---
title: Perguntas frequentes-soluções VMware do Azure (AVS)
description: Perguntas frequentes sobre as soluções do Azure VMware (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c3808491c84f6c76a51c914aac6ee5e5ee370970
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025054"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-avs"></a>Perguntas frequentes sobre a solução VMware por AVS

## <a name="avs-service"></a>Serviço AVS

**O que é o AVS (Azure VMware Solutions)?**

As soluções do Azure VMware (AVS) transformam e ampliam cargas de trabalho do VMware para nuvens privadas e dedicadas no Azure em minutos. A AVS cuida do provisionamento, do gerenciamento da infraestrutura e da orquestração das cargas de trabalho entre o local e o Azure. Como seus aplicativos são executados exatamente no mesmo local e no Azure, você se beneficia da elasticidade e dos serviços da nuvem sem a complexidade de rearquitetar seus aplicativos. A AVS reduz o custo total de propriedade com um modelo de consumo de nuvem que fornece provisionamento sob demanda, pagamento conforme o crescimento e otimização da capacidade. Veja [o que é a solução VMware no Azure por AVS](cloudsimple-vmware-solutions-overview.md) para recursos, benefícios e cenários.

**O que é uma nuvem privada de AVS?**

Uma nuvem privada de AVS é uma nuvem privada e dedicada que consiste em um ambiente de computação, armazenamento e rede de alto desempenho implantado na infraestrutura de Microsoft Azure (hardware e espaço do Datacenter) nos locais do Azure. Uma nuvem privada de AVS fornece uma "plataforma como serviço" do VMware nativo. Nos termos do VMware, cada nuvem privada de AVS contém exatamente uma instância do vCenter Server. O vCenter Server gerencia vários nós ESXi contidos em um ou mais clusters vSphere, juntamente com o armazenamento de rede SAN virtual (vSAN) correspondente. Um serviço AVS pode conter várias nuvens do AVS privado em sua assinatura do Azure. Para obter mais detalhes, consulte [visão geral da sincronização de nuvem privada](cloudsimple-private-cloud.md).

**Onde o serviço AVS está disponível?**

A AVS está disponível nas regiões leste dos EUA, oeste dos EUA e Europa Ocidental com regiões adicionais em breve.

**Como fazer habilitar minha assinatura para a AVS?**

Você pode entrar em contato com seu representante de conta Microsoft em [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) para habilitar sua assinatura para o serviço AVS. Forneça sua ID de assinatura no email para o qual você deseja habilitar o serviço AVS. 

**Como fazer acessar o portal da AVS?**

Você acessa o portal da AVS da portal do Azure. Para obter detalhes, consulte [acessar o portal da AVS (soluções VMware) no portal do Azure](access-cloudsimple-portal.md).

**Como fazer aumentar a capacidade de uma nuvem privada de AVS?**

Para aumentar a capacidade, compre nós adicionais do portal do Azure e, em seguida, use os nós para expandir sua nuvem privada da AVS no portal da AVS. Você pode adicionar nós adicionais a um cluster vSphere existente ou adicioná-los a um novo cluster vSphere. Para obter detalhes, consulte [expandir uma nuvem privada de AVS](expand-private-cloud.md).

**O que acontece com minha nuvem privada da AVS durante a manutenção?**

A AVS fornece notificações vários dias antes de um intervalo de manutenção agendado. A manutenção é feita de maneira sem interrupções para garantir a disponibilidade da sua nuvem privada de AVS. A manutenção pode ser dos seguintes tipos:

* **Infraestrutura de AVS**. A infraestrutura da AVS foi projetada para ser altamente disponível. Durante esse tipo de intervalo de manutenção, os componentes redundantes são atualizados um de cada vez para evitar qualquer interrupção de serviço. Você mantém o acesso à sua nuvem privada do AVS privado, todas as máquinas virtuais, a conexão com a Internet da sua nuvem privada de AVS e conexões para o local ou o Azure.
* **Portal de sincronização automática**. Durante esse tipo de intervalo de manutenção, alguns recursos no portal da AVS podem estar desabilitados ou inacessíveis. A notificação antes do intervalo de manutenção inclui detalhes sobre limitações de recursos enquanto a manutenção está ocorrendo.

## <a name="connectivity"></a>Conectividade

**Quais são minhas opções de conectividade para a rede da região AVS?**

A AVS fornece as seguintes opções de conectividade para se conectar à sua rede de região da AVS. Várias opções podem ser usadas ao mesmo tempo.

* **Conexão de ExpressRoute do datacenter local para a rede da região AVS**. Esta é uma conexão privada segura de alta velocidade, baixa latência, que usa Alcance Global para ligar o circuito do ExpressRoute local ao seu circuito de ExpressRoute do AVS. Para obter instruções sobre como configurar a conexão, consulte [conectar-se do local para a AVS usando o ExpressRoute](on-premises-connection.md).
* **Conexão de ExpressRoute de sua rede virtual do Azure para sua rede de região da AVS**. Esta é uma conexão privada segura de alta velocidade e baixa latência que usa gateways de rede virtual para ligar sua rede virtual no Azure ao circuito do ExpressRoute do AVS. Para obter instruções sobre como configurar a conexão, consulte [conectar seu ambiente de nuvem privada da AVS à rede virtual do Azure usando o ExpressRoute](azure-expressroute-connection.md).
* **Conexão VPN site a site do seu datacenter local para sua rede de região AVS**. Essa é uma rede privada virtual segura do seu dispositivo VPN local para sua região de nuvem privada da AVS. Para obter detalhes, consulte [configurar gateways de VPN na rede AVS](vpn-gateway.md).

**Como fazer conectar-se a uma nuvem privada de AVS?**

Você pode exibir detalhes da sua nuvem privada de AVS no portal da AVS. Para se conectar ao vCenter que corresponde à sua nuvem privada de AVS, primeiro verifique se uma conexão de rede é estabelecida usando VPN site a site, VPN ponto a site ou ExpressRoute. Em seguida, inicie o portal da AVS na portal do Azure e clique em **Iniciar vSphere cliente** na home page ou na página de detalhes da nuvem privada da AVS.

**Qual é a vantagem dos circuitos do ExpressRoute?**

Um circuito do Azure ExpressRoute é uma conexão segura de alta velocidade, baixa latência. A AVS fornece um circuito de ExpressRoute dedicado por região por cliente. Usando esse circuito, você pode estabelecer uma conexão segura do local ou da sua assinatura do Azure.

**Quais são os custos de rede para se conectar à AVS? Os encargos de egresso se aplicam entre a AVS e o Azure, ou entre regiões?**

Não há nenhuma cobrança de sincronização automática para saída de rede. As taxas padrão do Azure se aplicam a qualquer tráfego de saída de sua rede virtual ou de seu circuito de ExpressRoute local.

## <a name="networking"></a>Rede

**Quais recursos de rede estão disponíveis para minha nuvem privada de AVS?**

Você pode provisionar VLANs (e suas sub-redes) e tabelas de firewall e atribuir endereços IP públicos que são mapeados para uma máquina virtual em execução na sua nuvem privada de AVS. Para obter detalhes sobre os recursos de rede, consulte [visão geral de VLANs e sub-redes](cloudsimple-vlans-subnets.md), [visão geral das tabelas de firewall](cloudsimple-firewall-tables.md)e [visão geral do endereço IP público](cloudsimple-public-ip-address.md).

**Como fazer configurar sub-redes diferentes para meus aplicativos em minha nuvem privada da AVS?**

Você cria VLANs em sua nuvem privada de AVS no portal da AVS. Depois de criar uma VLAN, você pode criar um grupo de portas distribuídas em seu vCenter de nuvem privada da AVS usando a VLAN e criar máquinas virtuais que estão conectadas ao grupo de portas distribuídas. Você pode habilitar as tabelas de firewall para VLAN/sub-rede e definir regras de firewall para proteger o tráfego de rede.

**Quais configurações de firewall estão disponíveis para minhas nuvens privadas da AVS?**

Você pode configurar regras para o tráfego Norte-Sul e leste-oeste. As regras são definidas em uma tabela de firewall. A tabela de firewall pode ser anexada a VLANs em sua nuvem privada de AVS. Para obter detalhes, consulte [configurar as tabelas e regras de firewall para nuvens privadas da AVS](firewall.md).

**Posso atribuir endereços IP públicos a VMs em meu ambiente de nuvem privada da AVS?**

No portal da AVS, você pode alocar um novo endereço IP público e associá-lo ao endereço IP privado de uma máquina virtual ou de um dispositivo. Você também pode criar novas regras de firewall ou aplicar regras de firewall existentes para permitir o tráfego de portas e endereços IP específicos no Portal. Para obter detalhes, consulte [alocar endereços IP públicos para o ambiente de nuvem privada da AVS](public-ips.md).

## <a name="security"></a>Segurança

**Quais são minhas opções de segurança na AVS?**

A AVS fornece os seguintes recursos de segurança para proteger seu ambiente de nuvem privada da AVS:

* **Criptografia de dados em repouso**. Você pode criptografar dados em repouso que residem no armazenamento do vSAN em sua nuvem privada de AVS. o vSAN dá suporte a servidores de gerenciamento de chaves externas, que podem ser implantados na vNet do Azure ou no ambiente local. Para obter detalhes, consulte [Configurar a criptografia vSAN para sua nuvem privada da AVS](vsan-encryption.md).
* **Segurança de rede**. Controle o fluxo de tráfego de rede com regras de firewall que se aplicam entre sua nuvem privada de AVS e a Internet, sua nuvem privada de AVS e seu ambiente local, ou em sub-redes da sua nuvem privada de AVS.
* **Conexão segura e privada**. Uma conexão segura e privada é estabelecida entre sua rede local e sua assinatura do Azure.

## <a name="compute"></a>Computação

**Que tipos de hosts estão disponíveis?**

A AVS oferece estes tipos de host:

* **Nó CS28:** CPU: 2x 2,2 GHz, total de 28 núcleos, 48 HT.  RAM: 256 GB.  Armazenamento: cache NVMe de 1600 GB, 5760 GB de dados (todos-flash). Rede: NIC 4x25Gbe
* **Nó CS36:** CPU 2x 2,3 GHz, total de 36 núcleos, 72 HT.  RAM: 512 GB.  Armazenamento: 3200 GB NVMe cache de 11520 GB de dados (todos-flash).  Rede: NIC 4x25Gbe
* **Nó CS36m:** CPU 2x 2,3 GHz, total de 36 núcleos, 72 HT.  RAM: 576 GB.  Armazenamento: 3200 GB NVMe cache de 13360 GB de dados (todos-flash).  Rede: NIC 4x25Gbe

**Como as falhas de hardware são tratadas?**

Toda a infraestrutura da AVS é monitorada continuamente pela plataforma AVS e pelas nossas equipes de operações de serviço. Se uma falha de hardware for detectada, um novo nó será adicionado à sua nuvem privada de AVS e o nó com falha será removido.

## <a name="storage"></a>Armazenamento

**Que tipo de armazenamento tem suporte em uma nuvem privada de AVS?**

A AVS oferece todos-flash de armazenamento da vSAN do VMware com cada nuvem privada de AVS. Cada vSphere é criado com seu próprio armazenamento de datavsan. Para obter detalhes, consulte [componentes de nuvem privada da sincronização de VMware-vSAN Storage](vmware-components.md#vsan-storage).

**Há suporte para a criptografia de dados?**
Sim. Você pode configurar o armazenamento vSAN na sua nuvem privada de AVS para usar um servidor de gerenciamento de chaves (KMS) que é implantado localmente ou no Azure para criptografar os dados armazenados no vSAN.

**Como os discos com falha são manipulados?**

A AVS monitora continuamente todos os componentes de hardware da nuvem privada da AVS. Se uma falha de disco for detectada ou um disco for identificado como com falha (com base em heurística), um novo nó será adicionado automaticamente à nuvem privada da AVS. O nó com o disco que falhou ou com falha é removido da nuvem privada da AVS.

## <a name="vmware"></a>VMware

**Como fazer executar upload ou migração em larga escala de aplicativos e dados do local?**

A AVS fornece uma solução VMware vSphere nativa. Todas as ferramentas do VMware para migração de dados em massa podem ser usadas com sua nuvem privada de AVS. As opções incluem:

* VMware HCX para migração em massa de dados.
* Migração fria de dados usando o Storage vMotion do local para a AVS.

**Posso instalar as ferramentas do VMware?**

A AVS fornece uma solução VMware vSphere nativa. Todas as ferramentas do VMware usadas para gerenciar seu ambiente vSphere local podem ser usadas na AVS. A AVS dá suporte a um modelo BYOL (traga sua própria licença) para a instalação das ferramentas do VMware.

**Como as atualizações são gerenciadas?**

A AVS gerencia e atualiza todos os componentes de infraestrutura da sua nuvem privada da AVS de maneira contínua sem interrupções. Todas as atualizações e patches de segurança lançados por fornecedores VMware ou de infraestrutura são agendados para atualização assim que são qualificados pela AVS.

A AVS não executa atualizações ou atualizações de aplicativos instalados na nuvem privada da AVS.

## <a name="azure-integration"></a>Integração do Azure

**Quais serviços do Azure têm suporte?**

A AVS fornece uma conexão do Azure ExpressRoute à sua assinatura no Azure. Todos os serviços em execução na sua assinatura podem se conectar à sua nuvem privada de AVS. Por exemplo:

* **Azure Active Directory** como uma fonte de identidade para seu AVS vCenter.
* **Armazenamento do Azure** para armazenar backups, imagens e outros dados da sua nuvem privada de AVS.
* **Aplicativos híbridos** com uma arquitetura de aplicativo que abrange nuvens privadas públicas e AVS. Por exemplo, você pode criar servidores Webno Azure que acessam aplicativos e servidores de banco de dados em sua nuvem privada de AVS.
* **Azure monitor** e **central de segurança do Azure** para cargas de trabalho em execução em log de suporte do VMware, métricas de desempenho e gerenciamento de segurança.

**Como fazer mapear meus locatários VMware para o Azure?**

A AVS fornece a capacidade exclusiva de gerenciar suas VMs VMware em uma nuvem privada de AVS na portal do Azure. Um pool de recursos do vCenter configurado com restrições de recursos desejados pode ser mapeado para sua assinatura pelo administrador global. 

**Quais benefícios de licenciamento obtenho com o Azure?**

Com a AVS, você pode aproveitar o benefício de uso híbrido do Azure e economizar até 90% em licenças. Esse benefício preserva seu investimento em licenças da Microsoft e reduz seu TCO em relação a outras soluções de nuvem. Você também obtém atualizações de segurança estendidas para o Windows Server 2008 e o Microsoft SQL Server 2008. O modelo BYOL (traga sua própria licença) ajuda a manter os custos baixos para aplicativos comuns, como Veeam e zerto. 
