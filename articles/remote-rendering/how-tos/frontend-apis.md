---
title: APIs do Azure Frontend para autenticação
description: Explica como usar a API frontend C# para autenticação
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.openlocfilehash: 04296a3dab61fdb569126abc1bc1f975d69e226d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681344"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>Use as APIs do Azure Frontend para autenticação

Nesta seção, descreveremos como usar a API C# para autenticação.

## <a name="azurefrontendaccountinfo"></a>AzureFrontendAccountInfo

O AzureFrontendAccountInfo é usado para configurar ```AzureFrontend``` as informações de autenticação para uma instância no SDK.

Os campos importantes são:

```cs

    public class AzureFrontendAccountInfo
    {
        // Something akin to "<region>.mixedreality.azure.com"
        public string AccountDomain;

        // Can use one of:
        // 1) ID and Key.
        // 2) AuthenticationToken.
        // 3) AccessToken.
        public string AccountId = Guid.Empty.ToString();
        public string AccountKey = string.Empty;
        public string AuthenticationToken = string.Empty;
        public string AccessToken = string.Empty;
    }

```

Para a _parte da região_ no domínio, use uma região próxima a [você.](../reference/regions.md)

As informações da conta podem ser obtidas no portal conforme descrito no parágrafo de informações da [conta de recuperação.](create-an-account.md#retrieve-the-account-information)

## <a name="azure-frontend"></a>Azure Frontend

As classes ```AzureFrontend``` relevantes são e ```AzureSession```. ```AzureFrontend```é usado para gerenciamento de contas e funcionalidade de nível de conta, o que inclui: conversão de ativos e criação de sessão de renderização. ```AzureSession```é usado para funcionalidade de nível de sessão e inclui: atualização de sessão, consultas, renovação e descomissionamento.

Cada aberto/criado ```AzureSession``` manterá uma referência ao frontend que o criou. Para desligar de forma limpa, todas as sessões devem ser desalocadas antes que a parte frontal seja desalocada.

A negociação de uma sessão não impedirá `AzureSession.StopAsync` o VM no Azure, deve ser explicitamente chamada.

Uma vez que uma sessão tenha sido criada e seu estado tenha sido `AzureSession.ConnectToRuntime`marcado como pronto, ele pode se conectar ao tempo de execução de renderização remota com .

### <a name="threading"></a>Threading

Todas as chamadas azureSession e AzureFrontend async são concluídas em um segmento de fundo, não no segmento principal do aplicativo.

### <a name="conversion-apis"></a>APIs de conversão

Para obter mais informações sobre o serviço de conversão, consulte [a API REST de conversão do modelo](conversion/conversion-rest-api.md).

#### <a name="start-asset-conversion"></a>Iniciar a conversão de ativos

``` cs
private StartConversionAsync _pendingAsync = null;

void StartAssetConversion(AzureFrontend frontend, string modelName, string modelUrl, string assetContainerUrl)
{
    _pendingAsync = frontend.StartConversionAsync(
        new AssetConversionParams(modelName, modelUrl, assetContainerUrl));
    _pendingAsync.Completed +=
        (StartConversionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to start asset conversion!");
            }
        };

        _pendingAsync = null;
}
```

#### <a name="get-conversion-status"></a>Obter status de conversão

``` cs
private ConversionStatusAsync _pendingAsync = null
void GetConversionStatus(AzureFrontend frontend, string assetId)
{
    _pendingAsync = frontend.GetConversionStatusAsync(assetId);
    _pendingAsync.Completed +=
        (ConversionStatusAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get status of asset conversion!");
            }

            _pendingAsync = null;
        };
}
```

### <a name="rendering-apis"></a>Renderização de APIs

Consulte [a API de gerenciamento de sessões REST](session-rest-api.md) para obter detalhes sobre o gerenciamento de sessões.

Uma sessão de renderização pode ser criada dinamicamente no serviço ou um ID de sessão já existente pode ser 'aberto' em um objeto AzureSession.

#### <a name="create-rendering-session"></a>Criar sessão de renderização

``` cs
private CreateSessionAsync _pendingAsync = null;
void CreateRenderingSession(AzureFrontend frontend, RenderingSessionVmSize vmSize, ARRTimeSpan maxLease)
{
    _pendingAsync = frontend.CreateNewRenderingSessionAsync(
        new RenderingSessionCreationParams(vmSize, maxLease));

    _pendingAsync.Completed +=
        (CreateSessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to create session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="open-an-existing-rendering-session"></a>Abra uma sessão de renderização existente

Abrir uma sessão existente é uma chamada síncrona.

``` cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

#### <a name="get-current-rendering-sessions"></a>Obtenha sessões de renderização atuais

``` cs
private SessionPropertiesArrayAsync _pendingAsync = null;
void GetCurrentRenderingSessions(AzureFrontend frontend)
{
    _pendingAsync = frontend.GetCurrentRenderingSessionsAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesArrayAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get current rendering sessions!");
            }
            _pendingAsync = null;
        };
}
```

### <a name="session-apis"></a>APIs de sessão

#### <a name="get-rendering-session-properties"></a>Obtenha propriedades de sessão de renderização

``` cs
private SessionPropertiesAsync _pendingAsync = null;
void GetRenderingSessionProperties(AzureSession session)
{
    _pendingAsync = session.GetPropertiesAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get properties of session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="update-rendering-session"></a>Sessão de renderização de atualização

``` cs
private SessionAsync _pendingAsync;
void UpdateRenderingSession(AzureSession session, ARRTimeSpan updatedLease)
{
    _pendingAsync = session.RenewAsync(
        new RenderingSessionUpdateParams(updatedLease));
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session renewed succeeded!");
            }
            else
            {
                Console.WriteLine("Failed to renew rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="stop-rendering-session"></a>Parar sessão de renderização

``` cs
private SessionAsync _pendingAsync;
void StopRenderingSession(AzureSession session)
{
    _pendingAsync = session.StopAsync();
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session stopped successfully!");
            }
            else
            {
                Console.WriteLine("Failed to stop rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="connect-to-arr-inspector"></a>Conecte-se ao inspetor ARR

``` cs
private ArrInspectorAsync _pendingAsync = null;
void ConnectToArrInspector(AzureSession session, string hostname)
{
    _pendingAsync = session.ConnectToArrInspectorAsync(hostname);
    _pendingAsync.Completed +=
        (ArrInspectorAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                // Launch the html file with default browser
                string htmlPath = res.Result;
#if WINDOWS_UWP
                UnityEngine.WSA.Application.InvokeOnUIThread(async () =>
                {
                    var file = await Windows.Storage.StorageFile.GetFileFromPathAsync(htmlPath);
                    await Windows.System.Launcher.LaunchFileAsync(file);
                }, true);
#else
                InvokeOnAppThreadAsync(() =>
                {
                    System.Diagnostics.Process.Start("file:///" + htmlPath);
                });
#endif
            }
            else
            {
                Console.WriteLine("Failed to connect to ARR inspector!");
            }
        };
}
```

## <a name="next-steps"></a>Próximas etapas

* [Criar uma conta](create-an-account.md)
* [Exemplo de scripts PowerShell](../samples/powershell-example-scripts.md)
