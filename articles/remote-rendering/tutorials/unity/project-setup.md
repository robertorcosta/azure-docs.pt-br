---
title: Configuração de um projeto do Unity do zero
description: Explica como configurar um projeto do Unity em branco para uso com o Azure Remote Rendering.
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: 7901f12763cb97fa76c0908e76755247ae934a20
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300582"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>Tutorial: Configuração de um projeto do Unity do zero

Neste tutorial, você irá aprender:

> [!div class="checklist"]
>
> * Configuração de um projeto do Unity do zero para o ARR.
> * Criação e interrupção de sessões de renderização.
> * Reutilização de sessões existentes.
> * Conexão e desconexão de sessões.
> * Carregamento de modelos em uma sessão de renderização.
> * Exibição de estatísticas de conexão.

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, é necessário:

* As informações da conta (ID da conta, chave de conta, ID da assinatura). Se você ainda não tiver uma conta, [crie uma](../../how-tos/create-an-account.md).
* SDK do Windows 10.0.18362.0 [(baixar)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* A versão mais recente do Visual Studio 2019 [(baixar)](https://visualstudio.microsoft.com/vs/older-downloads/). 
* [Ferramentas do Visual Studio para Realidade Misturada](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). Especificamente, as seguintes instalações de *carga de trabalho* são obrigatórias:
  * **Desenvolvimento para desktop com C++**
  * **Desenvolvimento da UWP (Plataforma Universal do Windows)**
* GIT [(baixar)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(baixar)](https://unity3d.com/get-unity/download)
  * Instale estes módulos no Unity:
    * **UWP** – Suporte ao Build da Plataforma Universal do Windows
    * **IL2CPP** – Suporte ao Build do Windows (IL2CPP)

> [!TIP]
> O [repositório de amostras do ARR](https://github.com/Azure/azure-remote-rendering) contém projetos do Unity preparados para todos os tutoriais. Você pode usar esses projetos como referência.

## <a name="create-a-new-unity-project"></a>Criar um projeto do Unity

No hub do Unity, crie um projeto.
Neste exemplo, presumiremos que o projeto está sendo criado em uma pasta chamada `RemoteRendering`.

![janela novo projeto](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>Configurar o manifesto do projeto

Você precisa modificar o arquivo `Packages/manifest.json` que está localizado na pasta do projeto do Unity. Abra o arquivo em um editor de texto e acrescente as linhas listadas abaixo:

```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    "com.unity.render-pipelines.universal": "7.2.1",
    ...existing dependencies...
  }
}
```

O pacote de pipeline de renderização universal é opcional, mas recomendado por motivos de desempenho.
Depois que você modificar e salvar o manifesto, o Unity será atualizado automaticamente. Confirme se os pacotes foram carregados na janela *Projeto*:

![confirmar importações de pacote](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Verificar se tem a versão mais recente do pacote

As etapas a seguir garantem que seu projeto esteja usando a versão mais recente do pacote de renderização remota.
1. Selecione o pacote na janela do Projeto e clique no ícone :::no-loc text="package":::: ![Selecionar o ícone de pacote](media/package-icons.png)
1. No Inspetor, clique em “Exibir no Gerenciador de Pacotes”: ![inspetor de pacotes](media/package-properties.png)
1. Na página Gerenciador de Pacotes do pacote de renderização remoto, veja se o botão Atualizar está disponível. Se estiver, clicar nele atualizará o pacote para a versão mais recente disponível: ![O pacote do ARR no Gerenciador de Pacotes](media/package-manager.png)
1. Às vezes, atualizar o pacote pode levar a erros no console. Se isso ocorrer, tente fechar e reabrir o projeto.

## <a name="configure-the-camera"></a>Configurar a câmera

Selecione o nó **Câmera principal**.

1. Redefina a *Transformação*:

    ![redefinir transformação da câmera](media/camera-reset-transform.png)

1. Definir **:::no-loc text="Clear flags":::** como *:::no-loc text="Solid Color":::*

1. Definir **:::no-loc text="Background":::** como *:::no-loc text="Black":::*

1. Defina **:::no-loc text="Clipping Planes":::** como *Próximo = 0,3* e *Distante = 20*. Isso significa que a renderização recortará a geometria que estiver a menos de 30 cm ou a mais de 20 metros.

    ![Propriedades da câmera do Unity](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Ajustar as configurações do projeto

1. Abra *Editar > Configurações do Projeto...*
1. Na lista à esquerda, selecione Qualidade.
1. Altere o **Nível de qualidade padrão** para *Baixo*

    ![Alterar as configurações de qualidade do projeto](media/settings-quality.png)

1. Selecione **Gráficos** à esquerda.
1. Altere a configuração de **Pipeline de renderização passível de script** para *HybridRenderingPipeline*. Ignore esta etapa se o pipeline de renderização universal não for usado.

    ![alterar as configurações de gráficos do projeto](media/settings-graphics-lwrp.png) Às vezes, a interface do usuário não preenche a lista de tipos de pipeline disponíveis dos pacotes. Nesse caso, o *HybridRenderingPipeline* ativo deve ser arrastado para o campo manualmente: ![alterar as configurações de gráficos do projeto](media/hybrid-rendering-pipeline.png)
1. Selecione **Player** à esquerda.
1. Selecione a guia **Configurações da Plataforma Universal do Windows**
1. Altere as **Configurações de XR** para dar suporte ao Windows Mixed Reality: ![configurações do player](media/xr-player-settings.png)
1. Selecione as configurações como na captura de tela acima:
    1. Habilite a **Realidade virtual com suporte**
    1. Defina o **Formato da profundidade** como *Profundidade de 16 bits*
    1. Habilite o **Compartilhamento de buffer de profundidade**
    1. Defina **Modo de renderização estéreo** como *Instanciado de passagem única*

1. Na mesma janela, acima de *Configurações de XR*, expanda **Configurações de publicação**
1. Role a tela para baixo até **Recursos** e selecione:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * Opcional para desenvolvimento: **PrivateNetworkClientServer**

      Essa opção será necessária se você quiser conectar o depurador remoto do Unity ao seu dispositivo.

1. Em **Famílias de dispositivos com suporte**, habilite **Holográfico** e **Desktop**

1. Se você quiser usar o Kit de Ferramentas de Realidade Misturada, confira a [documentação do MRTK](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview) para obter mais informações sobre as configurações e os recursos recomendados.

## <a name="validate-project-setup"></a>Validar configuração do projeto

Execute as etapas a seguir para validar se as configurações do projeto estão corretas.

1. Escolha a entrada ValidateProject no menu RemoteRendering na barra de ferramentas do editor do Unity.
1. Use a janela ValidateProject para verificar e corrigir as configurações do projeto quando necessário.

    ![Validação de projeto do editor do Unity](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>Criar um script para inicializar o Azure Remote Rendering

Crie um [script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) e dê a ele o nome **RemoteRendering**. Abra o arquivo de script e substitua todo o conteúdo pelo código abaixo:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

#if UNITY_WSA
    using UnityEngine.XR.WSA;
#endif

// ask Unity to automatically append an ARRServiceUnity component when a RemoteRendering script is attached
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRendering : MonoBehaviour
{
    // fill out the variables below with your account details

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // See the documentation for the list of available regions.
    public string AccountDomain = "westus2.mixedreality.azure.com";
    public string AccountId = "<enter your account id here>";
    public string AccountKey = "<enter your account key here>";

    public uint MaxLeaseTimeHours = 0;
    public uint MaxLeaseTimeMinutes = 10;
    public RenderingSessionVmSize VMSize = RenderingSessionVmSize.Standard;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        // initialize Azure Remote Rendering for use in Unity:
        // it needs to know which camera is used for rendering the scene
        RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
        RemoteManagerUnity.InitializeManager(clientInit);

        // lookup the ARRServiceUnity component and subscribe to session events
        arrService = GetComponent<ARRServiceUnity>();
        arrService.OnSessionErrorEncountered += ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged += ARRService_OnSessionStatusChanged;
    }

#if !UNITY_EDITOR
    private void Start()
    {
        StartCoroutine(AutoStartSessionAsync());
    }
#endif

    private void OnDestroy()
    {
        arrService.OnSessionErrorEncountered -= ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged -= ARRService_OnSessionStatusChanged;

        RemoteManagerStatic.ShutdownRemoteRendering();
    }

    private void CreateFrontend()
    {
        if (arrService.Frontend != null)
        {
            // early out if the front-end has been created before
            return;
        }

        // initialize the ARR service with our account details
        AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
        accountInfo.AccountKey = AccountKey;
        accountInfo.AccountId = AccountId;
        accountInfo.AccountDomain = AccountDomain;

        arrService.Initialize(accountInfo);
    }

    public void CreateSession()
    {
        CreateFrontend();

        // StartSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.StartSession(new RenderingSessionCreationParams(VMSize, MaxLeaseTimeHours, MaxLeaseTimeMinutes));
    }

    public void StopSession()
    {
        arrService.StopSession();
    }

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);
    }

    private void ARRService_OnSessionErrorEncountered(ARRServiceUnity caller, SessionGeneralContext context)
    {
        Debug.LogError($"Session error encountered. Context: {context.ErrorMessage}. Request Cv: {context.RequestCorrelationVector}. Response Cv: {context.ResponseCorrelationVector}");
    }

    private async void LogSessionStatus(AzureSession session)
    {
        if (session != null)
        {
            var sessionProperties = await session.GetPropertiesAsync().AsTask();
            LogSessionStatus(sessionProperties);
        }
        else
        {
            var sessionProperties = arrService.LastProperties;
            Debug.Log($"Session ended: Id={sessionProperties.Id}");
        }
    }

    private void LogSessionStatus(RenderingSessionProperties sessionProperties)
    {
        Debug.Log($"Session '{sessionProperties.Id}' is {sessionProperties.Status}. Size={sessionProperties.Size}" +
            (!string.IsNullOrEmpty(sessionProperties.Hostname) ? $", Hostname='{sessionProperties.Hostname}'" : "") +
            (!string.IsNullOrEmpty(sessionProperties.Message) ? $", Message='{sessionProperties.Message}'" : ""));
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
}
```

Esse script inicializa o Azure Remote Rendering, informa qual objeto de câmera usar para renderização e coloca um botão **Criar sessão** no visor, quando o *Modo de reprodução* estiver ativado.

> [!CAUTION]
> Modificar o script e salvá-lo enquanto o modo de reprodução está ativo no Unity poderá resultar em congelamento do Unity. Você será forçado a desligá-lo por meio do Gerenciador de Tarefas. Portanto, sempre pare o modo de reprodução antes de editar o script *RemoteRendering*.

## <a name="test-azure-remote-rendering-session-creation"></a>Testar a criação de sessão do Azure Remote Rendering

Crie um GameObject na cena e adicione o componente *RemoteRendering* a ele. Preencha o *Domínio da conta*, a *ID da conta* e a *Chave de Conta* adequados para a conta do Remote Rendering:

![Propriedades do componente Remote Rendering](media/remote-rendering-component.png)

Inicie o aplicativo no editor (**pressione Executar** ou CTRL+P). Você deve ver o botão **Criar sessão** aparecer no visor. Clique nele para iniciar sua primeira sessão do ARR:

![Criar uma primeira sessão](media/test-create.png)

Se isso falhar, verifique se você inseriu os detalhes da conta corretamente nas propriedades do componente RemoteRendering. Caso contrário, uma mensagem será exibida na janela do console mostrando a ID da sessão atribuída a você e informando que a sessão está atualmente no estado *Iniciando*:

![Saída de início da sessão](media/create-session-output.png)

Neste momento, o Azure está provisionando um servidor para você e inicializando uma máquina virtual de renderização remota. Em geral, isso **leva de 3 a 5 minutos**. Quando a VM estiver pronta, o retorno de chamada `OnSessionStatusChanged` do script do Unity será executado e imprimirá o novo status da sessão:

![Saída de sessão pronta](media/create-session-output-2.png)

É isso aí! Por enquanto, não ocorrerá mais nada. Para evitar cobranças, você deve sempre parar as sessões quando elas não forem mais necessárias. Nesta amostra, você pode fazer isso clicando no botão **Interromper sessão** ou interrompendo a simulação do Unity. Devido à propriedade **Interromper sessão automaticamente** no componente *ARRServiceUnity*, que está ativado por padrão, a sessão será interrompida automaticamente para você. Se tudo falhar, devido a indisponibilidades ou problemas de conexão, sua sessão poderá ser executada durante o *MaxLeaseTime* antes de ser desligada pelo servidor.

> [!NOTE]
> A interrupção de uma sessão terá efeito imediato e não poderá ser desfeita. Quando for interrompida, você precisará criar uma sessão, com a mesma sobrecarga de inicialização.

## <a name="reusing-sessions"></a>Reutilizar sessões

A criação de uma sessão é uma operação demorada. Portanto, tente criar sessões raramente e reutilize-as sempre que possível.

Insira o seguinte código no script *RemoteRendering* e remova as versões antigas das funções duplicadas:

```csharp
    public string SessionId = null;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
        }
    }

    public async void QueryActiveSessions()
    {
        CreateFrontend();

        var allSessionsProperties = await arrService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();

        Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");

        foreach (var sessionProperties in allSessionsProperties)
        {
            if (string.IsNullOrEmpty(SessionId))
            {
                Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");
                SessionId = sessionProperties.Id;
            }

            LogSessionStatus(sessionProperties);
        }
    }

    public void UseExistingSession()
    {
        CreateFrontend();

        // OpenSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.OpenSession(SessionId);
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
```

> [!CAUTION]
> Antes de executar esse código, desative a opção **Interromper sessão automaticamente** no componente RemoteRendering. Caso contrário, todas as sessões que você criar serão automaticamente interrompidas quando você parar a simulação. As tentativas de reutilizá-la falharão.

Ao pressionar *Executar*, você terá três botões no visor: **Criar sessão**, **Consultar sessões ativas** e **Usar sessão existente**. O primeiro botão sempre cria uma sessão. O segundo botão consulta quais sessões *ativas* existem. Se você não tiver especificado manualmente uma ID de sessão para tentar usar, essa ação selecionará automaticamente a ID de sessão para uso futuro. O terceiro botão tenta se conectar a uma sessão existente. Ou uma sessão que você especificou manualmente por meio da propriedade do componente *ID da sessão* ou uma encontrada por *Consultar sessões ativas*.

A função **AutoStartSessionAsync** é usada para simular os pressionamentos de botão fora do editor.

> [!TIP]
> É possível abrir sessões que foram interrompidas, expiradas ou estão em estado de erro. Embora elas não possam mais ser usadas para renderização, você pode consultar os detalhes depois de abrir uma sessão inativa. O código acima verifica o status de uma sessão em `ARRService_OnSessionStarted`, para parar automaticamente quando a sessão se tornar inutilizável.

Com essa funcionalidade, agora você pode criar e reutilizar sessões, o que deve aprimorar significativamente o fluxo de trabalho de desenvolvimento.

Normalmente, a criação de uma sessão seria disparada fora do aplicativo cliente por causa do tempo necessário para criar o servidor.

## <a name="connect-to-an-active-session"></a>Conectar-se a uma sessão ativa

Até agora, criamos ou abrimos sessões. A próxima etapa é *conectar* a uma sessão. Após a conexão, o servidor de renderização produzirá imagens e enviará um fluxo de vídeo para nosso aplicativo.

Insira o seguinte código no script *RemoteRendering* e remova as versões antigas das funções duplicadas:

```csharp
    private bool isConnected = false;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;

            // If our session properties are 'Ready' we are ready to start connecting to the runtime of the service.
            if (status == RenderingSessionStatus.Ready)
            {
                session.ConnectionStatusChanged += AzureSession_OnConnectionStatusChanged;
            }
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
            session.ConnectionStatusChanged -= AzureSession_OnConnectionStatusChanged;
        }
    }

    private void AzureSession_OnConnectionStatusChanged(ConnectionStatus status, Result result)
    {
        Debug.Log($"Connection status: '{status}', result: '{result}'");
        isConnected = (status == ConnectionStatus.Connected);
    }

    public void ConnectSession()
    {
        arrService.CurrentActiveSession?.ConnectToRuntime(new ConnectToRuntimeParams());
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }
    }

    private void LateUpdate()
    {
        // The session must have its runtime pump updated.
        // The update will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
        arrService.CurrentActiveSession?.Actions.Update();
    }

    private void OnDisable()
    {
        DisconnectSession();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
    }
#endif
```

Para testar essa funcionalidade:

1. Pressione **Executar** no Unity.
1. Abra uma sessão:
    1. Se você já tiver uma sessão, pressione **Consultar sessões ativas** e, em seguida, **Usar sessão existente**.
    1. Caso contrário, pressione **Criar sessão**.
1. Pressione **Conectar**.
1. Depois de alguns segundos, a saída do console deve imprimir que você está conectado.
1. Por enquanto, nada mais deve acontecer.
1. Pressione **Desconectar** ou pare o modo de reprodução do Unity.

>[!NOTE]
> Vários usuários podem *abrir* uma sessão para consultar suas informações, mas apenas um usuário pode ser *conectado* a uma sessão de cada vez. Se outro usuário já estiver conectado, a conexão falhará com um **erro handshake**.

## <a name="load-a-model"></a>Carregar um modelo

Insira o seguinte código no script *RemoteRendering* e remova as versões antigas das funções duplicadas:

```csharp

    public string ModelName = "builtin://Engine";

    private Entity modelEntity = null;
    private GameObject modelEntityGO = null;

#if UNITY_WSA
    private WorldAnchor modelWorldAnchor = null;
#endif

    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();

        // get the game object representation of this entity
        modelEntityGO = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the entity will sync translations with the server
        var sync = modelEntityGO.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        // set position to an arbitrary distance from the parent
        modelEntityGO.transform.position = Camera.main.transform.position + Camera.main.transform.forward * 2;
        modelEntityGO.transform.localScale = Vector3.one;

#if UNITY_WSA
        // anchor the model in the world
        modelWorldAnchor = modelEntityGO.AddComponent<WorldAnchor>();
#endif

        // load a model that will be parented to the entity
        // We are using the 'from SAS' flavor because that variant can load the built-in model
        var loadModelParams = new LoadModelFromSASParams(ModelName, modelEntity);
        var async = arrService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        async.ProgressUpdated += (float progress) =>
        {
            Debug.Log($"Loading: {progress * 100.0f}%");
        };

        await async.AsTask();
    }

    public void DestroyModel()
    {
        if (modelEntity == null)
        {
            return;
        }

#if UNITY_WSA
        DestroyImmediate(modelWorldAnchor);
#endif

        modelEntity.Destroy();
        modelEntity = null;

        DestroyImmediate(modelEntityGO);
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }

        DestroyModel();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }

                    if (modelEntity == null)
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Load Model"))
                        {
                            LoadModel();
                        }
                    }
                    else
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Destroy Model"))
                        {
                            DestroyModel();
                        }
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
        while (!isConnected)
        {
            yield return null;
        }
        LoadModel();
    }
