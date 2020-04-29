---
title: Instalar o pacote do Remote Rendering o para o Unity
description: Explica como instalar as DLLs de cliente de renderização remota para o Unity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681175"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Instalar o pacote do Remote Rendering o para o Unity

A renderização remota do Azure usa um pacote do Unity para encapsular a integração no Unity.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Gerenciar os pacotes de renderização remota no Unity

Os pacotes do Unity são contêineres que podem ser gerenciados por meio do [Gerenciador de pacotes](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)do Unity.
Esse pacote contém a API C# inteira, bem como todos os binários de plug-in necessários para usar a renderização remota do Azure com o Unity.
O esquema de nomenclatura do Unity a seguir para pacotes, o pacote é chamado de **com. Microsoft. Azure. Remote-renderizando**.

O pacote não faz parte do [repositório de exemplos de arr](https://github.com/Azure/azure-remote-rendering)e não está disponível no registro de pacote interno do Unity. Para adicioná-lo a um projeto, você precisa editar manualmente o arquivo do `manifest.md` projeto para adicionar o seguinte:
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
    ...existing dependencies...
  }
}
```
Depois que isso tiver sido adicionado, você poderá usar o Gerenciador de pacotes do Unity para garantir que tem a versão mais recente.
Instruções mais abrangentes são fornecidas no [tutorial: Configurando um projeto do Unity do zero](../../tutorials/unity/project-setup.md).

## <a name="unity-render-pipelines"></a>Pipelines de renderização do Unity

A renderização remota funciona com o **pipeline de renderização universal** e o **pipeline de renderização padrão**. Por motivos de desempenho, o pipeline de processamento universal é recomendado.

Para usar o **pipeline de processamento universal**, seu pacote deve ser instalado no Unity. Isso pode ser feito na interface do usuário **do Gerenciador de pacotes** do Unity (nome do pacote **Universal RP**, versão 7.2.1 ou mais recente `Packages/manifest.json` ) ou por meio do arquivo, conforme descrito no [tutorial de configuração do projeto do Unity](../../tutorials/unity/project-setup.md#configure-the-projects-manifest).

## <a name="next-steps"></a>Próximas etapas

* [Componentes e objetos de jogos do Unity](objects-components.md)
* [Tutorial: Configuração de um projeto do Unity do zero](../../tutorials/unity/project-setup.md)
