---
title: Como o SSO para recursos locais funciona em dispositivos associados ao Azure AD | Microsoft Docs
description: Saiba como configurar dispositivos adicionados ao Azure Active Directory híbrido.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9d8c0cd803424e117bd4dc7a3382b7b32df2d05
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672712"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Como o SSO para recursos locais funciona em dispositivos associados ao Microsoft Azure Active Directory

Provavelmente, não é surpresa que um dispositivo associado ao Azure Active Directory (Azure AD) forneça uma experiência de logon único (SSO) aos aplicativos em nuvem do seu locatário. Se o seu ambiente tiver um Active Directory Domain Services (AD) local, você poderá estender a experiência de SSO nesses dispositivos para ele.

Este artigo explica como isso funciona.

## <a name="prerequisites"></a>Prerequisites

 Se as máquinas Unidas do Azure AD não estiverem conectadas à rede da sua organização, uma VPN ou outra infraestrutura de rede será necessária. O SSO local requer comunicação de linha de visão com seus controladores de domínio AD DS locais.

## <a name="how-it-works"></a>Como ele funciona 

Porque você precisa se lembrar de apenas um único nome de usuário e senha, o SSO simplifica o acesso a seus recursos e melhora a segurança do seu ambiente. Com um dispositivo associado ao Azure Active Directory, seus usuários já têm uma experiência de SSO para os aplicativos na nuvem em seu ambiente. Se o seu ambiente tiver um Azure Active Directory e um AD local, você provavelmente desejará expandir o escopo da sua experiência de SSO para os aplicativos de linha de negócios (LOB), compartilhamentos de arquivos e impressoras no local.

Os dispositivos associados ao Azure Actibe Directory não têm conhecimento sobre seu ambiente de AD local porque não estão associados a ele. No entanto, você pode fornecer informações adicionais sobre seu AD local para esses dispositivos com o Azure AD Connect.

Um ambiente que possui um Microsoft Azure Active Directory e um AD local também é conhecido como ambiente híbrido. Se você tiver um ambiente híbrido, é provável que você já tenha o Microsoft Azure Active Directory Connect implantado para sincronizar suas informações de identidade local com a nuvem. Como parte do processo de sincronização, Azure AD Connect sincroniza informações de usuário local para o Azure AD. Quando um usuário entra em um dispositivo associado ao Microsoft Azure Active Directory em um ambiente híbrido:

1. O Microsoft Azure Active Directory envia o nome do domínio local do qual o usuário é membro de volta ao dispositivo.
1. O serviço de autoridade de segurança local (LSA) habilita a autenticação Kerberos no dispositivo.

Durante uma tentativa de acesso a um recurso que solicita o Kerberos no ambiente local do usuário, o dispositivo:

1. Envia as informações de domínio no local e as credenciais do usuário para o DC localizado para que o usuário seja autenticado.
1. Recebe um Ticket de Concessão de [Ticket (TGT) Kerberos](/windows/desktop/secauthn/ticket-granting-tickets) que é usado para acessar recursos ingressados no AD. Se a tentativa de obter o TGT para o domínio do AAD Connect falhar (o tempo limite de DCLocator relacionado pode causar um atraso), as entradas do Gerenciador de credenciais serão tentadas ou o usuário poderá receber um pop-up de autenticação solicitando credenciais para o recurso de destino.

Todos os aplicativos configurados para **autenticação integrada do Windows** obtêm SSO com facilidade quando um usuário tenta acessá-los.

O Windows Hello for Business requer configuração adicional para habilitar o SSO local de um dispositivo associado do Microsoft Azure Active Directory. Para obter mais informações, consulte [Configurar dispositivos associados do Microsoft Azure Active Directory para logon único local usando o Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## <a name="what-you-get"></a>O que você ganha

Com o SSO, em um dispositivo associado ao Microsoft Azure Active Directory, você pode: 

- Acessar um caminho UNC em um servidor membro do AD
- Acessar um servidor da Web membro do AD configurado para segurança integrada do Windows 

Se você quiser gerenciar seu AD local a partir de um dispositivo Windows, instale o [Remote Server Administration Tools para Windows 10](https://www.microsoft.com/download/details.aspx?id=45520).

Você pode usar:

- O snap-in Usuários e Computadores do Active Directory Domain Services (ADUC) para administrar todos os objetos do AD. No entanto, você precisa especificar o domínio ao qual deseja se conectar manualmente.
- O snap-in do DHCP para administrar um servidor DHCP associado à AD. No entanto, você pode precisar especificar o nome ou endereço do servidor DHCP.
 
## <a name="what-you-should-know"></a>O que você deve saber

Talvez você precise ajustar sua [ filtragem baseada em domínio ](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) no Microsoft Azure Active Directory Connect para garantir que os dados sobre os domínios necessários sejam sincronizados.

Aplicativos e recursos que dependem da autenticação de máquina do Active Directory Domain Services não funcionam porque os dispositivos associados ao Microsoft Azure Active Directory Domain Services não têm um objeto de computador no AD. 

Você não pode compartilhar arquivos com outros usuários em um dispositivo associado ao Microsoft Azure Active Directory Domain Services.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [O que é gerenciamento de dispositivos no Azure Active Directory Domain Services?](overview.md) 