#endif
```

Agora, quando pressionar Executar, abrir uma sessão e se conectar a ela, o botão **Carregar modelo** aparecerá. Depois de clicar nele, a saída do console mostrará o progresso do carregamento. Quando atingir 100%, você deverá ver o modelo de um mecanismo aparecer:

![Modelo carregado no editor](media/model-loaded-replace-me.png)

O [WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) é um componente importante usado para a [estabilidade do holograma](https://docs.microsoft.com/windows/mixed-reality/hologram-stability). No entanto, ele só terá efeito quando for implantado em um dispositivo de Realidade Misturada.

> [!TIP]
> Se você seguiu o [Início Rápido: Converter um modelo para renderização](../../quickstarts/convert-model.md), já sabe como converter seus modelos. Tudo o que você precisa fazer agora para renderizá-lo é colocar o URI em um modelo convertido na propriedade *Nome do modelo*.

## <a name="display-frame-statistics"></a>Exibir estatísticas de quadro

O Azure Remote Rendering acompanha várias informações sobre a qualidade da conexão. Para uma forma rápida de exibir essas informações, faça o seguinte:

Crie um [script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) e dê a ele o nome **RemoteFrameStats**. Abra o arquivo de script e substitua todo o conteúdo pelo código abaixo:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class RemoteFrameStats : MonoBehaviour
{
    public Text FrameStats = null;

    ARRServiceStats arrServiceStats = null;

#if UNITY_EDITOR
    private void OnEnable()
    {
        arrServiceStats = new ARRServiceStats();
    }

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            FrameStats.text = string.Empty;
            return;
        }

        arrServiceStats.Update(RemoteManagerUnity.CurrentSession);

        if (FrameStats != null)
        {
            FrameStats.text = arrServiceStats.GetStatsString();
        }
    }
#endif
}
```

