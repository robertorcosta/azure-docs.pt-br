---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c8e7e5272f180c482ca7fdd44302f49eb888b25
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "67171782"
---
Use um certificado raiz que foi gerado com uma solução corporativa (recomendado) ou gere um certificado autoassinado. Depois de criar o certificado raiz, exporte os dados de certificado público (não a chave privada) como o arquivo .cer X.509 codificado em Base64. Em seguida, carregue os dados do certificado público para o servidor do Azure.

* **Certificado corporativo:** Se você estiver usando uma solução corporativa, poderá usar sua cadeia de certificados existente. Adquira o arquivo .cer do certificado raiz que você deseja usar.
* **Certificado raiz autoassinado:** Se você não estiver usando uma solução de certificado corporativo, crie um certificado raiz autoassinado. Caso contrário, os certificados criados não serão compatíveis com suas conexões P2S e os clientes receberão um erro de conexão quando tentarem se conectar. Você pode usar o Azure PowerShell, MakeCert ou OpenSSL. As etapas nos artigos a seguir descrevem como gerar um certificado raiz autoassinado compatível:

  * [Instruções do Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): essas instruções exigem o Windows 10 e o PowerShell para gerar certificados. Os certificados de cliente gerados a partir do certificado raiz podem ser instalados em qualquer cliente de P2S com suporte.
  * [Instruções de MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): use MakeCert se você não tiver acesso a um computador com Windows 10 para gerar certificados. Embora o MakeCert tenha sido preterido, você ainda pode usá-lo para gerar certificados. Os certificados de cliente gerados usando o certificado raiz podem ser instalados em qualquer cliente de P2S com suporte.
  * [Instruções do Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)
