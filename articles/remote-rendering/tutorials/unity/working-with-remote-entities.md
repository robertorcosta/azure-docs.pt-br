---
title: Trabalho com entidades remotas no Unity
description: Tutorial que mostra como trabalhar com entidades de ARR.
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: db1f6a53121e05b29f7e3441af027985a141bc2e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310204"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>Tutorial: Trabalho com entidades remotas no Unity

[Tutorial: Configuração de um projeto do Unity do zero](project-setup.md) mostrou como configurar um novo projeto do Unity para trabalhar com o Azure Remote Rendering. Neste tutorial, damos uma olhada na funcionalidade mais comum que cada usuário do ARR precisa.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Escolher objetos usando ray-casts.
> * Substituir estados do objeto, como cor de tonalidade, estado de seleção e visibilidade.
> * Excluir entidades remotas.
> * Mover entidades remotas.
> * Usar planos de corte para olhar dentro de objetos.

## <a name="prerequisites"></a>Pré-requisitos

* Este tutorial é baseado no [Tutorial: Configurar um projeto do Unity do zero](project-setup.md).

> [!TIP]
> O [repositório de exemplos do ARR](https://github.com/Azure/azure-remote-rendering) contém projetos do Unity preparados para todos os tutoriais na pasta *Unity*, que você pode usar como referência.

## <a name="pick-objects"></a>Escolher objetos

Queremos interagir com objetos. Portanto, a primeira coisa que precisamos é escolher objetos sob o cursor do mouse.

Crie um [script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) chamado **RemoteRaycaster** e substitua todo o conteúdo pelo código abaixo:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using System.Threading.Tasks;
using UnityEngine;

[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRaycaster : MonoBehaviour
{
    public double MaxDistance = 30.0;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        arrService = GetComponent<ARRServiceUnity>();
    }

    private async Task<Entity> RemoteRayCast(Vector3 origin, Vector3 dir)
    {
        Entity entity = null;

        var raycast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), MaxDistance, HitCollectionPolicy.ClosestHit);

        var hits = await arrService.CurrentActiveSession.Actions.RayCastQueryAsync(raycast).AsTask();

        if (hits != null)
        {
            foreach (var hit in hits)
            {
                var hitEntity = hit.HitEntity;
                if (hitEntity == null)
                {
                    continue;
                }

                entity = hitEntity;
                break;
            }
        }

        return entity;
    }

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
        var ray = Camera.main.ScreenPointToRay(position);

        Raycast(ray.origin, ray.direction);
    }

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var entity = await RemoteRayCast(origin, direction);
        if (entity != null)
        {
            Debug.Log("Object Hit: " + entity.Name);
        }
    }
}
```

Adicione esse componente ao objeto *RemoteRendering* em sua cena.

> [!WARNING]
>
> O componente *RemoteRaycaster* requer que um componente *ARRServiceUnity* seja anexado ao mesmo objeto. *ARRServiceUnity* é uma classe auxiliar para acessar algumas funcionalidades do ARR com mais facilidade. No entanto, só pode haver uma instância desse componente na cena. Portanto, adicione todos os componentes que exigem *ARRServiceUnity* para o mesmo GameObject.
> Se você quiser acessar a funcionalidade do ARR de vários objetos de jogos, adicione o componente *ARRServiceUnity* apenas a um deles e faça referência a ele nos outros scripts ou acesse a funcionalidade do ARR diretamente.

Pressione Executar, conecte-se a uma sessão e carregue um modelo. Agora aponte para objetos na cena e observe a saída do console. Ele deve imprimir o nome do objeto de cada bloco sobre o qual você passar o mouse.

## <a name="highlight-objects"></a>Realçar objetos

Como próxima etapa, queremos fornecer comentários visuais sobre para quais blocos de um modelo o usuário está apontando. Para conseguir isso, anexamos um [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) à entidade que escolhemos. Esse componente pode ser usado para habilitar ou desabilitar vários recursos em um objeto. Aqui, é usado para definir uma cor de tonalidade e habilitar a [renderização de estrutura de tópicos](../../overview/features/outlines.md).

Crie outro arquivo de script chamado **RemoteModelEntity** e substitua seu conteúdo pelo seguinte código:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

public class RemoteModelEntity : MonoBehaviour
{
    public Color HighlightColor = new Color(1.0f, 0.0f, 0.0f, 0.1f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;

    private RemoteEntitySyncObject localSyncObject = null;
    private ARRHierarchicalStateOverrideComponent localStateOverride = null;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();
        localStateOverride = GetComponent<ARRHierarchicalStateOverrideComponent>();

        if (localStateOverride == null)
        {
            localStateOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
            var remoteStateOverride = localSyncObject.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

            if (remoteStateOverride == null)
            {
                // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                localStateOverride.Create(RemoteManagerUnity.CurrentSession);
            }
            else
            {
                // otherwise, bind our local stateOverride component to the remote component
                localStateOverride.Bind(remoteStateOverride);
            }
        }

        localStateOverride.RemoteComponent.TintColor = HighlightColor.toRemote();
    }

    public void OnDisable()
    {
        SetStateOverride(false);

        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.Enabled = false;
            localStateOverride.enabled = false;
        }
    }

    private void SetStateOverride(bool on)
    {
        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.UseTintColorState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.SelectedState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);
    }
}
```
> [!CAUTION]
> Não atribua esse script a nenhum objeto de jogo, pois ele será atribuído programaticamente pelo código abaixo.

