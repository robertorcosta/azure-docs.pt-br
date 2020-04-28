---
title: Portal do usuário para o servidor MFA do Azure-Azure Active Directory
description: Introdução ao Azure MFA e ao portal do usuário.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1869fac973cd4cd68e1e91be89c25fdf1427f6a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80653202"
---
# <a name="user-portal-for-the-azure-multi-factor-authentication-server"></a>Portal do usuário para o Servidor de Autenticação Multifator do Azure

O portal do usuário é um site do IIS que permite aos usuários se registrar na MF (Autenticação Multifator do Azure) e manter suas contas. Um usuário pode alterar o número de telefone, alterar o PIN ou optar por ignorar a verificação em duas etapas durante a próxima entrada.

Os usuários entram no portal do usuário com o seu nome de usuário normal e uma senha e, depois, concluem uma chamada de verificação em duas etapas ou respondem a perguntas de segurança para concluir a autenticação. Se o registro do usuário for autorizado, o usuário configurará seu número de telefone e PIN na primeira vez que entrar no portal do usuário.

Os Administradores do portal do usuário podem receber uma configuração e permissão para adicionar novos usuários e atualizar usuários existentes.

Dependendo do seu ambiente, convém implantar o portal do usuário no mesmo servidor do Servidor de Autenticação Multifator do Azure ou em outro servidor voltado para a internet.

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferecerá mais o servidor MFA para novas implantações. Novos clientes que queiram exigir a autenticação multifator de seus usuários devem usar a autenticação multifator do Azure baseada em nuvem. Os clientes existentes que ativaram o servidor MFA antes de 1º de julho poderão baixar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

![Página de logon do portal do usuário do servidor MFA](./media/howto-mfaserver-deploy-userportal/portal.png)

> [!NOTE]
> O portal do usuário está disponível apenas com o Servidor de Autenticação Multifator. Se você usar Autenticação Multifator na nuvem, encaminhe os seus usuários para [Configurar sua conta para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-first-time.md) ou [Gerenciar as configurações de verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md).

## <a name="install-the-web-service-sdk"></a>Instalar o SDK de Serviço Web

Seja qual for o cenário, se o SDK de Serviço Web da Autenticação Multifator do Azure ainda **não** estiver instalado no Servidor da MFA (Autenticação Multifator) do Azure, conclua as etapas a seguir.

1. Abra o console do Servidor de Autenticação Multifator.
2. Vá para o **SDK do serviço Web** e selecione **Instalar o SDK do serviço Web**.
3. Conclua a instalação usando os padrões, a não ser que você precise alterá-los por algum motivo.
4. Associar um certificado TLS/SSL ao site no IIS.

Se você tiver dúvidas sobre como configurar um certificado TLS/SSL em um servidor IIS, consulte o artigo [como configurar o SSL no IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis).

O SDK do serviço Web deve ser protegido com um certificado TLS/SSL. Um certificado autoassinado é suficiente para essa finalidade. Importe o certificado para o repositório "autoridades de certificação raiz confiáveis" da conta do computador local no servidor Web do portal do usuário para que ele confie no certificado ao iniciar a conexão TLS.

![SDK do serviço Web de configuração do Servidor MFA](./media/howto-mfaserver-deploy-userportal/sdk.png)

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Implantar o Portal do Usuário no servidor que o Servidor de Autenticação Multifator do Azure

Os pré-requisitos a seguir são necessários para instalar o portal do usuário no **mesmo servidor** que o servidor de autenticação multifator do Azure:

* O IIS, incluindo a compatibilidade da metabase do IIS 6 e ASP.NET (para o IIS 7 ou superior)
* Uma conta com direitos de administrador para o computador e o Domínio, se aplicável. A conta precisa ter permissões para criar grupos de segurança do Active Directory.
* Proteja o portal do usuário com um certificado TLS/SSL.
* Proteja o SDK do serviço Web da autenticação multifator do Azure com um certificado TLS/SSL.

Para implantar o portal do usuário, siga estas etapas:

1. Abra o console do Servidor de Autenticação Multifator do Azure, clique no ícone **Portal do Usuário** no menu à esquerda e clique em **Instalar Portal do Usuário**.
2. Conclua a instalação usando os padrões, a não ser que você precise alterá-los por algum motivo.
3. Associar um certificado TLS/SSL ao site no IIS

   > [!NOTE]
   > Esse certificado TLS/SSL é geralmente um certificado TLS/SSL assinado publicamente.

