---
title: Registro de Fala SDK - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como ativar o login no Speech SDK (C++, C#, Python, Objective-C, Java).
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: 707a0f801a739a7a91cee19635e609305cd8f021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74805783"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Habilite o login no SDK de fala

O registro de arquivos é um recurso opcional para o Speech SDK. Durante o registro de desenvolvimento, o registro fornece informações adicionais e diagnósticos dos componentes principais do Speech SDK. Ele pode ser ativado definindo a propriedade `Speech_LogFilename` em um objeto de configuração de fala para o local e o nome do arquivo log. O registro será ativado globalmente assim que um resumidor for criado a partir dessa configuração e não poderá ser desativado depois. Você não pode alterar o nome de um arquivo de log durante uma sessão de registro em execução.

> [!NOTE]
> O registro está disponível desde a versão 1.4.0 do Speech SDK em todas as linguagens de programação do Speech SDK suportadas, com exceção do JavaScript.

## <a name="sample"></a>Amostra

O nome do arquivo de registro é especificado em um objeto de configuração. Tomando `SpeechConfig` o exemplo e assumindo que você `config`criou uma instância chamada:

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

Você pode criar um reconhistário a partir do objeto de configuração. Isso permitirá o registro para todos os reconhecedores.

> [!NOTE]
> Se você `SpeechSynthesizer` criar um objeto de configuração, ele não permitirá o registro. Se o registro estiver ativado, porém, você `SpeechSynthesizer`também receberá diagnósticos do .

## <a name="create-a-log-file-on-different-platforms"></a>Criar um arquivo de log em diferentes plataformas

Para Windows ou Linux, o arquivo de log pode estar em qualquer caminho para o quais o usuário tenha permissão de gravação. As permissões de gravação para localizações do sistema de arquivos em outros sistemas operacionais podem ser limitadas ou restritas por padrão.

### <a name="universal-windows-platform-uwp"></a>UWP (Plataforma Universal do Windows)

Os aplicativos UWP precisam ser colocados arquivos de registro em um dos locais de dados do aplicativo (local, roaming ou temporário). Um arquivo de log pode ser criado na pasta de aplicativos local:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Mais sobre a permissão de acesso a arquivos para aplicativos UWP está disponível [aqui](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Você pode salvar um arquivo de registro no armazenamento interno, no armazenamento externo ou no diretório de cache. Os arquivos criados no armazenamento interno ou no diretório de cache são privados do aplicativo. É preferível criar um arquivo de log no armazenamento externo.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

O código acima salvará um arquivo de log no armazenamento externo na raiz de um diretório específico do aplicativo. Um usuário pode acessar o arquivo com `Android/data/ApplicationName/logfile.txt`o gerenciador de arquivos (geralmente em ). O arquivo será excluído quando o aplicativo for desinstalado.

Você também precisa `WRITE_EXTERNAL_STORAGE` solicitar permissão no arquivo manifesto:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Mais sobre dados e armazenamento de arquivos para aplicativos Android está disponível [aqui](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Apenas diretórios dentro da caixa de areia do aplicativo são acessíveis. Os arquivos podem ser criados nos documentos, biblioteca e diretórios temporários. Os arquivos no diretório de documentos podem ser disponibilizados a um usuário. O seguinte trecho de código mostra a criação de um arquivo de log no diretório de documentos do aplicativo:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Para acessar um arquivo criado, adicione `Info.plist` as propriedades abaixo à lista de propriedades do aplicativo:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Mais sobre o sistema de arquivos iOS está disponível [aqui](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar nossas amostras no GitHub](https://aka.ms/csspeech/samples)
