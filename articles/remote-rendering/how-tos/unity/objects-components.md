---
title: Componentes e objetos de jogos do Unity
description: Descreve métodos específicos do Unity para trabalhar com entidades e componentes de renderização remota.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 662c28196b06f5fbe49f69cb7145fdd33805e000
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89019038"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Interagir com componentes e objetos de jogos do Unity

A renderização remota do Azure (ARR) é otimizada para grandes números de objetos (consulte [limitações](../../reference/limits.md)). Embora seja possível gerenciar hierarquias grandes e complexas no host, é impossível replicar todas elas no Unity em dispositivos de baixa energia.

Portanto, quando um modelo é carregado no host, a renderização remota do Azure espelha as informações sobre a estrutura do modelo no dispositivo cliente (o que incorrerá no tráfego de rede), mas não replica os objetos e componentes no Unity. Em vez disso, ele espera que você solicite os objetos e componentes do jogo do Unity manualmente, de modo que você pode limitar a sobrecarga para o que realmente é necessário. Dessa forma, você tem mais controle sobre o desempenho do lado do cliente.

Consequentemente, a integração do Unity do processamento remoto do Azure vem com funcionalidade adicional para replicar a estrutura de renderização remota sob demanda.

## <a name="load-a-model-in-unity"></a>Carregar um modelo no Unity

Ao carregar um modelo, você obtém uma referência ao objeto raiz do modelo carregado. Essa referência não é um objeto de jogo do Unity, mas você pode transformá-la em uma usando o método de extensão `Entity.GetOrCreateGameObject()` . Essa função espera um argumento do tipo `UnityCreationMode` . Se você passar `CreateUnityComponents` , o objeto de jogo do Unity recém-criado também será populado com componentes de proxy para todos os componentes de renderização remota existentes no host. No entanto, é recomendável preferir `DoNotCreateUnityComponents` manter a sobrecarga mínima.

### <a name="load-model-with-task"></a>Carregar modelo com tarefa

```cs
LoadModelAsync _pendingLoadTask = null;
void LoadModelWithTask()
{
    _pendingLoadTask = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    _pendingLoadTask.Completed += (LoadModelAsync res) =>
    {
        // turn the root object into a Unity game object
        var gameObject = res.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        _pendingLoadTask = null;
    };

    // also listen to progress updates:
    _pendingLoadTask.ProgressUpdated += (float progress) =>
    {
        // progress is a fraction in [0..1] range
        int percentage = (int)(progress * 100.0f);
        // do something...
        // Since the updates are triggered by the main thread, we may access unity objects here.
    };
}
```

### <a name="load-model-with-unity-coroutines"></a>Carregar modelo com corotinas de Unity

```cs
IEnumerator LoadModelWithCoroutine()
{
    LoadModelAsync task = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    while (!task.IsCompleted)
    {
        int percentage = (int)(task.Progress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }

    task = null;
}
```

### <a name="load-model-with-await-pattern"></a>Carregar modelo com padrão de Await

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

Os exemplos de código acima usaram o caminho de carregamento do modelo via SAS porque o modelo interno está carregado. Abordar o modelo por meio de contêineres de BLOB (usando `LoadModelAsync` e `LoadModelParams` ) funciona de forma totalmente análoga.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

A criação de um objeto de jogo Unity adiciona implicitamente um `RemoteEntitySyncObject` componente ao objeto Game. Esse componente é usado para sincronizar a transformação de entidade para o servidor. Por padrão, `RemoteEntitySyncObject` o usuário precisa chamar explicitamente `SyncToRemote()` para sincronizar o estado local do Unity com o servidor. Habilitar `SyncEveryFrame` irá sincronizar o objeto automaticamente.

Os objetos com um `RemoteEntitySyncObject` podem ter seus filhos remotos instanciados e mostrados no editor do Unity por meio do **:::no-loc text="Show children":::** botão.

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Componentes do wrapper

Os [componentes](../../concepts/components.md) anexados às entidades de renderização remota são expostos ao Unity por meio de proxy `MonoBehavior` s. Esses proxies representam o componente remoto no Unity e encaminham todas as modificações para o host.

Para criar componentes de renderização remota de proxy, use o método de extensão `GetOrCreateArrComponent` :

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Tempos de vida acoplados

O tempo de vida de uma [entidade](../../concepts/entities.md) remota e um objeto de jogo do Unity são acoplados enquanto estão ligados por meio de um `RemoteEntitySyncObject` . Se você chamar `UnityEngine.Object.Destroy(...)` com esse objeto de jogo, a entidade remota também será removida.

Para destruir o objeto de jogo do Unity, sem afetar a entidade remota, primeiro você precisará chamar `Unbind()` no `RemoteEntitySyncObject` .

O mesmo é verdadeiro para todos os componentes de proxy. Para destruir apenas a representação do lado do cliente, você precisa chamar `Unbind()` primeiro o componente de proxy:

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>Próximas etapas

* [Configurar o Remote Rendering para o Unity](unity-setup.md)
* [Tutorial: manipular entidades remotas no Unity](../../tutorials/unity/manipulate-models/manipulate-models.md)