Em seguida, precisamos estender nosso *RemoteRaycaster* para adicionar o componente *RemoteModelEntity* ao objeto que acabamos de escolher.

Adicione o seguinte código à implementação do **RemoteRaycaster** e remova as funções duplicadas:

```csharp
    private RemoteModelEntity focusedModel = null;

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var remoteEntity = await RemoteRayCast(origin, direction);

        if (focusedModel != null)
        {
            if (focusedModel.Entity == remoteEntity)
            {
                // picked the same object as before
                return;
            }

            ClearFocus();
        }

        if (remoteEntity == null)
        {
            // picked no object at all
            return;
        }

        // get the instance of a Unity GameObject for the ARR entity
        var entityGO = remoteEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the game object has the RemoteModelEntity component
        focusedModel = entityGO.GetComponent<RemoteModelEntity>();
        if (focusedModel == null)
        {
            focusedModel = entityGO.AddComponent<RemoteModelEntity>();
        }

        focusedModel.SetFocus(true);
    }

    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        focusedModel = null;
    }
```

Execute o projeto e aponte para um modelo. Você deve vê-lo obter uma tonalidade vermelha e uma estrutura de tópicos de seleção branca.

## <a name="isolate-the-selected-object"></a>Isolar o objeto selecionado

Outro uso do [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) é a capacidade de substituir a visibilidade. Isso permite isolar um objeto selecionado do restante do modelo. Abra o script **RemoteModelEntity**, adicione o seguinte código e remova as funções duplicadas:

```csharp
    private bool isolated = false;
    private HierarchicalStateOverrideComponent parentOverride = null;

    public void ToggleIsolate()
    {
        SetIsolated(!isolated);
    }

    public void SetIsolated(bool on)
    {
        if (localStateOverride == null || !localStateOverride.IsComponentValid || isolated == on)
        {
            return;
        }

        // find the top most parent object that has a HierarchicalStateOverrideComponent
        if (parentOverride == null)
        {
            var modelRoot = transform;

            while (modelRoot.parent != null)
            {
                modelRoot = modelRoot.parent;

                var parentSyncObject = modelRoot.GetComponent<RemoteEntitySyncObject>();

                var stateOverrideComp = parentSyncObject?.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                if (stateOverrideComp != null)
                {
                    parentOverride = stateOverrideComp;
                }
            }
        }

        if (parentOverride != null)
        {
            isolated = on;

            parentOverride.HiddenState = isolated ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.HiddenState = isolated ? HierarchicalEnableState.ForceOff : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);

        if (!on)
        {
            SetIsolated(false);
        }
    }
```

Esse código depende de ter um componente de substituição de estado no objeto mais alto na hierarquia, o que torna todos os objetos invisíveis. Em seguida, ele substitui a visibilidade novamente no objeto selecionado para tornar esse objeto visível. Portanto, precisamos criar um componente de substituição de estado no objeto raiz.

Abra o script **RemoteRendering** e insira o código abaixo na parte superior da função *LoadModel*:

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

Por fim, precisamos de uma forma de ativar/desativar a visibilidade. Abra o script **RemoteRaycaster** e substitua a função *Atualizar*:

```csharp
    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        var ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        Raycast(ray.origin, ray.direction);

        if (Input.GetMouseButtonDown(1) && focusedModel != null)
        {
            focusedModel.ToggleIsolate();
        }
    }
```

Execute o código e clique com o botão direito do mouse em um bloco do modelo. O restante do modelo desaparecerá e apenas a peça realçada permanecerá visível.

## <a name="remove-gameobject-instances-of-remote-entities"></a>Remover instâncias GameObject de entidades remotas

Talvez você tenha notado que o código continua criando objetos, mas nunca os limpa. Isso também é visível no painel de hierarquia de objetos. Ao expandir a hierarquia de objetos remotos durante a simulação, você pode ver cada vez mais objetos aparecendo sempre que passa o mouse sobre um novo bloco do modelo.

