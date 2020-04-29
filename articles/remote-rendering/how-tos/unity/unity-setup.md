---
title: Configurar o Remote Rendering para o Unity
description: Como inicializar a renderização remota do Azure em um projeto do Unity
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: 0415c0e7ee1432521c3cc2026feff5fc2a41d77e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681136"
---
# <a name="set-up-remote-rendering-for-unity"></a>Configurar o Remote Rendering para o Unity

Para habilitar a renderização remota do Azure (ARR) no Unity, fornecemos métodos dedicados que cuidam de alguns aspectos específicos do Unity.

## <a name="startup-and-shutdown"></a>Inicialização e desligamento

Para inicializar a renderização remota, `RemoteManagerUnity`use. Essa classe chama o genérico `RemoteManager` , mas já implementa detalhes específicos do Unity para você. Por exemplo, o Unity usa um sistema de coordenadas específico. Ao chamar `RemoteManagerUnity.Initialize`, a Convenção apropriada será configurada. A chamada também exige que você forneça a câmera do Unity que deve ser usada para exibir o conteúdo renderizado remotamente.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Para desligar a renderização remota, chame `RemoteManagerStatic.ShutdownRemoteRendering()`.

Depois que `AzureSession` um tiver sido criado e escolhido como a sessão de renderização primária, ele deverá ser `RemoteManagerUnity`registrado com:

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Código de exemplo completo

O código a seguir demonstra todas as etapas necessárias para inicializar a renderização remota do Azure no Unity:

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

### <a name="session-state-events"></a>Eventos de estado de sessão

`RemoteManagerUnity.OnSessionUpdate`emite eventos para quando o estado da sessão for alterado, consulte a documentação do código para obter detalhes.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity`é um componente opcional para simplificar o gerenciamento de instalação e de sessão. Ele contém opções para interromper automaticamente sua sessão quando o modo de saída ou de reprodução do aplicativo é encerrado no editor, bem como renovar automaticamente a concessão da sessão quando necessário. Ele armazena em cache dados como as propriedades da sessão (consulte `LastProperties` sua variável) e expõe eventos para alterações de estado de sessão e erros de sessão.

Não pode haver mais de uma instância de `ARRServiceUnity` por vez. Ela é destinada a começar mais rapidamente com a implementação de algumas funcionalidades comuns. No entanto, para um aplicativo maior, pode ser preferível fazer essas coisas por conta própria.

Para obter um exemplo de como configurar e usar `ARRServiceUnity` consulte [tutorial: Configurando um projeto do Unity do zero](../../tutorials/unity/project-setup.md).

## <a name="next-steps"></a>Próximas etapas

* [Instalar o pacote do Remote Rendering o para o Unity](install-remote-rendering-unity-package.md)
* [Tutorial: Configuração de um projeto do Unity do zero](../../tutorials/unity/project-setup.md)
