---
title: Login em aplicativos MSAL | Azure
titleSuffix: Microsoft identity platform
description: Saiba como fazer registro em log em aplicativos da MSAL (Biblioteca de Autenticação da Microsoft).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/11/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 58697cc535357710c6889f05060b5e04e129ae7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084884"
---
# <a name="logging-in-msal-applications"></a>Login em aplicativos MSAL

Os aplicativos msal (Microsoft Authentication Library, biblioteca de autenticação da Microsoft) geram mensagens de log que podem ajudar a diagnosticar problemas. Um aplicativo pode configurar o registro em log com algumas linhas de código, ter um controle personalizado sobre o nível de detalhes e determinar se dados pessoais e organizacionais serão registrados. Recomendamos que você crie um retorno de chamada de registro MSAL e forneça uma maneira de os usuários enviarem logs quando tiverem problemas de autenticação.

## <a name="logging-levels"></a>Níveis de log

O MSAL fornece vários níveis de detalhes de registro:

- Erro: Indica que algo deu errado e um erro foi gerado. Use para depuração e identificação de problemas.
- Aviso: Não houve necessariamente um erro ou falha, mas destina-se a diagnósticos e problemas de identificação.
- Informações: A MSAL registrará eventos destinados a fins informativos não necessariamente destinados à depuração.
- Verbose: Padrão. A MSAL registra todos os detalhes do comportamento da biblioteca.

## <a name="personal-and-organizational-data"></a>Dados pessoais e organizacionais

Por padrão, o logger MSAL não captura nenhum dado pessoal ou organizacional altamente sensível. A biblioteca oferece a opção de ativar o registro de dados pessoais e organizacionais se você decidir fazê-lo.

Para obter detalhes sobre o login do MSAL em um idioma específico, escolha a guia que corresponde ao seu idioma:

## <a name="net"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>Registro em log no MSAL.NET

 > [!NOTE]
 > Consulte o [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) para obter amostras de registro de MSAL.NET e muito mais.

Na MSAL 3.x, o registro em log é definido por aplicativo na criação do aplicativo usando o modificador de construtor `.WithLogging`. Esse método usa parâmetros opcionais:

- `Level`permite que você decida qual nível de registro você deseja. Configurá-lo como Erros só registrará erros
- `PiiLoggingEnabled`permite que você registre dados pessoais e organizacionais se definido como verdadeiro. Por padrão, ele fica definido como false, para que seu aplicativo não registre dados pessoais.
- `LogCallback`é definido como um delegado que faz o registro. Se `PiiLoggingEnabled` for verdade, este método receberá as mensagens duas vezes: uma vez com o parâmetro é igual a `containsPii` falsa e a mensagem sem dados pessoais, e uma segunda vez com o `containsPii` parâmetro igual a verdadeiro e a mensagem pode conter dados pessoais. Em alguns casos (quando a mensagem não contém dados pessoais), a mensagem será a mesma.
- `DefaultLoggingEnabled`permite o registro padrão da plataforma. Por padrão, é false. Se você defini-lo como true, ele usará o rastreamento de eventos em aplicativos de área de trabalho/UWP, NSLog no iOS e logcat no Android.

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

## <a name="android"></a>[Android](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>Login em MSAL para Android usando Java

Ative o login na criação do aplicativo criando um retorno de chamada de registro. O retorno de chamada toma esses parâmetros:

- `tag`é uma string passada para o retorno de chamada pela biblioteca. Ele está associado à entrada de registro e pode ser usado para classificar mensagens de registro.
- `logLevel`permite que você decida qual nível de registro você deseja. Os níveis de log `Error` `Warning`suportados são: , e `Info` `Verbose`.
- `message`é o conteúdo da entrada de log.
- `containsPII`especifica se as mensagens que contêm dados pessoais ou dados organizacionais estão registradas. Por padrão, isso é definido como falso, para que seu aplicativo não registre dados pessoais. Se `containsPII` `true`for, este método receberá as mensagens `containsPII` duas vezes: uma com o parâmetro definido `false` e sem `message` dados pessoais, e uma segunda vez com o `containsPii` parâmetro definido `true` e a mensagem pode conter dados pessoais. Em alguns casos (quando a mensagem não contém dados pessoais), a mensagem será a mesma.

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