4. Abra um navegador da Web em qualquer computador e navegue até a URL em que o portal do usuário foi instalado `https://mfa.contoso.com/MultiFactorAuth`(exemplo:). Certifique-se de que nenhum aviso de certificado ou erro seja exibido.

![Instalação do portal de usuário do Servidor MFA](./media/howto-mfaserver-deploy-userportal/install.png)

Se você tiver dúvidas sobre como configurar um certificado TLS/SSL em um servidor IIS, consulte o artigo [como configurar o SSL no IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis).

## <a name="deploy-the-user-portal-on-a-separate-server"></a>Implantar o portal do usuário em um servidor separado

Se o servidor no qual o Azure Servidor de Autenticação Multifator está em execução não for voltado para a Internet, você deverá instalar o portal do usuário em um **servidor separado da Internet**.

Se sua organização usa o aplicativo Microsoft Authenticator como um dos métodos de verificação e deseja implantar o portal do usuário em seu próprio servidor, complete os seguintes requisitos:

* Use a v6.0 ou superior do Servidor de Autenticação Multifator do Azure.
* Instale portal do usuário em um servidor da Web da Internet executando o Microsoft Internet Information Services (IIS) 6.x ou superior.
* Ao usar o IIS 6.x, certifique-se de que o ASP.NET v2.0.50727 esteja instalado, registrado e definido como **Permitido**.
* Ao usar o IIS 7.x ou superior, o IIS, incluindo autenticação básica, ASP.NET e compatibilidade de metabase com o IIS 6.
* Proteja o portal do usuário com um certificado TLS/SSL.
* Proteja o SDK do serviço Web da autenticação multifator do Azure com um certificado TLS/SSL.
* Verifique se o portal do usuário pode se conectar ao SDK do serviço Web da autenticação multifator do Azure sobre TLS/SSL.
* Certifique-se de que o portal do usuário pode realizar a autenticação para o SDK do Serviço Web Autenticação Multifator do Azure usando as credenciais de uma conta de serviço no grupo de segurança "PhoneFactor Admins". Essa conta de serviço e o grupo devem existir no Active Directory se o Servidor de Autenticação Multifator do Azure for executado em um servidor ingressado em um domínio. Essa conta de serviço e o grupo existem localmente no Servidor de Autenticação Multifator do Azure se ele não tiver ingressado em um domínio.

A instalação do portal do usuário em um servidor diferente do Servidor de Autenticação Multifator do Azure exige as seguintes etapas:

1. **No Servidor MFA**, navegue até o caminho de instalação (exemplo: C:\Arquivos de Progrtama\Servidor de Autenticação Multifator) e copie o arquivo **MultiFactorAuthenticationUserPortalSetup64** em um local acessível para o servidor da Internet onde ele será instalado.
2. **No servidor Web voltado para a Internet**, execute o arquivo de instalação MultiFactorAuthenticationUserPortalSetup64 como administrador, mude o site, se desejar, e encurte o nome do diretório virtual, se desejar.
3. Associar um certificado TLS/SSL ao site no IIS.

   > [!NOTE]
   > Esse certificado TLS/SSL é geralmente um certificado TLS/SSL assinado publicamente.

4. Navegue até **C:\inetpub\wwwroot\MultiFactorAuth**
5. Edite o arquivo Web.Config no Bloco de Notas

    * Localize a chave **"USE_WEB_SERVICE_SDK"** e altere **value="false"** para **value="true"**
    * Localize a chave **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** e altere **value=""** para **value="DOMAIN\User"**, em que DOMAIN/User é uma conta de serviço que faz parte do Grupo “Administradores do PhoneFactor”.
    * Localize a chave **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** e altere **value=""** para **value="Password"**, em que Password é a senha da conta de serviço inserida na linha anterior.
    * Localize o valor **https://www.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx** e altere essa URL de espaço reservado para a URL do SDK do serviço Web que instalamos na etapa 2.
    * Salve o arquivo Web.Config e feche o Bloco de Notas.

6. Abra um navegador da Web em qualquer computador e navegue até a URL em que o portal do usuário foi instalado `https://mfa.contoso.com/MultiFactorAuth`(exemplo:). Certifique-se de que nenhum aviso de certificado ou erro seja exibido.

Se você tiver dúvidas sobre como configurar um certificado TLS/SSL em um servidor IIS, consulte o artigo [como configurar o SSL no IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis).

## <a name="configure-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Configurar o portal do usuário no Servidor de Autenticação Multifator do Microsoft Azure

Agora que o portal do usuário está instalado, você precisa configurar o Servidor de Autenticação Multifator do Azure para trabalhar com o portal.

