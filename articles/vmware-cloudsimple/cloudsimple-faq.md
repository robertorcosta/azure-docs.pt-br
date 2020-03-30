---
title: FAQ - Solução Azure VMware por CloudSimple
description: Perguntas frequentes para a Solução Azure VMware pela CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d8c2974ea372dd59d15a9f2cc31ed171acd932
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025054"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Perguntas frequentes sobre a Solução VMware pela CloudSimple

## <a name="cloudsimple-service"></a>Serviço CloudSimple

**O que é a solução Azure VMware da CloudSimple?**

A Solução VMware do CloudSimple transforma e estende as cargas de trabalho do VMware para nuvens privadas e dedicadas no Azure em minutos. O CloudSimple cuida do provisionamento, gerenciamento da infra-estrutura e orquestração de cargas de trabalho entre o local e o Azure. Como seus aplicativos são exatamente os mesmos no local e no Azure, você se beneficia da elasticidade e dos serviços da nuvem sem a complexidade de rearquitetar seus aplicativos. O CloudSimple reduz seu custo total de propriedade com um modelo de consumo em nuvem que fornece provisionamento sob demanda, pay-as-you-grow e otimização de capacidade.  Veja [o que é a solução VMware no Azure by CloudSimple](cloudsimple-vmware-solutions-overview.md) para obter recursos, benefícios e cenários.

**O que é uma Nuvem Privada CloudSimple?**

A CloudSimple Private Cloud é uma nuvem privada e dedicada que consiste em um ambiente de computação, armazenamento e rede de alto desempenho implantado na infra-estrutura do Microsoft Azure (espaço de hardware e data center) em locais do Azure.  Uma Nuvem Privada fornece uma "plataforma como serviço" nativa do VMware. Em termos VMware, cada Nuvem Privada contém exatamente uma instância do vCenter Server. O vCenter Server gerencia vários nós ESXi contidos em um ou mais clusters vSphere, juntamente com o armazenamento correspondente de SAN Virtual (vSAN). Um serviço CloudSimple pode conter várias nuvens privadas em sua assinatura do Azure.  Para obter mais detalhes, consulte [visão geral do Private Cloud](cloudsimple-private-cloud.md).

**Onde o serviço CloudSimple está disponível?**

CloudSimple está disponível nas regiões leste dos EUA, Oeste dos EUA e Europa Ocidental com regiões adicionais em breve.

**Como habilitar minha assinatura para o CloudSimple?**

Você pode entrar em [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) contato com o representante da sua conta microsoft para habilitar sua assinatura para o serviço CloudSimple. Forneça seu ID de assinatura no e-mail para o qual você deseja o serviço CloudSimple ativado.  

**Como acessar o portal CloudSimple?**

Você acessa o portal CloudSimple a partir do portal Azure.  Para obter detalhes, consulte [Acesse a solução VMware pelo portal CloudSimple no portal Azure](access-cloudsimple-portal.md).

**Como aumentar a capacidade de uma Nuvem Privada?**

Para aumentar a capacidade, compre outros nódulos do portal Azure e, em seguida, use os álos para expandir sua Nuvem Privada a partir do portal CloudSimple.  Você pode adicionar nomes adicionais a um cluster vSphere existente ou adicioná-los a um novo cluster vSphere.  Para obter detalhes, consulte [Expanda uma nuvem privada do CloudSimple](expand-private-cloud.md).

**O que acontece com minha Nuvem Privada durante a manutenção?**

O CloudSimple fornece notificação vários dias antes de um intervalo de manutenção programado.  A manutenção é feita de forma sem interrupções para garantir a disponibilidade da sua Nuvem Privada.  A manutenção pode ser dos seguintes tipos:

* **Infra-estrutura CloudSimple**.  A infra-estrutura CloudSimple foi projetada para estar altamente disponível.  Durante este tipo de intervalo de manutenção, os componentes redundantes são atualizados um de cada vez para evitar qualquer interrupção de serviço. Você mantém acesso ao seu vCenter private cloud, todas as máquinas virtuais, a conexão com a internet da sua Nuvem Privada e conexões com o local ou o Azure.
* **Portal CloudSimple**. Durante esse tipo de intervalo de manutenção, alguns recursos no portal CloudSimple podem ser desativados ou inacessíveis.  A notificação antes do intervalo de manutenção inclui detalhes sobre as limitações de recursos durante a manutenção.

## <a name="connectivity"></a>Conectividade

**Quais são as minhas opções de conectividade para a rede de região CloudSimple?**

O CloudSimple oferece as seguintes opções de conectividade para se conectar à sua rede de região CloudSimple. Várias opções podem ser usadas ao mesmo tempo.

