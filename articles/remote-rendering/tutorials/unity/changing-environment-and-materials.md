---
title: Alteração do ambiente e dos materiais
description: Tutorial que mostra como modificar o mapa do céu e os materiais do objeto em uma cena do Unity.
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678677"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>Tutorial: Alteração do ambiente e dos materiais

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Alterar o mapa de ambiente de uma cena.
> * Modificar os parâmetros de material.
> * Carregar texturas personalizadas.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial presume que você está familiarizado com o [Tutorial: Trabalho com entidades remotas no Unity](working-with-remote-entities.md). No entanto, você só precisa de um projeto do Unity com o qual possa se conectar a sessões e carregar um modelo, conforme mostrado no [Tutorial: Configurar um projeto do Unity do zero](project-setup.md).

> [!TIP]
> O [repositório de exemplos do ARR](https://github.com/Azure/azure-remote-rendering) contém projetos do Unity preparados para todos os tutoriais na pasta *Unity*, que você pode usar como referência.

## <a name="change-the-environment-map"></a>Alterar o mapa do ambiente

O Azure Remote Rendering dá suporte ao uso de [caixas de céu](../../overview/features/sky.md) (também chamadas, às vezes, de “mapas de ambiente”) para simular a iluminação ambiente. Isso é especialmente útil quando seus objetos usam a *[renderização de base física](../../overview/features/pbr-materials.md)* , como fazem nossos modelos de amostra. O ARR também vem com uma variedade de texturas de céu internas, que usaremos neste tutorial.

Crie um script chamado **RemoteSky** e adicione-o a um novo GameObject. Abra o arquivo de script e substitua-o pelo seguinte código:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteSky : MonoBehaviour
{
    private int skyIndex = 0;

    private LoadTextureFromSASParams[] builtIns =
    {
        new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)
    };

    public async void ToggleSky()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        skyIndex = (skyIndex + 1) % builtIns.Length;

        var texture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(builtIns[skyIndex]).AsTask();

        Debug.Log($"Switching sky to: {builtIns[skyIndex].TextureId}");

        var settings = RemoteManagerUnity.CurrentSession.Actions.SkyReflectionSettings;

        settings.SkyReflectionTexture = texture;
    }

    private void OnGUI()
    {
        if (RemoteManagerUnity.IsConnected)
        {
            if (GUI.Button(new Rect(10, Screen.height - 50, 175, 30), "Toggle Sky"))
            {
                ToggleSky();
            }
        }
    }
}
```

Observe que a variante `LoadTextureFromSASAsync` é usada acima porque texturas internas são carregadas. No caso de carregamento de [armazenamentos de blob vinculados](../../how-tos/create-an-account.md#link-storage-accounts), use a variante `LoadTextureAsync`. Um exemplo de como isso funciona para modelos pode ser encontrado na [seção de carregamento de modelos](../../concepts/models.md#loading-models).

Ao executar o código e alternar entre os mapas de céu, você perceberá uma iluminação drasticamente diferente em seu modelo. No entanto, a tela de fundo ficará preta e você não poderá ver a textura do céu real. Isso é intencional, pois a renderização de uma tela de fundo seria confusa com um dispositivo de realidade aumentada. Em um aplicativo adequado, você deve usar texturas de céu que sejam semelhantes aos seus arredores do mundo real, pois isso ajudará a dar uma aparência mais real aos objetos.

## <a name="modify-materials"></a>Modificar materiais

No tutorial anterior, usamos [componentes de substituição de estado](../../overview/features/override-hierarchical-state.md) para alterar a cor da tonalidade dos objetos selecionados. Agora, queremos obter um efeito semelhante, mas fazer isso modificando o [material](../../concepts/materials.md) de um objeto.

Primeiro, precisamos de um script para escolher objetos, como fizemos no [segundo tutorial](working-with-remote-entities.md). Se você ainda não tiver um script **RemoteRaycaster**, crie-o agora. Substitua o conteúdo dele pelo código a seguir:

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
}
```

Adicione o componente ao seu objeto de jogo *RemoteRendering*. Ele é responsável por escolher objetos sob o mouse e adicionar componentes de *RemoteModelEntity* aos objetos selecionados. Essa é a classe de componente na qual implementamos a funcionalidade real de alteração de material.

Se você ainda não tiver um script **RemoteModelEntity**, crie-o e substitua seu conteúdo por este código:

