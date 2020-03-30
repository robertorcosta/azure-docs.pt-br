---
title: Gerenciar aplicativos para vários ambientes
description: Os aplicativos do Azure Service Fabric podem ser executados em clusters que variam de tamanho de um computador para milhares de computadores. Em alguns casos, você desejará configurar seu aplicativo de forma diferente para esses ambientes variados. Este artigo aborda como definir parâmetros de aplicativo diferentes por ambiente.
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 0bca690fd585b288f15cbab21c1c951474390318
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196972"
---
# <a name="manage-applications-for-multiple-environments"></a>Gerenciar aplicativos para vários ambientes

Os clusters do Azure Service Fabric permitem criar clusters usando de um a milhares de computadores. Na maioria dos casos, você precisará implantar seu aplicativo em configurações de vários clusters: o cluster de desenvolvimento local, um cluster de desenvolvimento compartilhado e o cluster de produção. Todos esses clusters são considerados ambientes diferentes em que seu código deve ser executado. Os binários de aplicativo podem ser executados sem modificação em um amplo espectro, mas, com frequência, você desejará configurar o aplicativo de forma diferente.

Considere dois exemplos simples:
  - seu serviço escuta em uma porta definida, mas você precisa que essa porta seja diferente entre os ambientes
  - você precisa fornecer credenciais de associação diferentes para um banco de dados entre os ambientes

## <a name="specifying-configuration"></a>Especificando a configuração

A configuração que você fornece pode ser dividida em duas categorias:

- Configuração que se aplica a como os serviços são executados
  - Por exemplo, o número da porta para um ponto de extremidade ou o número de instâncias de um serviço
  - Essa configuração é especificada no arquivo de manifesto do serviço ou aplicativo
- Configuração que se aplica ao código do aplicativo
  - Por exemplo, as informações de associação para um banco de dados
  - Essa configuração pode ser fornecida por meio de arquivos de configuração ou variáveis de ambiente

> [!NOTE]
> Nem todos os atributos no arquivo de manifesto do serviço e aplicativo dão suporte a parâmetros.
> Nesses casos, você precisa substituir cadeias de caracteres como parte do fluxo de trabalho de implantação. No Azure DevOps, é possível usar uma extensão como Substituir Tokens: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens ou no Jenkins seria possível executar uma tarefa de script para substituir os valores.
>

## <a name="specifying-parameters-during-application-creation"></a>Especificando parâmetros durante a criação de aplicativos

Ao criar instâncias de um aplicativo nomeado no Service Fabric, você tem a opção de passar parâmetros. A maneira de fazer isso depende de como você cria a instância do aplicativo.

  - No PowerShell, [`New-ServiceFabricApplication`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) o cmdlet toma os parâmetros da aplicação como um hashtable.
  - Usando sfctl, [`sfctl application create`](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) o comando toma parâmetros como uma seqüência JSON. O script install.sh usa sfctl.
  - O Visual Studio fornece um conjunto de arquivos de parâmetro na pasta Parâmetros no projeto do aplicativo. Esses arquivos de parâmetros são usados ao publicar no Visual Studio, usando o Azure DevOps Services ou o Azure DevOps Server. No Visual Studio, os arquivos de parâmetro são passados para o script Deploy-FabricApplication.ps1.

## <a name="next-steps"></a>Próximas etapas
Os artigos a seguir mostram como usar alguns dos conceitos descritos aqui:

- [Como especificar variáveis de ambiente para serviços no Service Fabric](service-fabric-how-to-specify-environment-variables.md)
- [Como especificar o número da porta de um serviço usando parâmetros no Service Fabric](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Como parametrizar os arquivos de configuração](service-fabric-how-to-parameterize-configuration-files.md)

- [Referência da variável de ambiente](service-fabric-environment-variables-reference.md)