* **Conexão ExpressRoute do seu datacenter local para a rede de região CloudSimple**. Esta é uma conexão privada segura e de alta velocidade, de baixa latência, que usa o Global Reach para conectar o circuito ExpressRoute no local ao circuito CloudSimple ExpressRoute. Para obter instruções sobre como configurar a conexão, consulte [Conecte-se do local ao CloudSimple usando o ExpressRoute](on-premises-connection.md).
* **Conexão ExpressRoute da sua rede virtual Azure à sua rede de região CloudSimple**. Esta é uma conexão privada segura e de alta velocidade, de baixa latência, que usa gateways de rede virtuais para conectar sua rede virtual no Azure ao circuito CloudSimple ExpressRoute. Para obter instruções sobre como configurar a conexão, consulte [Conecte seu ambiente CloudSimple Private Cloud à rede virtual Azure usando o ExpressRoute](azure-expressroute-connection.md).
* **Conexão VPN site-to-site do seu datacenter local para a rede de região CloudSimple**. Esta é uma rede privada virtual segura do seu dispositivo VPN local para a região da Nuvem Privada Cloud.  Para obter detalhes, consulte [Configurar gateways VPN na rede CloudSimple](vpn-gateway.md).

**Como me conectar a uma nuvem privada?**

Você pode visualizar detalhes da sua Nuvem Privada no portal CloudSimple. Para se conectar ao vCenter que corresponde à sua Nuvem Privada, verifique primeiro se uma conexão de rede é estabelecida usando VPN site-to-site, VPN ponto a site ou ExpressRoute. Em seguida, inicie o portal CloudSimple a partir do portal Azure e clique em **Iniciar o cliente vSphere** na página inicial ou na página de detalhes da Nuvem Privada.

**Qual é a vantagem dos circuitos ExpressRoute?**

Um circuito Azure ExpressRoute é uma conexão segura e de alta velocidade.  O CloudSimple fornece um circuito ExpressRoute dedicado por região por cliente.  Usando este circuito, você pode estabelecer uma conexão segura a partir do local ou da sua assinatura do Azure.

**Quais são os custos de rede para se conectar ao CloudSimple?  Alguma cobrança de egresso se aplica entre cloudsimple e azure, ou entre regiões?**

Não há cobrança do CloudSimple para a saída de rede.  As taxas padrão do Azure se aplicam a qualquer tráfego de saída de sua rede virtual ou do circuito ExpressRoute no local.

## <a name="networking"></a>Rede

**Quais recursos de rede estão disponíveis para o meu Private Cloud?**

Você pode provisionar VLANs (e suas sub-redes) e tabelas de firewall e atribuir endereços IP públicos que mapeiam para uma máquina virtual em execução em sua Nuvem Privada. Para obter detalhes sobre os recursos de rede, consulte [VLANs e sub-redes visão geral,](cloudsimple-vlans-subnets.md) [visão geral das tabelas de firewall](cloudsimple-firewall-tables.md)e visão geral do endereço IP [público](cloudsimple-public-ip-address.md).

**Como configurar diferentes sub-redes para minhas aplicações na minha Nuvem Privada?**

Você cria VLANs em sua Nuvem Privada a partir do portal CloudSimple.  Depois de criar uma VLAN, você pode criar um grupo de portas distribuídas no seu vCenter de Nuvem Privada usando o VLAN e criar máquinas virtuais conectadas ao grupo de portas distribuídas.  Você pode habilitar tabelas de firewall para a VLAN/sub-rede e definir regras de firewall para proteger o tráfego de rede.

**Quais configurações de firewall estão disponíveis para minhas nuvens privadas?**

Você pode configurar regras para o tráfego norte-sul e leste-oeste.  As regras são definidas em uma tabela de firewall.  A tabela de firewall pode ser anexada a VLANs em sua Nuvem Privada.  Para obter detalhes, [consulte Configurar tabelas de firewall e regras para Nuvens Privadas](firewall.md).

**Posso atribuir endereços IP públicos a VMs no meu ambiente Private Cloud?**

No portal CloudSimple, você pode alocar um novo endereço IP público e associá-lo com o endereço IP privado de uma máquina virtual ou um aparelho.  Você também pode criar novas regras de firewall ou aplicar regras de firewall existentes para permitir o tráfego de portas específicas e endereços IP no portal. Para obter detalhes, consulte [Alocar endereços IP públicos para o ambiente Private Cloud](public-ips.md).

## <a name="security"></a>Segurança

**Quais são as minhas opções de segurança no CloudSimple?**

O CloudSimple fornece os seguintes recursos de segurança para proteger seu ambiente de Nuvem Privada:

* **Dados em criptografia de repouso**. Você pode criptografar dados em repouso que residem no armazenamento vSAN em sua Nuvem Privada. O vSAN suporta servidores de gerenciamento de chaves externas, que podem ser implantados no ambiente azure vNet ou no local.  Para obter detalhes, consulte [Configurar a criptografia vSAN para sua Nuvem Privada CloudSimple](vsan-encryption.md).
* **Segurança da rede**. Controle o fluxo de tráfego de rede com regras de firewall que se aplicam entre sua Nuvem Privada e a internet, seu ambiente de Nuvem Privada e no local, ou dentro de sub-redes da sua Nuvem Privada.
* **Conexão segura e privada.** Uma conexão segura e privada é estabelecida entre sua rede local e sua assinatura do Azure.

## <a name="compute"></a>Computação

**Que tipo de hosts estão disponíveis?**

O CloudSimple oferece esses tipos de host:

