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
ms.openlocfilehash: 4d773e6302edf0b799e6dfccc702750a9cc74f60
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406693"
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

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>Verifique o suporte a componentes de máquina e back-end para certificado de confiança proxy do aplicativo

**Objetivo:** Verifique se a máquina conectora, o proxy backend e o firewall podem suportar o certificado criado pelo conector para confiança futura e se o certificado é válido.

>[!NOTE]
>O conector tenta criar um certificado SHA512 com suporte a TLS1.2. Se a máquina ou o firewall e proxy backend não suportarem o TLS1.2, a instalação falhará.
>
>

**Revise os pré-requisitos necessários:**

1.  Verifique se o computador dá suporte a TLS1.2 – todas as versões do Windows após 2012 R2 devem oferecer suporte a TLS 1.2. Se seu computador de conector for de uma versão de 2012 R2 ou anterior, certifique-se de que os seguintes KBs estão instalados no computador: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Entre em contato com seu administrador de rede e peça para verificar se o proxy de back-end e o firewall não bloqueiam SHA512 para tráfego de saída.

**Para verificar o certificado do cliente:**

Verifique a impressão digital do certificado de cliente atual. O armazenamento de certificados pode ser encontrado em %ProgramData%\microsoft\Microsoft AAD Application Proxy Connector\Config\TrustSettings.xml

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

Aqui estão os possíveis valores e significados **do IsInUserStore:**

- **falso** - O certificado cliente foi criado durante a instalação ou registro iniciado pelo comando Register-AppProxyConnector. É armazenado no recipiente pessoal na loja de certificados da máquina local. 

Siga as etapas para verificar o certificado:

1. Executar **certlm.msc**
2. No console de gerenciamento expanda o contêiner Personal e clique em Certificados
3. Localize o certificado emitido por **connectorregistrationca.msappproxy.net**

- **verdadeiro** - O certificado renovado automaticamente é armazenado no recipiente pessoal na loja de certificados do usuário do Serviço de Rede. 

Siga as etapas para verificar o certificado:

1. Baixar [PsTools.zip](https://docs.microsoft.com/sysinternals/downloads/pstools)
2. Extrair [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) do pacote e executar **psexec -i -u "nt authority\network service" cmd.exe** a partir de um prompt de comando elevado.
3. Executar **certmgr.msc** no prompt de comando recém-apareceu
2. No console de gerenciamento expanda o contêiner Personal e clique em Certificados
3. Localize o certificado emitido por **connectorregistrationca.msappproxy.ne

**Para renovar o certificado do cliente:**

Se um conector não estiver conectado ao serviço por vários meses, seus certificados poderão estar desatualizados. A falha na renovação do certificado leva a um certificado vencido. Isso faz com que o serviço do conector pare de funcionar. O evento 1000 é registrado no registro de admin do conector:

"Falha no recadastramento do conector: o certificado de confiança do Connector expirou. Execute o conector PowerShell cmdlet Register-AppProxyConnector no computador no qual o Conector está sendo executado para reregistrar o conector."

Neste caso, desinstale e reinstale o conector para acionar o registro ou você pode executar os seguintes comandos PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

Para saber mais sobre o comando Register-AppProxyConnector, consulte [Criar um script de instalação autônomo para o conector proxy do aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-register-connector-powershell)

## <a name="verify-admin-is-used-to-install-the-connector"></a>Verifique se o administrador é usado para instalar o conector

**Objetivo:** Verifique se o usuário que tenta instalar o conector é um administrador com as credenciais corretas. Atualmente, o usuário deve ser pelo menos um administrador de aplicativos para que a instalação tenha sucesso.

**Para verificar se as credenciais estão corretas:**

Conecte-se ao `https://login.microsoftonline.com` e use as mesmas credenciais. Verifique se o logon foi bem-sucedido. Você pode verificar a função do usuário indo para **usuários e grupos**  - &gt; **do Azure Active Directory**  - &gt; e grupos **de todos os usuários**. 

Selecione sua conta de usuário e, em seguida, "Função de diretório" no menu resultante. Verifique se a função selecionada é "Administrador de aplicativos". Se não for possível acessar qualquer uma das páginas ao longo dessas etapas, você tem a função exigida.

## <a name="next-steps"></a>Próximas etapas
[Entenda os conectores proxy do aplicativo Azure AD](application-proxy-connectors.md)
