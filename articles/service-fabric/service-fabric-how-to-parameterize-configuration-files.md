---
title: Parametrizar arquivos de configuração no tecido de serviço do Azure
description: Aprenda a parametrizar arquivos de configuração no Service Fabric, uma técnica útil ao gerenciar vários ambientes.
author: mikkelhegn
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: 4e96a732cffd70b0a5c24e7ebafe214297a72720
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644606"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Como parametrizar os arquivos de configuração no Service Fabric

Este artigo mostra como parametrizar os arquivos de configuração no Service Fabric.  Caso ainda não esteja familiarizado com os conceitos básicos de gerenciamento de aplicativos para vários ambientes, leia [Gerenciar aplicativos para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedimento para parametrizar arquivos de configuração

Neste exemplo, você deve substituir um valor de configuração usando os parâmetros na implantação do aplicativo.

1. Abra * \<o arquivo MyService>\PackageRoot\Config\Settings.xml* no seu projeto de serviço.
1. Defina um nome de parâmetro de configuração e valor, por exemplo, tamanho do cache igual a 25, ao adicionar o seguinte XML:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Salve e feche o arquivo.
1. Abra * \<o arquivo MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml.*
1. No arquivo ApplicationManifest.xml, declare um parâmetro e um valor padrão no elemento `Parameters`.  É recomendado que o nome do parâmetro contenha o nome do serviço (por exemplo, "MyService").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. Na `ServiceManifestImport` seção do arquivo ApplicationManifest.xml, adicione um `ConfigOverrides` elemento, `ConfigOverride` fazendo referência ao pacote de configuração, à seção e ao parâmetro.

   ```xml
    <ConfigOverrides>
      <ConfigOverride Name="Config">
          <Settings>
            <Section Name="MyConfigSection">
                <Parameter Name="CacheSize" Value="[MyService_CacheSize]" />
            </Section>
          </Settings>
      </ConfigOverride>
    </ConfigOverrides>
   ```

> [!NOTE]
> No caso em que você adicionar um ConfigOverride, o Service Fabric sempre escolherá os parâmetros do aplicativo ou o valor padrão especificado no manifesto do aplicativo.
>
>

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre outras funcionalidades de gerenciamento de aplicativo disponíveis no Visual Studio, confira [Gerenciar seus aplicativos do Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).
