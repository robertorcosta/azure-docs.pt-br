---
title: Soluções VMware do Azure (AVS)-segurança para serviços de AVS
description: Descreve os modelos de responsabilidade compartilhada para segurança de serviços de AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d2d45f827d4165175a4a5429f0b9393a55e2ff1e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024986"
---
# <a name="avs-security-overview"></a>Visão geral da segurança da AVS

Este artigo fornece uma visão geral de como a segurança é implementada na solução Azure VMware por meio do serviço AVS, da infraestrutura e do datacenter. Você aprende sobre proteção de dados e segurança, segurança de rede e como as vulnerabilidades e os patches são gerenciados.

## <a name="shared-responsibility"></a>Responsabilidade compartilhada

A solução Azure VMware por AVS usa um modelo de responsabilidade compartilhada para segurança. A segurança confiável na nuvem é obtida por meio das responsabilidades compartilhadas de clientes e da Microsoft como um provedor de serviços. Essa matriz de responsabilidade fornece maior segurança e elimina pontos únicos de falha.

## <a name="azure-infrastructure"></a>Infraestrutura do Azure

As considerações de segurança de infraestrutura do Azure incluem os data centers e o local do equipamento.

### <a name="datacenter-security"></a>Segurança do datacenter

A Microsoft tem uma divisão completa dedicada à criação, à criação e à operação de instalações físicas que dão suporte ao Azure. Essa equipe está investida em manter a segurança física de última geração. Para obter detalhes sobre segurança física, consulte [instalações do Azure, local e segurança física](../security/azure-physical-security.md).

### <a name="equipment-location"></a>Local do equipamento

O equipamento de hardware bare-metal que executa suas nuvens privadas de AVS é hospedado em locais de datacenter do Azure. Os compartimentos em que esse equipamento se encontra, exigem a autenticação de dois fatores baseada em biométrica para obter acesso.

## <a name="dedicated-hardware"></a>Hardware dedicado

Como parte do serviço AVS, todos os clientes de AVS recebem hosts bare-metal dedicados com discos anexados locais que são fisicamente isolados de outro hardware de locatário. Um hipervisor ESXi com vSAN é executado em todos os nós. Os nós são gerenciados por meio do VMware vCenter e do NSX dedicados ao cliente. O compartilhamento de hardware entre locatários fornece uma camada adicional de isolamento e proteção de segurança.

## <a name="data-security"></a>Segurança dos dados

Os clientes mantêm o controle e a propriedade de seus dados. A administração de dados dos dados do cliente é responsabilidade do cliente.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Proteção de dados para dados em repouso e dados em movimento em redes internas

Para dados em repouso no ambiente de nuvem privada da AVS, você pode usar a criptografia vSAN. a criptografia vSAN funciona com os servidores de gerenciamento de chaves externas (KMS) certificados pela VMware em sua própria rede virtual ou local. Você controla as chaves de criptografia de dados por conta própria. Para dados em movimento na nuvem privada da AVS, o vSphere dá suporte à criptografia de dados pela rede para todo o tráfego de VMkernel (incluindo o tráfego do vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Proteção de dados para dados que são necessários para se mover por meio de redes públicas

Para proteger dados que se movem por meio de redes públicas, você pode criar túneis de VPN IPsec e SSL para suas nuvens privadas de AVS. Há suporte para métodos de criptografia comuns, incluindo o AES de 128 bytes e de 256 bytes. Os dados em trânsito (incluindo autenticação, acesso administrativo e dados do cliente) são criptografados com mecanismos de criptografia padrão (SSH, TLS 1,2 e RDP seguro). A comunicação que transporta informações confidenciais usa os mecanismos de criptografia padrão.

### <a name="secure-disposal"></a>Eliminação segura

Se o serviço AVS expirar ou for encerrado, você será responsável por remover ou excluir seus dados. A AVS irá cooperar com você para excluir ou retornar todos os dados do cliente conforme fornecido no contrato do cliente, exceto pela medida que a AVS é exigida pela lei aplicável para reter alguns ou todos os dados pessoais. Se necessário, para manter todos os dados pessoais, a AVS arquivará os dados e implementará medidas razoáveis para evitar que os dados do cliente sejam processados.

### <a name="data-location"></a>Local dos dados

Ao configurar suas nuvens de AVS privado, você escolhe a região do Azure onde elas serão implantadas. Os dados da máquina virtual do VMware não são movidos desse datacenter físico, a menos que você execute a migração de dados ou o backup de dados externo. Você também pode hospedar cargas de trabalho e armazenar dados em várias regiões do Azure, se apropriado para suas necessidades.

Os dados do cliente residentes em nós hiperconvergentes da nuvem privada da AVS não atravessam os locais sem a ação explícita do administrador de locatários. É sua responsabilidade implementar suas cargas de trabalho de uma maneira altamente disponível.

### <a name="data-backups"></a>Backups de dados

A AVS não faz backup ou arquiva dados do cliente. A AVS executa backup periódico de dados do vCenter e do NSX para fornecer alta disponibilidade de servidores de gerenciamento. Antes do backup, todos os dados são criptografados na fonte do vCenter usando APIs do VMware. Os dados criptografados são transportados e armazenados no blob do Azure. As chaves de criptografia para backups são armazenadas em um cofre gerenciado de AVS altamente seguro em execução na rede virtual AVS no Azure.

## <a name="network-security"></a>Segurança de Rede

A solução AVS depende de camadas de segurança de rede.

### <a name="azure-edge-security"></a>Segurança do Azure Edge

Os serviços de AVS são criados sobre a segurança de rede base fornecida pelo Azure. O Azure aplica técnicas de defesa aprofundada para detecção e resposta oportuna a ataques baseados em rede associados a padrões de tráfego de entrada ou saída anormais e ataques de DDoS (negação de serviço distribuído). Esse controle de segurança se aplica a ambientes de nuvem privada da AVS e ao software do plano de controle desenvolvido pela AVS.

### <a name="segmentation"></a>Segmentação

O serviço AVS separa redes de camada 2 logicamente que restringem o acesso às suas próprias redes privadas em seu ambiente de nuvem privada de AVS. Você pode proteger ainda mais suas redes de nuvem privada da AVS usando um firewall. O portal da AVS permite que você defina as regras de controle de tráfego de rede do ovo e do NS para todo o tráfego de rede, incluindo tráfego de nuvem privada da sincronização interna, tráfego de nuvem privada entre AVS, tráfego geral para a Internet e tráfego de rede para o local por VPN IPsec ou Conexão do ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Gerenciamento de vulnerabilidades e patches

A AVS é responsável pela aplicação de patch de segurança periódica do software VMware gerenciado (ESXi, vCenter e NSX).

## <a name="identity-and-access-management"></a>Gerenciamento de identidades e acessos

Os clientes podem se autenticar em sua conta do Azure (no Azure AD) usando a autenticação multifator ou SSO como preferencial. No portal do Azure, você pode iniciar o portal da AVS sem reinserir as credenciais.

O AVS dá suporte à configuração opcional de uma origem de identidade para o vCenter privado de nuvem da AVS. Você pode usar uma [fonte de identidade local](set-vcenter-identity.md), uma nova fonte de identidade para a nuvem privada da AVS ou o [Azure ad](azure-ad.md).

Por padrão, os clientes recebem os privilégios necessários para as operações do dia a dia do vCenter na nuvem privada da AVS. Esse nível de permissão não inclui acesso administrativo ao vCenter. Se o acesso administrativo for temporariamente necessário, você poderá [escalonar seus privilégios](escalate-private-cloud-privileges.md) por um período limitado enquanto concluir as tarefas administrativas.
