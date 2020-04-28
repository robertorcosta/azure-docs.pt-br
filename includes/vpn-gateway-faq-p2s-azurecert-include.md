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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75751701"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="what-should-i-do-if-im-getting-a-certificate-mismatch-when-connecting-using-certificate-authentication"></a>O que devo fazer se estiver recebendo uma incompatibilidade de certificado ao se conectar usando a autenticação de certificado?

Desmarque **"verificar a identidade do servidor Validando o certificado"** ou **adicione o FQDN do servidor junto com o certificado** ao criar um perfil manualmente. Você pode fazer isso executando o **Rasphone** em um prompt de comando e escolhendo o perfil na lista suspensa.

Não é recomendável ignorar a validação de identidade do servidor em geral, mas com a autenticação de certificado do Azure, o mesmo certificado está sendo usado para validação do servidor no protocolo IKEv2/SSTP e no protocolo EAP. Como o certificado do servidor e o FQDN já foram validados pelo protocolo de encapsulamento de VPN, ele é redundante para validar o mesmo novamente no EAP.

![ponto a site](./media/vpn-gateway-faq-p2s-all-include/servercert.png "Certificado de servidor")

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>Posso usar minha própria AC raiz de PKI interna para gerar certificados para conectividade ponto a site?

Sim. Anteriormente, somente os certificados raiz autoassinados podiam ser usados. Você ainda pode carregar 20 certificados raiz.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Posso usar certificados de Azure Key Vault?

Não.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Quais ferramentas posso usar para criar certificados?

Você pode usar sua solução de Enterprise PKI (sua PKI interna), Azure PowerShell, MakeCert e OpenSSL.

### <a name="are-there-instructions-for-certificate-settings-and-parameters"></a><a name="certsettings"></a>Há instruções para configurações e parâmetros de certificado?

* **Solução PKI interna/Enterprise PKI **: ver as etapas para [Gerar certificados](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** consulte o artigo [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) para ver as etapas.

* **MakeCert**: consulte o artigo [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) para ver as etapas.

* **OpenSSL** 

    * Ao exportar certificados, verifique se converteu o certificado raiz em Base64.

    * Para o certificado do cliente:

      * Ao criar a chave privada, especifique o período como 4096.
      * Ao criar o certificado, para o parâmetro *-extensions*, especifique *usr_cert*.