Crie um GameObject e nomeie-o como *FrameStats*. Anexe-o como um nó filho ao objeto *Câmera principal* e defina a posição como **x = 0, y = 0, z = 0,325**. Adicione o componente **RemoteFrameStats** ao objeto.

Adicione um objeto filho **UI > Tela** ao objeto *FrameStats* e defina as propriedades assim:

![propriedades de tela](media/framestats-canvas.png)

Adicione um objeto **UI > Texto** como filho da tela e defina as propriedades assim:

![propriedades de texto](media/framestats-text.png)

Selecione o objeto *FrameStats* e preencha o **campo FrameStats** clicando no ícone de círculo e selecionando o objeto **Texto**:

![definir a propriedade de texto](media/framestats-set-text.png)

Agora, quando estiver conectado à sessão remota, o texto deve mostrar as estatísticas de streaming:

![saída das estatísticas de quadro](media/framestats-output.png)

O código desabilita a atualização de estatísticas fora do editor, pois uma caixa de texto bloqueada por cabeçalho seria confusa. Uma implementação mais sofisticada é encontrada no projeto [Início Rápido](../../quickstarts/render-model.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu todas as etapas necessárias para pegar um projeto do Unity em branco e fazê-lo funcionar com o Azure Remote Rendering. No próximo tutorial, vamos examinar mais de perto como trabalhar com entidades remotas.

> [!div class="nextstepaction"]
> [Tutorial: Trabalho com entidades remotas no Unity](working-with-remote-entities.md)
