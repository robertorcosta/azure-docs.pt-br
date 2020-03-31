---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f322803d3484b4ec2d5449e19d67d75b35d6d92f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75751701"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="what-should-i-do-if-im-getting-a-certificate-mismatch-when-connecting-using-certificate-authentication"></a>O que devo fazer se estou recebendo uma incompatibilidade de certificado ao me conectar usando autenticação de certificado?

Desmarque **"Verifique a identidade do servidor validando o certificado"** ou **adicione o FQDN do servidor junto com o certificado ao** criar um perfil manualmente. Você pode fazer isso executando **rasphone** a partir de um prompt de comando e escolhendo o perfil da lista de paradas.

Ignorar a validação da identidade do servidor não é recomendado em geral, mas com a autenticação do certificado Azure, o mesmo certificado está sendo usado para validação do servidor no protocolo de tunelamento VPN (IKEv2/SSTP) e no protocolo EAP. Uma vez que o certificado de servidor e fqdn já está validado pelo protocolo de tunelamento VPN, é redundante validar o mesmo novamente no EAP.

![ponto a ponto](./media/vpn-gateway-faq-p2s-all-include/servercert.png "Certificado de servidor")

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>Posso usar meu próprio CA raiz PKI interno para gerar certificados de conectividade ponto a ponto?

Sim. Anteriormente, somente os certificados raiz autoassinados podiam ser usados. Você ainda pode carregar 20 certificados raiz.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Posso usar certificados do Azure Key Vault?

Não.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Quais ferramentas posso usar para criar certificados?

Você pode usar sua solução de Enterprise PKI (sua PKI interna), Azure PowerShell, MakeCert e OpenSSL.

### <a name="are-there-instructions-for-certificate-settings-and-parameters"></a><a name="certsettings"></a>Há instruções para configurações e parâmetros de certificado?

* **Solução PKI interna/Enterprise PKI **: ver as etapas para [Gerar certificados](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** consulte o artigo [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) para ver as etapas.

* **MakeCert**: consulte o artigo [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) para ver as etapas.

* **Openssl:** 

    * Ao exportar certificados, verifique se converteu o certificado raiz em Base64.

    * Para o certificado do cliente:

      * Ao criar a chave privada, especifique o período como 4096.
      * Ao criar o certificado, para o parâmetro *-extensions*, especifique *usr_cert*.
