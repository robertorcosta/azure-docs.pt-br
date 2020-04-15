---
title: 'Azure AD Connect: Considerações de identidade híbrida para o governo azure'
description: Considerações especiais para implantar o Azure AD Connect com a nuvem do governo.
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378919"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Considerações de identidade híbrida para o governo azure 
O documento a seguir descreve as considerações para a implementação de um ambiente híbrido com a nuvem do Governo Azure.  Essas informações são fornecidas como referência para administradores e arquitetos que estão trabalhando com a nuvem do Governo Azure.  
> [!NOTE] 
> Para integrar um ambiente AD no local com a nuvem Azure Governemnt, você precisa atualizar para a versão mais recente do [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594). 

> [!NOTE] 
> Para obter uma lista completa de pontos finais do DoD do governo dos EUA, consulte a [documentação](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) 

## <a name="pass-through-authentication"></a>Autenticação de Passagem 
As informações a seguir são fornecidas para a implementação da autenticação de passagem (PTA) e da nuvem do Governo Azure.

### <a name="allow-access-to-urls"></a>Permitir acesso às URLs  
Antes de implantar o agente de autenticação de passagem, verifique se há um firewall entre seus servidores e o Azure AD. Se o firewall ou proxy permitir a lista de whitelisting do DNS, adicione as seguintes conexões: 
> [!NOTE]
> A seguinte orientação também se aplica à instalação do [conector Proxy de aplicativo](https://aka.ms/whyappproxy) para ambientes do Governo Azure.

|URL |Como ele é usado|
|-----|-----| 
|*.msappproxy.us *.servicebus.usgovcloudapi.net|Comunicação entre o agente e o serviço de nuvem Azure AD |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 ocsp.msocsp.us:80 www.microsoft.us:80| O agente usa esses URLs para verificar certificados.| 
|login.windows.us *secure.aadcdn.microsoftonline-p.com </br>(microsoftonline.us *microsoftonline-p.us). </br>*msauth.net </br> *msauthimages.net . </br>*msecnd.net</br>* msftauth.net . </br>*msftauthimages.net</br>*.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| O agente usa esses URLs durante o processo de registro.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Instale o agente para a nuvem do Governo Azure 
Para instalar o agente para a nuvem do Governo Azure, você deve seguir estas etapas específicas: No terminal da linha de comando, navegue até a pasta onde o executável para a instalação do agente está localizado. Execute o seguinte comando que especifica a instalação é para O Governo Do Azure. 

Para autenticação de passagem: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

Para proxy de aplicativo:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Logon único 
Configure seu servidor Azure AD Connect: Se você usar a Autenticação de Passagem como método de login, nenhuma verificação adicional de pré-requisito será necessária. Se você usar a sincronização de hash de senha como seu método de login e se houver um firewall entre o Azure AD Connect e o Azure AD, certifique-se de que:
- Está usando a versão 1.1.644.0 ou superior do Azure AD Connect. 
- Se o firewall ou proxy permitir a listagem em branco DNS, adicione as conexões às URLs *.msapproxy.us sobre a porta 443. Caso assim, permita o acesso às faixas IP do data center do Azure, que são atualizadas semanalmente. Esse pré-requisito é aplicável somente quando você habilita o recurso. Não é obrigatório para logons de usuário real. 

### <a name="rolling-out-seamless-sso"></a>Lançando SSO sem emendas 
Você pode gradualmente implantar o SSO Contínuo para seus usuários utilizando as instruções abaixo. Você começa adicionando a seguinte URL Azure AD a todas ou configurações da região intranet dos usuários selecionados usando a Diretiva de grupo no Active Directory:https://autologon.microsoft.us 

Além disso, você precisa ativar uma configuração de diretiva de região intranet chamada Permitir atualizações para barra de status via script através de Diretiva de Grupo. Considerações do navegador Mozilla Firefox (todas as plataformas) O Mozilla Firefox não usa automaticamente a autenticação do Kerberos. Cada usuário precisa adicionar manualmente as URLs do Azure Active Directory às configurações do Firefox com as seguintes etapas: 
1. Execute o Firefox e digite sobre:configure na barra de endereços. Ignore as notificações que aparecerem. 
2. Procure a preferência network.negotiate-auth.trusted-uris. Esta preferência lista os sites confiáveis do Firefox para a autenticação Kerberos. 
3. Clique com o botão direito do mouse e selecione Modificar. 
4. Entre https://autologon.microsoft.us no campo.
5. Selecione OK e, em seguida, reabra o navegador. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge baseado no Chromium (todas as plataformas) 
Se você tiver substituído `AuthNegotiateDelegateAllowlist` as `AuthServerAllowlist` configurações de diretiva ou as configurações de diretiva emhttps://autologon.microsoft.us) seu ambiente, certifique-se de adicionar a URL do Azure AD (a eles também. 

### <a name="google-chrome-all-platforms"></a>Google Chrome (todas as plataformas) 
Se você tiver substituído `AuthNegotiateDelegateWhitelist` as `AuthServerWhitelist` configurações de diretiva ou as configurações de diretiva emhttps://autologon.microsoft.us) seu ambiente, certifique-se de adicionar a URL do Azure AD (a eles também. 

## <a name="next-steps"></a>Próximas etapas
[Aprovação de autenticação](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[única de login](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) 
