---
title: Gerar e exportar certificados para conexões VPN de usuário de WAN virtual do Azure | Microsoft Docs
description: Crie um certificado raiz autoassinado, exportar a chave pública e gerar os certificados do cliente usando o PowerShell no Windows 10 ou no Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 57d730a92c687a297a35b8cd6cccd955025694af
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514908"
---
# <a name="generate-and-export-certificates-for-virtual-wan-user-vpn-connections"></a>Gerar e exportar certificados para conexões VPN de usuário de WAN virtual

As conexões VPN de usuário usam certificados para autenticar. Este artigo mostra como criar um certificado raiz autoassinado e gerar certificados do cliente utilizando o PowerShell no Windows 10 ou no Windows Server 2016.

Execute as etapas deste artigo em um computador que executa o Windows 10 ou no Windows Server 2016. Os cmdlets do PowerShell que você utiliza para gerar certificados são parte do sistema operacional e não funcionam em outras versões do Windows. O computador Windows 10 ou Windows Server 2016 é necessário apenas para gerar os certificados. Depois que os certificados forem gerados, você poderá carregá-los ou instalá-los em qualquer sistema operacional cliente com suporte.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Próximas etapas

Continue com as [etapas de WAN virtual para a conexão VPN do usuário](virtual-wan-about.md)
