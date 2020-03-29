---
title: Autenticação intermediada no Android | Azure
titlesuffix: Microsoft identity platform
description: Uma visão geral da autenticação intermediada & autorização para Android na plataforma de identidade Microsoft
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: a734589178438fd65d9a2d156fd91fc82807f578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697890"
---
# <a name="brokered-authentication-in-android"></a>Autenticação intermediada no Android

Você deve usar um dos corretores de autenticação da Microsoft para participar do SSO (Single Sign-On) em todo o dispositivo e para atender às políticas organizacionais de Acesso Condicional. A integração com um corretor oferece os seguintes benefícios:

- Dispositivo único de adesão
- Acesso condicional para:
  - Proteção de Aplicativo do Intune
  - Registro de dispositivos (Workplace Join)
  - Gerenciamento do Dispositivo Móvel
- Gerenciamento de contas em todo o dispositivo
  -  via Android AccountManager & Configurações da conta
  - "Conta de trabalho" - tipo de conta personalizada

No Android, o Microsoft Authentication Broker é um componente incluído no [Microsoft Authenticator App](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [no Intune Company Portal](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)

> [!TIP]
> Apenas um aplicativo que hospeda o corretor estará ativo como o corretor de cada vez. Qual aplicativo está ativo como corretor é determinado por ordem de instalação no dispositivo. O primeiro a ser instalado, ou o último presente no dispositivo, torna-se o corretor ativo.

O diagrama a seguir ilustra a relação entre seu aplicativo, a Microsoft Authentication Library (MSAL) e os corretores de autenticação da Microsoft.

![Diagrama de implantação de corretores](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Instalando aplicativos que hospedam um corretor

Os aplicativos de hospedagem de corretores podem ser instalados pelo proprietário do dispositivo em sua loja de aplicativos (normalmente Google Play Store) a qualquer momento. No entanto, algumas APIs (recursos) são protegidas por Políticas de Acesso Condicional que exigem que os dispositivos sejam:

- Registrado (local de trabalho aderido) e/ou
- Inscrito em gerenciamento de dispositivos ou
- Inscrito na Intune App Protection

Se um dispositivo ainda não tiver um aplicativo de corretora instalado, a MSAL instrui o usuário a instalar um assim que o aplicativo tentar obter um token de forma interativa. Em seguida, o aplicativo precisará levar o usuário através das etapas para tornar o dispositivo compatível com a política necessária.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Efeitos da instalação e desinstalação de um corretor

### <a name="when-a-broker-is-installed"></a>Quando um corretor é instalado

Quando um corretor é instalado em um dispositivo, todas `acquireToken()`as solicitações de token interativos subseqüentes (chamadas para ) são tratadas pela corretora e não localmente pela MSAL. Qualquer estado SSO anteriormente disponível para a MSAL não está disponível para o corretor. Como resultado, o usuário precisará autenticar novamente ou selecionar uma conta da lista existente de contas conhecidas pelo dispositivo.

Instalar um corretor não requer que o usuário faça login novamente. Somente quando o usuário `MsalUiRequiredException` precisar resolver um testamento a próxima solicitação irá para o corretor. `MsalUiRequiredException`é jogado por uma série de razões, e precisa ser resolvido interativamente. Estas são algumas razões comuns:

- O usuário alterou a senha associada à sua conta.
- A conta do usuário não atende mais a uma política de Acesso Condicional.
- O usuário revogou seu consentimento para que o aplicativo fosse associado à sua conta.

### <a name="when-a-broker-is-uninstalled"></a>Quando um corretor é desinstalado

Se houver apenas um aplicativo de hospedagem de corretor instalado e ele for removido, o usuário precisará fazer login novamente. A desinstalação da corretora ativa remove a conta e os tokens associados do dispositivo.

Se o Portal da Empresa Intune estiver instalado e estiver operando como corretor ativo, e o Microsoft Authenticator também estiver instalado, então se o Portal da Empresa Intune (corretor ativo) for desinstalado, o usuário precisará fazer login novamente. Uma vez que eles fazem login novamente, o aplicativo Microsoft Authenticator torna-se o corretor ativo.

## <a name="integrating-with-a-broker"></a>Integrando-se com um corretor

### <a name="generating-a-redirect-uri-for-a-broker"></a>Gerando um URI de redirecionamento para um corretor

Você deve registrar um URI de redirecionamento compatível com o corretor. O URI de redirecionamento para a corretora precisa incluir o nome do pacote do seu aplicativo, bem como a representação codificada base64 da assinatura do seu aplicativo.

O formato do URI de redirecionamento é:`msauth://<yourpackagename>/<base64urlencodedsignature>`

Gere sua assinatura codificada de url Base64 usando as chaves de assinatura do aplicativo. Aqui estão alguns comandos de exemplo que usam suas chaves de assinatura de depuração:

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Consulte [Assinar seu aplicativo](https://developer.android.com/studio/publish/app-signing) para obter informações sobre como assinar seu aplicativo.

> [!IMPORTANT]
> Use sua chave de assinatura de produção para a versão de produção do seu aplicativo.

### <a name="configure-msal-to-use-a-broker"></a>Configure o MSAL para usar um corretor

Para usar um corretor em seu aplicativo, você deve atestar que você configurou o redirecionamento do seu corretor. Por exemplo, inclua o uri de redirecionamento habilitado para corretor — e indique que você o registrou — incluindo o seguinte no arquivo de configuração MSAL:

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> A nova ui de registro de aplicativo do portal Azure ajuda a gerar o uri de redirecionamento da corretora. Se você registrou seu aplicativo usando a experiência mais antiga, ou o fez usando o portal de registro de aplicativos da Microsoft, você pode precisar gerar o URI redirecionado e atualizar a lista de URIs de redirecionamento no portal manualmente.

### <a name="broker-related-exceptions"></a>Exceções relacionadas a corretores

A MSAL se comunica com o corretor de duas maneiras:

- Serviço vinculado ao corretor
- Gerente de conta do Android

A MSAL primeiro usa o serviço vinculado ao corretor porque ligar para este serviço não requer nenhuma permissão do Android. Se a vinculação ao serviço vinculado falhar, o MSAL usará a API do Android AccountManager. A MSAL só faz isso se `"READ_CONTACTS"` o seu aplicativo já tiver recebido a permissão.

Se você `MsalClientException` obter um `"BROKER_BIND_FAILURE"`código de erro, então há duas opções:

- Peça ao usuário para desativar a otimização de energia para o aplicativo Microsoft Authenticator e o Portal da Empresa Intune.
- Peça ao usuário `"READ_CONTACTS"` para conceder a permissão