```csharp
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

// to prevent namespace conflicts
using ARRTexture = Microsoft.Azure.RemoteRendering.Texture;

public class RemoteModelEntity : MonoBehaviour
{
    private Color HighlightColor = new Color(1.0f, 0.4f, 0.1f, 1.0f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;
    private RemoteEntitySyncObject localSyncObject = null;
    private ARRMeshComponent meshComponent = null;
    private Color4 originalColor;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();

        meshComponent = GetComponent<ARRMeshComponent>();
        if (meshComponent == null)
        {
            var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
            if (mesh != null)
            {
                gameObject.BindArrComponent<ARRMeshComponent>(mesh);
                meshComponent = gameObject.GetComponent<ARRMeshComponent>();
            }
        }
        meshComponent.enabled = true;
    }

    public void SetFocus(bool on)
    {
        SetMaterialColor(on);
    }

    private void SetMaterialColor(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        // get the list of materials from the picked mesh
        // we only modify the first material; depending on how the model is authored, this may
        // work exactly as desired, or not ;-)
        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        // there are different types of materials, cast as necessary
        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
    }
}
```

Quando você executa esse código, os objetos sobre os quais passa o mouse são realçados. O efeito é semelhante ao que fizemos no tutorial 2, mas a maneira como é alcançado é diferente. Aqui, obtemos a lista de materiais no objeto selecionado e, em seguida, modificamos o primeiro para ter uma cor de albedo diferente.

> [!IMPORTANT]
> Lembre-se de que o realce dos blocos corretos de um modelo depende de como um modelo é criado. Ele funcionará perfeitamente se cada objeto usar exatamente um material. No entanto, se o modelo não tiver uma relação 1:1 entre blocos e materiais, o código ingênuo acima não fará a coisa certa.

## <a name="use-a-different-texture"></a>Usar uma textura diferente

As [texturas](../../concepts/textures.md) geralmente fazem parte de um modelo de origem. Durante a [conversão do modelo](../../quickstarts/convert-model.md), todas as texturas são convertidas para o formato de runtime necessário e empacotadas no arquivo de modelo final. Para substituir uma textura em runtime, você precisa salvá-la em [formato de arquivo DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface) e carregá-la no Armazenamento de Blobs do Azure. Confira [este guia de início rápido](../../quickstarts/convert-model.md) para saber como criar um contêiner de blob do Azure. Quando tiver um contêiner de blob, você poderá abri-lo no Gerenciador de Armazenamento do Microsoft Azure e carregar o arquivo por meio de arrastar e soltar.

No lado do runtime, você pode lidar com um ativo de textura no Armazenamento de Blobs de duas maneiras distintas:

* Lidar com a textura com o URI do SAS. Para isso, clique com o botão direito do mouse no arquivo carregado e selecione “**Obter Assinatura de Acesso Compartilhado...** ” no menu de contexto. Use esse URI do SAS com a variável de função `LoadTextureFromSASAsync` (consulte o código de exemplo abaixo).
* Lidar com a textura diretamente pelos parâmetros de Armazenamento de Blobs, caso o [Armazenamento de Blobs esteja vinculado à conta](../../how-tos/create-an-account.md#link-storage-accounts). Nesse caso, a função de carregamento relevante é `LoadTextureAsync`.

Agora, abra o script **RemoteModelEntity**, adicione o seguinte código e remova as funções duplicadas:

```csharp
    private string textureFile = ""; //<SAS URI for your texture>
    private ARRTexture originalTexture = null;

    private async void SetMaterialTexture(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        ARRTexture newTexture = originalTexture;

        if (on)
        {
            var textureParams = new LoadTextureFromSASParams(textureFile, TextureType.Texture2D);

            newTexture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(textureParams).AsTask();
        }

        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
    }

    public void SetFocus(bool on)
    {
        if (string.IsNullOrEmpty(textureFile))
        {
            SetMaterialColor(on);
        }
        else
        {
            SetMaterialTexture(on);
        }
    }
```

Execute este código e passe o mouse sobre seu modelo. Se o modelo tiver coordenadas de UV adequadas, você deverá ver sua textura aparecer. Caso contrário, poderá observar apenas uma alteração de cor.

## <a name="next-steps"></a>Próximas etapas

Isso conclui nossa série de introdução sobre como usar o Azure Remote Rendering com o Unity. Na próxima etapa, você deve se familiarizar com alguns conceitos fundamentais do ARR, como [sessões](../../concepts/sessions.md), [entidades](../../concepts/entities.md) e [modelos](../../concepts/models.md) para obter uma compreensão mais profunda. Também há vários recursos, como [luzes](../../overview/features/lights.md), [renderização de estrutura de tópicos](../../overview/features/outlines.md), [substituições de estado hierárquico](../../overview/features/override-hierarchical-state.md) e [materiais](../../concepts/materials.md), que você deve explorar mais detalhadamente.

> [!div class="nextstepaction"]
> [Componentes e objetos de jogos do Unity](../../how-tos/unity/objects-components.md)
