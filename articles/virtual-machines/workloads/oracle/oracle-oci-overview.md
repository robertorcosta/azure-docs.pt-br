---
title: Integre o Microsoft Azure com a Oracle Cloud Infrastructure | Microsoft Docs
description: Conheça as soluções que integram aplicativos Oracle em execução no Microsoft Azure com bancos de dados em Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: b5d5ebda850163989e6ef1af94ec623d2d758f07
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878282"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>Soluções de aplicativos Oracle integrando a Infra-estrutura de nuvem Microsoft Azure e Oracle

A Microsoft e a Oracle fizeram uma parceria para fornecer baixa latência, alta conectividade entre nuvens de throughput, permitindo que você aproveite o melhor de ambas as nuvens. 

Usando essa conectividade entre nuvens, você pode particionar um aplicativo de vários níveis para executar seu nível de banco de dados no Oracle Cloud Infrastructure (OCI), e o aplicativo e outros níveis no Microsoft Azure. A experiência é semelhante a executar toda a pilha de soluções em uma única nuvem. 

Se você estiver interessado em implantar soluções Oracle inteiramente na infra-estrutura do Azure, consulte [imagens Oracle VM e sua implantação no Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Visão geral do cenário

A conectividade entre nuvens fornece uma solução para você executar os aplicativos líderes do setor da Oracle e seus próprios aplicativos personalizados, em máquinas virtuais do Azure, enquanto desfruta dos benefícios dos serviços de banco de dados hospedados no OCI. 

> [!IMPORTANT]
> A Oracle certificará esses aplicativos para serem executados no Azure ao usar a solução de interconexão Azure/Oracle Cloud até maio de 2020.

Os aplicativos que você pode executar em uma configuração entre nuvens incluem:

* Suíte E-Business
* JD Edwards EnterpriseOne
* Peoplesoft
* Aplicativos oracle de varejo
* Oracle Hyperion Gestão Financeira

O diagrama a seguir é uma visão geral de alto nível da solução conectada. Para simplificar, o diagrama mostra apenas um nível de aplicativo e um nível de dados. Dependendo da arquitetura do aplicativo, sua solução pode incluir níveis adicionais, como um nível web no Azure. Para obter mais informações, consulte as próximas seções.

![Visão geral da solução Azure OCI](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Disponibilidade da região 

A conectividade entre nuvens é limitada às seguintes regiões:
* Azure East EUA (eastus) & OCI Ashburn (Leste dos EUA)
* Azure UK South (uksouth) & OCI London (Reino Unido Sul)
* Azure Canada Central (canadacentral) & OCI Toronto (Canadá Sudeste)
* Azure West Europe (europa ocidental) & OCI Amsterdam (Noroeste dos Países Baixos)

## <a name="networking"></a>Rede

Os clientes corporativos geralmente optam por diversificar e implantar cargas de trabalho em várias nuvens por várias razões comerciais e operacionais. Para diversificar, os clientes interconectam redes em nuvem usando a internet, ipsec VPN ou usando a solução de conectividade direta do provedor de nuvem através de sua rede local. A interconexão de redes em nuvem pode exigir investimentos significativos em tempo, dinheiro, design, aquisição, instalação, testes e operações. 

Para enfrentar esses desafios ao cliente, a Oracle e a Microsoft habilitaram uma experiência integrada em várias nuvens. A rede entre nuvens é estabelecida conectando um circuito [ExpressRoute](../../../expressroute/expressroute-introduction.md) no Microsoft Azure com um circuito [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) em OCI. Essa conectividade é possível quando um local de peering do Azure ExpressRoute está próximo ou no mesmo local de peering do OCI FastConnect. Esta configuração permite uma conectividade segura e rápida entre as duas nuvens sem a necessidade de um provedor de serviços intermediário.

Usando expressroute e FastConnect, os clientes podem espiar uma rede virtual no Azure com uma rede de nuvem virtual no OCI, desde que o espaço de endereço IP privado não se sobreponha. Peering das duas redes permite que um recurso na rede virtual se comunique com um recurso na rede de nuvem virtual OCI como se ambos estivessem na mesma rede.

## <a name="network-security"></a>Segurança de rede

A segurança de rede é um componente crucial de qualquer aplicativo corporativo e é central para essa solução multi-nuvem. Qualquer tráfego que passa pelo ExpressRoute e pelo FastConnect passa por uma rede privada. Essa configuração permite uma comunicação segura entre uma rede virtual Azure e uma rede de nuvem virtual Oracle. Você não precisa fornecer um endereço IP público para nenhuma máquina virtual no Azure. Da mesma forma, você não precisa de um gateway de internet no OCI. Toda comunicação acontece através do endereço IP privado das máquinas.

Além disso, você pode configurar listas de [segurança](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) em sua rede virtual de nuvem OCI e regras de segurança (anexadas a [grupos de segurança de rede](../../../virtual-network/security-overview.md)Do Zure). Use essas regras para controlar o tráfego entre máquinas nas redes virtuais. As regras de segurança da rede podem ser adicionadas a nível de máquina, em um nível de sub-rede, bem como no nível de rede virtual.
 
## <a name="identity"></a>Identidade

A identidade é um dos pilares centrais da parceria entre a Microsoft e a Oracle. Um trabalho significativo foi feito para integrar o [Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) com o [Azure Active Directory](../../../active-directory/index.yml) (Azure AD). O Azure AD é o serviço de gerenciamento de acesso e identidade baseado em nuvem da Microsoft. Ele ajuda seus usuários a fazer login e acessar vários recursos. O Azure AD também permite que você gerencie seus usuários e suas permissões.

Atualmente, essa integração permite que você gerencie em um local central, que é o Azure Active Directory. O Azure AD sincroniza quaisquer alterações no diretório com o diretório Oracle correspondente e é usado para o login único em soluções Oracle em nuvem.

## <a name="next-steps"></a>Próximas etapas

Comece com uma [rede de nuvem cruzada](configure-azure-oci-networking.md) entre a Azure e a OCI. 

Para obter mais informações e whitepapers sobre o OCI, consulte a documentação do [Oracle Cloud.](https://docs.cloud.oracle.com/iaas/Content/home.htm)
