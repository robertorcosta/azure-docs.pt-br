---
title: Solução de problemas secure LDAP in Azure AD Domain Services | Microsoft Docs
description: Saiba como solucionar problemas lDAP (LDAPS) seguro para um domínio gerenciado do Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 22d1b6e2344256b52cfdbc48720a680a770a4216
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132159"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Solucionar problemas de conectividade LDAP seguros para um domínio gerenciado do Azure Active Directory Domain Services

Aplicativos e serviços que usam o protocolo de acesso a diretórios leves (LDAP) para se comunicar com o Azure Active Directory Domain Services (Azure AD DS) podem ser [configurados para usar LDAP seguro](tutorial-configure-ldaps.md). Um certificado apropriado e portas de rede necessárias devem estar abertas para que o LDAP seguro funcione corretamente.

Este artigo ajuda você a solucionar problemas com acesso LDAP seguro no Azure AD DS.

## <a name="common-connection-issues"></a>Problemas comuns de conexão

Se você tiver problemas para se conectar a um domínio gerenciado pelo Azure AD DS usando LDAP seguro, revise as etapas a seguir de solução de problemas. Após cada etapa de solução de problemas, tente se conectar ao domínio gerenciado pelo Azure AD DS novamente:

* A cadeia de emissor do certificado LDAP seguro deve ser confiável no cliente. Você pode adicionar a autoridade de certificação Raiz (CA) ao armazenamento de certificados raiz confiável no cliente para estabelecer a confiança.
    * Certifique-se [de exportar e aplicar o certificado aos computadores clientes][client-cert].
* Verifique se o certificado LDAP seguro para o domínio gerenciado tem o nome DNS no *atributo 'Nomes* *alternativos* de assunto'.
    * Revise os [requisitos seguros do certificado LDAP][certs-prereqs] e crie um certificado de substituição, se necessário.
* Verifique se o cliente LDAP, como *o LDP.exe,* se conecta ao ponto final LDAP seguro usando um nome DNS, não o endereço IP.
    * O certificado aplicado ao domínio gerenciado pelo Azure AD DS não inclui os endereços IP do serviço, apenas os nomes DNS.
* Verifique o nome DNS que o cliente LDAP se conecta. Ele deve resolver para o endereço IP público para LDAP seguro no domínio gerenciado pelo Azure AD DS.
    * Se o nome DNS for resolvido no endereço IP interno, atualize o registro DNS para resolver no endereço IP externo.
* Para conectividade externa, o grupo de segurança da rede deve incluir uma regra que permita o tráfego para a porta TCP 636 a partir da internet.
    * Se você puder se conectar ao domínio gerenciado pelo Azure AD DS usando LDAP seguro a partir de recursos diretamente conectados à rede virtual, mas não a conexões externas, certifique-se de [criar uma regra de grupo de segurança de rede para permitir tráfego LDAP seguro.][ldaps-nsg]

## <a name="next-steps"></a>Próximas etapas

Se você ainda tiver problemas, [abra uma solicitação de suporte do Azure][azure-support] para assistência adicional de solução de problemas.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
