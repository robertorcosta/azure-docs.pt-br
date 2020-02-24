---
title: Visão geral da implantação da Versão Prévia do Gerenciador de Firewall do Azure
description: Conheça as etapas de implantação de alto nível necessárias para a versão prévia do Gerenciador de Firewall do Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: c3a94cea838609f65511a21ee2f64e8782a6adea
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443118"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Visão geral da implantação da Versão Prévia do Gerenciador de Firewall do Azure

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Há mais de uma maneira de implantar a versão prévia do Gerenciador de firewall do Azure, mas o processo geral a seguir é recomendado.

## <a name="general-deployment-process"></a>Processo de implantação geral

### <a name="hub-virtual-networks"></a>Redes virtuais de hub

1.  Criar uma política de firewall

    - Criar uma nova política
<br>*or*<br>
    - Derivar uma política de base e personalizar uma política local
<br>*or*<br>
    - Importar regras de um Firewall do Azure existente. Verifique se você removeu as regras NAT de políticas que devem ser aplicadas a vários firewalls
1. Criar sua arquitetura de hub e spoke
   - Criar uma rede virtual de hub usando o Gerenciador de Firewall do Azure e redes virtuais spoke emparelhadas usando o emparelhamento de rede virtual
<br>*or*<br>
    - Criar uma rede virtual e adicionar conexões de rede virtual e redes virtuais spoke emparelhadas a ela usando o emparelhamento de rede virtual

3. Selecione provedores de segurança e associe uma política de firewall. Atualmente, somente o Firewall do Azure é um provedor compatível.

   - Isso é feito enquanto você cria uma rede virtual de hub
<br>*or*<br>
    - Converta uma rede virtual existente em uma rede virtual de hub. Também é possível converter várias redes virtuais.

4. Configure as Rotas Definidas Pelo Usuário para rotear o tráfego para o firewall de rede virtual de hub.


### <a name="secured-virtual-hubs"></a>Hubs virtuais seguros

1. Criar sua arquitetura de hub e spoke

   - Crie um hub virtual protegido usando o Gerenciador de Firewall do Azure e adicione conexões de rede virtual.<br>*or*<br>
   - Crie um hub de WAN virtual do Azure e adicione conexões de rede virtual.
2. Selecionar os provedores de segurança

   - Feito durante a criação de um hub virtual seguro.<br>*or*<br>
   - Converta um Hub de WAN Virtual existente a um Hub Virtual Seguro.
3. Crie uma política de firewall e associe-a ao seu hub

   - Aplicável somente se estiver usando o Firewall do Azure.
   - As políticas de SECaaS (segurança como serviço) de terceiros são configuradas por meio da experiência de gerenciamento de parceiros.
4. Definir configurações de rota para rotear o tráfego para o Hub protegido

   - Encaminhe facilmente o tráfego para o hub seguro para filtragem e registro sem as Rotas Definidas pelo Usuário (UDR) nas redes virtuais spoke usando a página de Configuração de rota de hub virtual seguro.

> [!NOTE]
> - Você não pode ter mais de um hub por WAN virtual por região. Mas você pode adicionar várias WANs virtuais na região para conseguir isso.
> - Não é possível ter espaços IP sobrepostos para hubs em uma vWAN.
> - As conexões de VNet do Hub devem estar na mesma região que o hub.

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Proteja sua rede na nuvem com a Versão prévia do Gerenciador de Firewall do Azure usando o portal do Azure](secure-cloud-network.md)