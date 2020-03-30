---
title: Logon e diagnósticos
description: Explicação detalhada de como gerar e recuperar registros e diagnósticos em Âncoras Espaciais Azure.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f4359db1deda2295a66bcb97cf374d0fe9bc3ef7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270120"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Registro e diagnóstico em Âncoras Espaciais Azure

O Azure Spatial Anchors fornece um mecanismo de registro padrão que é útil para o desenvolvimento de aplicativos. O modo de registro de diagnóstico susdetectado de âncoras espaciais é útil quando você precisa de mais informações para depuração. O registro de diagnósticos armazena imagens do ambiente.

## <a name="standard-logging"></a>Registro padrão
Na API Âncoras Espaciais, você pode assinar o mecanismo de registro para obter registros úteis para o desenvolvimento e depuração de aplicativos. As APIs de registro padrão não armazenam imagens do ambiente no disco do dispositivo. O SDK fornece esses registros como retornos de eventos. Cabe a você integrar esses logs no mecanismo de registro do aplicativo.

### <a name="configuration-of-log-messages"></a>Configuração de mensagens de log
Há dois retornos de interesse para o usuário. A amostra a seguir mostra como configurar a sessão.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>Eventos e propriedades

Esses retornos de chamada de evento são fornecidos para processar registros e erros da sessão:

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Especifica o nível de detalhes para os eventos a serem recebidos a partir do tempo de execução.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Fornece eventos padrão de registro de depuração.
- [Erro](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): Fornece eventos de registro que o tempo de execução considera como erros.

## <a name="diagnostics-logging"></a>Log de diagnósticos

Além do modo padrão de operação para registro, as Âncoras Espaciais também possui um modo de diagnóstico. O modo diagnóstico captura imagens do ambiente e as registra no disco. Você pode usar este modo para depurar certos tipos de problemas, como a falha em localizar uma âncora. Habilite o registro de diagnósticos apenas para reproduzir um problema específico. Então desabilite-o. Não habilite diagnósticos quando estiver executando seus aplicativos normalmente.

Durante uma interação de suporte com a Microsoft, um representante da Microsoft pode perguntar se você está disposto a enviar um pacote de diagnósticos para uma investigação mais aprofundada. Neste caso, você pode decidir ativar diagnósticos e reproduzir o problema para que você possa enviar o pacote de diagnóstico.

Se você enviar um registro de diagnóstico para a Microsoft sem o reconhecimento prévio de um representante da Microsoft, o envio ficará sem resposta.

As seções a seguir mostram como ativar o modo diagnóstico e também como enviar registros de diagnósticos para a Microsoft.

### <a name="enable-diagnostics-logging"></a>Habilitar o registro em log de diagnóstico

Quando você habilita uma sessão para registro de diagnósticos, todas as operações da sessão têm o registro de diagnósticos correspondente no sistema de arquivos local. Durante o registro, as imagens do ambiente são salvas no disco.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>Enviar o pacote de diagnósticos

O trecho de código a seguir mostra como enviar um pacote de diagnósticos para a Microsoft. Este pacote incluirá imagens do ambiente capturado pela sessão após ativar diagnósticos.

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>Partes de um pacote de diagnósticos
O pacote de diagnósticos pode conter as seguintes informações:

- **Imagens de quadro-chave**: Imagens do ambiente capturado durante a sessão enquanto os diagnósticos foram ativados.
- **Logs**: Registre eventos registrados pelo tempo de execução.
- **Metadados da sessão**: Metadados que identificam a sessão.
