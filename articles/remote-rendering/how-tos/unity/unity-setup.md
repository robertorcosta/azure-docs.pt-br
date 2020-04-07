---
title: Configurar renderização remota para unidade
description: Como inicializar a renderização remota do Azure em um projeto unity
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: 0415c0e7ee1432521c3cc2026feff5fc2a41d77e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681136"
---
# <a name="set-up-remote-rendering-for-unity"></a>Configurar renderização remota para unidade

Para habilitar o ARR (Azure Remote Rendering) no Unity, fornecemos métodos dedicados que cuidam de alguns aspectos específicos da Unidade.

## <a name="startup-and-shutdown"></a>Inicialização e desligamento

Para inicializar a `RemoteManagerUnity`renderização remota, use . Esta classe chama `RemoteManager` para o genérico, mas já implementa detalhes específicos da Unidade para você. Por exemplo, unity usa um sistema de coordenadas específico. Ao `RemoteManagerUnity.Initialize`ligar, a convenção apropriada será criada. A chamada também exige que você forneça a câmera Unity que deve ser usada para exibir o conteúdo renderizado remotamente.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Para desligar a renderização remota, ligue `RemoteManagerStatic.ShutdownRemoteRendering()`.

Depois `AzureSession` de criado e escolhido como a sessão de renderização `RemoteManagerUnity`primária, ele deve ser registrado com :

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Código de exemplo completo

O código abaixo demonstra todas as etapas necessárias para inicializar a renderização remota do Azure na Unidade:

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// ... fill out accountInfo ...
AzureFrontend frontend = new AzureFrontend(accountInfo);

// start a session
AzureSession session = await frontend.CreateNewRenderingSessionAsync(new RenderingSessionCreationParams(RenderingSessionVmSize.Standard, 0, 30)).AsTask();

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

session.ConnectToRuntime(new ConnectToRuntimeParams());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>Funções de conveniência

### <a name="session-state-events"></a>Eventos estaduais de sessão

`RemoteManagerUnity.OnSessionUpdate`emite eventos para quando o estado de sessão for alterado, consulte a documentação do código para obter detalhes.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity`é um componente opcional para simplificar a configuração e o gerenciamento de sessões. Ele contém opções para interromper automaticamente sua sessão quando o aplicativo está saindo ou o modo de reprodução é saído no editor, bem como renovar automaticamente a locação da sessão quando necessário. Ele armazena dados como as propriedades `LastProperties` da sessão (veja sua variável) e expõe eventos para alterações de estado de sessão e erros de sessão.

Não pode haver mais de `ARRServiceUnity` um exemplo de cada vez. É para fazer você começar mais rápido implementando alguma funcionalidade comum. Para uma aplicação maior pode ser preferível fazer essas coisas você mesmo, no entanto.

Para um exemplo de como `ARRServiceUnity` configurar e usar ver [Tutorial: Configurando um projeto Unity do zero](../../tutorials/unity/project-setup.md).

## <a name="next-steps"></a>Próximas etapas

* [Instale o pacote de renderização remota para unidade](install-remote-rendering-unity-package.md)
* [Tutorial: Configurando um projeto unity do zero](../../tutorials/unity/project-setup.md)
