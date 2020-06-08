---
title: Redefinição de senha self-service para Windows - Azure Active Directory
description: Como habilitar a redefinição de senha self-service usando a senha esquecida na tela de logon do Windows
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95d1ffec6a849cb97a6151717c3e30dc362b1403
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826597"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>Como fazer: Habilitar redefinição de senha da tela de logon do Windows

Para computadores que executam o Windows 7, 8, 8.1 e 10, você pode habilitar que os usuários redefinam sua senha na tela de logon do Windows. Os usuários não precisam mais localizar um dispositivo com um navegador da Web para acessar o [portal do SSPR](https://aka.ms/sspr).

![Exemplo de telas de logon do Windows 7 e 10 que mostra o link do SSPR](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Limitações gerais

- Atualmente não há suporte para a redefinição de senha a partir de uma Área de Trabalho Remota ou de sessões aprimoradas com Hyper-V.
- Alguns provedores de credenciais de terceiros são conhecidos por causar problemas com esse recurso.
- Desabilitação do UAC por meio da modificação de [chave do registro EnableLUA](https://docs.microsoft.com/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) é conhecida por causar problemas.
- Esse recurso não funciona para redes com autenticação de rede 802.1x implantada e a opção “Executar imediatamente antes do logon do usuário”. Para redes com autenticação de rede 802.1x implantada, é recomendável usar a autenticação de computador para habilitar esse recurso.
- Os computadores ingressados no Azure Active Directory híbrido devem ter a linha de visão de conectividade de rede para um controlador de domínio para usar a nova senha e atualizar as credenciais armazenadas em cache. Isso significa que os dispositivos devem estar na rede interna da organização ou em uma VPN com acesso à rede para um controlador de domínio local. 
- Se estiver usando uma imagem, antes de executar o sysprep, veja se o cache da Web está desmarcado para o Administrador interno, antes de executar a etapa CopyProfile. Mais informações sobre esse assunto podem ser encontradas no artigo de suporte [Desempenho insatisfatório ao usar perfil de usuário padrão personalizado](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- As configurações a seguir são conhecidas por interferir com a capacidade de usar e redefinir senhas nos dispositivos com Windows 10
    - Se as teclas de atalho Ctrl+Alt+Del forem exigidas pela política nas versões do Windows 10 anteriores a v1809, a **Redefinição de senha** não funcionará.
    - Se as notificações de tela de bloqueio estiverem desativadas, a **Redefinição de senha** não funcionará.
    - HideFastUserSwitching é definido como habilitado ou 1
    - DontDisplayLastUserName é definido como habilitado ou 1
    - NoLockScreen é definido como habilitado ou 1
    - EnableLostMode é definido no dispositivo
    - Explorer.exe é substituído por um shell personalizado
- A combinação das seguintes três configurações específicas pode fazer com que esse recurso não funcione.
    - Logon interativo: Não exigir CTRL+ALT+DEL = Desabilitado
    - DisableLockScreenAppNotifications = 1 ou Habilitado
    - O SKU do Windows não é Home ou Professional Edition

## <a name="windows-10-password-reset"></a>Redefinição de senha do Windows 10

### <a name="windows-10-prerequisites"></a>Pré-requisitos do Windows 10

- Um administrador precisa habilitar a redefinição de senha self-service do Microsoft Azure Active Directory no portal do Azure.
- **Os usuários precisam se registrar para SSPR antes de usar esse recurso**
- Requisitos de proxy de rede
   - Dispositivos com Windows 10 
       - Porta 443 para `passwordreset.microsoftonline.com` e `ajax.aspnetcdn.com`
       - Dispositivos com Windows 10 dão suporte apenas à configuração de proxy no nível de computador
- Executar, pelo menos, o Windows 10, versão Atualização de abril de 2018 (v1803), e os dispositivos precisam estar:
    - Adicionado ao Azure AD
    - Adicionado ao Azure AD híbrido

### <a name="enable-for-windows-10-using-intune"></a>Habilitar para Windows 10 usando o Intune

Implantar a alteração de configuração para habilitar a redefinição de senha da tela de logon usando o Intune é o método mais flexível. O Intune permite que você implante a alteração de configuração para um grupo específico de computadores que você definir. Esse método requer o registro do dispositivo no Intune.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Criar uma política de configuração do dispositivo no Intune

1. Entre no [portal do Azure](https://portal.azure.com) e clique em **Intune**.
1. Criar um novo perfil de configuração do dispositivo acessando **Configuração do dispositivo** > **Perfis** > **Criar perfil**
   - Forneça um nome significativo para o perfil
   - Opcionalmente, forneça uma descrição significativa do perfil
   - Plataforma **Windows 10 e superior**
   - Tipo de perfil **Personalizado**
1. Definir **Configurações**
   - **Adicionar** a seguinte configuração de OMA-URI para habilitar o link Redefinir senha
      - Forneça um nome significativo para explicar o que a configuração está fazendo
      - Opcionalmente, forneça uma descrição significativa da configuração
      - **OMA-URI** definido como `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - **Tipo de dados** definido como **Inteiro**
      - **Valor** definido como **1**
      - Clique em **OK**
   - Clique em **OK**
1. Clique em **Criar**
1. Essa política pode ser atribuída a usuários, dispositivos ou grupos específicos. Mais informações podem ser encontradas no artigo [Atribuir perfis de usuário e de dispositivo no Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

### <a name="enable-for-windows-10-using-the-registry"></a>Habilitar para Windows 10 usando o registro

1. Entre no Windows PC usando credenciais administrativas
1. Execute o **regedit** como administrador
1. Defina a seguinte chave do registro
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Solucionar problemas de redefinição de senha do Windows 10

O log de auditoria do Microsoft Azure AD inclui informações sobre o endereço IP e o ClientType em que a redefinição de senha ocorreu.

![Exemplo de redefinição de senha do Windows 7 no log de auditoria do Azure Active Directory](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Quando os usuários redefinem a senha na tela de logon de um dispositivo Windows 10, uma conta temporária com baixos privilégios chamada `defaultuser1` é criada. Essa conta é usada para manter o processo de redefinição de senha seguro. A própria conta tem uma senha gerada aleatoriamente, não é exibida para credenciais do dispositivo e será removida automaticamente após o usuário redefinir sua senha. Vários perfis `defaultuser` podem existir, mas podem ser ignorados com segurança.

## <a name="windows-7-8-and-81-password-reset"></a>Redefinição de senha do Windows 7, 8 e 8.1

### <a name="windows-7-8-and-81-prerequisites"></a>Pré-requisitos do Windows 7, 8 e 8.1

- Um administrador precisa habilitar a redefinição de senha self-service do Microsoft Azure Active Directory no portal do Azure.
- **Os usuários precisam se registrar para SSPR antes de usar esse recurso**
- Requisitos de proxy de rede
   - Dispositivos Windows 7, 8 e 8.1
       - Porta 443 para `passwordreset.microsoftonline.com`
- Sistema operacional Windows 7 ou Windows 8.1 corrigido.
- TLS 1.2 habilitado usando a diretriz encontrada em [Configurações de registro do protocolo TLS](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12).
- Se mais de um provedor de credenciais de terceiros estiver habilitado em seu computador, os usuários verão mais de um perfil de usuário na tela de logon.

> [!WARNING]
> O TLS 1.2 precisa ser habilitado, e não apenas configurado para negociar automaticamente

### <a name="install"></a>Instalar

1. Baixe o instalador correto para a versão do Windows que você quer habilitar.
   - O software está disponível no Centro de Download da Microsoft em [https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Entre no computador em que você quer instalar e execute o instalador.
1. Após a instalação, uma reinicialização é altamente recomendável.
1. Após a reinicialização, na tela de logon, escolha um usuário e clique em “Esqueceu a senha?” para iniciar o fluxo de trabalho de redefinição de senha.
1. Conclua o fluxo de trabalho seguindo as etapas na tela para redefinir sua senha.

![Exemplo de clique em "Esqueceu a senha?" no Windows 7 Fluxo de SSPR](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Instalação silenciosa

- Para uma instalação silenciosa, use o comando "msiexec /i SsprWindowsLogon.PROD.msi /qn"
- Para uma desinstalação silenciosa, use o comando "msiexec /x SsprWindowsLogon.PROD.msi /qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Solucionar problemas de redefinição de senha do Windows 7, 8 e 8.1

Os eventos serão registrados no computador e no Azure AD. Os eventos do Azure AD incluirão informações sobre o endereço IP e o ClientType em que a redefinição de senha ocorreu.

![Exemplo de redefinição de senha do Windows 7 no log de auditoria do Azure Active Directory](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Se for necessário outro registro em log, uma chave do Registro no computador poderá ser alterada para habilitar o registro em log detalhado. Habilite o registro detalhado somente para fins de solução de problemas.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- Para habilitar o log detalhado, crie um `REG_DWORD: "EnableLogging"` e o configure como 1.
- Para desabilitar o log detalhado, altere o `REG_DWORD: "EnableLogging"` para 0.

## <a name="what-do-users-see"></a>O que os usuários veem

Agora que você configurou a redefinição de senha para seus dispositivos Windows, o que muda para o usuário? Como eles sabem que podem redefinir sua senha na tela de logon?

![Exemplo de telas de logon do Windows 7 e 10 que mostra o link do SSPR](./media/howto-sspr-windows/windows-reset-password.png)

Quando os usuários tentarem se conectar, eles verão um link **Redefinir senha** ou **Esqueceu a senha** que abre a experiência de redefinição de senha self-service na tela de logon. Essa funcionalidade permite aos usuários redefinir a senha sem a necessidade de usar outro dispositivo para acessar um navegador da Web.

Os usuários podem encontrar orientações sobre esse recuso em [Redefinir sua senha corporativa ou de estudante](../user-help/active-directory-passwords-update-your-own-password.md)

## <a name="next-steps"></a>Próximas etapas

[Planejar métodos de autenticação para permitir](concept-authentication-methods.md)

[Configurar o Windows 10](https://docs.microsoft.com/windows/configuration/)
