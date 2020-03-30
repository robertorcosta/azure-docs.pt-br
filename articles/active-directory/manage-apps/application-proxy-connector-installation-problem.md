---
title: Problemas ao instalar o conector de agente do Application Proxy | Microsoft Docs
description: Como solucionar problemas que você poderá enfrentar ao instalar o conector de agente do Application Proxy
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 466e1ce0efbdec3f5475634f3857d02554d93d98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049142"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problema ao instalar o conector de agente de Application Proxy

Conector do Application Proxy do Microsoft AAD é um componente de domínio interno que usa conexões de saída para estabelecer a conectividade do ponto de extremidade da nuvem disponível para o domínio interno.

## <a name="general-problem-areas-with-connector-installation"></a>Áreas de problemas gerais com a instalação do conector

Quando a instalação de um conector falhar, a causa raiz é geralmente uma das seguintes áreas:

1.  **Conectividade** – para concluir uma instalação bem-sucedida, o novo conector precisa registrar e estabelecer as propriedades de confiança futuras. Isso é feito ao conectar ao serviço de nuvem do Application Proxy do AAD.

2.  **Estabelecimento de confiança** – o novo conector cria um certificado autoassinado e registra ao serviço de nuvem.

3.  **Autenticação do administrador** – durante a instalação, o usuário deve fornecer credenciais de administrador para concluir a instalação do conector.

> [!NOTE]
> Os registros de instalação do Conector podem ser encontrados na pasta %TEMP% e podem ajudar a fornecer informações adicionais sobre o que está causando uma falha de instalação.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Verifique a conectividade com o serviço de Application Proxy de nuvem e a página de Logon da Microsoft

**Objetivo:** Verifique se o computador do conector pode se conectar ao ponto de extremidade de registro do Application Proxy do AAD, bem como página de logon do Microsoft.

1.  No servidor de conectores, execute um teste de porta usando [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) ou outra ferramenta de teste de porta para verificar se as portas 443 e 80 estão abertas.

2.  Se alguma dessas portas não for bem sucedida, verifique se o proxy firewall ou backend tem acesso aos domínios e portas necessários ver, [Prepare seu ambiente local](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Abra um navegador (guia separada) e vá para a seguinte página da Web: `https://login.microsoftonline.com`, certifique-se de que você pode fazer logon nessa página.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Verifique se os componentes do computador e de back-end oferecem suporte para certificados de confiança de Application Proxy

**Objetivo:** Verifique se que o computador do conector, o proxy de back-end e o firewall podem dar suporte ao certificado criado pelo conector para confiança futura.

>[!NOTE]
>O conector tenta criar um certificado SHA512 com suporte a TLS1.2. Se a máquina ou o firewall e proxy backend não suportarem o TLS1.2, a instalação falhará.
>
>

**Para resolver o problema:**

1.  Verifique se o computador dá suporte a TLS1.2 – todas as versões do Windows após 2012 R2 devem oferecer suporte a TLS 1.2. Se seu computador de conector for de uma versão de 2012 R2 ou anterior, certifique-se de que os seguintes KBs estão instalados no computador: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Entre em contato com seu administrador de rede e peça para verificar se o proxy de back-end e o firewall não bloqueiam SHA512 para tráfego de saída.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Verifique se o administrador é usado para instalar o conector

**Objetivo:** Verifique se o usuário que tenta instalar o conector é um administrador com as credenciais corretas. Atualmente, o usuário deve ser pelo menos um administrador de aplicativos para que a instalação tenha sucesso.

**Para verificar se as credenciais estão corretas:**

Conecte-se ao `https://login.microsoftonline.com` e use as mesmas credenciais. Verifique se o logon foi bem-sucedido. Você pode verificar a função do usuário indo para **usuários e grupos**  - &gt; **do Azure Active Directory**  - &gt; e grupos **de todos os usuários**. 

Selecione sua conta de usuário e, em seguida, "Função de diretório" no menu resultante. Verifique se a função selecionada é "Administrador de aplicativos". Se não for possível acessar qualquer uma das páginas ao longo dessas etapas, você tem a função exigida.

## <a name="next-steps"></a>Próximas etapas
[Entenda os conectores proxy do aplicativo Azure AD](application-proxy-connectors.md)
