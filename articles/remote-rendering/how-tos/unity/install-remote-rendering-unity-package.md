---
title: Instale o pacote de renderização remota para unidade
description: Explica como instalar os DLLs do cliente de renderização remota para unidade
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681175"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Instale o pacote de renderização remota para unidade

A renderização remota do Azure usa um pacote Unity para encapsular a integração no Unity.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Gerenciar os pacotes de renderização remota no Unity

Os pacotes unity são contêineres que podem ser gerenciados através do [Gerenciador de Pacotes](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)da Unity .
Este pacote contém toda a API C#, bem como todos os binários de plugin necessários para usar a renderização remota do Azure com unity.
Seguindo o esquema de nomeação do Unity para pacotes, o pacote é chamado **de renderização remota com.microsoft.azure.remote**.

O pacote não faz parte do [repositório](https://github.com/Azure/azure-remote-rendering)de amostras ARR e não está disponível no registro interno do pacote da Unity. Para adicioná-lo a um projeto, você tem `manifest.md` que editar manualmente o arquivo do projeto para adicionar o seguinte:
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
Uma vez adicionado, você pode usar o Unity Package Manager para garantir que você tenha a versão mais recente.
Instruções mais abrangentes são dadas no [Tutorial: Configuração de um projeto Unity do zero](../../tutorials/unity/project-setup.md).

## <a name="unity-render-pipelines"></a>A unidade render gasodutos

A renderização remota funciona tanto com o **gasoduto de renderização Universal** quanto com o pipeline de **renderização Standard**. Por razões de desempenho, recomenda-se o pipeline de renderização Universal.

Para utilizar o **gasoduto de renderização Universal,** seu pacote deve ser instalado no Unity. Isso pode ser feito na UI do **Unit Package Manager** (nome do pacote Universal **RP,** versão 7.2.1 ou mais recente), ou através do `Packages/manifest.json` arquivo, conforme descrito no tutorial de [configuração](../../tutorials/unity/project-setup.md#configure-the-projects-manifest)do projeto Unity .

## <a name="next-steps"></a>Próximas etapas

* [Objetos e componentes do jogo de unidade](objects-components.md)
* [Tutorial: Configurando um projeto unity do zero](../../tutorials/unity/project-setup.md)
