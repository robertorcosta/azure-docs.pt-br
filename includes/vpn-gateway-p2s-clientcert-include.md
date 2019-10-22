---
title: arquivo de inclusão
description: arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: 31ccf14c82f6248c74d6af932fe9e338d26d2747
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "67171784"
---
Cada computador cliente que você conecta a uma VNet com uma conexão ponto a site deve ter um certificado de cliente instalado. Você o gera a partir do certificado raiz e o instala em cada computador cliente. Se você não instalar um certificado de cliente válido, a autenticação falhará quando o cliente tentar se conectar à VNet.

Você pode gerar um certificado exclusivo para cada cliente ou pode usar o mesmo certificado para vários clientes. A vantagem de gerar certificados de cliente exclusivos é a capacidade de revogar um único certificado. Caso contrário, se vários clientes usarem o mesmo certificado de cliente para autenticar e você revogá-lo, você precisará gerar e instalar novos certificados para cada cliente que usa esse certificado.

Você pode gerar certificados de cliente usando os seguintes métodos:

- **Certificado corporativo:**

  - Se você estiver usando uma solução de certificado corporativo, gere um certificado de cliente com o nome de formato de valor de nome comum *\@yourdomain. com*. Use esse formato em vez do formato de *domínio \ nomedeusuário* .
  - Verifique se o certificado do cliente é baseado em um modelo de certificado de usuário que tem *autenticação de cliente* listada como o primeiro item na lista de usuários. Verifique o certificado clicando duas vezes nele e exibindo o **uso avançado de chave** na guia **detalhes** .

- **Certificado raiz autoassinado:** Siga as etapas em um dos seguintes artigos do certificado P2S para que os certificados do cliente criados sejam compatíveis com suas conexões do P2S. As etapas nesses artigos geram um certificado de cliente compatível: 

  * [Instruções do Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): essas instruções exigem que o Windows 10 e o PowerShell gerem certificados. Os certificados gerados podem ser instalados em qualquer cliente P2S com suporte.
  * [Instruções MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Use MakeCert se não tiver acesso a um computador com Windows 10 para gerar certificados. Embora o MakeCert seja preterido, você ainda pode usá-lo para gerar certificados. Você pode instalar os certificados gerados em qualquer cliente P2S com suporte.
  * [Instruções do Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

  Quando você gera um certificado de cliente de um certificado raiz autoassinado, ele é instalado automaticamente no computador que você usou para gerá-lo. Se você quiser instalar um certificado de cliente em outro computador cliente, exporte-o como um arquivo. pfx, juntamente com toda a cadeia de certificados. Isso criará um arquivo. pfx que contém as informações de certificado raiz necessárias para o cliente se autenticar. 

**Para exportar o certificado**

Para obter as etapas para exportar um certificado, consulte [gerar e exportar certificados para ponto a site usando o PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).
