---
title: 'Azure AD Connect: considerações de identidade híbrida para o Azure governamental'
description: Considerações especiais para a implantação de Azure AD Connect com a nuvem governamental.
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
ms.openlocfilehash: 2ad28beb68afb5207e7b36c5d76c4dac808c5114
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378919"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Considerações de identidade híbrida para o Azure Government 
O documento a seguir descreve as considerações para implementar um ambiente híbrido com a nuvem do Azure governamental.  Essas informações são fornecidas como referência para administradores e arquitetos que trabalham com a nuvem do Azure governamental.  
> [!NOTE] 
> Para integrar um ambiente do AD local com a nuvem do Azure governemnt, você precisa atualizar para a versão mais recente do [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594). 

> [!NOTE] 
> Para obter uma lista completa dos pontos de extremidade do DoD do governo dos EUA, consulte a [documentação](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) 

## <a name="pass-through-authentication"></a>Autenticação de Passagem 
As informações a seguir são fornecidas para a implementação da autenticação de passagem (PTA) e da nuvem do Azure governamental.

### <a name="allow-access-to-urls"></a>Permitir acesso às URLs  
Antes de implantar o agente de autenticação de passagem, verifique se há um firewall entre os servidores e o Azure AD. Se o firewall ou o proxy permitir a lista de permissões de DNS, adicione as seguintes conexões: 
> [!NOTE]
> As diretrizes a seguir também se aplicam à instalação do [conector de proxy de aplicativo](https://aka.ms/whyappproxy) para ambientes do Azure governamental.

|URL |Como ele é usado|
|-----|-----| 
|*. msappproxy.us *. servicebus.usgovcloudapi.net|Comunicação entre o agente e o serviço de nuvem do Azure AD |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| O agente usa essas URLs para verificar os certificados.| 
|login.Windows.us Secure.aadcdn.microsoftonline-p.com *. microsoftonline.us </br> *. microsoftonline-p.us </br>*. msauth.NET </br> *. msauthimages.net </br>*. msecnd.NET</br>*. msftauth.net </br>*. msftauthimages.NET</br>*. PhoneFactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| O agente usa essas URLs durante o processo de registro.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Instalar o agente para a nuvem do Azure governamental 
Para instalar o agente para a nuvem do Azure governamental, você deve seguir estas etapas específicas: no terminal de linha de comando, navegue até a pasta onde o executável para instalar o agente está localizado. Execute o comando a seguir, que especifica a instalação do Azure governamental. 

Para autenticação de passagem: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

Para o proxy de aplicativo:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Logon único 
Configurar seu servidor de Azure AD Connect: se você usar a autenticação de passagem como seu método de entrada, nenhuma verificação de pré-requisito adicional será necessária. Se você usar a sincronização de hash de senha como seu método de entrada e se houver um firewall entre Azure AD Connect e o Azure AD, verifique se:
- Está usando a versão 1.1.644.0 ou superior do Azure AD Connect. 
- Se o firewall ou o proxy permitir a lista de permissões de DNS, adicione as conexões às URLs *. msapproxy.us pela porta 443. Caso contrário, permita o acesso aos intervalos de IP do datacenter do Azure, que são atualizados semanalmente. Esse pré-requisito é aplicável somente quando você habilita o recurso. Não é obrigatório para logons de usuário real. 

### <a name="rolling-out-seamless-sso"></a>Distribuindo o SSO contínuo 
Você pode gradualmente implantar o SSO Contínuo para seus usuários utilizando as instruções abaixo. Você começa adicionando a seguinte URL do Azure AD a todas ou a configurações de zona de intranet dos usuários selecionados usando Política de Grupo no Active Directory:https://autologon.microsoft.us 

Além disso, você precisa habilitar uma configuração de política de zona de intranet chamada permitir atualizações na barra de status por meio de script por meio de Política de Grupo. As considerações do navegador Mozilla Firefox (todas as plataformas) Mozilla Firefox não usam automaticamente a autenticação Kerberos. Cada usuário precisa adicionar manualmente as URLs do Azure Active Directory às configurações do Firefox com as seguintes etapas: 
1. Execute o Firefox e digite about: config na barra de endereços. Ignore as notificações que aparecerem. 
2. Procure a preferência Network. Negotiate. Trusted-URIs. Esta preferência lista os sites confiáveis do Firefox para a autenticação Kerberos. 
3. Clique com o botão direito do mouse e selecione Modificar. 
4. Insira https://autologon.microsoft.us no campo.
5. Selecione OK e reabra o navegador. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge com base em Chromium (todas as plataformas) 
Se você tiver substituído as `AuthNegotiateDelegateAllowlist` configurações de `AuthServerAllowlist` política ou em seu ambiente, certifique-se de adicionar a URL do Azurehttps://autologon.microsoft.us) AD (a elas também. 

### <a name="google-chrome-all-platforms"></a>Google Chrome (todas as plataformas) 
Se você tiver substituído as `AuthNegotiateDelegateWhitelist` configurações de `AuthServerWhitelist` política ou em seu ambiente, certifique-se de adicionar a URL do Azurehttps://autologon.microsoft.us) AD (a elas também. 

## <a name="next-steps"></a>Próximas etapas
[Pass-through Authentication](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[Logon único](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) de autenticação de passagem 