* **Nó CS28:** CPU:2x 2,2 GHz, total de 28 núcleos, 48 HT.  RAM: 256 GB.  Armazenamento: cache NVMe de 1600 GB, dados de 5760 GB (All-Flash). Rede: 4x25Gbe NIC
* **Nó CS36:** CPU 2x 2,3 GHz, total de 36 núcleos, 72 HT.  RAM: 512 GB.  Armazenamento: 3200 GB NVMe cache 11520 GB dados (All-Flash).  Rede: 4x25Gbe NIC
* **Nó CS36m:** CPU 2x 2,3 GHz, total de 36 núcleos, 72 HT.  RAM: 576 GB.  Armazenamento: 3200 GB NVMe cache 13360 GB dados (All-Flash).  Rede: 4x25Gbe NIC

**Como são tratadas as falhas de hardware?**

Toda a infra-estrutura cloudsimple é continuamente monitorada pela plataforma CloudSimple e nossas equipes de operações de serviço.  Se uma falha de hardware for detectada, um novo nó será adicionado à sua Nuvem Privada e o nó com falha será removido.

## <a name="storage"></a>Armazenamento

**Que tipo de armazenamento é suportado em uma Nuvem Privada?**

O CloudSimple oferece armazenamento vSAN vSAN vSAN com todas as Nuvem Privada.  Cada vSphere é criado com seu próprio datastore vSAN.  Para obter detalhes, consulte [componentes private Cloud VMware - armazenamento vSAN](vmware-components.md#vsan-storage).

**A criptografia de dados é suportada?**
Sim.  Você pode configurar o armazenamento vSAN em sua Nuvem Privada para usar um servidor de gerenciamento de chaves (KMS) que é implantado no local ou no Azure para criptografar dados armazenados no vSAN.

**Como os discos com falha são tratados?**

O CloudSimple monitora continuamente todos os componentes de hardware da Nuvem Privada.  Se uma falha de disco for detectada ou um disco for identificado como falha (com base na heurística), um novo nó será adicionado automaticamente à Nuvem Privada.  O nó com o disco com falha ou falha é removido da Nuvem Privada.

## <a name="vmware"></a>VMware

**Como faço para realizar o upload ou a migração em larga escala de aplicativos e dados de locais?**

O CloudSimple fornece uma solução vSphere nativa.  Todas as ferramentas VMware para migração de dados em massa podem ser usadas com sua Nuvem Privada.  As opções incluem:

* VMware HCX para migração em massa de dados.
* Migração fria de dados usando o storage vMotion de on-premises para CloudSimple.

**Posso instalar alguma ferramenta VMware?**

O CloudSimple fornece uma solução vSphere nativa.  Todas as ferramentas VMware usadas para gerenciar seu ambiente vSphere no local podem ser usadas no CloudSimple.  O CloudSimple suporta um modelo BYOL (bring-your-own-license) para instalar ferramentas VMware.

**Como as atualizações e atualizações são gerenciadas?**

O CloudSimple gerencia e atualiza todos os componentes de infra-estrutura do seu Private Cloud de forma perfeita e sem interrupções.  Todas as atualizações e patches de segurança liberados pela VMware ou fornecedores de infra-estrutura são programados para atualização assim que forem qualificados pelo CloudSimple.

O CloudSimple não realiza atualizações ou atualizações de aplicativos instalados na Nuvem Privada.

## <a name="azure-integration"></a>Integração do Azure

**Quais serviços do Azure são suportados?**

O CloudSimple fornece uma conexão Azure ExpressRoute à sua assinatura no Azure.  Todos os serviços em execução em sua assinatura podem se conectar à sua Nuvem Privada.  Os exemplos incluem:

* **Azure Active Directory** como fonte de identidade para o seu VCenter CloudSimple.
* **Armazenamento azure** para armazenar backups, imagens e outros dados da sua Nuvem Privada.
* **Aplicações híbridas** com uma arquitetura de aplicativo que abrange nuvens públicas e privadas.  Por exemplo, você pode criar servidores web no Azure que acessam servidores de aplicativos e bancos de dados em sua Nuvem Privada.
* **Monitor azure** e **centro de segurança do Azure** para cargas de trabalho em execução no registro de suporte ao VMware, métricas de desempenho e gerenciamento de segurança.

**Como mapeio meus inquilinos da VMware para o Azure?**

O CloudSimple oferece a capacidade única de gerenciar suas VMS VMware na Nuvem Privada a partir do portal Azure.  Um pool de recursos do vCenter configurado com as restrições de recursos desejadas pode ser mapeado para sua assinatura pelo administrador global.  

**Que benefícios de licenciamento eu recebo com o Azure?**

Com o CloudSimple, você pode aproveitar o Benefício de Uso Híbrido do Azure e economizar até 90% em licenças. Esse benefício preserva seu investimento em licenças Microsoft e reduz seu TCO em relação a outras soluções em nuvem. Você também recebe atualizações de segurança estendidas para o Windows Server 2008 e o Microsoft SQL Server 2008.  O modelo bring-your-own-license (BYOL) ajuda você a manter os custos baixos para aplicativos comuns como Veeam e Zerto.  
