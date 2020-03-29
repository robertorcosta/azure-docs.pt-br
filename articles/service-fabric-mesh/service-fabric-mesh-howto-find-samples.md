---
title: Encontre amostras de malha de malha de malha de malha de malha de serviço do Azure
description: Aqui está um índice de aplicações de amostra de malha de malha de malha de malha de malha de serviço disponíveis. O código-fonte nesses exemplos mostra como obter um cenário específico usando o modelo de recursos do Service Fabric.
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: c944ddc26f2e2d099cf9552acb8287c363d0c768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461914"
---
# <a name="find-service-fabric-mesh-samples"></a>Localizar Exemplos da Malha do Service Fabric

Essa tabela descreve os aplicativos de exemplo da malha do Service Fabric disponíveis. O código-fonte nesses exemplos mostra como obter um cenário específico usando o modelo de recursos do Service Fabric.

Para obter mais informações sobre como implantar modelos diretamente no Azure, confira a [Página de modelo de exemplo do GitHub.](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)

|Modelo de Exemplo|Descrição do cenário|Código-fonte|Ferramentas de Desenvolvedor|
|------------|--------------------|----------|----------------------|
| [Aplicativo Olá, Mundo](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Página da Web estática hospedada em um contêiner. Para Linux, ele usa nginx, para o Windows, IIS | [Código Fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Não há requisitos |
| [Aplicativo de contador para Volumes de arquivos do Azure](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Armazene o estado ao montar o volume baseado em arquivos do Azure dentro do contêiner. <br><br> **Observação:** esse modelo requer que um compartilhamento dos Arquivos do Azure já seja provisionado [Instruções](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) | [Código Fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Ferramentas da Malha do Visual Studio |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Crie um aplicativo com um serviço de front-end e back-end que use a resolução baseada em DNS. Usado como um tutorial [aqui](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore) | [Código Fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Ferramentas da Malha do Visual Studio |
| [Aplicativo de Objetos Visuais](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Microsserviços de dimensionamento e atualização dentro de um aplicativo. | [Código Fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Ferramentas da Malha do Visual Studio |
| [Aplicativo de votação](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Criar um aplicativo com um serviço de front-end e back-end que use a resolução baseada em DNS | [Código Fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Visual Studio Mesh Tooling para a versão do Windows, VS Code / dotnet CLI pode ser usado para a versão Linux |
| [Aplicativo de Contador para Volumes Confiáveis do Service Fabric](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Armazene o estado ao montar o volume baseado em disco confiável do Service Fabric dentro do contêiner.| [Código Fonte](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Ferramentas da Malha do Visual Studio |
