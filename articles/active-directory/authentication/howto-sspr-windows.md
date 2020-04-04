---
title: Redefinição de senha de autoatendimento para Windows - Azure Active Directory
description: Como ativar a redefinição de senha de autoatendimento usando senha esquecida na tela de login do Windows
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
ms.openlocfilehash: 0a8675756aeef1140dbebd94023d7f7fb4c7af99
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652252"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>Como: Ativar redefinição de senha da tela de login do Windows

Para máquinas que executam o Windows 7, 8, 8.1 e 10, você pode permitir que os usuários refinam sua senha na tela de login do Windows. Os usuários não precisam mais encontrar um dispositivo com navegador web para acessar o [portal SSPR](https://aka.ms/sspr).

![Exemplo de telas de login do Windows 7 e 10 com link SSPR mostrado](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Limitações gerais

- A redefinição de senha não é suportada atualmente a partir de uma área de trabalho remota ou de sessões aprimoradas hyper-V.
- Alguns provedores de credenciais de terceiros são conhecidos por causar problemas com esse recurso.
- A desativação do UAC através da modificação da chave de [registro EnableLUA](https://docs.microsoft.com/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) é conhecida por causar problemas.
- Esse recurso não funciona para redes com autenticação de rede 802.1x implantada e a opção "Executar imediatamente antes do logon do usuário". Para redes com autenticação de rede 802.1x implantada, é recomendável usar a autenticação de computador para habilitar esse recurso.
- As máquinas aderidas ao Azure AD híbrido devem ter linha de visão de conectividade de rede a um controlador de domínio para usar a nova senha e atualizar credenciais em cache.
- Se estiver usando uma imagem, antes de executar o sysprep, certifique-se de que o cache da Web esteja liberado para o administrador interno antes de executar a etapa CopyProfile. Mais informações sobre essa etapa podem ser encontradas no artigo de suporte [Desempenho ruim ao usar o perfil de usuário padrão personalizado](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- As seguintes configurações são conhecidas por interferir na capacidade de usar e redefinir senhas em dispositivos Windows 10
    - Se as teclas de atalho Ctrl+Alt+Del forem exigidas pela política nas versões do Windows 10 anteriores a v1809, a **Redefinição de senha** não funcionará.
    - Se as notificações de tela de bloqueio estiverem desativadas, a **Redefinição de senha** não funcionará.
    - HideFastUserSwitching é definido como habilitado ou 1
    - DontDisplayLastUserName é definido como habilitado ou 1
    - NoLockScreen é definido como habilitado ou 1
    - EnableLostMode é definido no dispositivo
    - Explorer.exe é substituído por um shell personalizado
- A combinação das três configurações específicas a seguir pode fazer com que esse recurso não funcione.
    - Logon interativo: Não requeira CTRL+ALT+DEL = Desativado
    - DesativarLockScreenAppNotifications = 1 ou Ativado
    - IsContentDeliveryPolicyEnforced = 1 ou True

## <a name="windows-10-password-reset"></a>Redefinição de senha do Windows 10

### <a name="windows-10-prerequisites"></a>Pré-requisitos do Windows 10

- Um administrador deve habilitar a senha de autoatendimento do Azure AD redefinida a partir do portal Azure.
- **Os usuários devem se registrar no SSPR antes de usar este recurso**
- Requisitos de proxy de rede
   - Dispositivos Windows 10 
       - Porta 443 `passwordreset.microsoftonline.com` para e`ajax.aspnetcdn.com`
       - Os dispositivos Windows 10 só suportam a configuração de proxy em nível de máquina
- Execute pelo menos o Windows 10, versão April 2018 Update (v1803), e os dispositivos devem ser:
    - Adicionado ao Azure AD
    - Adicionado ao Azure AD híbrido

### <a name="enable-for-windows-10-using-intune"></a>Habilitar para o Windows 10 usando o Intune

Implantar a alteração de configuração para habilitar a redefinição de senha da tela de logon usando o Intune é o método mais flexível. O Intune permite que você implante a alteração de configuração para um grupo específico de computadores que você definir. Esse método requer o registro do dispositivo no Intune.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Criar uma política de configuração do dispositivo no Intune

1. Entre no [portal do Azure](https://portal.azure.com) e clique em **Intune**.
1. Crie um novo perfil de configuração do dispositivo indo para a **configuração** > do dispositivo**Perfis de criação** > **de perfil**
   - Forneça um nome significativo para o perfil
   - Opcionalmente, forneça uma descrição significativa do perfil
   - Plataforma **Windows 10 e superior**
   - Tipo de perfil **Personalizado**
1. Configurar **configurações**
   - **Adicionar** a seguinte configuração de OMA-URI para habilitar o link Redefinir senha
      - Forneça um nome significativo para explicar o que a configuração está fazendo
      - Opcionalmente, forneça uma descrição significativa da configuração
      - **OMA-URI** definido como `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - **Tipo de dados** definido como **Inteiro**
      - **Valor** definido como **1**
      - Clique em **OK**.
   - Clique em **OK**.
1. Clique **em Criar**
1. Essa política pode ser atribuída a usuários, dispositivos ou grupos específicos. Mais informações podem ser [encontradas](https://docs.microsoft.com/intune/device-profile-assign)no artigo Atribuir perfis de usuários e dispositivos no Microsoft Intune .

### <a name="enable-for-windows-10-using-the-registry"></a>Habilitar para o Windows 10 usando o Registro

1. Entre no Windows PC usando credenciais administrativas
1. Execute o **regedit** como administrador
1. Defina a seguinte chave do registro
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Redefinição de senha do Windows 10

O log de auditoria do Microsoft Azure AD inclui informações sobre o endereço IP e o ClientType em que a redefinição de senha ocorreu.

![Exemplo de redefinição de senha do Windows 7 no registro de auditoria do Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Quando os usuários redefinem sua senha da tela de login de `defaultuser1` um dispositivo Windows 10, uma conta temporária de baixo privilégio chamada é criada. Essa conta é usada para manter o processo de redefinição de senha seguro. A conta em si tem uma senha gerada aleatoriamente, não aparece para o login do dispositivo e será removida automaticamente após o usuário redefinir sua senha. Vários `defaultuser` perfis podem existir, mas podem ser ignorados com segurança.

## <a name="windows-7-8-and-81-password-reset"></a>Redefinição de senha do Windows 7, 8 e 8.1

### <a name="windows-7-8-and-81-prerequisites"></a>Pré-requisitos do Windows 7, 8 e 8.1

- Um administrador deve habilitar a senha de autoatendimento do Azure AD redefinida a partir do portal Azure.
- **Os usuários devem se registrar no SSPR antes de usar este recurso**
- Requisitos de proxy de rede
   - Dispositivos Windows 7, 8 e 8.1
       - Porta 443 para`passwordreset.microsoftonline.com`
- Sistema operacional Windows 7 ou Windows 8.1 corrigido.
- TLS 1.2 habilitado usando a diretriz encontrada em [Configurações de registro do protocolo TLS](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12).
- Se mais de um provedor de credencial de terceiros estiver habilitado em sua máquina, os usuários verão mais de um perfil de usuário na tela de login.

> [!WARNING]
> O TLS 1.2 deve ser habilitado, não apenas definido para negociar automaticamente

### <a name="install"></a>Instalar

1. Baixe o instalador correto para a versão do Windows que você quer habilitar.
   - Software está disponível no centro de downloads da Microsoft em[https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Entre no computador em que você quer instalar e execute o instalador.
1. Após a instalação, uma reinicialização é altamente recomendável.
1. Após a reinicialização, na tela de login escolha um usuário e clique em "Esqueci a senha?" para iniciar o fluxo de trabalho de redefinição de senha.
1. Conclua o fluxo de trabalho seguindo as etapas na tela para redefinir sua senha.

![Exemplo de clique em "Esqueceu a senha?" no Windows 7 Fluxo SSPR](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Instalação silenciosa

- Para instalação silenciosa, use o comando "msiexec /i SsprWindowsLogon.PROD.msi /qn"
- Para desinstalar silenciosamente, use o comando "msiexec /x SsprWindowsLogon.PROD.msi /qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Solução de problemas Reinicialindo senha do Windows 7, 8 e 8.1

Os eventos serão registrados no computador e no Azure AD. Os eventos do Azure AD incluirão informações sobre o endereço IP e o ClientType em que a redefinição de senha ocorreu.

![Exemplo de redefinição de senha do Windows 7 no registro de auditoria do Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Se for necessário outro registro em log, uma chave do Registro no computador poderá ser alterada para habilitar o registro em log detalhado. Habilite o registro detalhado somente para fins de solução de problemas.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- Para habilitar o registro `REG_DWORD: "EnableLogging"`verbose, crie um e defina-o como 1.
- Para desativar o registro verbose, altere o `REG_DWORD: "EnableLogging"` 0.

## <a name="what-do-users-see"></a>O que os usuários veem

Agora que você configurou redefinição de senha para seus dispositivos Windows, o que muda para o usuário? Como eles sabem que podem redefinir sua senha na tela de logon?

![Exemplo de telas de login do Windows 7 e 10 com link SSPR mostrado](./media/howto-sspr-windows/windows-reset-password.png)

Quando os usuários tentam fazer login, eles agora vêem uma **senha redefinida** ou um link **de senha esquecida** que abre a experiência de redefinição de senha de autoatendimento na tela de login. Essa funcionalidade permite aos usuários redefinir a senha sem a necessidade de usar outro dispositivo para acessar um navegador da Web.

Os usuários podem encontrar orientações sobre esse recuso em [Redefinir sua senha corporativa ou de estudante](../user-help/active-directory-passwords-update-your-own-password.md)

## <a name="next-steps"></a>Próximas etapas

[Planejar métodos de autenticação para permitir](concept-authentication-methods.md)

[Configurar o Windows 10](https://docs.microsoft.com/windows/configuration/)
