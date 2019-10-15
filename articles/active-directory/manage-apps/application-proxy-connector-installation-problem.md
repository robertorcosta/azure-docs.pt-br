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
ms.openlocfilehash: a35558b81d064680981bcf403a3584e3a3d00e4f
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311749"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problema ao instalar o conector de agente de Application Proxy

Conector do Application Proxy do Microsoft AAD é um componente de domínio interno que usa conexões de saída para estabelecer a conectividade do ponto de extremidade da nuvem disponível para o domínio interno.

## <a name="general-problem-areas-with-connector-installation"></a>Áreas de problemas gerais com a instalação do conector

Quando a instalação de um conector falhar, a causa raiz é geralmente uma das seguintes áreas:

1.  **Conectividade** – para concluir uma instalação bem-sucedida, o novo conector precisa registrar e estabelecer as propriedades de confiança futuras. Isso é feito ao conectar ao serviço de nuvem do Application Proxy do AAD.

2.  **Estabelecimento de confiança** – o novo conector cria um certificado autoassinado e registra ao serviço de nuvem.

3.  **Autenticação do administrador** – durante a instalação, o usuário deve fornecer credenciais de administrador para concluir a instalação do conector.

> [!NOTE]
> Os logs de instalação do conector podem ser encontrados na pasta% TEMP% e podem ajudar a fornecer informações adicionais sobre o que está causando uma falha de instalação.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Verifique a conectividade com o serviço de Application Proxy de nuvem e a página de Logon da Microsoft

**Objetivo:** verificar se o computador do conector pode se conectar ao ponto de extremidade de registro do Proxy do Aplicativo do AAD, bem como à página de logon da Microsoft.

1.  No servidor do conector, execute um teste de porta usando [Telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) ou outra ferramenta de teste de porta para verificar se as portas 443 e 80 estão abertas.

2.  Se qualquer uma dessas portas não for bem-sucedida, verifique se o firewall ou proxy de back-end tem acesso aos domínios e portas necessários, confira [preparar seu ambiente local](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Abra um navegador (guia separada) e vá para a seguinte página da Web: <https://login.microsoftonline.com>, certifique-se de que você pode fazer logon nessa página.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Verifique se os componentes do computador e de back-end oferecem suporte para certificados de confiança de Application Proxy

**Objetivo:** verificar se o computador do conector, o proxy de back-end e o firewall podem oferecer suporte ao certificado criado pelo conector para confiança futura.

>[!NOTE]
>O conector tenta criar um certificado SHA512 com suporte a TLS1.2. Se o computador ou o firewall de back-end e o proxy não suportarem o TLS 1.2, a instalação falhará.
>
>

**Como resolver o problema:**

1.  Verifique se o computador dá suporte a TLS1.2 – todas as versões do Windows após 2012 R2 devem oferecer suporte a TLS 1.2. Se seu computador de conector for de uma versão de 2012 R2 ou anterior, certifique-se de que os seguintes KBs estão instalados no computador: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Entre em contato com seu administrador de rede e peça para verificar se o proxy de back-end e o firewall não bloqueiam SHA512 para tráfego de saída.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Verifique se o administrador é usado para instalar o conector

**Objetivo:** verificar se o usuário que tenta instalar o conector é um administrador com as credenciais corretas. No momento, o usuário deve ter pelo menos um administrador de aplicativos para que a instalação seja realizada com sucesso.

**Para verificar se as credenciais estão corretas:**

Conecte-se ao <https://login.microsoftonline.com> e use as mesmas credenciais. Verifique se o logon foi bem-sucedido. Você pode verificar a função de usuário acessando **Azure Active Directory** -&gt;**Usuários e grupos** -&gt;**Todos os usuários**. 

Selecione sua conta de usuário, em seguida, "função do Directory" no menu resultante. Verifique se a função selecionada é "administrador do aplicativo". Se não for possível acessar qualquer uma das páginas ao longo dessas etapas, você tem a função exigida.

## <a name="next-steps"></a>Próximas etapas
[Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-connectors.md)
