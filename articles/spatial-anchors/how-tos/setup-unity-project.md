---
title: Instalar âncoras espaciais do Azure para o Unity
description: Configurar um projeto do Unity para usar âncoras espaciais do Azure
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2330310b0bf4e165af71208477db128650a787cf
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95496929"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Configurando âncoras espaciais do Azure em um projeto do Unity

Este guia mostrará como começar a usar o SDK de âncoras espaciais do Azure em seu projeto do Unity.

## <a name="requirements"></a>Requisitos

As âncoras espaciais do Azure atualmente dão suporte ao Unity 2019,4 (LTS) com as seguintes configurações.

* O Unity 2019,4 com o AR Foundation 3,1 tem suporte nas âncoras espaciais do Azure 2.4.0 +.

## <a name="configuring-a-project"></a>Configurando um projeto

### <a name="add-the-unity-package-manager-packages-to-your-project"></a>[Adicionar pacotes do Gerenciador de pacotes do Unity ao seu projeto](#tab/UPMPackage)

As âncoras espaciais do Azure para o Unity atualmente são distribuídas usando pacotes UPM (Gerenciador de pacotes do Unity). Esses pacotes podem ser encontrados em nosso [registro NPM](https://bintray.com/microsoft/AzureMixedReality-NPM). Para saber mais sobre como trabalhar com registros de pacote com escopo em um projeto do Unity, consulte a documentação oficial do Unity [aqui](https://docs.unity3d.com/Manual/upm-scoped.html).

#### <a name="add-the-registry-to-your-unity-project"></a>Adicionar o registro ao projeto do Unity

1. Em um explorador de arquivos, navegue até a pasta `Packages` do projeto do Unity. Abra o arquivo de manifesto do projeto, `manifest.json`, em um editor de texto.
2. Na parte superior do arquivo, no mesmo nível que a seção `dependencies`, adicione a entrada a seguir para incluir o registro de Âncoras Espaciais do Azure no projeto. A entrada `scopedRegistries` informa ao Unity onde procurar os pacotes do SDK de Âncoras Espaciais do Azure.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-19&highlight=2-10)]

#### <a name="add-the-sdk-packages-to-your-unity-project"></a>Adicionar os pacotes do SDK ao seu projeto do Unity

| Plataforma | Nome do pacote                                    |
|----------|-------------------------------------------------|
| Android  | com. Microsoft. Azure. Spatial-ancoras-SDK. Android |
| iOS      | com. Microsoft. Azure. Spatial-Anchors-SDK. Ios     |
| HoloLens | com. Microsoft. Azure. Spatial-ancoras-SDK. Windows |

1. Para cada plataforma (Android/iOS/HoloLens) para a qual você gostaria de dar suporte em seu projeto, adicione uma entrada com o nome do pacote e a versão do pacote à `dependencies` seção no manifesto do projeto. Veja um exemplo abaixo.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-22&highlight=12-14)]

2. Salve e feche o arquivo `manifest.json`. Quando você retornar ao Unity, ele deverá detectar automaticamente a alteração no manifesto do projeto e recuperar os pacotes especificados. Você pode expandir a pasta `Packages` na exibição do Projeto para verificar se os pacotes corretos foram importados.

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
> A distribuição do pacote de ativos do Unity do SDK de âncoras espaciais do Azure foi preterida na versão 2.5.0 e não está mais disponível a partir de 2.6.0.

1. Baixe o `AzureSpatialAnchors.unitypackage` arquivo da versão que você deseja direcionar das [versões do GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases).
2. Siga as instruções [aqui](https://docs.unity3d.com/Manual/AssetPackagesImport.html) para importar o pacote de ativos do Unity para seu projeto.

---

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: criar e localizar âncoras no Unity](./create-locate-anchors-unity.md)