1. No console do Servidor de Autenticação Multifator do Azure, clique no ícone **Portal do Usuário**. Na guia Configurações, insira a URL no Portal do Usuário na caixa de texto **URL do Portal do Usuário**. Se a funcionalidade de email tiver sido habilitada, essa URL é incluída em emails que são enviados aos usuários quando eles são importados para o Servidor de Autenticação Multifator do Azure.
2. Escolha as configurações que você deseja usar no Portal do Usuário. Por exemplo, se os usuários tiverem permissão para escolher seus métodos de autenticação, verifique se **Permitir que usuários selecionem o método** está selecionado junto com os métodos que eles podem escolher.
3. Defina quem deve ser administradores na guia **Administradores** . Você pode criar permissões administrativas granulares usando as caixas de seleção e os menus suspensos nas caixa Adicionar/Editar.

Configuração opcional:

- **Perguntas de Segurança**: defina perguntas de segurança aprovadas para seu ambiente e o idioma em que elas aparecem.
- **Sessões Passadas**: configure a integração do portal do usuário com um site baseado em formulário usando a MFA.
- **IPs confiáveis**: permita que os usuários ignorem a MFA ao autenticar de uma lista de IPs ou intervalos confiáveis.

![Configuração do portal de usuário do Servidor MFA](./media/howto-mfaserver-deploy-userportal/config.png)

O servidor de Autenticação Multifator do Azure fornece várias opções para o portal do usuário. A tabela a seguir fornece uma lista dessas opções e uma explicação de como elas são usadas.

| Configurações do Portal de Usuário | Descrição |
|:--- |:--- |
| URL do portal do usuário | Insira a URL de onde o portal está sendo hospedado. |
| Autenticação primária | Especifique o tipo de autenticação a ser usado ao entrar no portal. Autenticação do Windows, Radius ou LDAP. |
| Permitir que os usuários façam logon | Permita que os usuários insiram um nome de usuário e uma senha na página de entrada para o portal do usuário. Se essa opção não estiver selecionada, as caixas ficarão esmaecidas. |
| Permitir registro de usuário | Permita que o usuário se registre na Autenticação Multifator levando-os para uma tela de instalação que solicita informações adicionais, como número de telefone. O prompt de telefone de backup permite que os usuários especifiquem um número de telefone secundário. O prompt de token OATH permite que os usuários especifiquem um token OATH de terceiros. |
| Permitir que os usuários iniciem Desvio Único | Permitir que os usuários iniciem um desvio único. Se um usuário definir essa opção, ela terá efeito na próxima vez em que ele entrar. O prompt de ignorar segundos fornece ao usuário uma caixa para que ele possa alterar o padrão de 300 segundos. Caso contrário, o desvio único é válido somente por 300 segundos. |
| Permitir que os usuários selecionem o método | Permita aos usuários especificar o método de contato principal. Esse método pode ser uma chamada telefônica, uma mensagem de texto, um aplicativo móvel ou token OATH. |
| Permitir que os usuários selecionem o idioma | Permita que os usuários alterem o idioma usado para chamada telefônica, mensagem de texto, aplicativo móvel ou token OATH. |
| Permitir que os usuários ativem aplicativos móveis | Permita que os usuários gerem um código de ativação para concluir o processo de ativação de aplicativo móvel que seja usado com o servidor.  Você também pode definir o número de dispositivos em que eles podem ativar o aplicativo, entre 1 e 10. |
| Usar perguntas de segurança para fallback | Permita perguntas de segurança caso a verificação em duas etapas falhe. Você pode especificar o número de perguntas de segurança que devem ser respondidas com sucesso. |
| Permitir que os usuários associem token OATH de terceiros | Permita que os usuários especifiquem um token OATH de terceiros. |
| Usar token OATH para fallback | Permita o uso de um token OATH caso a verificação em duas etapas não seja bem-sucedida. Você também pode especificar o tempo limite da sessão em minutos. |
| Habilitar o registro em log | Habilite o registro em log no portal do usuário. Os arquivos de log estão localizados em: C:\Program Files\Servidor de Autenticação Multifator\Logs. |

> [!IMPORTANT]
> A partir de março de 2019, as opções de telefonema não estarão disponíveis para usuários do servidor MFA em locatários gratuitos/de avaliação do Azure AD. As mensagens SMS não são afetadas por essa alteração. A chamada telefônica continuará disponível para os usuários em locatários pagos do Azure AD. Essa alteração afeta apenas os locatários gratuitos/de avaliação do Azure AD.

