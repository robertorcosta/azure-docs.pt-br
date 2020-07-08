---
title: APIs de front-end do Azure para autenticação
description: Explica como usar a API de front-end C# para autenticação
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.openlocfilehash: c43ed412116d0cb30f7d06ba65467fd529f367ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85552686"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>Usar as APIs de front-end do Azure para autenticação

Nesta seção, descreveremos como usar a API C# para autenticação.

## <a name="azurefrontendaccountinfo"></a>AzureFrontendAccountInfo

AzureFrontendAccountInfo é usado para configurar as informações de autenticação para uma instância de ```AzureFrontend``` no SDK.

Os campos importantes são:

```cs

public class AzureFrontendAccountInfo
{
    // Something akin to "<region>.mixedreality.azure.com"
    public string AccountDomain;

    // Can use one of:
    // 1) ID and Key.
    // 2) ID and AuthenticationToken.
    // 3) ID and AccessToken.
    public string AccountId = Guid.Empty.ToString();
    public string AccountKey = string.Empty;
    public string AuthenticationToken = string.Empty;
    public string AccessToken = string.Empty;
}
```

O equivalente C++ é semelhante a:

```cpp
struct AzureFrontendAccountInfo
{
    std::string AccountDomain{};
    std::string AccountId{};
    std::string AccountKey{};
    std::string AuthenticationToken{};
    std::string AccessToken{};
};

```

Para o bloco _região_ no domínio, use uma [região perto de você](../reference/regions.md).

As informações da conta podem ser obtidas no portal, conforme descrito no parágrafo sobre como [recuperar informações da conta](create-an-account.md#retrieve-the-account-information).

## <a name="azure-frontend"></a>Azure Frontend

As classes relevantes são ```AzureFrontend``` e ```AzureSession```. ```AzureFrontend``` é usado para gerenciamento de conta e funcionalidade de nível de conta, o que inclui: conversão de ativos e criação de sessão de renderização. ```AzureSession``` é usado para a funcionalidade no nível de sessão e inclui: atualização de sessão, consultas, renovação e encerramento.

Cada ```AzureSession``` aberta/criada manterá uma referência ao front-end que o criou. Para desligar corretamente, todas as sessões devem ser desalocadas antes que o front-end seja desalocado.

A desalocação de uma sessão não interromperá a VM no Azure, `AzureSession.StopAsync` deve ser chamado explicitamente.

Depois que uma sessão tiver sido criada e seu estado tiver sido marcado como pronto, ele poderá se conectar ao runtime do Remote Rendering com `AzureSession.ConnectToRuntime`.

### <a name="threading"></a>Threading

Todas as chamadas assíncronas do AzureSession e do AzureFrontend são concluídas em um thread em segundo plano, não no thread do aplicativo principal.

### <a name="conversion-apis"></a>APIs de conversão

Para obter mais informações sobre o serviço de conversão, veja [API REST de conversão de modelo](conversion/conversion-rest-api.md).

#### <a name="start-asset-conversion"></a>Iniciar conversão de ativo

```cs
private StartConversionAsync _pendingAsync = null;

void StartAssetConversion(AzureFrontend frontend, string storageContainer, string blobinputpath, string bloboutpath, string modelName, string outputName)
{
    _pendingAsync = frontend.StartConversionAsync(
        new AssetConversionInputParams(storageContainer, blobinputpath, "", modelName),
        new AssetConversionOutputParams(storageContainer, bloboutpath, "", outputName)
        );
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

```cpp
void StartAssetConversion(ApiHandle<AzureFrontend> frontend, std::string storageContainer, std::string blobinputpath, std::string bloboutpath, std::string modelName, std::string outputName)
{
    AssetConversionInputParams input;
    input.BlobContainerInformation.BlobContainerName = blobinputpath;
    input.BlobContainerInformation.StorageAccountName = storageContainer;
    input.BlobContainerInformation.FolderPath = "";
    input.InputAssetPath = modelName;

    AssetConversionOutputParams output;
    output.BlobContainerInformation.BlobContainerName = blobinputpath;
    output.BlobContainerInformation.StorageAccountName = storageContainer;
    output.BlobContainerInformation.FolderPath = "";
    output.OutputAssetPath = outputName;

    ApiHandle<StartAssetConversionAsync> conversionAsync = *frontend->StartAssetConversionAsync(input, output);
    conversionAsync->Completed([](ApiHandle<StartAssetConversionAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            //use res.Result
        }
        else
        {
            printf("Failed to start asset conversion!");
        }
    }
    );
}
```


#### <a name="get-conversion-status"></a>Obter status de conversão

```cs
private ConversionStatusAsync _pendingAsync = null
void GetConversionStatus(AzureFrontend frontend, string assetId)
{
    _pendingAsync = frontend.GetAssetConversionStatusAsync(assetId);
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

```cpp
void GetConversionStatus(ApiHandle<AzureFrontend> frontend, std::string assetId)
{
    ApiHandle<ConversionStatusAsync> pendingAsync = *frontend->GetAssetConversionStatusAsync(assetId);
    pendingAsync->Completed([](ApiHandle<ConversionStatusAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            // use res->Result
        }
        else
        {
            printf("Failed to get status of asset conversion!");
        }

    });
}
```


### <a name="rendering-apis"></a>APIs de renderização

Veja [API REST de gerenciamento de sessão](session-rest-api.md) para ver detalhes sobre o gerenciamento de sessão.

Uma sessão de renderização pode ser criada dinamicamente no serviço, ou uma ID de sessão já existente pode ser "aberta" em um objeto AzureSession.

#### <a name="create-rendering-session"></a>Criar uma sessão de renderização

```cs
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

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend, RenderingSessionVmSize vmSize, const ARRTimeSpan& maxLease)
{
    RenderingSessionCreationParams params;
    params.MaxLease = maxLease;
    params.Size = vmSize;
    ApiHandle<CreateSessionAsync> pendingAsync = *frontend->CreateNewRenderingSessionAsync(params);

    pendingAsync->Completed([] (ApiHandle<CreateSessionAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            //use res->Result
        }
        else
        {
            printf("Failed to create session!");
        }
    });
}
```

#### <a name="open-an-existing-rendering-session"></a>Abrir uma sessão de renderização existente

A abertura de uma sessão existente é uma chamada síncrona.

```cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend, std::string sessionId)
{
    ApiHandle<AzureSession> session = *frontend->OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```


#### <a name="get-current-rendering-sessions"></a>Obter sessões de renderização atuais

```cs
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

```cpp
void GetCurrentRenderingSessions(ApiHandle<AzureFrontend> frontend)
{
    ApiHandle<SessionPropertiesArrayAsync> pendingAsync = *frontend->GetCurrentRenderingSessionsAsync();
    pendingAsync->Completed([](ApiHandle<SessionPropertiesArrayAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            // use res.Result
        }
        else
        {
            printf("Failed to get current rendering sessions!");
        }
    });
}
```

### <a name="session-apis"></a>APIs de sessão

#### <a name="get-rendering-session-properties"></a>Obter propriedades da sessão de renderização

```cs
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

