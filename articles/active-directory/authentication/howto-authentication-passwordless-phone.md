---
title: Entrada sem senha com o Microsoft Authenticator app-Azure Active Directory
description: Habilitar a entrada sem senha no Azure AD usando o aplicativo Microsoft Authenticator (versão prévia)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 09/29/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b4792e73f6326bb9ac67ce3aabe10b8314bb826
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91568192"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Habilitar a entrada sem senha com o aplicativo Microsoft Authenticator (versão prévia)

O aplicativo Microsoft Authenticator pode ser usado para entrar em qualquer conta do Azure AD sem usar uma senha. Similar à tecnologia do [Windows Hello para Empresas](/windows/security/identity-protection/hello-for-business/hello-identity-verification), o Microsoft Authenticator usa a autenticação baseada em chave para habilitar uma credencial de usuário que esteja associada a um dispositivo e use biometria ou PIN. Esse método de autenticação pode ser usado em qualquer plataforma de dispositivo, incluindo dispositivos móveis e com qualquer aplicativo ou site que se integre com as bibliotecas de autenticação da Microsoft.

![Exemplo de uma entrada de navegador solicitando que o usuário aprove a entrada](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Em vez de ver um prompt para uma senha depois de inserir um nome de usuário, uma pessoa que habilitou a entrada por telefone do aplicativo Microsoft Authenticator vê uma mensagem que solicita que toque um número em seu aplicativo. Para concluir o processo de entrada no aplicativo, o usuário deve corresponder ao número, escolher **aprovar**e, em seguida, fornecer seu PIN ou biométrica.

## <a name="prerequisites"></a>Pré-requisitos

Para usar a entrada por telefone sem senha com o aplicativo Microsoft Authenticator, os seguintes pré-requisitos devem ser atendidos:

- Autenticação multifator do Azure, com notificações por push permitidas como um método de verificação.
- Versão mais recente do Microsoft Authenticator instalada em dispositivos que executam o iOS 8.0 ou superior ou o Android 6.0 ou superior.

> [!NOTE]
> Se você tiver habilitado Microsoft Authenticator visualização de entrada sem senha do aplicativo usando o PowerShell do Azure AD, ela foi habilitada para todo o diretório. Se você habilitar o uso desse novo método, ele substituirá a política do PowerShell. Recomendamos que você habilite para todos os usuários em seu locatário por meio do menu novos *métodos de autenticação* ; caso contrário, os usuários que não estiverem na nova política não poderão mais entrar sem uma senha.

## <a name="enable-passwordless-authentication-methods"></a>Habilitar métodos de autenticação com senha

Para usar a autenticação sem senha no Azure AD, primeiro habilite a experiência de registro combinada e, em seguida, habilite os usuários para a senha menos o método.

### <a name="enable-the-combined-registration-experience"></a>Habilitar a experiência de registro combinado

Os recursos de registro para métodos de autenticação com senha contam com o recurso de registro combinado. Para permitir que os usuários concluam o registro combinado, siga as etapas para [habilitar o registro de informações de segurança combinadas](howto-registration-mfa-sspr-combined.md).

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Habilitar métodos de autenticação de entrada por telefone sem senha

O Azure AD permite que você escolha quais métodos de autenticação podem ser usados durante o processo de entrada. Em seguida, os usuários se registram para os métodos que desejam usar.

Para habilitar o método de autenticação para entrada no telefone sem senha, conclua as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de *administrador global* .
1. Procure e selecione *Azure Active Directory*e, em seguida, navegue até **segurança**  >  **métodos de autenticação**  >  **método de autenticação política (versão prévia)**
1. Em **entrada no telefone sem senha**, escolha as seguintes opções:
   1. **Habilitar** -Sim ou não
   1. **Destino** -todos os usuários ou Selecionar usuários
1. Para aplicar a nova política, selecione **salvar**.

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Registro de usuário e gerenciamento do aplicativo Microsoft Authenticator

Com o método de autenticação sem senha disponível para uso no Azure AD, os usuários agora devem se registrar para o método de autenticação sem senha usando as seguintes etapas:

1. Navegue até [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Entre e adicione o aplicativo autenticador selecionando **Adicionar método > aplicativo autenticador**e, em seguida, **Adicionar**.
1. Siga as instruções para instalar e configurar o aplicativo Microsoft Authenticator em seu dispositivo.
1. Selecione **concluído** para concluir a configuração do autenticador.
1. Em **Microsoft Authenticator** aplicativo, escolha **Habilitar entrada pelo telefone** no menu suspenso da conta registrada.
1. Siga as instruções no aplicativo para concluir o registro da conta para entrada por telefone sem senha.

As organizações podem direcionar seus usuários para [entrar com seu telefone, não com sua senha](../user-help/user-help-auth-app-sign-in.md) para obter mais assistência para configurar o aplicativo Microsoft Authenticator e habilitar a entrada pelo telefone.

> [!NOTE]
> Os usuários que não são permitidos pela política para usar a entrada pelo telefone não podem mais habilitá-lo no aplicativo Microsoft Authenticator.  

## <a name="sign-in-with-passwordless-credential"></a>Entrar com credencial sem senha

Um usuário pode começar a usar a entrada sem senha quando um administrador tiver habilitado seu locatário e o usuário tiver atualizado seu aplicativo Microsoft Authenticator para habilitar a entrada pelo telefone.

Para começar a usar a entrada pelo telefone, depois de digitar um nome de usuário na página de entrada e selecionar **Avançar**, os usuários podem ter que selecionar **outras maneiras de entrar**e, em seguida, **aprovar uma solicitação em meu aplicativo Microsoft Authenticator**. Em seguida, os usuários são apresentados com um número e são solicitados em seu aplicativo Microsoft Authenticator para selecionar o número apropriado para autenticar em vez de usar sua senha. Depois que os usuários tiverem usado a entrada no telefone sem senha, eles serão solicitados a usá-lo novamente, até que eles usem o método escolher outro.

![Exemplo de uma entrada no navegador usando o aplicativo Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Problemas conhecidos

Os seguintes problemas conhecidos existem na experiência de visualização atual.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Não está vendo a opção para entrar no telefone sem senha

Se um usuário tiver uma verificação de entrada de telefone sem senha não respondida pendente e tentar entrar novamente, o usuário poderá ver apenas uma opção para inserir uma senha. Abra o Microsoft Authenticator e responda a quaisquer avisos de notificação para continuar a usar a entrada no telefone sem senha.

### <a name="federated-accounts"></a>Contas federadas

Quando um usuário tiver habilitado qualquer credencial com senha, os logons do Azure AD deixarão de usar login_hint para acelerar o usuário para um local de logon federado. Essa lógica impede que os usuários em um locatário híbrido sejam direcionados para AD FS para verificação de entrada sem que o usuário execute uma etapa adicional para clicar em "usar sua senha".

### <a name="azure-mfa-server"></a>Servidor MFA do Azure

Os usuários finais que estão habilitados para MFA por meio do servidor do Azure MFA local de uma organização ainda podem criar e usar uma única credencial de entrada de telefone sem senha. Se o usuário tentar atualizar várias instalações (+5) do Microsoft Authenticator com a credencial, essa alteração poderá resultar em um erro.  

### <a name="device-registration"></a>Registro de dispositivos

Um dos pré-requisitos para criar essa nova credencial forte é que o dispositivo, em que o aplicativo Microsoft Authenticator está instalado, também deve ser registrado no locatário do Azure AD para um usuário individual. Devido às restrições de registro do dispositivo atual, um dispositivo só pode ser registrado em um único locatário. Esse limite significa que apenas uma conta corporativa ou de estudante no aplicativo Microsoft Authenticator pode ser habilitada para entrada por telefone.

> [!NOTE]
> O registro do dispositivo não é o mesmo que o gerenciamento de dispositivos ou "MDM". Ele só associa uma ID de dispositivo e uma ID de usuário ao diretório do Azure AD.  

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a autenticação do Azure AD e métodos com senha, consulte os seguintes artigos:

* [Saiba como funciona a autenticação com senha](concept-authentication-passwordless.md)
* [Saiba mais sobre registro de dispositivo](../devices/overview.md#getting-devices-in-azure-ad)
* [Saiba mais sobre a Autenticação Multifator do Azure](../authentication/howto-mfa-getstarted.md)