Essas configurações se tornarão visíveis para o usuário no portal quando forem habilitadas e ele se conectar ao portal do usuário.

![Gerenciar sua conta do servidor MFA usando o portal do usuário](./media/howto-mfaserver-deploy-userportal/portalsettings.png)

### <a name="self-service-user-enrollment"></a>Registro de usuário de autoatendimento

Se desejar que os usuários entrem e se registrem, você deverá selecionar a opção **permitir que os usuários façam logon** e **Permitir registro de usuário** na guia Configurações. Lembre-se de que as configurações selecionadas afetam a experiência de entrada do usuário.

Por exemplo, quando um usuário entra no portal do usuário pela primeira vez, é levado para a página de Configuração de Usuário do Autenticação Multifator do Azure. Dependendo de como você configurou a Autenticação Multifator do Azure, o usuário poderá selecionar o método de autenticação.

Se o método de verificação Chamada de Voz tiver sido selecionado ou houver pré-configuração para usar esse método, a página solicitará que o usuário insira seu número de telefone principal e extensão, se aplicável. Eles também podem ser autorizados a inserir um número de telefone de backup.

![Registrar números de telefone principal e de backup](./media/howto-mfaserver-deploy-userportal/backupphone.png)

Se o usuário for solicitado a usar um PIN quando autenticar, a página também lhe pedirá para criar um PIN. Depois de digitar seu número de telefone e PIN (se aplicável), o usuário clica no botão **Telefonar para Mim para Autenticar**. A autenticação multifator do Azure executa uma verificação de telefonema para o número de telefone principal do usuário. O usuário deve responder à chamada telefônica, inserir o PIN (se aplicável) e pressionar # para passar para a próxima etapa do processo de auto-registro.

Se o usuário selecionar o método de verificação de Mensagem de Texto ou tiver sido pré-configurado para usar esse método, a página solicitará ao usuário seu número de telefone celular. Se o usuário for solicitado a usar um PIN quando autenticar, a página também lhe pedirá para inserir um PIN.  Depois de digitar seu número de telefone e PIN (se aplicável), o usuário clica no botão **Enviar uma Mensagem de Texto para Autenticar**. A autenticação multifator do Azure executa uma verificação de SMS para o telefone celular do usuário. O usuário recebe a mensagem de texto com uma OTP (senha de uso único) e responde à mensagem com esta OTP e seu PIN (se aplicável).

![Verificação do portal do usuário usando o SMS](./media/howto-mfaserver-deploy-userportal/text.png)

Se o usuário selecionar o método de verificação de Aplicativo Móvel, a página solicitará que o usuário instale o aplicativo Microsoft Authenticator em seu dispositivo e gere um código de ativação. Depois de instalar o aplicativo, o usuário clica no botão Gerar Código de Ativação.

> [!NOTE]
> Para usar o aplicativo Microsoft Authenticator, o usuário deve habilitar notificações por push para seu dispositivo.

A página exibe um código de ativação e uma URL junto com uma imagem de código de barras. Se o usuário for solicitado a usar um PIN quando autenticar, a página também lhe pedirá para inserir um PIN. O usuário insere o código de ativação e a URL no aplicativo Microsoft Authenticator ou usa o scanner de código de barras para digitalizar a imagem do código de barras e clica no botão Ativar.

Após concluir a ativação, o usuário clica no botão **Autenticar Agora**. A autenticação multifator do Azure executa uma verificação para o aplicativo móvel do usuário. O usuário deve inserir seu PIN (se aplicável) e pressionar o botão Autenticar em seu aplicativo móvel para passar para a próxima etapa do processo de auto-registro.

Se os administradores tiverem configurado o Servidor de Autenticação Multifator do Azure para coletar perguntas e respostas de segurança, o usuário será levado para a página Perguntas de Segurança. O usuário deve selecionar quatro perguntas de segurança e fornecer respostas às perguntas selecionadas.

![Perguntas de segurança do portal do usuário](./media/howto-mfaserver-deploy-userportal/secq.png)

O autorregistro do usuário agora está concluído e o usuário está conectado ao portal do usuário. Os usuários podem entrar no portal do usuário a qualquer momento no futuro para alterar seus números de telefone, PINs, métodos de autenticação e perguntas de segurança se alteração dos métodos for permitida pelos seus administradores.

## <a name="next-steps"></a>Próximas etapas

- [Implantar o serviço Web do aplicativo móvel Servidor de Autenticação Multifator do Azure](howto-mfaserver-deploy-mobileapp.md)