```cpp
void GetRenderingSessionProperties(ApiHandle<AzureSession> session)
{
    ApiHandle<SessionPropertiesAsync> pendingAsync = *session->GetPropertiesAsync();
    pendingAsync->Completed([](ApiHandle<SessionPropertiesAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            //use res.Result
        }
        else
        {
            printf("Failed to get properties of session!");
        }
    });
}
```

#### <a name="update-rendering-session"></a>Atualizar sessão de renderização

```cs
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

```cpp
void UpdateRenderingSession(ApiHandle<AzureSession> session, const ARRTimeSpan& updatedLease)
{
    RenderingSessionUpdateParams params;
    params.MaxLease = updatedLease;
    ApiHandle<SessionAsync> pendingAsync = *session->RenewAsync(params);
    pendingAsync->Completed([](ApiHandle<SessionAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            printf("Rendering session renewed succeeded!");
        }
        else
        {
            printf("Failed to renew rendering session!");
        }
    });
}
```

#### <a name="stop-rendering-session"></a>Parar a renderização da sessão

```cs
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

```cpp
void StopRenderingSession(ApiHandle<AzureSession> session)
{
    ApiHandle<SessionAsync> pendingAsync = *session->StopAsync();
    pendingAsync->Completed([](ApiHandle<SessionAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            printf("Rendering session stopped successfully!");
        }
        else
        {
            printf("Failed to stop rendering session!");
        }
    });
}
```

#### <a name="connect-to-arr-inspector"></a>Conectar-se ao inspetor de ARR

```cs
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

```cpp
void ConnectToArrInspector(ApiHandle<AzureSession> session, std::string hostname)
{
    ApiHandle<ArrInspectorAsync> pendingAsync = *session->ConnectToArrInspectorAsync(hostname);
    pendingAsync->Completed([](ApiHandle<ArrInspectorAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            // Launch the html file with default browser
            std::string htmlPath = "file:///" + *res->Result();
            ShellExecuteA(NULL, "open", htmlPath.c_str(), NULL, NULL, SW_SHOWDEFAULT);
        }
        else
        {
            printf("Failed to connect to ARR inspector!");
        }
    });
}
```

## <a name="next-steps"></a>Próximas etapas

* [Criar uma conta](create-an-account.md)
* [Scripts de exemplo do PowerShell](../samples/powershell-example-scripts.md)
