---
title: Proxy de aplicativo AD do Azure frequentemente fez perguntas | Microsoft Docs
description: Aprenda respostas às perguntas frequentes (FAQ) sobre o uso do Azure AD Application Proxy para publicar aplicativos internos e locais para usuários remotos.
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
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: d1929f937d86001a0f2a399b1ebd92e47bbd2c86
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990898"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Active Directory (Azure AD) Proxy de aplicativos frequentemente fez perguntas

Esta página responde perguntas frequentes sobre o Proxy de aplicativo do Azure Active Directory (Azure AD).

## <a name="enabling-azure-ad-application-proxy"></a>Habilitando o Proxy de Aplicativo do AD do Azure.

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Qual é a licença necessária para usar o Proxy de aplicativo AD do Azure?

Para usar o Proxy de aplicativo Azure AD, você deve ter uma licença Azure AD Premium P1 ou P2. Para obter mais informações sobre licenciamento, consulte [Preços do Diretório Ativo do Azure](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Por que o botão "Ativar proxy de aplicativo está acinzentado?

Certifique-se de ter pelo menos uma licença AZure AD Premium P1 ou P2 e um conector proxy de aplicativo AD azure instalado. Depois de instalar com sucesso seu primeiro conector, o serviço Proxy do aplicativo Azure AD será ativado automaticamente.

## <a name="connector-configuration"></a>Configuração do conector

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Os serviços do Aplicativo Proxy Connector podem ser executados em um contexto de usuário diferente do padrão?

Não, este cenário não é suportado. As configurações padrão são:

- Conector proxy de aplicativo Microsoft AAD - WAPCSvc - Serviço de rede
- Microsoft AAD Application Proxy Connector Updater - WAPCUpdaterSvc - NT Authority\System

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>Meu aplicativo back-end está hospedado em vários servidores da Web e requer persistência de sessão do usuário (stickiness). Como posso alcançar a persistência da sessão? 

Para recomendações, consulte [Alta disponibilidade e balanceamento de carga dos conectores e aplicativos proxy do aplicativo](application-proxy-high-availability-load-balancing.md).

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>O término do TLS (inspeção ou aceleração TLS/HTTPS) no tráfego dos servidores conectores para o Azure é suportado?

O Conector proxy do aplicativo executa a autenticação baseada em certificados no Azure. O TLS Termination (inspeção ou aceleração TLS/HTTPS) quebra esse método de autenticação e não é suportado. O tráfego do conector para o Azure deve contornar todos os dispositivos que estejam executando o TLS Termination.  

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>Posso colocar um dispositivo proxy avançado entre o servidor conector e o servidor de aplicativo back-end?
Sim, este cenário é suportado a partir da versão conector 1.5.1526.0. Consulte [Trabalhar com servidores proxy existentes no local](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Devo criar uma conta dedicada para registrar o conector com o Proxy do aplicativo Azure AD?

Não há razão para isso. Qualquer conta de administrador global ou administrador de aplicativos funcionará. As credenciais inseridas durante a instalação não são usadas após o processo de registro. Em vez disso, um certificado é emitido para o conector, que é usado para autenticação a partir desse ponto.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Como posso monitorar o desempenho do conector Proxy do aplicativo Azure AD?

Existem contadores de Monitor de Desempenho que são instalados junto com o conector. Para exibi-los:  

1. Selecione **Iniciar,** digite "Perfmon" e pressione ENTER.
2. Selecione **Monitor de** **+** desempenho e clique no ícone verde.
3. Adicione os contadores **do Aplicativo Proxy do Aplicativo Microsoft AAD** que você deseja monitorar.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>O conector proxy do aplicativo Azure AD tem que estar na mesma sub-rede que o recurso?

O conector não é necessário para estar na mesma sub-rede. No entanto, ele precisa de resolução de nomes (DNS, arquivo hosts) para o recurso e a conectividade de rede necessária (roteamento para o recurso, portas abertas no recurso, etc.). Para obter recomendações, consulte [considerações de topologia de rede ao usar o Proxy do Aplicativo de Diretório Ativo do Azure](application-proxy-network-topology.md).

## <a name="application-configuration"></a>Configuração de aplicativo

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Qual é o comprimento do tempo de back-end padrão e "longo"? O tempo pode ser prorrogado?

O comprimento padrão é de 85 segundos. A configuração "longa" é de 180 segundos. O limite de tempo não pode ser estendido.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Como altero a página de aterrissagem das cargas do meu aplicativo?

Na página Registros de aplicativos, você pode alterar a URL da página inicial para a URL externa desejada da página de inicial. A página especificada será carregada quando o aplicativo for lançado no Meus Aplicativos ou no Portal office 365. Para as etapas de configuração, consulte [Definir uma página inicial personalizada para aplicativos publicados usando o Proxy do aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Somente aplicativos baseados em IIS podem ser publicados? E quanto aos aplicativos web em execução em servidores web não-Windows? O conector deve ser instalado em um servidor com o IIS instalado?

Não, não há exigência do IIS para aplicações que são publicadas. Você pode publicar aplicativos web em execução em outros servidores que não o Windows Server. No entanto, você pode não ser capaz de usar a pré-autenticação com um servidor não-Windows, dependendo se o servidor web suporta negociar (autenticação Kerberos). O IIS não é necessário no servidor onde o conector está instalado.

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Quando devo usar o método PrincipalsAllowedToDelegateToAccount ao configurar o Kerberos Constrained Delegation (KCD)?

O método PrincipalsAllowedToDelegateToAccount é usado quando os servidores conectores estão em um domínio diferente da conta de serviço de aplicativo web. Requer o uso de delegação restrita baseada em recursos.
Se os servidores conectores e a conta de serviço de aplicativo web estiverem no mesmo domínio, você poderá usar usuários e computadores do Active Directory para configurar as configurações de delegação em cada uma das contas da máquina conectora, permitindo que eles delegem ao SPN de destino.

Se os servidores conectores e a conta de serviço de aplicativo web estiverem em diferentes domínios, a delegação baseada em recursos será usada. As permissões de delegação são configuradas na conta de serviço de servidor web e aplicativo web. Este método de Delegação Restrita é relativamente novo. O método foi introduzido no Windows Server 2012, que suporta delegação de domínio cruzado, permitindo que o proprietário do recurso (serviço web) controle qual máquina e contas de serviço podem delegar a ele. Não há ui para ajudar com esta configuração, então você precisará usar powerShell.
Para obter mais informações, consulte o whitepaper [Understanding Kerberos Constrained Delegation with Application Proxy](https://aka.ms/kcdpaper).

## <a name="pass-through-authentication"></a>Autenticação de passagem

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Posso usar políticas de acesso condicional para aplicativos publicados com autenticação de passagem?

As Políticas de Acesso Condicional só são aplicadas para usuários pré-autenticados com sucesso no Azure AD. A autenticação de passagem não desencadeia a autenticação do Azure AD, portanto, as Políticas de Acesso Condicional não podem ser aplicadas. Com a autenticação de passagem, as políticas de MFA devem ser implementadas no servidor local, se possível, ou ativando a pré-autenticação com o Azure AD Application Proxy.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Posso publicar um aplicativo web com o requisito de autenticação de certificado de cliente?

Não, este cenário não é suportado porque o Proxy do aplicativo encerrará o tráfego TLS.  

## <a name="remote-desktop-gateway-publishing"></a>Publicação remota do Gateway de Desktop

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Como posso publicar o Remote Desktop Gateway no Proxy de aplicativo Azure AD?

Consulte [publicar desktop remoto com proxy de aplicativo Azure AD](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>Posso usar a Delegação Restrita Kerberos (Single Sign-On - Windows Integrated Authentication) no cenário de publicação do Remote Desktop Gateway?

Não, este cenário não é suportado.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Meus usuários não usam o Internet Explorer 11 e o cenário de pré-autenticação não funciona para eles. Isso é esperado?

Sim, é esperado. O cenário de pré-autenticação requer um controle ActiveX, que não é suportado em navegadores de terceiros.

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>O Cliente Web de Desktop Remoto (HTML5) é suportado?

Não, este cenário não é suportado no momento. Siga nosso fórum de comentários [uservoice](https://aka.ms/aadapuservoice) para obter atualizações sobre este recurso.

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Depois que configurei o cenário de pré-autenticação, percebi que o usuário tem que autenticar duas vezes: primeiro no formulário de login do Azure AD e, em seguida, no formulário de login RDWeb. Isso é esperado? Como posso reduzir isso a um login?

Sim, é esperado. Se o computador do usuário for aazure AD, o usuário entra automaticamente no Azure AD. O usuário precisa fornecer suas credenciais apenas no formulário de login RDWeb.

## <a name="sharepoint-publishing"></a>Publicação sharepoint

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Como posso publicar o SharePoint no Proxy do Aplicativo Azure AD?

Consulte [Ativar acesso remoto ao SharePoint com proxy de aplicativo Azure AD](application-proxy-integrate-with-sharepoint-server.md).

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>Posso usar o aplicativo móvel SharePoint (iOS/ Android) para acessar um servidor SharePoint publicado?

O [aplicativo móvel SharePoint](https://docs.microsoft.com/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises) não suporta a pré-autenticação do Azure Active Directory atualmente.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Publicação de Serviços ativos da Federação de Diretórios (AD FS) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Posso usar o Proxy de aplicativo Ad do Azure como proxy AD FS (como proxy de aplicativo web)?

Não. O Azure AD Application Proxy foi projetado para trabalhar com o Azure AD e não preenche os requisitos para atuar como um proxy AD FS.

## <a name="websocket"></a>Soquete web

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>O suporte ao WebSocket funciona para outros aplicativos além do QlikSense?

Atualmente, o suporte ao protocolo WebSocket ainda está em visualização pública e pode não funcionar para outros aplicativos. Alguns clientes tiveram sucesso misto usando o protocolo WebSocket com outros aplicativos. Se você testar tais cenários, adoraríamos ouvir seus resultados. Por favor, envie-nos o seu feedback em aadapfeedback@microsoft.com.

Os recursos (Eventlogs, PowerShell e Remote Desktop Services) no Windows Admin Center (WAC) ou no Remote Desktop Web Client (HTML5) não funcionam através do Proxy de aplicativo Azure AD atualmente.

## <a name="link-translation"></a>Tradução de link

### <a name="does-using-link-translation-affect-performance"></a>O uso da tradução link afeta o desempenho?

Sim. A tradução de link afeta o desempenho. O serviço Proxy de aplicativo verifica o aplicativo para links codificados e os substitui por seus respectivos URLs externos publicados antes de apresentá-los ao usuário. 

Para obter um melhor desempenho, recomendamos o uso de URLs internos e externos idênticos, configurando [domínios personalizados.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain) Se o uso de domínios personalizados não for possível, você pode melhorar o desempenho de tradução de link usando o My Apps Secure Sign in Extension ou o Microsoft Edge Browser no celular. Consulte [Redirecionar links codificados para aplicativos publicados com o Azure AD Application Proxy](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Curingas

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Como usar curingas para publicar dois aplicativos com o mesmo nome de domínio personalizado, mas com protocolos diferentes, um para HTTP e outro para HTTPS?

Este cenário não é suportado diretamente. Suas opções para este cenário são:

1. Publique os URLs HTTP e HTTPS como aplicativos separados com um curinga, mas dê a cada um deles um domínio personalizado diferente. Essa configuração funcionará, pois eles têm URLS externos diferentes.

2. Publique a URL HTTPS através de um aplicativo curinga. Publique os aplicativos HTTP separadamente usando estes cmdlets do Proxy PowerShell do aplicativo:
   - [Gerenciamento de aplicativos proxy de aplicativos](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [Gerenciamento do conector proxy de aplicativos](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
