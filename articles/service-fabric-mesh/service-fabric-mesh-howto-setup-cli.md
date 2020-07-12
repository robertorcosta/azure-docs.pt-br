---
title: Configurar a CLI da malha de Service Fabric do Azure
description: A Interface de linha de comando (CLI) da Malha do Service Fabric é necessária para implantar e gerenciar recursos localmente e na Malha do Azure Service Fabric. Veja como configurá-lo.
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 3ce2dbcefa63cc55a66e712664d1436ca24d3a1d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86248485"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Configurar CLI da Malha do Service Fabric
A Interface de linha de comando (CLI) da Malha do Service Fabric é necessária para implantar e gerenciar recursos localmente e na Malha do Azure Service Fabric. Veja como configurá-lo.

Há três tipos de CLI que podem ser usados e eles estão resumidos na tabela a seguir.

| Módulo de CLI | Ambiente de destino |  Descrição | 
|---|---|---|
| malha do az | Malha do Microsoft Azure Service Fabric | A CLI principal que permite implantar seus aplicativos e gerenciar recursos no ambiente da Malha do Azure Service Fabric. 
| sfctl | Clusters locais | CLI do Service Fabric que permite a implantação e o teste de recursos do Service Fabric em clusters locais.  
| CLI do Maven | Clusters locais e a Malha do Azure Service Fabric | Um wrapper `az mesh` e `sfctl` que permite aos desenvolvedores de Java usar uma experiência de linha de comando familiar para a experiência de desenvolvimento local e do Azure.  

Para a versão prévia, a CLI da Malha do Azure Service Fabric é gravada como uma extensão da CLI do Azure. Você pode instalá-lo no Azure Cloud Shell ou uma instalação local da CLI do Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Instalar a CLI da Malha do Azure Service Fabric
1. Você deve instalar o CLI do Azure versão 2.0.67 ou posterior. Execute `az --version` para encontrar a versão. Para instalar ou atualizar para a versão mais recente da CLI, confira [Instalar a CLI do Azure][azure-cli-install].

2. Instale o módulo de extensão de CLI da Malha do Azure Service Fabric usando o comando a seguir. 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. Atualize um módulo da CLI da Malha do Azure Service Fabric existente usando o comando a seguir.

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>Instalar a CLI do Service Fabric (sfctl) 

Siga as instruções em [Configurar a CLI do Service Fabric](../service-fabric/service-fabric-cli.md). O módulo **sfctl** pode ser usado na implantação de aplicativos baseados no modelo de recursos em clusters do Service Fabric em seu computador local. 

## <a name="install-the-maven-cli"></a>Instalar a CLI do Maven 

Para usar a CLI do Maven, as seguintes condições devem ser instalados em seu computador: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* A CLI da Malha do Azure (az mesh) - Para direcionar a Malha do Azure Service Fabric 
* SFCTL (sfctl) - Para direcionar clusters locais 

A CLI do Maven do Service Fabric ainda está em versão prévia. 

Para usar o plugin do Maven em seu aplicativo Maven Java, adicione o seguinte snippet ao seu arquivo pom.xml:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Leia a seção [referência da CLI do Maven](service-fabric-mesh-reference-maven.md) para saber mais sobre o uso detalhado.

## <a name="next-steps"></a>Próximas etapas

Você também pode configurar o seu [ambiente de desenvolvimento do Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Encontre respostas para [dúvidas e problemas comuns](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
