---
title: Habilite o acesso remoto ao Power BI com proxy de aplicativo Azure AD
description: Abrange o básico sobre como integrar um POWER BI no local com o Proxy de aplicativo AD do Azure.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc96c94152b39cc70cfc4553690faaa5b9cb8d20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111579"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Habilitar acesso remoto ao Power BI Mobile com o Proxy de Aplicativo do Azure AD

Este artigo discute como usar o Azure AD Application Proxy para permitir que o aplicativo móvel Power BI se conecte ao Power BI Report Server (PBIRS) e ao SQL Server Reporting Services (SSRS) 2016 e posteriormente. Por meio dessa integração, os usuários que estão longe da rede corporativa podem acessar seus relatórios de Power BI a partir do aplicativo móvel Power BI e ser protegidos pela autenticação Azure AD. Essa proteção inclui [benefícios de segurança,](application-proxy-security.md#security-benefits) como acesso condicional e autenticação multifatorial.  

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já implantou serviços de relatório e [ativou o Proxy de aplicativos](application-proxy-add-on-premises-application.md).

- Ativar o Proxy do aplicativo requer a instalação de um conector em um servidor Windows e completar os [pré-requisitos para](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) que o conector possa se comunicar com os serviços do Azure AD.  
- Ao publicar o Power BI, recomendamos que você use os mesmos domínios internos e externos. Para saber mais sobre domínios personalizados, consulte [Trabalhando com domínios personalizados no Proxy de aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain).
- Esta integração está disponível para o aplicativo **Power BI Mobile iOS e Android.**

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Passo 1: Configurar a Delegação Restrita Kerberos (KCD)

Para aplicativos locais que usam a autenticação do Windows, você pode obter SSO (logon único) usando o protocolo de autenticação Kerberos e um recurso chamado KCD (delegação restrita de Kerberos). Quando configurado, o KCD permite que o conector Proxy do aplicativo obtenha um token do Windows para um usuário, mesmo que o usuário não tenha entrado diretamente no Windows. Para saber mais sobre o KCD, consulte [A Visão Geral da Delegação Restrita kerberos](https://technet.microsoft.com/library/jj553400.aspx) e a delegação restrita de [Kerberos para o login único em seus aplicativos com proxy de aplicativo](application-proxy-configure-single-sign-on-with-kcd.md).

Não há muito o que configurar no lado do Reporting Services. Apenas certifique-se de ter um Nome Principal de Serviço (SPN) válido para permitir que a autenticação kerberos adequada ocorra. Certifique-se também de que o servidor Reporting Services esteja habilitado para autenticação de Negociação.

Para configurar os serviços de KCD para Relatórios, continue com as seguintes etapas.

### <a name="configure-the-service-principal-name-spn"></a>Configure o Nome Principal do Serviço (SPN)

O SPN é um identificador exclusivo para um serviço que usa a autenticação Kerberos. Você precisará ter certeza de que tem um SPN HTTP adequado presente para o seu servidor de relatórios. Para obter informações sobre como configurar o SPN (Nome da Entidade de Serviço) adequado para seu servidor de relatório, consulte [Registrar um SPN (Nome da Entidade de Serviço) para um servidor de relatório](https://msdn.microsoft.com/library/cc281382.aspx).
Você pode verificar se o SPN foi adicionado executando o comando Setspn com a opção -L. Para saber mais sobre esse comando, confira [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Habilitar a autenticação de negociação

Para habilitar um servidor de relatórios para usar a autenticação kerberos, configure o tipo de autenticação do servidor de relatórios para ser RSWindowsNegotiate. Configure esta configuração usando o arquivo rsreportserver.config.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Para obter mais informações, consulte [Modify a Reporting Services Configuration File (Modificar um arquivo de configuração de serviços)](https://msdn.microsoft.com/library/bb630448.aspx) e [Configurar a Autenticação do Windows no servidor de relatório](https://msdn.microsoft.com/library/cc281253.aspx).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Certifique-se de que o Conector é confiável para a delegação ao SPN adicionado à conta do pool de aplicativos do Reporting Services
Configure o KCD para que o serviço Proxy do aplicativo Azure AD possa delegar as identidades dos usuários na conta do pool de aplicativos do Reporting Services. Configure a KCD habilitando o conector Proxy de Aplicativo para recuperar tíquetes Kerberos para seus usuários que foram autenticados no Azure AD. Em seguida, esse servidor passa o contexto para o aplicativo de destino ou Serviços de relatórios neste caso.

Para configurar o KCD, repita as seguintes etapas para cada máquina conectora:

1. Faça login em um controlador de domínio como administrador de domínio e, em seguida, abra **usuários e computadores do Active Directory**.
2. Encontre o computador em que o conector está em sendo executado.  
3. Clique duas vezes no computador e selecione a guia **Delegação.**
4. Defina as configurações da delegação para confiar neste computador apenas para a delegação para **os serviços especificados.** Então, selecione **Usar qualquer protocolo de autenticação**.
5. Selecione **Adicionar**e, em seguida, selecione **Usuários ou Computadores**.
6. Digite a conta de serviço que você está usando para serviços de relatórios. Esta é a conta que você adicionou o SPN para dentro da configuração do Reporting Services.
7. Clique em **OK**. Para salvar as alterações, clique em **OK** novamente.

Para obter mais informações, consulte [A Delegação Restrita kerberos para fazer login único em seus aplicativos com proxy de aplicativo](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Passo 2: Publicar serviços de relatório através do proxy do aplicativo Azure AD

Agora você está pronto para configurar o Proxy de aplicativo Azure AD.

1. Publique serviços de relatório através do Proxy do aplicativo com as seguintes configurações. Para obter instruções passo-a-passo sobre como publicar um aplicativo através do Proxy do aplicativo, consulte [aplicativos de publicação usando o Proxy de aplicativo Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   - **URL interno**: Digite a URL no Servidor de Relatórios que o conector pode alcançar na rede corporativa. Certifique-se de que essa URL pode ser acessível a partir do servidor em que o conector está instalado. Uma prática recomendada é usar um `https://servername/` domínio de alto nível, como evitar problemas com subcaminhos publicados através do Proxy de aplicativo. Por exemplo, `https://servername/` use `https://servername/reports/` `https://servername/reportserver/`e não ou .
     > [!NOTE]
     > Recomendamos o uso de uma conexão HTTPS segura ao Servidor de Relatórios. Consulte [Configurar conexões SSL em um servidor de relatório de modo nativo](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017) para obter informações sobre como.
   - **URL externo**: Digite a URL pública a que o aplicativo móvel Power BI se conectará. Por exemplo, pode `https://reports.contoso.com` parecer que se um domínio personalizado for usado. Para usar um domínio personalizado, carregue um certificado para o domínio e aponte um registro de DNS para o domínio padrão msappproxy.net para o seu aplicativo. Para obter etapas detalhadas, consulte [Trabalhando com domínios personalizados no Proxy de aplicativo Azure AD](application-proxy-configure-custom-domain.md).

   - **Método de pré-autenticação**: Diretório Ativo do Azure

2. Depois que seu aplicativo for publicado, defina as configurações de logon único com as seguintes etapas:

   a. Na página de aplicativo no portal, selecione **Logon único**.

   b. Para **o modo de inscrição única,** selecione **Autenticação integrada do Windows**.

   c. Defina o **SPN do Aplicativo Interno** para o valor que você definiu anteriormente.  

   d. Escolha a **Identidade de Logon Delegada** para que o conector use em nome de seus usuários. Para obter mais informações, consulte [Trabalhando com diferentes identidades no local e na nuvem.](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

   e. Clique em **Salvar** para salvar as alterações.

Para concluir a configuração do seu aplicativo, vá até a seção **Usuários e grupos** e designe os usuários para acessar este aplicativo.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>Passo 3: Modificar os URI de resposta para o aplicativo

Antes que o aplicativo móvel Power BI possa se conectar e acessar os Serviços de Relatório, você deve configurar o Registro de Aplicativo que foi criado automaticamente para você na etapa 2. 

1. Na página **Visão geral** do Diretório Ativo do Azure, selecione registros **do aplicativo**.
2. Na guia **Todos os aplicativos,** procure o aplicativo criado na etapa 2.
3. Selecione o aplicativo e selecione **Autenticação**.
4. Adicione as URIs de redirecionamento a seguir com base na plataforma que você está usando.

   Ao configurar o aplicativo para Power BI Mobile **iOS**, adicione as seguintes URIs de redirecionamento do tipo Cliente Público (Mobile & Desktop):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   Ao configurar o aplicativo para Power BI Mobile **Android,** adicione as seguintes URIs de redirecionamento do tipo Cliente Público (Mobile & Desktop):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D` 
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > Os URIs de redirecionamento devem ser adicionados para que o aplicativo funcione corretamente. Se você estiver configurando o aplicativo para Power BI Mobile iOS e Android, adicione o seguinte Uri redirecionado do tipo Cliente `urn:ietf:wg:oauth:2.0:oob`Público (Mobile & Desktop) à lista de URIs de redirecionamento configurados para iOS: .

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Passo 4: Conecte-se a partir do power bi aplicativo móvel

1. No aplicativo móvel Power BI, conecte-se à instância dos Serviços de Relatórios. Para fazer isso, digite a **URL externa** para o aplicativo que você publicou através do Proxy do aplicativo.

   ![Aplicativo móvel Power BI com URL externo](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Selecione **Conectar**. Você será direcionado para o sinal do Diretório Ativo do Azure na página.

3. Digite credenciais válidas para o usuário e selecione **Fazer login**. Você verá os elementos do servidor de serviços de relatórios.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Passo 5: Configure a política Intune para dispositivos gerenciados (opcional)

Você pode usar o Microsoft Intune para gerenciar os aplicativos clientes que a força de trabalho da sua empresa usa. O Intune permite que você use recursos como criptografia de dados e requisitos adicionais de acesso. Para saber mais sobre o gerenciamento de aplicativos através do Intune, consulte Intune App Management. Para permitir que o aplicativo móvel Power BI trabalhe com a diretiva Intune, use as seguintes etapas.

1. Vá para **o Azure Active Directory** e, em seguida, **registros de aplicativos**.
2. Selecione o aplicativo configurado na Etapa 3 ao registrar seu aplicativo cliente nativo.
3. Na página do aplicativo, selecione **Permissões de API**.
4. Clique **em Adicionar uma permissão**. 
5. Em **APIs que minha organização usa,** procure por "Microsoft Mobile Application Management" e selecione-o.
6. Adicionar a permissão **DeviceManagementManagedApps.ReadWrite** ao aplicativo
7. Clique em **conceder o consentimento do admin** grant para conceder o acesso à permissão ao aplicativo.
8. Configure a diretiva Intune desejando referindo-se [a Como criar e atribuir políticas de proteção de aplicativos](https://docs.microsoft.com/intune/app-protection-policies).

## <a name="troubleshooting"></a>Solução de problemas

Se o aplicativo retornar uma página de erro depois de tentar carregar um relatório por mais de alguns minutos, talvez seja necessário alterar a configuração de tempo. Por padrão, o Proxy de aplicativo suporta aplicativos que levam até 85 segundos para responder a uma solicitação. Para alongar essa configuração para 180 segundos, selecione o tempo de backup para **Long** na página de configurações do App Proxy para o aplicativo. Para obter dicas sobre como criar relatórios rápidos e confiáveis, consulte [As Melhores Práticas do Power BI Reports](https://docs.microsoft.com/power-bi/power-bi-reports-performance).

## <a name="next-steps"></a>Próximas etapas

- [Habilite aplicativos clientes nativos para interagir com aplicativos proxy](application-proxy-configure-native-client-application.md)
- [Exibir KPIs e relatórios locais do servidor de relatório nos aplicativos móveis do Power BI](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
