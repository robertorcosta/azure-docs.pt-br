---
title: Login da chave de segurança sem senha Windows - Azure Active Directory
description: Saiba como ativar o login da chave de segurança sem senha no Azure Active Directory usando chaves de segurança FIDO2 (visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b8f5d6aaa96c24eb37eb78d237a489f1d25293c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653997"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory-preview"></a>Habilite o login da chave de segurança sem senha em dispositivos Windows 10 com o Azure Active Directory (visualização)

Este documento se concentra em habilitar a autenticação sem senha baseada em chave de segurança FIDO2 com dispositivos Windows 10. No final deste artigo, você poderá fazer login no seu Azure AD e no Azure AD híbrido juntou dispositivos Windows 10 com sua conta Azure AD usando uma chave de segurança FIDO2.

|     |
| --- |
| As chaves de segurança FIDO2 são um recurso de visualização pública do Azure Active Directory. Para obter mais informações sobre visualizações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Requisitos

| Tipo de dispositivo | Adicionado ao Azure AD | Adicionado ao Azure AD híbrido |
| --- | --- | --- |
| [Autenticação Multifator do Azure](howto-mfa-getstarted.md) | X | X |
| [Visualização combinada de registro de informações de segurança](concept-registration-mfa-sspr-combined.md) | X | X |
| Chaves de [segurança FIDO2 compatíveis](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| WebAuthN requer windows 10 versão 1809 ou superior | X | X |
| [Azure AD aderiu dispositivos](../devices/concept-azure-ad-join.md) que exigem windows 10 versão 1903 ou superior | X |   |
| [Dispositivos aderidos ao Azure AD híbrido exigem](../devices/concept-azure-ad-join-hybrid.md) windows 10 Insider Build 18945 ou superior |   | X |
| Controladores de domínio do Windows Server 2016/2019 totalmente corrigidos. |   | X |
| [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) versão 1.4.32.0 ou posterior |   | X |
| [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (Opcional) | X | X |
| Pacote de provisionamento (Opcional) | X | X |
| Política de grupo (Opcional) |   | X |

### <a name="unsupported-scenarios"></a>Cenários sem suporte

Os seguintes cenários não são suportados:

- AD DS (AD DS) dedomínio com a implantação de Domain(somente dispositivos no local) do Windows Server Active Directory Services (AD DS).
- Cenários RDP, VDI e Citrix usando uma chave de segurança.
- S/MIME usando uma chave de segurança.
- "Corra como" usando uma chave de segurança.
- Faça login em um servidor usando uma chave de segurança.
- Se você não usou sua chave de segurança para fazer login no seu dispositivo enquanto estiver online, você não pode usá-la para fazer login ou desbloquear offline.
- Fazer login ou desbloquear um dispositivo Windows 10 com uma chave de segurança contendo várias contas Ad do Azure. Este cenário utiliza a última conta adicionada à chave de segurança. O WebAuthN permite que os usuários escolham a conta que desejam usar.
- Desbloqueie um dispositivo executando o Windows 10 versão 1809. Para obter a melhor experiência, use o Windows 10 versão 1903 ou superior.

## <a name="prepare-devices-for-preview"></a>Preparar dispositivos para visualização

O Azure AD juntou-se aos dispositivos que você está pilotando durante a pré-visualização do recurso com a versão 1809 do Windows 10 ou superior. A melhor experiência está no Windows 10 versão 1903 ou superior.

Os dispositivos azure ad híbridos devem executar o Windows 10 Insider Build 18945 ou mais novo.

## <a name="enable-security-keys-for-windows-sign-in"></a>Habilite as chaves de segurança para o login do Windows

As organizações podem optar por usar um ou mais dos seguintes métodos para permitir o uso de chaves de segurança para login do Windows com base nos requisitos de sua organização:

- [Habilitar com Intune](#enable-with-intune)
- [Implantação de Intune direcionada](#targeted-intune-deployment)
- [Habilite com um pacote de provisionamento](#enable-with-a-provisioning-package)
- [Habilitar com a Política de Grupo (somente dispositivos do Hybrid Azure AD aderiu)](#enable-with-group-policy)

> [!IMPORTANT]
> As organizações com **dispositivos híbridos Azure AD** **também** devem completar as etapas do artigo, habilitar a [autenticação FIDO2 aos recursos locais](howto-authentication-passwordless-security-key-on-premises.md) antes que a autenticação da chave de segurança FIDO2 do Windows 10 funcione.
>
> As organizações com **dispositivos Azure AD devem** fazer isso antes que seus dispositivos possam autenticar recursos no local com chaves de segurança FIDO2.

### <a name="enable-with-intune"></a>Habilitar com Intune

Para habilitar o uso de chaves de segurança usando o Intune, complete as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Navegue até a**inscrição** >  **do Microsoft Intune** > Device**no Windows** > **Inscrições do Windows Hello for Business** > **Properties**.
1. Em **Configurações,** **configure Usar as teclas de segurança para o login** **ativado**.

A configuração das chaves de segurança para login não depende da configuração do Windows Hello for Business.

### <a name="targeted-intune-deployment"></a>Implantação de Intune direcionada

Para segmentar grupos de dispositivos específicos para habilitar o provedor de credenciais, use as seguintes configurações personalizadas via Intune:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Navegue até a**configuração** > do dispositivo **Microsoft Intune** > **Perfis criar** > **perfil**.
1. Configure o novo perfil com as seguintes configurações:
   - Nome: Chaves de segurança para login do Windows
   - Descrição: Permite que as chaves de segurança FIDO sejam usadas durante o login do Windows
   - Plataforma: Windows 10 e posterior
   - Tipo de perfil: Personalizado
   - Configurações personalizadas do OMA-URI:
      - Nome: Ativar chaves de segurança FIDO para o login do Windows
      - OMA-URI: ./Dispositivo/Fornecedor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - Tipo de dados: Inteiro
      - Valor: 1
1. Essa política pode ser atribuída a usuários, dispositivos ou grupos específicos. Para obter mais informações, consulte [Atribuir perfis de usuário e dispositivo no Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Criação da política de configuração de dispositivos personalizados intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Habilite com um pacote de provisionamento

Para dispositivos não gerenciados pela Intune, um pacote de provisionamento pode ser instalado para habilitar a funcionalidade. O aplicativo Windows Configuration Designer pode ser instalado na [Microsoft Store](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22). Complete as seguintes etapas para criar um pacote de provisionamento:

1. Inicie o Windows Configuration Designer.
1. Selecione **Arquivo** > **Novo projeto**.
1. Dê um nome ao seu projeto e tome nota do caminho onde seu projeto foi criado e selecione **Next**.
1. Deixar *o pacote de provisionamento* selecionado como o fluxo de trabalho do projeto **selecionado** e selecionar **Next**.
1. Selecione *Todas as edições de desktop do Windows* em Escolha quais **configurações visualizar e configurar**e, em seguida, selecione **Next**.
1. Selecione **Concluir**.
1. Em seu projeto recém-criado, navegue até **as configurações** > de tempo de execução**do WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Definir **UseSecurityKeyForSignIn** para *Habilitar*.
1. Selecione o**pacote de provisionamento de** **exportação** > 
1. Deixe os padrões na janela **'Criar'** **em Descrever o pacote de provisionamento**e, em seguida, selecione **'Seguir**'
1. Deixe os padrões na janela **'Criar'** em **'Criar', selecione detalhes de segurança para o pacote de provisionamento** e selecione **'Seguir '''**
1. Tome nota ou altere o caminho nas janelas **De construir** em **Selecionar onde salvar o pacote de provisionamento** e selecione **Next**.
1. Selecione **Construir** na página Construir a página **do pacote de provisionamento.**
1. Salve os dois arquivos criados *(ppkg* e *gato)* em um local onde você pode aplicá-los em máquinas mais tarde.
1. Para aplicar o pacote de provisionamento criado, consulte [Aplicar um pacote de provisionamento](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package).

> [!NOTE]
> Dispositivos que executam o Windows 10 Versão 1809 também devem habilitar o modo PC compartilhado *(EnableSharedPCMode*). Para obter mais informações sobre como ativar essa funcionalidade, consulte [Configurar um PC compartilhado ou convidado com o Windows 10](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc).

### <a name="enable-with-group-policy"></a>Habilitar com a política de grupo

Para **dispositivos adeptos híbridos do Azure AD,** as organizações podem configurar a seguinte configuração de Diretiva de Grupo para habilitar o login da chave de segurança FIDO. A configuração pode ser encontrada em **Configuração de computador** > **Modelos administrativos** > **logon Logon** > **Logon** > **Ativar o login da chave de segurança**:

- A definição dessa diretiva para **Habilitado** permite que os usuários entrem com chaves de segurança.
- A definição dessa diretiva para **Desabilitação** ou **Não Configurada** impede que os usuários entrem com chaves de segurança.

Esta configuração diretiva de grupo `credentialprovider.admx` requer uma versão atualizada do modelo De diretiva de grupo. Este novo modelo está disponível com a próxima versão do Windows Server e com o Windows 10 20H1. Essa configuração pode ser gerenciada com um dispositivo executando uma dessas versões mais recentes do Windows ou centralmente seguindo a orientação no tópico de suporte, [Como criar e gerenciar a Loja Central para Modelos Administrativos de Políticas de Grupo no Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="sign-in-with-fido2-security-key"></a>Faça login com a chave de segurança FIDO2

No exemplo abaixo, um usuário chamado Bala Sandhu já provisionou sua chave de segurança FIDO2 usando as etapas do artigo anterior, Habilitar o [login de chave de segurança sem senha](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). Para dispositivos aderidos a Azure AD híbridos, certifique-se de que você também [habilitou o login da chave de segurança sem senha para os recursos no local](howto-authentication-passwordless-security-key-on-premises.md). O Bala pode escolher o provedor de credencial da chave de segurança na tela de bloqueio do Windows 10 e inserir a chave de segurança para entrar no Windows.

![Entrada da chave de segurança na tela de bloqueio do Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Gerenciar a chave de segurança biométrica, PIN ou redefinir a chave de segurança

* Windows 10 versão 1903 ou superior
   * Os usuários podem abrir **configurações do Windows** em seu dispositivo >**chave de segurança de** **contas** > 
   * Os usuários podem alterar seu PIN, atualizar a biometria ou redefinir sua chave de segurança

## <a name="troubleshooting-and-feedback"></a>Solução de problemas e feedback

Se você quiser compartilhar feedback ou encontrar problemas durante a visualização deste recurso, compartilhe através do aplicativo Windows Feedback Hub usando as seguintes etapas:

1. Inicie **o Feedback Hub** e certifique-se de que você está conectado.
1. Enviar feedback sob a seguinte categorização:
   - Categoria: Segurança e Privacidade
   - Subcategoria: FIDO
1. Para capturar logs, use a opção para **recriar meu problema**

## <a name="next-steps"></a>Próximas etapas

[Habilite o acesso aos recursos locais para a azure AD e dispositivos híbridos Azure AD](howto-authentication-passwordless-security-key-on-premises.md)

[Saiba mais sobre o registro de dispositivos](../devices/overview.md)

[Saiba mais sobre a Autenticação Multifator do Azure](../authentication/howto-mfa-getstarted.md)
