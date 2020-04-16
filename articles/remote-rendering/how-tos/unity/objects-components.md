---
title: Componentes e objetos de jogos do Unity
description: Descreve métodos específicos da Unity para trabalhar com entidades e componentes de renderização remota.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.openlocfilehash: a34276c73211c1d9bea291f449cbc7041a3e78a2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409865"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Interagir com componentes e objetos de jogos do Unity

A Renderização Remota Do Azul (ARR) é otimizada para um grande número de objetos (ver [Limitações](../../reference/limits.md)). Embora seja possível gerenciar hierarquias grandes e complexas no host, replicá-las todas em Unity em dispositivos de baixa potência é inviável.

Portanto, quando um modelo é carregado no host, o Azure Remote Rendering espelha as informações sobre a estrutura do modelo no dispositivo cliente (que incorrerá no tráfego da rede), mas não replica os objetos e componentes no Unity. Em vez disso, espera que você solicite os objetos e componentes necessários do jogo Unity manualmente, de modo que você possa limitar a sobrecarga ao que é realmente necessário. Dessa forma, você tem mais controle sobre o desempenho do lado do cliente.

Consequentemente, a integração Unity da Renderização Remota do Azure vem com funcionalidade adicional para replicar a estrutura de renderização remota sob demanda.

## <a name="load-a-model-in-unity"></a>Carregar um modelo em Unity

Quando você carrega um modelo, você recebe uma referência ao objeto raiz do modelo carregado. Esta referência não é um objeto de jogo Unity, mas `Entity.GetOrCreateGameObject()`você pode transformá-lo em um usando o método de extensão . Essa função espera um `UnityCreationMode`argumento do tipo . Se você `CreateUnityComponents`passar, o objeto de jogo Unity recém-criado será preenchido com componentes proxy para todos os componentes de renderização remota existentes no host. Recomenda-se, porém, preferir, `DoNotCreateUnityComponents`manter a sobrecarga mínima.

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

### <a name="load-model-with-unity-coroutines"></a>Modelo de carga com corotinas unity

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

### <a name="load-model-with-await-pattern"></a>Modelo de carga com padrão de espera

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

As amostras de código acima usaram o caminho de carregamento do modelo via SAS porque o modelo embutido está carregado. Abordar o modelo através de recipientes blob (usando `LoadModelAsync` e `LoadModelParams`) funciona de forma totalmente análoga.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

Criar um objeto de jogo `RemoteEntitySyncObject` Unity adiciona implicitamente um componente ao objeto do jogo. Este componente é usado para sincronizar a transformação da entidade para o servidor. Por `RemoteEntitySyncObject` padrão, o usuário `SyncToRemote()` chama explicitamente para sincronizar o estado de Unidade local com o servidor. A `SyncEveryFrame` ativação sincronizará o objeto automaticamente.

Objetos `RemoteEntitySyncObject` com um podem ter seus filhos remotos instanciados e mostrados no editor Unity através do botão **Show Children.**

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Componentes do invólucro

[Os componentes](../../concepts/components.md) anexados às entidades de renderização remota são expostos à Unidade através de proxys. `MonoBehavior` Esses proxies representam o componente remoto no Unity e encaminham todas as modificações para o host.

Para criar componentes de renderização `GetOrCreateArrComponent`remota proxy, use o método de extensão:

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Vidas acopladas

A vida útil de uma [entidade](../../concepts/entities.md) remota e um objeto `RemoteEntitySyncObject`de jogo Unity é acoplado enquanto eles são amarrados através de um . Se você `UnityEngine.Object.Destroy(...)` ligar com tal objeto de jogo, a entidade remota também será removida.

Para destruir o objeto do jogo Unity, sem `Unbind()` afetar `RemoteEntitySyncObject`a entidade remota, primeiro você precisa chamar o .

O mesmo vale para todos os componentes proxy. Para destruir apenas a representação do `Unbind()` lado do cliente, você precisa chamar primeiro o componente proxy:

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
* [Tutorial: Trabalho com entidades remotas no Unity](../../tutorials/unity/working-with-remote-entities.md)
