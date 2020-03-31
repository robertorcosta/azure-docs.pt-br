---
title: Arquivo de configuração Android MSAL | Azure
titleSuffix: Microsoft identity platform
description: Uma visão geral do arquivo de configuração MSAL (Android Microsoft Authentication Library, biblioteca de autenticação do Microsoft), que representa a configuração de um aplicativo no Azure Active Directory.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 9e35ba5a3f3705a52e80262da9bbfbfda489bf83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050371"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Arquivo de configuração da Biblioteca de Autenticação do Android Microsoft

O MSAL (Android Microsoft Authentication Library, biblioteca de autenticação microsoft) é fornecido com um [arquivo JSON de configuração padrão](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) que você personaliza para definir o comportamento do seu aplicativo cliente público para coisas como a autoridade padrão, quais autoridades você usará, e assim por diante.

Este artigo irá ajudá-lo a entender as várias configurações no arquivo de configuração e como especificar o arquivo de configuração a ser usado em seu aplicativo baseado em MSAL.

## <a name="configuration-settings"></a>Definições de configuração

### <a name="general-settings"></a>Configurações gerais

| Propriedade | Tipo de Dados | Obrigatório | Observações |
|-----------|------------|-------------|-------|
| `client_id` | String | Sim | ID do cliente do seu aplicativo a partir da página de registro do [aplicativo](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `redirect_uri`   | String | Sim | O URI de redirecionamento do aplicativo da página de registro do [aplicativo](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `authorities` | >\<de Autoridade de Lista | Não | A lista de autoridades que seu aplicativo precisa |
| `authorization_user_agent` | AuthorizationAgent (enum) | Não | Valores `DEFAULT`possíveis: , `BROWSER``WEBVIEW` |
| `http` | httpconfiguration | Não | Configurar `HttpUrlConnection` `connect_timeout` e`read_timeout` |
| `logging` | Configuração de registro | Não | Especifica o nível de detalhes de registro. As configurações `pii_enabled`opcionais incluem: , que `log_level`leva `ERROR`um `WARNING` `INFO`valor `VERBOSE`booleano, e , o que leva , , , ou . . |

### <a name="client_id"></a>client_id

O ID do cliente ou iD do aplicativo que foi criado quando você registrou seu aplicativo.

### <a name="redirect_uri"></a>redirect_uri

O URI de redirecionamento que você registrou quando registrou seu aplicativo. Se o URI redirecionar for para um aplicativo de corretora, consulte [redirecionar o URI para aplicativos públicos do cliente](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) para garantir que você está usando o formato URI de redirecionamento correto para o seu aplicativo de corretora.

### <a name="authorities"></a>Autoridades

A lista de autoridades que são conhecidas e confiáveis por você. Além das autoridades listadas aqui, a MSAL também consulta a Microsoft para obter uma lista de nuvens e autoridades conhecidas pela Microsoft. Nesta lista de autoridades, especifique o tipo de `"audience"`autoridade e quaisquer parâmetros opcionais adicionais, como, que deve se alinhar com o público do seu aplicativo com base no registro do seu aplicativo. A seguir, uma lista de exemplos de autoridades:

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken or acquireTokenSilent call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenantId": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>Mapear autoridade AAD & audiência para os pontos finais da plataforma de identidade da Microsoft

| Type | Público | ID do locatário | Authority_Url | Ponto final resultante | Observações |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftConta | | | `https://login.microsoftonline.com/common` | `common`é um alias inquilino para onde a conta está. Como um inquilino específico do Azure Active Directory ou o sistema de conta da Microsoft. |
| AAD | AzureADMyOrg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | Somente contas presentes em contoso.com podem adquirir um token. Qualquer domínio verificado, ou o GUID do inquilino, pode ser usado como id do inquilino. |
| AAD | AzureADMultipleOrgS | | | `https://login.microsoftonline.com/organizations` | Apenas contas do Azure Active Directory podem ser usadas com este ponto final. As contas da Microsoft podem ser membros de organizações. Para adquirir um token usando uma conta Microsoft para um recurso em uma organização, especifique o inquilino organizacional do qual você deseja o token. |
| AAD | PersonalMicrosoftAccount | | | `https://login.microsoftonline.com/consumers` | Apenas contas microsoft pode usar este ponto final. |
| B2C | | | Ver ponto final resultante | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | Somente as contas presentes no contoso.onmicrosoft.com inquilino pode adquirir um token. Neste exemplo, a diretiva B2C faz parte do caminho do URL da Autoridade. |

> [!NOTE]
> A validação de autoridade não pode ser habilitada e desativada no MSAL.
> As autoridades são conhecidas por você como desenvolvedor, conforme especificado via configuração ou conhecido pela Microsoft através de metadados.
> Se a MSAL receber uma solicitação de um `MsalClientException` token para uma autoridade desconhecida, um tipo `UnknownAuthority` de resultado.

#### <a name="authority-properties"></a>Propriedades de autoridade

| Propriedade | Tipo de dados  | Obrigatório | Observações |
|-----------|-------------|-----------|--------|
| `type` | String | Sim | Espelha o público ou a conta digitando os alvos do seu aplicativo. Valores `AAD`possíveis: ,`B2C` |
| `audience` | Objeto | Não | Só se aplica`AAD`quando o tipo= . Especifica a identidade que seu aplicativo visa. Use o valor do seu registro de aplicativo |
| `authority_url` | String | Sim | Obrigatório apenas quando`B2C`digitar= . Especifica a URL ou a política de autoridade que seu aplicativo deve usar  |
| `default` | booleano | Sim | Um `"default":true` único é necessário quando uma ou mais autoridades são especificadas. |

#### <a name="audience-properties"></a>Propriedades do público

| Propriedade | Tipo de Dados  | Obrigatório | Observações |
|-----------|-------------|------------|-------|
| `type` | String | Sim | Especifica o público que seu aplicativo deseja segmentar. Valores `AzureADandPersonalMicrosoftAccount`possíveis: , `PersonalMicrosoftAccount`, `AzureADMultipleOrgs``AzureADMyOrg` |
| `tenant_id` | String | Sim | Exigido somente `"type":"AzureADMyOrg"`quando . Opcional para `type` outros valores. Este pode ser um `contoso.com`domínio inquilino, como , `72f988bf-86f1-41af-91ab-2d7cd011db46`ou um ID inquilino, como ) |

### <a name="authorization_user_agent"></a>authorization_user_agent

Indica se deve usar uma webview incorporada ou o navegador padrão no dispositivo, ao fazer login em uma conta ou autorizar o acesso a um recurso.

Valores possíveis:
- `DEFAULT`: Prefere o navegador do sistema. Usa a visualização web incorporada se um navegador não estiver disponível no dispositivo.
- `WEBVIEW`: Use a visualização web incorporada.
- `BROWSER`: Usa o navegador padrão no dispositivo.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

Para clientes que suportam `true`várias nuvens nacionais, especifique . A plataforma de identidade da Microsoft redirecionará automaticamente para a nuvem nacional correta durante a autorização e o resgate de tokens. Você pode determinar a nuvem nacional da conta de entrada `AuthenticationResult`examinando a autoridade associada ao . Observe que `AuthenticationResult` o não fornece o endereço final do recurso específico para nuvem nacional para o qual você solicita um token.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Um booleano que indica se você está usando um corretor de identidade compatível com o Microsoft IUri. Defina `false` para se você não quiser usar o corretor dentro do seu aplicativo.

Se você estiver usando a Autoridade AAD com audiência definida para `"MicrosoftPersonalAccount"`, o corretor não será usado.

### <a name="http"></a>http

Configure configurações globais para tempoouts HTTP, tais como:

| Propriedade | Tipo de dados | Obrigatório | Observações |
| ---------|-----------|------------|--------|
| `connect_timeout` | INT | Não | Tempo em milissegundos |
| `read_timeout` | INT | Não | Tempo em milissegundos |

### <a name="logging"></a>registro em log

As seguintes configurações globais são para registro:

| Propriedade | Tipo de Dados  | Obrigatório | Observações |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | booleano | Não | Quer emitir dados pessoais |
| `log_level`   | booleano | Não | Quais mensagens de registro para saída |
| `logcat_enabled` | booleano | Não | Se para sair para registrar gato, além da interface de registro |

### <a name="account_mode"></a>account_mode

Especifica quantas contas podem ser usadas dentro do seu aplicativo de cada vez. Os valores possíveis são:

- `MULTIPLE` (Padrão)
- `SINGLE`

A construção `PublicClientApplication` de um modo de conta que não corresponda a essa configuração resultará em uma exceção.

Para obter mais informações sobre as diferenças entre contas simples e múltiplas, consulte [Aplicativos de conta única e múltipla](single-multi-account.md).

### <a name="browser_safelist"></a>browser_safelist

Uma lista de navegadores compatíveis com o MSAL. Esses navegadores lidam corretamente com redirecionamentos para intenções personalizadas. Você pode adicionar a esta lista. O padrão é fornecido na configuração padrão mostrada abaixo.
``
## <a name="the-default-msal-configuration-file"></a>O arquivo de configuração MSAL padrão

A configuração MSAL padrão que é enviado com MSAL é mostrada abaixo. Você pode ver a versão mais recente no [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json).

Esta configuração é complementada por valores que você fornece. Os valores fornecidos anulam os padrões.

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>Exemplo de configuração básica

O exemplo a seguir ilustra uma configuração básica que especifica o ID do cliente, redirecionar uri, se um redirecionamento de corretor está registrado e uma lista de autoridades.

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>Como usar um arquivo de configuração

1. Crie um arquivo de configuração. Recomendamos que você crie seu `res/raw/auth_config.json`arquivo de configuração personalizado em . Mas você pode colocá-lo em qualquer lugar que quiser.
2. Diga à MSAL onde procurar sua `PublicClientApplication`configuração quando construir o . Por exemplo: 

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
