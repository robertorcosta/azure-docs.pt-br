---
title: Login sem senha com o aplicativo Microsoft Authenticator - Azure Active Directory
description: Habilite o login sem senha no Azure AD usando o aplicativo Microsoft Authenticator (visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 853e7143834a340b870b71ef1a287dab136e2783
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654063"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Habilite o login sem senha com o aplicativo Microsoft Authenticator (visualização)

O aplicativo Microsoft Authenticator pode ser usado para entrar em qualquer conta do Azure AD sem usar uma senha. Similar à tecnologia do [Windows Hello para Empresas](/windows/security/identity-protection/hello-for-business/hello-identity-verification), o Microsoft Authenticator usa a autenticação baseada em chave para habilitar uma credencial de usuário que esteja associada a um dispositivo e use biometria ou PIN. Este método de autenticação pode ser usado em qualquer plataforma de dispositivos, incluindo mobile, e com qualquer aplicativo ou site que se integre às bibliotecas de autenticação da Microsoft. 

![Exemplo de login de navegador pedindo que o usuário aprove o login](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Em vez de ver um pedido de senha depois de inserir um nome de usuário, uma pessoa que habilitou o login de telefone do aplicativo Microsoft Authenticator verá uma mensagem dizendo-lhes para tocar em um número em seu aplicativo. No aplicativo, o usuário deverá indicar o mesmo número, escolher Aprovar, informar o PIN ou biometria e, em seguida, a autenticação será concluída.

> [!NOTE]
> Esse recurso está no aplicativo Microsoft Authenticator desde março de 2017, então existe a possibilidade de que, quando a política estiver habilitada para um diretório, os usuários possam encontrar esse fluxo imediatamente e ver uma mensagem de erro se não tiverem sido habilitadas pela política. Esteja atento e prepare seus usuários para essa alteração.

## <a name="prerequisites"></a>Pré-requisitos

- Autenticação multifatorial do Azure, com notificações push permitidas como método de verificação 
- Versão mais recente do Microsoft Authenticator instalada em dispositivos que executam o iOS 8.0 ou superior ou o Android 6.0 ou superior.

> [!NOTE]
> Se você habilitou a pré-visualização de login sem senha do aplicativo Microsoft Authenticator usando o Azure AD PowerShell, ele seria habilitado para todo o seu diretório. Se você habilitar o uso deste novo método, ele substituirá a diretiva PowerShell. Recomendamos a habilitação para todos os usuários do seu inquilino através dos novos Métodos de Autenticação, caso contrário, os usuários que não estão na nova política não poderão mais fazer login sem senha. 

## <a name="enable-passwordless-authentication-methods"></a>Habilite métodos de autenticação sem senha

### <a name="enable-the-combined-registration-experience"></a>Habilite a experiência de registro combinada

Os recursos de registro para métodos de autenticação sem senha dependem da visualização combinada do registro. Siga as etapas do artigo [Habilite o registro combinado de informações de segurança (pré-visualização)](howto-registration-mfa-sspr-combined.md), para habilitar a visualização combinada do registro.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Habilite métodos de autenticação de login de telefone sem senha

1. Faça login no [portal Azure](https://portal.azure.com)
1. Pesquise *Azure Active Directory* e selecione-o. Selecione **métodos** > **Authentication methods** > de autenticação de segurança Política do método de**autenticação (Visualização)**
1. Em **login de telefone sem senha,** escolha as seguintes opções
   1. **Habilitar** - Sim ou Não
   1. **Destino** - Todos os usuários ou usuários selecionados
1. **Salvar** para definir a nova política

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Registro e gerenciamento de usuários do aplicativo Microsoft Authenticator

1. Navegue até [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. Faça login se ainda não
1. Adicione um aplicativo autenticador clicando em **Adicionar método,** escolhendo **o aplicativo Authenticator**e clicando **em Adicionar**
1. Siga as instruções para instalar e configurar o aplicativo Microsoft Authenticator em seu dispositivo
1. Clique **em Concluído** para concluir o fluxo de configuração do aplicativo Authenticator MFA. 
1. No **Microsoft Authenticator,** escolha **Ativar o login** do telefone no menu suspenso da conta
1. Siga as instruções no aplicativo para terminar de se cadastrar para o login de telefone sem senha. 

As organizações podem apontar seus usuários para o artigo [Faça login com seu telefone, não sua senha](../user-help/microsoft-authenticator-app-phone-signin-faq.md) para obter mais assistência configurando-se no aplicativo Microsoft Authenticator e habilitando o login do telefone. Para aplicar essas configurações, talvez seja necessário sair e fazer login novamente no inquilino. 

## <a name="sign-in-with-passwordless-credential"></a>Faça login com credencial sem senha

Para versão prévia pública, não há nenhuma maneira de impor que os usuários criem ou usem essa nova credencial. Um usuário só encontrará login sem senha quando um admin habilitar seu inquilino **e** o usuário tiver atualizado seu aplicativo Microsoft Authenticator para habilitar o login do telefone.

Depois de digitar seu nome de usuário na web e selecionar **Next,** os usuários são apresentados com um número e são solicitados em seu aplicativo Microsoft Authenticator para selecionar o número apropriado para autenticar em vez de usar sua senha. 

![Exemplo de login de navegador usando o aplicativo Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>O usuário não está habilitado pela política, mas ainda tem o método de login do telefone sem senha no Microsoft Authenticator

É possível que um usuário tenha criado em algum momento uma credencial de login de telefone sem senha em seu aplicativo Microsoft Authenticator atual, ou em um dispositivo anterior. Uma vez que um admin habilite a política do método de autenticação para login de telefone sem senha, qualquer usuário com uma credencial registrada, começará a experimentar o novo prompt de login, independentemente de ter sido habilitado a usar a política ou não. Se o usuário não tiver permissão para usar a credencial por política, verá um erro após completar o fluxo de autenticação. 

O admin pode optar por permitir que o usuário use o login de telefone sem senha, ou o usuário deve remover o método. Se o usuário não tiver mais o dispositivo [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) registrado, ele pode ir e removê-lo. Se eles ainda estiverem usando o Autenticador para MFA, eles podem escolher Desativar o login do **telefone** dentro do Autenticador microsoft.  

### <a name="ad-fs-integration"></a>Integração AD FS

Quando um usuário tiver habilitado a credencial sem senha do Microsoft Authenticator, a autenticação para esse usuário sempre enviará como padrão uma notificação de aprovação. Essa lógica impede que os usuários de um inquilino híbrido sejam direcionados ao ADFS para verificação de login sem que o usuário dê um passo adicional para clicar em "Usar sua senha em vez disso". Esse processo também ignorará quaisquer políticas de Acesso Condicional locais e fluxos de autenticação de Passagem. 

Se um usuário tiver uma verificação de login de telefone sem senha sem resposta pendente e tentar entrar novamente, o usuário pode ser levado ao ADFS para inserir uma senha.  

### <a name="azure-mfa-server"></a>Servidor MFA do Azure

Os usuários finais que estão habilitados para o MFA através do servidor Azure MFA no local de uma organização ainda podem criar e usar um único sinal de telefone sem senha na credencial. Se o usuário tentar atualizar várias instalações (+5) do Microsoft Authenticator com a credencial, essa alteração poderá resultar em um erro.  

### <a name="device-registration"></a>Registro de dispositivos

Um dos pré-requisitos para criar essa nova credencial forte, é que o dispositivo, onde o aplicativo Microsoft Authenticator está instalado, também deve ser registrado dentro do inquilino Azure AD para um usuário individual. Devido às restrições atuais de registro do dispositivo, um dispositivo só pode ser registrado em um único inquilino. Esse limite significa que apenas uma conta corporativa ou de estudante no aplicativo Microsoft Authenticator pode ser habilitada para entrada por telefone.

### <a name="intune-mobile-application-management"></a>Gerenciamento de aplicativo móvel do Intune 

Os usuários finais que estão sujeitos a uma política que exige o gerenciamento de aplicativos móveis (MAM) não podem registrar a credencial sem senha no aplicativo Microsoft Authenticator. 

> [!NOTE]
> O registro de dispositivos não é o mesmo que o gerenciamento de dispositivos ou "MDM". Ele só associa um ID de dispositivo e um ID do usuário juntos no diretório Azure AD.  

## <a name="next-steps"></a>Próximas etapas

[O que é sem senha?](concept-authentication-passwordless.md)

[Saiba mais sobre registro de dispositivo](../devices/overview.md#getting-devices-in-azure-ad)

[Saiba mais sobre a Autenticação Multifator do Azure](../authentication/howto-mfa-getstarted.md)
