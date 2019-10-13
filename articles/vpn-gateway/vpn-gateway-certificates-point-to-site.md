---
title: 'Gerar e exportar certificados para ponto a site: PowerShell: Azure | Microsoft Docs'
description: Crie um certificado raiz autoassinado, exportar a chave pública e gerar os certificados do cliente usando o PowerShell no Windows 10 ou no Windows Server 2016.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: cherylmc
ms.openlocfilehash: 8f7d4f71853e1640146a38fb39384c20ca9553eb
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285754"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Gerar e exportar certificados para Ponto a Site usando o PowerShell

Conexões ponto a site usam certificados para se autenticar. Este artigo mostra como criar um certificado raiz autoassinado e gerar certificados do cliente utilizando o PowerShell no Windows 10 ou no Windows Server 2016. Se você estiver procurando por instruções de certificado diferentes, confira [Certificados – Linux](vpn-gateway-certificates-point-to-site-linux.md) ou [Certificados – MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Execute as etapas deste artigo em um computador que executa o Windows 10 ou no Windows Server 2016. Os cmdlets do PowerShell que você utiliza para gerar certificados são parte do sistema operacional e não funcionam em outras versões do Windows. O computador Windows 10 ou Windows Server 2016 é necessário apenas para gerar os certificados. Depois que os certificados forem gerados, você poderá carregá-los ou instalá-los em qualquer sistema operacional cliente com suporte.

Se você não tiver acesso a um computador com Windows 10 ou Windows Server 2016 poderá utilizar o [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) para gerar certificados. Os certificados que você gerar utilizando qualquer um dos métodos podem ser instalados em qualquer sistema operacional cliente [com suporte](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq).

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install"></a>Instalar um certificado do cliente exportado

Cada cliente que conecta-se à VNet através de uma conexão P2S requer um certificado do cliente para ser instalado localmente.

Para instalar um certificado de cliente, confira [Instalar um certificado de cliente para conexões Ponto a Site](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Próximas etapas

Continue com a configuração de Ponto a Site.

* Para conferir as etapas do modelo de implantação do **Gerenciador de Recursos**, confira [Configurar P2S usando a autenticação de certificado nativa do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Para as etapas do modelo de implantação **clássico**, veja [Configurar uma conexão VPN Ponto a Site para uma VNet (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).