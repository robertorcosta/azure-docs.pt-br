---
title: Instalar âncoras espaciais do Azure para o Unity
description: Configurar um projeto do Unity para usar âncoras espaciais do Azure
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 08/17/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 57ead9636b7218ecfc7d72bb605b469d6a7d1ac6
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536341"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Configurando âncoras espaciais do Azure em um projeto do Unity

Este guia mostrará como começar a usar o SDK de âncoras espaciais do Azure em seu projeto do Unity.

## <a name="requirements"></a>Requisitos

As âncoras espaciais do Azure atualmente dão suporte ao Unity 2019,4 (LTS) com as seguintes configurações.

* O Unity 2019,4 com o AR Foundation 3,1 tem suporte nas âncoras espaciais do Azure 2.4.0 +.

## <a name="configuring-a-project"></a>Configurando um projeto

### <a name="add-the-unity-package-manager-packages-to-your-project"></a>[Adicionar pacotes do Gerenciador de pacotes do Unity ao seu projeto](#tab/UPMPackage)

As âncoras espaciais do Azure para o Unity atualmente são distribuídas usando pacotes UPM (Gerenciador de pacotes do Unity). Esses pacotes podem ser encontrados em nosso [registro NPM](https://bintray.com/microsoft/AzureMixedReality-NPM). Para saber mais sobre como trabalhar com registros de pacote com escopo em um projeto do Unity, consulte a documentação oficial do Unity [aqui](https://docs.unity3d.com/Manual/upm-scoped.html).

#### <a name="add-the-registry-to-your-unity-project"></a>Adicionar o registro ao seu projeto do Unity

1. Em um explorador de arquivos, navegue até a pasta do seu projeto do Unity `Packages` . Abra o arquivo de manifesto do projeto, `manifest.json` , em um editor de texto.
2. Na parte superior do arquivo, no mesmo nível que a `dependencies` seção, adicione a seguinte entrada para incluir o registro de âncoras espaciais do Azure em seu projeto. A `scopedRegistries` entrada informa ao Unity onde procurar os pacotes do SDK de âncoras espaciais do Azure.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-19&highlight=2-10)]

#### <a name="add-the-sdk-packages-to-your-unity-project"></a>Adicionar os pacotes do SDK ao seu projeto do Unity

| Plataforma | Nome do pacote                                    |
|----------|-------------------------------------------------|
| Android  | com. Microsoft. Azure. Spatial-ancoras-SDK. Android |
| iOS      | com. Microsoft. Azure. Spatial-Anchors-SDK. Ios     |
| HoloLens | com. Microsoft. Azure. Spatial-ancoras-SDK. Windows |

1. Para cada plataforma (Android/iOS/HoloLens) para a qual você gostaria de dar suporte em seu projeto, adicione uma entrada com o nome do pacote e a versão do pacote à `dependencies` seção no manifesto do projeto. Veja um exemplo abaixo.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-22&highlight=12-14)]

2. Salve e feche o arquivo `manifest.json`. Quando você retorna ao Unity, o Unity deve detectar automaticamente a alteração do manifesto do projeto e recuperar os pacotes especificados. Você pode expandir a `Packages` pasta no modo de exibição de projeto para verificar se os pacotes corretos foram importados.

#### <a name="android-only-configure-the-maintemplategradle-file"></a>Somente Android: Configure o arquivo MainTemplate. gradle

1. Vá para **Editar** > **Configurações do Projeto** > **Player**.
2. No **painel Inspetor** para **configurações do Player**, selecione o ícone do **Android** .
3. Na seção **Compilar** , marque a caixa de seleção **modelo de gradle principal personalizado** para gerar um modelo de gradle personalizado em `Assets\Plugins\Android\mainTemplate.gradle` .
4. Abra o arquivo `mainTemplate.gradle` em um editor de texto. 
5. Na `dependencies` seção, Cole as seguintes dependências:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Quando tudo estiver concluído, sua `dependencies` seção deverá ter uma aparência semelhante a esta:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

### <a name="import-the-asset-package"></a>[Importar o pacote de ativos](#tab/UnityAssetPackage)

> [!WARNING]
> A distribuição do pacote de ativos do Unity do SDK de âncoras espaciais do Azure será preterida após a versão 2.5.0 do SDK.

1. Baixe o `AzureSpatialAnchors.unitypackage` arquivo da versão que você deseja direcionar das [versões do GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases). 
2. Siga as instruções [aqui](https://docs.unity3d.com/Manual/AssetPackagesImport.html) para importar o pacote de ativos do Unity para seu projeto.    

---

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: criar e localizar âncoras no Unity](./create-locate-anchors-unity.md)