Por padrão, o logger MSAL não capturará nenhuma informação pessoal identificável ou informações de identificação organizacional.
Para habilitar o registro de informações pessoais identificáveis ou informações identificáveis organizacionais:

```java
Logger.getInstance().setEnablePII(true);
```

Para desativar o registro de dados pessoais e dados da organização:

```java
Logger.getInstance().setEnablePII(false);
```

O registro padrão no logcat é desativado. Para habilitar:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascript"></a>[Javascript](#tab/javascript)

 Habilite o login no MSAL.js (JavaScript) passando um `UserAgentApplication` objeto logger durante a configuração para criar uma instância. O objeto logger tem as seguintes propriedades:

- `localCallback`: uma instância de callback que pode ser fornecida pelo desenvolvedor para consumir e publicar logs de forma personalizada. Implemente o método localCallback, dependendo de como você deseja redirecionar os logs.
- `level`(opcional): o nível de log configurável. Os níveis de log `Error` `Warning`suportados são: , e `Info` `Verbose`. O padrão é `Info`.
- `piiLoggingEnabled`(opcional): se definido como verdadeiro, registra dados pessoais e organizacionais. Por padrão, isso é falso para que seu aplicativo não registre dados pessoais. Logs de dados pessoais nunca são gravados em saídas padrão como Console, Logcat ou NSLog.
- `correlationId`(opcional): um identificador exclusivo, usado para mapear a solicitação com a resposta para fins de depuração. O padrão é guid RFC4122 versão 4 (128 bits).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
    },
     system: {
             logger: new Msal.Logger(
                                loggerCallback ,{
                                     level: Msal.LogLevel.Verbose,
                                     piiLoggingEnabled: false,
                                     correlationId: '1234'
                                }
                        )
     }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL para iOS e macOS loging-ObjC

Defina um retorno de chamada para capturar o registro do MSAL e incorpore-o no registro do seu próprio aplicativo. A assinatura do retorno de chamada é assim:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Por exemplo: 

```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

### <a name="personal-data"></a>Dados pessoais

Por padrão, a MSAL não captura ou registra nenhum dado pessoal (PII). A biblioteca permite que os desenvolvedores de aplicativos liguem isso através de uma propriedade na classe MSALLogger. Ao `pii.Enabled`ligar, o aplicativo assume a responsabilidade de manusear dados altamente confidenciais e seguir os requisitos normativos.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Níveis de log

Para definir o nível de registro ao fazer login usando O MSAL para iOS e macOS, use um dos seguintes valores:

|Nível  |Descrição |
|---------|---------|
| `MSALLogLevelNothing`| Desativar todo o registro |
| `MSALLogLevelError` | Nível padrão, imprime informações somente quando ocorrem erros |
| `MSALLogLevelWarning` | Warnings |
| `MSALLogLevelInfo` |  Pontos de entrada da biblioteca, com parâmetros e várias operações de chaveiro |
|`MSALLogLevelVerbose`     |  Rastreamento de API |

Por exemplo: 

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Formato de mensagem de log

A parte de mensagem das mensagens de log do MSAL está no formato de`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Por exemplo: 

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Fornecer IDs de correlação e carimbos de tempo são úteis para rastrear problemas. As informações de carimbo de tempo e correlação estão disponíveis na mensagem de registro. O único lugar confiável para recuperá-los é a partir de mensagens de registro MSAL.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL para iOS e macOS log-Swift

Defina um retorno de chamada para capturar o registro do MSAL e incorpore-o no registro do seu próprio aplicativo. A assinatura (representada no Objective-C) para o retorno de chamada é assim:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Por exemplo: 

