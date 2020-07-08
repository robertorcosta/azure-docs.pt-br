---
title: Configurar gMSA para os serviços de contêiner de Service Fabric do Azure
description: Aprenda agora a configurar contas de serviço gerenciado de grupo (gMSA) para um contêiner em execução no Azure Service Fabric.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75639200"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Configure o gMSA para contêineres do Windows em execução no Service Fabric

Para configurar o gMSA (Contas de Serviço Gerenciado de grupo), um arquivo de especificação de credencial (`credspec`) é colocado em todos os nós no cluster. O arquivo pode ser copiado em todos os nós usando uma extensão de VM.  O arquivo `credspec` deve conter as informações da conta gMSA. Para obter mais informações sobre o `credspec` arquivo, consulte [criar uma especificação de credencial](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). A especificação de credencial e a `Hostname` marca são especificadas no manifesto do aplicativo. A marca `Hostname` deve corresponder ao nome de conta de gMSA em que o contêiner é executado.  A marca `Hostname` permite que o contêiner para se autenticar em outros serviços no domínio usando a autenticação Kerberos.  Um exemplo para especificar o `Hostname` e o `credspec` no manifesto do aplicativo é mostrado no snippet a seguir:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Como uma próxima etapa, leia os seguintes artigos:

* [Implantar um contêiner do Windows no Service Fabric no Windows Server 2016](service-fabric-get-started-containers.md)
* [Implantar um contêiner do Docker no Service Fabric no Linux](service-fabric-get-started-containers-linux.md)
