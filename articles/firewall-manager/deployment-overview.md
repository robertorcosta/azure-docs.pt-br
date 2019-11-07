---
title: Visão geral da implantação da Versão Prévia do Gerenciador de Firewall do Azure
description: Conheça as etapas de implantação de alto nível necessárias para a versão prévia do Gerenciador de Firewall do Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: df87e652d2969d4ae12e97a2b455648cf39382c3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488252"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Visão geral da implantação da Versão Prévia do Gerenciador de Firewall do Azure

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Há mais de uma maneira de implantar a versão prévia do Gerenciador de firewall do Azure, mas o processo geral a seguir é recomendado.

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> A Versão prévia do Gerenciador de Firewall do Azure deve ser habilitada explicitamente usando o comando `Register-AzProviderFeature` do PowerShell.
>Em um prompt de comando do PowerShell, execute os seguintes comandos:
>
>```azure-powershell
>connect-azaccount
>Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
>```
>A conclusão do registro do recurso demora até 30 minutos. Execute o seguinte comando para verificar o >status do registro:
>
>`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`



## <a name="general-deployment-process"></a>Processo de implantação geral

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