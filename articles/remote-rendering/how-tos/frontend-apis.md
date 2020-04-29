---
title: APIs de front-end do Azure para autenticação
description: Explica como usar a API de front-end do C# para autenticação
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.openlocfilehash: 04296a3dab61fdb569126abc1bc1f975d69e226d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681344"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>Usar as APIs de front-end do Azure para autenticação

Nesta seção, descreveremos como usar a API do C# para autenticação.

## <a name="azurefrontendaccountinfo"></a>AzureFrontendAccountInfo

AzureFrontendAccountInfo é usado para configurar as informações de autenticação para uma ```AzureFrontend``` instância no SDK.

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

Para a parte da _região_ no domínio, use uma [região perto de você](../reference/regions.md).

As informações da conta podem ser obtidas no portal, conforme descrito no parágrafo [recuperar informações da conta](create-an-account.md#retrieve-the-account-information) .

## <a name="azure-frontend"></a>Front-end do Azure

As classes relevantes são ```AzureFrontend``` e ```AzureSession```. ```AzureFrontend```é usado para a funcionalidade de gerenciamento de conta e de nível de conta, que inclui: conversão de ativos e criação de sessão de renderização. ```AzureSession```é usado para a funcionalidade de nível de sessão e inclui: atualização de sessão, consultas, renovação e encerramento.

Cada aberta/criada ```AzureSession``` manterá uma referência ao front-end que o criou. Para desligar de forma limpa, todas as sessões devem ser desalocadas antes que o front-end seja desalocado.

A desalocação de uma sessão não irá parar a VM `AzureSession.StopAsync` no Azure, deve ser chamada explicitamente.

Depois que uma sessão tiver sido criada e seu estado tiver sido marcado como pronto, ele poderá se conectar ao tempo de execução `AzureSession.ConnectToRuntime`de renderização remoto com.

### <a name="threading"></a>Threading

Todas as chamadas assíncronas AzureSession e AzureFrontend são concluídas em um thread em segundo plano, não no thread do aplicativo principal.

### <a name="conversion-apis"></a>APIs de conversão

Para obter mais informações sobre o serviço de conversão, consulte [a API REST de conversão de modelo](conversion/conversion-rest-api.md).

#### <a name="start-asset-conversion"></a>Iniciar conversão de ativo

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

#### <a name="get-conversion-status"></a>Obter status da conversão

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

### <a name="rendering-apis"></a>APIs de renderização

Consulte [a API REST de gerenciamento de sessão](session-rest-api.md) para obter detalhes sobre o gerenciamento de sessão.

Uma sessão de renderização pode ser criada dinamicamente no serviço ou uma ID de sessão já existente pode ser "aberta" em um objeto AzureSession.

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

#### <a name="open-an-existing-rendering-session"></a>Abrir uma sessão de renderização existente

Abrir uma sessão existente é uma chamada síncrona.

``` cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

#### <a name="get-current-rendering-sessions"></a>Obter sessões de renderização atuais

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

#### <a name="get-rendering-session-properties"></a>Obter propriedades da sessão de renderização

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

#### <a name="update-rendering-session"></a>Atualizar sessão de renderização

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

#### <a name="stop-rendering-session"></a>Parar a renderização da sessão

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

#### <a name="connect-to-arr-inspector"></a>Conectar-se ao inspetor de ARR

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
* [Scripts de exemplo do PowerShell](../samples/powershell-example-scripts.md)