```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-data"></a>Dados pessoais

Por padrão, a MSAL não captura ou registra nenhum dado pessoal (PII). A biblioteca permite que os desenvolvedores de aplicativos liguem isso através de uma propriedade na classe MSALLogger. Ao `pii.Enabled`ligar, o aplicativo assume a responsabilidade de manusear dados altamente confidenciais e seguir os requisitos normativos.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Níveis de log

Para definir o nível de registro ao fazer login usando O MSAL para iOS e macOS, use um dos seguintes valores:

|Nível  |Descrição |
|---------|---------|
| `MSALLogLevelNothing`| Desativar todo o registro |
| `MSALLogLevelError` | Nível padrão, imprime informações somente quando ocorrem erros |
| `MSALLogLevelWarning` | Warnings |
| `MSALLogLevelInfo` |  Pontos de entrada da biblioteca, com parâmetros e várias operações de chaveiro |
|`MSALLogLevelVerbose`     |  Rastreamento de API |

Por exemplo: 

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Formato de mensagem de log

A parte de mensagem das mensagens de log do MSAL está no formato de`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Por exemplo: 

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Fornecer IDs de correlação e carimbos de tempo são úteis para rastrear problemas. As informações de carimbo de tempo e correlação estão disponíveis na mensagem de registro. O único lugar confiável para recuperá-los é a partir de mensagens de registro MSAL.

## <a name="java"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL para registro de Java

O MSAL for Java permite que você use a biblioteca de registro que você já está usando com o seu aplicativo, desde que seja compatível com o SLF4J. O MSAL for Java usa a [Fachada de Registro Simples para Java](http://www.slf4j.org/) (SLF4J) como uma fachada simples ou abstração para várias frameworks de registro, como [java.util.logging,](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html) [Logback](http://logback.qos.ch/) e [Log4j](https://logging.apache.org/log4j/2.x/). O SLF4J permite que o usuário conecte a estrutura de registro desejada no momento da implantação.

Por exemplo, para usar o Logback como a estrutura de registro em seu aplicativo, adicione a dependência do Logback ao arquivo maven pom para o seu aplicativo:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Em seguida, adicione o arquivo de configuração Logback:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

O SLF4J se liga automaticamente ao Logback no momento da implantação. Os registros MSAL serão gravados no console.

Para obter instruções sobre como se vincular a outras estruturas de registro, consulte o [manual SLF4J](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Informações pessoais e de organização

Por padrão, o registro do MSAL não captura ou registra dados pessoais ou organizacionais. No exemplo a seguir, o registro de dados pessoais ou organizacionais é desligado por padrão:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Ative o registro de `logPii()` dados pessoais e organizacionais definindo o construtor de aplicativos cliente. Se você ativar o registro de dados pessoais ou organizacionais, seu aplicativo deve assumir a responsabilidade de manusear dados altamente confidenciais e cumprir quaisquer requisitos normativos.

No exemplo a seguir, o registro de dados pessoais ou organizacionais está habilitado:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="python"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL para registro de Python

O login no MSAL Python usa o `logging.info("msg")` mecanismo padrão de registro Python, por exemplo Você pode configurar o registro MSAL da seguinte forma (e vê-lo em ação no [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Habilite o registro de depuração para todos os módulos

Por padrão, o login em qualquer script Python é desligado. Se você quiser ativar o registro de depuração para todos os módulos em todo o seu script Python, use:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Silenciar apenas o registro de MSAL

Para silenciar apenas o registro da biblioteca MSAL, ao mesmo tempo em que habilita o login de depuração em todos os outros módulos do seu script Python, desligue o logger usado pelo MSAL Python:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Dados pessoais e organizacionais em Python

O MSAL for Python não registra dados pessoais ou dados organizacionais. Não há propriedade para ativar ou desativar dados pessoais ou de organização.

Você pode usar o registro padrão python para registrar o que quiser, mas você é responsável por lidar com dados confidenciais com segurança e seguir os requisitos normativos.

Para obter mais informações sobre o login no Python, consulte o [Log ingENdo UI .](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)

---