Ter muitos objetos em uma cena afeta negativamente o desempenho. Você sempre deve limpar os objetos que não são mais necessários.

Insira o código abaixo no script **RemoteRaycaster** e remova as funções duplicadas:

```csharp
    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        CleanHierarchy(focusedModel.gameObject);
        focusedModel = null;
    }

    private void CleanHierarchy(GameObject focusedGO)
    {
        var sync = focusedGO?.GetComponent<RemoteEntitySyncObject>();
        if (sync == null || !sync.IsEntityValid)
        {
            return;
        }

        sync.Entity.DestroyGameObject(EntityExtensions.DestroyGameObjectFlags.DestroyEmptyParents | EntityExtensions.DestroyGameObjectFlags.KeepRemoteRoot);
    }
```

## <a name="move-objects"></a>Mover objetos

Na próxima etapa, desejamos mover um objeto selecionado. No script **RemoteRaycaster**, insira esse código e remova a função duplicada:

```csharp
    private Vector3 lastPosition = Vector3.zero;

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        if (Input.GetMouseButton(0))
        {
            if (focusedModel)
            {
                // note: mousePosition is in 2D screen-space coordinates and has no relation with
                // the 3D object position. This just happens to work good enough for demonstration.
                var delta = Input.mousePosition - lastPosition;
                focusedModel.transform.position += delta * Time.deltaTime;
            }
        }
        else
        {
            Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
            var ray = Camera.main.ScreenPointToRay(position);

            Raycast(ray.origin, ray.direction);

            if (Input.GetMouseButtonDown(1) && focusedModel != null)
            {
                focusedModel.ToggleIsolate();
            }
        }

        lastPosition = Input.mousePosition;
    }
```

> [!IMPORTANT]
> Se você executar esse código, perceberá que nada acontece. Isso ocorre porque a alteração da transformação de um objeto não sincroniza automaticamente a alteração de estado com o servidor, por motivos de desempenho. Em vez disso, você precisa enviar por push essa alteração de estado para o servidor manualmente ou habilitar **SyncEveryFrame** no componente *RemoteEntitySyncObject*.

Abra o script **RemoteModelEntity** e adicione esta linha:

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

Ao executar o código novamente, você deve ser capaz de clicar com o botão esquerdo do mouse em um objeto e arrastá-lo.

## <a name="add-a-cut-plane"></a>Adicionar um plano de corte

O recurso final que queremos experimentar neste tutorial é usar [planos de corte](../../overview/features/cut-planes.md). Um plano de corte corta blocos de objetos renderizados para que você possa olhar dentro deles.

Crie um GameObject na cena **CutPlane**. Crie um script e chame-o de **RemoteCutPlane**. Adicione o componente ao novo GameObject.

Abra o arquivo de script e substitua o conteúdo pelo seguinte código:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteCutPlane : MonoBehaviour
{
    private ARRCutPlaneComponent localCutPlaneComponent = null;
    private RemoteEntitySyncObject remoteEntitySync = null;

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            // can't do anything while we are not connected
            return;
        }

        if (localCutPlaneComponent == null)
        {
            localCutPlaneComponent = gameObject.CreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
        }

        if (remoteEntitySync == null)
        {
            remoteEntitySync = gameObject.GetComponent<RemoteEntitySyncObject>();
            remoteEntitySync.SyncEveryFrame = true;
        }

        localCutPlaneComponent.RemoteComponent.Normal = Axis.X;
        localCutPlaneComponent.RemoteComponent.FadeLength = 0.025f;
        localCutPlaneComponent.RemoteComponent.FadeColor = new Color4Ub(255, 128, 0, 255);
        localCutPlaneComponent.RemoteComponent.Enabled = true;
    }

    void OnDisable()
    {
        if (localCutPlaneComponent && localCutPlaneComponent.IsComponentValid)
        {
            localCutPlaneComponent.RemoteComponent.Enabled = false;
        }

        if (remoteEntitySync && remoteEntitySync.IsEntityValid)
        {
            remoteEntitySync.SyncEveryFrame = false;
        }
    }
}
```

Quando executar seu código agora, você deverá ver como o modelo é aberto pelo plano. Você pode selecionar o objeto *CutPlane* e movê-lo e girá-lo na janela *Cena*. É possível ativar e desativar o plano de corte desabilitando o objeto do plano de corte.

## <a name="next-steps"></a>Próximas etapas

Agora você conhece a funcionalidade mais importante para interagir com objetos remotos. No próximo tutorial, veremos como personalizar a aparência de uma cena.

> [!div class="nextstepaction"]
> [Tutorial: Alteração do ambiente e dos materiais](changing-environment-and-materials.md)
