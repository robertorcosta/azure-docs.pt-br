---
title: Solução Azure VMware by CloudSimple - Segurança para Serviços CloudSimple
description: Descreve os modelos de responsabilidade compartilhada para a segurança dos serviços CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1a33e20ec540a05885eb13a3828d28ffc9923fff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024986"
---
# <a name="cloudsimple-security-overview"></a>Visão geral da segurança do CloudSimple

Este artigo fornece uma visão geral de como a segurança é implementada na solução Azure VMware pelo serviço, infra-estrutura e data center Do CloudSimple. Você aprende sobre proteção e segurança de dados, segurança da rede e como vulnerabilidades e patches são gerenciados.

## <a name="shared-responsibility"></a>Responsabilidade compartilhada

A Solução VMware do Azure pela CloudSimple usa um modelo de responsabilidade compartilhada para segurança. A segurança confiável na nuvem é alcançada através das responsabilidades compartilhadas dos clientes e da Microsoft como provedora de serviços. Esta matriz de responsabilidade proporciona maior segurança e elimina pontos únicos de falha.

## <a name="azure-infrastructure"></a>Infraestrutura do Azure

As considerações de segurança da infra-estrutura do Azure incluem os data centers e a localização dos equipamentos.

### <a name="datacenter-security"></a>Segurança do datacenter

A Microsoft tem toda uma divisão dedicada a projetar, construir e operar as instalações físicas que suportam o Azure. Essa equipe está investida em manter a segurança física de última geração. Para obter detalhes sobre segurança física, consulte [instalações do Azure, instalações e segurança física.](../security/azure-physical-security.md)

### <a name="equipment-location"></a>Localização do equipamento

O equipamento de hardware de metal nu que executa suas Nuvens Privadas está hospedado em locais de data center do Azure.  As gaiolas onde esse equipamento está, requer autenticação biométrica de dois fatores para ter acesso.

## <a name="dedicated-hardware"></a>Hardware dedicado

Como parte do serviço CloudSimple, todos os clientes do CloudSimple recebem hosts dedicados de metal nu com discos conectados locais que estão fisicamente isolados de outros hardwares de inquilinos. Um hipervisor ESXi com vSAN é executado em todos os tópicos. Os nós são gerenciados através do VMware vCenter e NSX dedicado sustais. Não compartilhar hardware entre inquilinos fornece uma camada adicional de isolamento e proteção de segurança.

## <a name="data-security"></a>Segurança de dados

Os clientes mantêm o controle e a propriedade de seus dados. A administração de dados dos dados do cliente é de responsabilidade do cliente.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Proteção de dados em repouso e dados em movimento dentro de redes internas

Para obter dados em repouso no ambiente Private Cloud, você pode usar criptografia vSAN. A criptografia vSAN funciona com servidores de gerenciamento de chaves externas (KMS) certificados pela VMware em sua própria rede virtual ou no local.  Você mesmo controla as chaves de criptografia de dados. Para dados em movimento dentro da Nuvem Privada, o vSphere suporta criptografia de dados sobre o fio para todo o tráfego de vmkernel (incluindo tráfego vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Proteção de dados para dados que são necessários para se mover através de redes públicas

Para proteger os dados que se movem através de redes públicas, você pode criar túneis IPsec e SSL VPN para suas Nuvens Privadas. Métodos comuns de criptografia são suportados, incluindo AES de 128 bytes e 256 bytes. Os dados em trânsito (incluindo autenticação, acesso administrativo e dados do cliente) são criptografados com mecanismos de criptografia padrão (SSH, TLS 1.2 e Secure RDP). A comunicação que transporta informações confidenciais usa os mecanismos de criptografia padrão.

### <a name="secure-disposal"></a>Descarte seguro

Se o serviço CloudSimple expirar ou for encerrado, você será responsável pela remoção ou exclusão de seus dados. O CloudSimple cooperará com você para excluir ou devolver todos os dados do cliente conforme previsto no contrato com o cliente, exceto na medida em que o CloudSimple for exigido pela lei aplicável para reter alguns ou todos os dados pessoais. Se necessário para reter quaisquer dados pessoais, o CloudSimple arquivará os dados e implementará medidas razoáveis para evitar que os dados do cliente sejam processados posteriormente.

### <a name="data-location"></a>Local dos dados

Ao configurar suas nuvens privadas, você escolhe a região Azure onde elas serão implantadas. Os dados da máquina virtual VMware não são movidos daquele datacenter físico, a menos que você execute a migração de dados ou o backup de dados externos. Você também pode hospedar cargas de trabalho e armazenar dados em várias regiões do Azure, se apropriado para suas necessidades.

Os dados do cliente residentes em nódulos hiperconvergentes da Nuvem Privada não atravessam locais sem a ação explícita do administrador do inquilino. É sua responsabilidade implementar suas cargas de trabalho de forma altamente disponível.

### <a name="data-backups"></a>Backups de dados

O CloudSimple não faz backup ou arquiva dados de clientes. O CloudSimple realiza backup periódico de dados vCenter e NSX para fornecer alta disponibilidade de servidores de gerenciamento. Antes do backup, todos os dados são criptografados na fonte do vCenter usando APIs do VMware. Os dados criptografados são transportados e armazenados no blob do Azure. As chaves de criptografia para backups são armazenadas em um cofre gerenciado cloudsimple altamente seguro em execução na rede virtual CloudSimple no Azure.

## <a name="network-security"></a>Segurança de rede

A solução CloudSimple depende de camadas de segurança de rede.

### <a name="azure-edge-security"></a>Segurança da borda do Azure

Os serviços CloudSimple são construídos em cima da segurança de rede base fornecida pelo Azure. O Azure aplica técnicas de defesa em profundidade para detecção e resposta oportuna a ataques baseados em rede associados a padrões de tráfego anômalos ou saídas e ataques de negação de serviço distribuídos (DDoS). Este controle de segurança se aplica aos ambientes Private Cloud e ao software de plano de controle desenvolvido pela CloudSimple.

### <a name="segmentation"></a>Segmentação

O serviço CloudSimple tem logicamente separadas redes de Camada 2 que restringem o acesso às suas próprias redes privadas em seu ambiente Private Cloud. Você pode proteger ainda mais suas redes privadas de nuvem usando um firewall. O portal CloudSimple permite definir as regras de controle de tráfego de rede EW e NS para todo o tráfego de rede, incluindo tráfego intra Private Cloud, tráfego inter-Private Cloud, tráfego geral para a Internet e tráfego de rede para locais através do IPsec VPN ou Conexão ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Gerenciamento de vulnerabilidades e patches

O CloudSimple é responsável pelo patch de segurança periódico do software VMware gerenciado (ESXi, vCenter e NSX).

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

Os clientes podem autenticar em sua conta azure (no Azure AD) usando autenticação multifatorial ou SSO como preferido. A partir do portal Azure, você pode lançar o portal CloudSimple sem reinserir credenciais.

O CloudSimple suporta a configuração opcional de uma fonte de identidade para o Private Cloud vCenter. Você pode usar uma [fonte de identidade no local](set-vcenter-identity.md), uma nova fonte de identidade para a Nuvem Privada ou [Azure AD](azure-ad.md).

Por padrão, os clientes recebem os privilégios necessários para as operações diárias do vCenter dentro da Nuvem Privada. Este nível de permissão não inclui acesso administrativo ao vCenter. Se o acesso administrativo for temporariamente necessário, você pode [aumentar seus privilégios](escalate-private-cloud-privileges.md) por um período limitado enquanto você completa as tarefas administrativas.
