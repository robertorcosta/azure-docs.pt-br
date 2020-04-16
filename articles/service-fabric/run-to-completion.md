---
title: Semântica RunToComplet em Malha de Serviço
description: Descreve a semântica RunToComplet em Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: adf4b11412aa752144d4ed4fef06d2de1d76598d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431287"
---
# <a name="runtocompletion"></a>ExecuçãoCompletu

A partir da versão 7.1, o Service Fabric suporta semântica **RunToComplet** para [contêineres][containers-introduction-link] e aplicativos [executáveis de hóspedes.][guest-executables-introduction-link] Essas semânticas permitem aplicativos e serviços que completam uma tarefa e saem, ao contrário, sempre executando aplicativos e serviços.

Antes de prosseguir com este artigo, recomendamos familiarizar-se com o [modelo de aplicação service fabric][application-model-link] e o modelo de [hospedagem Service Fabric][hosting-model-link].

> [!NOTE]
> A semântica RunToComplet atualmente não é suportada para serviços escritos usando o modelo de programação [de Serviços Confiáveis.][reliable-services-link]
 
## <a name="runtocompletion-semantics-and-specification"></a>RunToComplet semântica e especificação
A semântica RunToComplet pode ser especificada como uma Diretiva de **Execução** [ao importar o ServiceManifest][application-and-service-manifests-link]. A diretiva especificada é herdada por todos os CodePackages que compõem o ServiceManifest. O seguinte trecho ApplicationManifest.xml fornece um exemplo.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy** permite os dois atributos a seguir:
* **Tipo:** **RunToComplet** é atualmente o único valor permitido para este atributo.
* **Reiniciar:** Este atributo especifica a política de reinicialização aplicada aos CodePackages que compõem o ServicePackage, em falha. Considera-se que um CodePackage saindo com um **código de saída não-zero** falhou. Os valores permitidos para este atributo são **OnFailure** e **Never** with **OnFailure** sendo o padrão.

Com a diretiva de reinicialização definida como **OnFailure**, se algum CodePackage falhar **(código de saída não zero)**, ele é reiniciado, com back-offs entre falhas repetidas. Com a diretiva de reinicialização definida como **Never**, se algum CodePackage falhar, o status de implantação do DeployedServicePackage será marcado como **Falha,** mas outros CodePackages podem continuar a execução. Se todos os Pacotes de Código que compõem o ServicePackage forem executados com sucesso de conclusão (código de **saída 0),** o status de implantação do DeployedServicePackage será marcado como **RanToComplet**. 

## <a name="complete-example-using-runtocompletion-semantics"></a>Exemplo completo usando a semântica RunToComplet

Vamos olhar para um exemplo completo usando a semântica RunToComplet.

> [!IMPORTANT]
> O exemplo a seguir assume a familiaridade com a criação de [aplicativos de contêiner do Windows usando O Fabric de Serviço e o Docker][containers-getting-started-link].
>
> Este exemplo faz referência mcr.microsoft.com/windows/nanoserver:1809. Os contêineres do Windows Server não são compatíveis em todas as versões de um sistema operacional do host. Para obter mais informações, consulte [Compatibilidade de versão de contêiner do Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

O serviceManifest.xml a seguir descreve um ServicePackage composto por dois CodePackages, que representam contêineres. *RunToCompletionCodePackage1* apenas registra uma mensagem para **stdout** e sai. *RunToCompletionCodePackage2* pinga o endereço de loopback por um tempo e, em seguida, sai com um código de saída de **0,** **1** ou **2**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

O aplicativo manifesto.xml a seguir descreve um aplicativo baseado no ServiceManifest.xml discutido acima. Ele especifica **RunToCompletion** **ExecutionPolicy** for *WindowsRunToCompletionServicePackage* com uma diretiva de reinicialização de **OnFailure**. Após a ativação do *WindowsRunToCompletionServicePackage,* seus CodePackages constituintes serão iniciados. *RunToCompletionCodePackage1* deve sair com sucesso na primeira ativação. No entanto, *RunToCompletionCodePackage2* pode falhar **(código de saída não zero)**, nesse caso ele será reiniciado uma vez que a diretiva de reinicialização é **OnFailure**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>Consultando o status de implantação de um DeployedServicePackage
O status de implantação de um DeployedServicePackage pode ser consultado pelo PowerShell usando [Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] ou de C# usando [FabricClient][fabric-client-link] API [GetDeployedServicePackageListAsync (String, Uri, String)][deployed-service-package-fabricclient-link]

## <a name="considerations-when-using-runtocompletion-semantics"></a>Considerações ao usar a semântica RunToComplet

Os seguintes pontos devem ser observados para o suporte atual do RunToComplet.
* Essas semânticas são suportadas apenas para [contêineres][containers-introduction-link] e aplicativos [executáveis de hóspedes.][guest-executables-introduction-link]
* Não são permitidos cenários de upgrade para aplicativos com semântica RunToComplet. Os usuários devem excluir e recriar tais aplicativos, se necessário.
* Eventos failover podem fazer com que os CodePackages sejam reexecutados após a conclusão bem-sucedida, no mesmo nó ou em outros nós do cluster. Exemplos de eventos de failover são, reinicializações de nó e upgrades de tempo de execução do Service Fabric em um nó.

## <a name="next-steps"></a>Próximas etapas

Veja os artigos a seguir para obter informações relacionadas.

* [Malha de serviço e contêineres.][containers-introduction-link]
* [Fabric de serviço e executáveis convidados.][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[fabric-client-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync

