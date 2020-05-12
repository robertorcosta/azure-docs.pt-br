---
title: 'Azure AD Connect: considerações de identidade híbrida para a nuvem do Azure governamental'
description: Considerações especiais para a implantação de Azure AD Connect com a nuvem do Azure governamental.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: acdc99ca50255bd9b75828f0a051f364c5218471
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115482"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Considerações de identidade híbrida para a nuvem do Azure governamental

Este artigo descreve as considerações para a integração de um ambiente híbrido com o Microsoft Azure Governamental Cloud. Essas informações são fornecidas como uma referência para administradores e arquitetos que trabalham com a nuvem do Azure governamental.

> [!NOTE]
> Para integrar um ambiente do Microsoft Azure Active Directory (Azure AD) local com a nuvem do Azure governamental, você precisa atualizar para a versão mais recente do [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594).

Para obter uma lista completa de pontos de extremidade do departamento de defesa Estados Unidos do governo, consulte a [documentação](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints).

## <a name="azure-ad-pass-through-authentication"></a>O que é a Autenticação de Passagem do Azure AD

As informações a seguir descrevem a implementação de autenticação de passagem e a nuvem do Azure governamental.

### <a name="allow-access-to-urls"></a>Permitir acesso às URLs

Antes de implantar o agente de autenticação de passagem, verifique se existe um firewall entre os servidores e o Azure AD. Se o firewall ou proxy permitir programas bloqueados ou seguros do sistema de nomes de domínio (DNS), adicione as seguintes conexões.

> [!NOTE]
> As diretrizes a seguir também se aplicam à instalação do [conector de proxy de aplicativo do AD do Azure](https://aka.ms/whyappproxy) para ambientes do Azure governamental.

|URL |Como ele é usado|
|-----|-----|
|&#42;. msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|O agente usa essas URLs para se comunicar com o serviço de nuvem do Azure AD. |
|mscrl.microsoft.us:80 </br>crl.microsoft.us:80 </br>ocsp.msocsp.us:80 </br>www.microsoft.us:80| O agente usa essas URLs para verificar os certificados.|
|login.windows.us </br>Secure.aadcdn.microsoftonline p.com </br>&#42;. microsoftonline.us </br>&#42;. microsoftonline-p.us </br>&#42;. msauth.net </br>&#42;. msauthimages.net </br>&#42;. msecnd.net</br>&#42;. msftauth.net </br>&#42;. msftauthimages.net</br>&#42;. phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| O agente usa essas URLs durante o processo de registro.

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Instalar o agente para a nuvem do Azure governamental

Siga estas etapas para instalar o agente para a nuvem do Azure governamental:

1. No terminal de linha de comando, vá para a pasta que contém o arquivo executável que instala o agente.
1. Execute os comandos a seguir, que especificam que a instalação é para o Azure governamental.

   Para autenticação de passagem:

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   Para o proxy de aplicativo:

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## <a name="single-sign-on"></a>Logon único

### <a name="set-up-your-azure-ad-connect-server"></a>Configurar o servidor de Azure AD Connect

Se você usar a autenticação de passagem como seu método de logon, nenhuma verificação de pré-requisito adicional será necessária. Se você usar a sincronização de hash de senha como seu método de logon e houver um firewall entre Azure AD Connect e o Azure AD, verifique se:

- Use Azure AD Connect versão 1.1.644.0 ou posterior.
- Se o firewall ou o proxy permitir programas bloqueados ou seguros do DNS, adicione as conexões às URLs do &#42;. msappproxy.us pela porta 443.

  Caso contrário, permita o acesso aos intervalos de IP do datacenter do Azure, que são atualizados semanalmente. Esse pré-requisito se aplica somente quando você habilita o recurso. Ele não é necessário para os logons reais do usuário.

### <a name="roll-out-seamless-single-sign-on"></a>Distribuir o logon único contínuo

Você pode distribuir gradualmente o logon único contínuo do Azure AD para seus usuários usando as instruções a seguir. Você começa adicionando a URL do Azure AD [https://autologon.microsoft.us](https://autologon.microsoft.us) a todas ou a configurações de zona de intranet dos usuários selecionados usando política de grupo em Active Directory.

Você também precisa habilitar a configuração de política de zona de intranet **permitir atualizações na barra de status por meio de script por meio de política de grupo**.

## <a name="browser-considerations"></a>Considerações de navegador

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (todas as plataformas)

O Mozilla Firefox não usa a autenticação Kerberos automaticamente. Cada usuário deve adicionar manualmente a URL do Azure AD às configurações do Firefox seguindo estas etapas:

1. Execute o Firefox e digite **about: config**   na barra de endereços. Ignore todas as notificações que você possa ver.
1. Procure a preferência **Network. Negotiate. Trusted-URIs**   . Essa preferência lista os sites confiáveis pelo Firefox para autenticação Kerberos.
1. Clique com o botão direito do mouse no nome da preferência e selecione **Modificar**.
1. Digite  [**https://autologon.microsoft.us**](https://autologon.microsoft.us**)   na caixa.
1. Selecione **OK**   e reabra o navegador.

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge com base em Chromium (todas as plataformas)

Se você tiver substituído as  `AuthNegotiateDelegateAllowlist`   `AuthServerAllowlist`   configurações de política ou em seu ambiente, certifique-se de adicionar a URL do Azure ad [https://autologon.microsoft.us](https://autologon.microsoft.us) a elas.

### <a name="google-chrome-all-platforms"></a>Google Chrome (todas as plataformas)

Se você tiver substituído as  `AuthNegotiateDelegateWhitelist`   `AuthServerWhitelist`   configurações de política ou em seu ambiente, certifique-se de adicionar a URL do Azure ad [https://autologon.microsoft.us](https://autologon.microsoft.us) a elas.

## <a name="next-steps"></a>Próximas etapas

- [Autenticação de passagem](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [Logon único](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
