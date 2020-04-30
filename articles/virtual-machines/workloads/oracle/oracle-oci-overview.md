---
title: Integre o Microsoft Azure com a infraestrutura de nuvem da Oracle | Microsoft Docs
description: Saiba mais sobre soluções que integram aplicativos Oracle em execução em Microsoft Azure com bancos de dados no Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: borisb
ms.custom: ''
ms.openlocfilehash: e70eedcfcdf548965b79e4a48a3a8bfa643f0396
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687440"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>Soluções de aplicativos Oracle que integram Microsoft Azure e infraestrutura de nuvem da Oracle

A Microsoft e a Oracle fizeram parceria para fornecer conectividade de nuvem cruzada de baixa latência e alta taxa de transferência, permitindo que você aproveite o melhor das duas nuvens. 

Usando essa conectividade entre nuvens, você pode particionar um aplicativo multicamadas para executar sua camada de banco de dados no Oracle Cloud Infrastructure (OCI) e o aplicativo e outras camadas em Microsoft Azure. A experiência é semelhante à execução de toda a pilha de solução em uma única nuvem. 

Se você estiver interessado em implantar soluções Oracle totalmente na infraestrutura do Azure, consulte [imagens de VM Oracle e sua implantação em Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Visão geral do cenário

A conectividade entre nuvem fornece uma solução para que você execute aplicativos líderes do setor da Oracle e seus próprios aplicativos personalizados, em máquinas virtuais do Azure, aproveitando os benefícios dos serviços de banco de dados hospedados no OCI. 

> [!IMPORTANT]
> A Oracle garantirá que esses aplicativos sejam executados no Azure ao usar a solução de interconexão de nuvem do Azure/Oracle de maio de 2020.

Os aplicativos que você pode executar em uma configuração de nuvem cruzada incluem:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplicativos de varejo da Oracle
* Gerenciamento financeiro do Oracle Hyperion

O diagrama a seguir é uma visão geral de alto nível da solução conectada. Para simplificar, o diagrama mostra apenas uma camada de aplicativo e uma camada de dados. Dependendo da arquitetura do aplicativo, sua solução pode incluir camadas adicionais, como uma camada da Web no Azure. Para obter mais informações, consulte as próximas seções.

![Visão geral da solução de OCI do Azure](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Disponibilidade da região 

A conectividade entre nuvem é limitada às seguintes regiões:
* Leste dos EUA do Azure (lesteus) & OCI Ashburn (leste dos EUA)
* Sul do Reino Unido do Azure (uksouth) & OCI Londres (Sul do Reino Unido)
* Azure Canada central (canadacentral) & OCI Toronto (sudeste do Canadá)
* Azure Europa Ocidental (westeurope) & OCI Amsterdã (noroeste dos Países Baixos)
* Azure Japão oriental (japaneast) & OCI Tóquio (leste do Japão)

## <a name="networking"></a>Rede

Os clientes corporativos geralmente optam por diversificar e implantar cargas de trabalho em várias nuvens por vários motivos operacionais e de negócios. Para diversificar, os clientes interconectam redes em nuvem usando a Internet, VPN IPSec ou usando a solução de conectividade direta do provedor de nuvem por meio de sua rede local. A interconexão de redes em nuvem pode exigir investimentos significativos em tempo, dinheiro, design, aquisição, instalação, teste e operações. 

Para resolver esses desafios do cliente, a Oracle e a Microsoft habilitaram uma experiência de várias nuvens integrada. A rede entre redes em nuvem é estabelecida conectando um circuito do [ExpressRoute](../../../expressroute/expressroute-introduction.md) no Microsoft Azure com um circuito [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) no OCI. Essa conectividade é possível quando um local de emparelhamento do ExpressRoute do Azure está em proximidade com ou no mesmo local de emparelhamento que o FastConnect de OCI. Essa configuração permite uma conectividade segura e rápida entre as duas nuvens sem a necessidade de um provedor de serviços intermediário.

Usando o ExpressRoute e o FastConnect, os clientes podem emparelhar uma rede virtual no Azure com uma rede de nuvem virtual no OCI, desde que o espaço de endereço IP privado não se sobreponha. O emparelhamento das duas redes permite que um recurso na rede virtual se comunique com um recurso na rede de nuvem virtual de OCI como se eles estivessem na mesma rede.

## <a name="network-security"></a>Segurança de rede

A segurança de rede é um componente crucial de qualquer aplicativo empresarial e é fundamental para essa solução de várias nuvens. Qualquer tráfego que passará pelo ExpressRoute e FastConnect passará por uma rede privada. Essa configuração permite a comunicação segura entre uma rede virtual do Azure e uma rede de nuvem virtual da Oracle. Você não precisa fornecer um endereço IP público para nenhuma máquina virtual no Azure. Da mesma forma, você não precisa de um gateway de Internet no OCI. Toda a comunicação ocorre por meio do endereço IP privado das máquinas.

Além disso, você pode configurar [listas de segurança](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) em sua rede de nuvem virtual de OCI e regras de segurança (conectadas a [grupos de segurança de rede](../../../virtual-network/security-overview.md)do Azure). Use essas regras para controlar o fluxo de tráfego entre as máquinas nas redes virtuais. As regras de segurança de rede podem ser adicionadas em um nível de máquina, em um nível de sub-rede, bem como no nível de rede virtual.
 
## <a name="identity"></a>Identidade

A identidade é um dos pilares principais da parceria entre a Microsoft e a Oracle. Foi feito um trabalho significativo para integrar o IDCS ( [serviço de nuvem de identidades](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) ) da Oracle com o [Azure Active Directory](../../../active-directory/index.yml) (Azure AD). O Azure AD é o serviço de gerenciamento de identidade e acesso baseado em nuvem da Microsoft. Ele ajuda os usuários a entrar e acessar vários recursos. O AD do Azure também permite que você gerencie seus usuários e suas permissões.

Atualmente, essa integração permite que você gerencie em um local central, que é Azure Active Directory. O Azure AD sincroniza todas as alterações no diretório com o diretório Oracle correspondente e é usado para logon único em soluções do Oracle de nuvem cruzada.

## <a name="next-steps"></a>Próximas etapas

Introdução a uma [rede entre nuvens](configure-azure-oci-networking.md) entre o Azure e o OCI. 

Para obter mais informações e White papers sobre o OCI, consulte a documentação da [nuvem do Oracle](https://docs.cloud.oracle.com/iaas/Content/home.htm) .
