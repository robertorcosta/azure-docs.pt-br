---
title: Inicializador CodePackages em malha de serviço
description: Descreve pacotes de código inicializador em malha de serviço.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 8483e00f55d0dd49ba57db58b99b237ce0a169e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430624"
---
# <a name="initializer-codepackages"></a>Pacotes de código inicializador

A partir da versão 7.1, o Service Fabric suporta **Initializer CodePackages** para [contêineres][containers-introduction-link] e aplicativos [executáveis de hóspedes.][guest-executables-introduction-link] CodePackages inicializantes fornecem a oportunidade de executar inicializações no escopo ServicePackage antes que outros CodePackages comecem a ser executados. Seu relacionamento com um ServicePackage é análogo ao que é um [SetupEntryPoint][setup-entry-point-link] para um CodePackage.

Antes de prosseguir com este artigo, recomendamos familiarizar-se com o [modelo de aplicação service fabric][application-model-link] e o modelo de [hospedagem Service Fabric][hosting-model-link].

> [!NOTE]
> Os pacotes de código inicializador não são suportados atualmente para serviços escritos usando o modelo de programação [de Serviços Confiáveis.][reliable-services-link]
 
## <a name="semantics"></a>Semântica

Espera-se que um Initializer CodePackage seja executado para **uma conclusão bem sucedida (código de saída 0)**. Um Initializer CodePackage com falha é reiniciado até que ele seja concluído com sucesso. Vários códigos inicializadores são permitidos e executados para **conclusão bem-sucedida,** **seqüencialmente,** **em uma ordem especificada** antes de outros CodePackages no ServicePackage iniciar a execução.

## <a name="specifying-initializer-codepackages"></a>Especificando pacotes de código inicializador
Você pode marcar um CodePackage como um Inicializador definindo o atributo **Initializer** como **verdadeiro** no ServiceManifest. Quando há vários Pacotes de Código inicializador, sua ordem de execução pode ser especificada através do atributo **ExecOrder.** **ExecOrder** deve ser um inteiro não negativo e só é válido para Initializer CodePackages. Códigos inicializantesOs pacotes com valores mais baixos de **ExecOrder** são executados primeiro. Se **execOrder** não for especificado para um Initializer CodePackage, um valor padrão de 0 será assumido. A ordem de execução relativa de Initializer CodePackages com o mesmo valor de **ExecOrder** não é especificada.

O trecho do ServiceManifest a seguir descreve três CodePackages dois dos quais são marcados como Iniciadores. Quando este ServicePackage é ativado, *o InitCodePackage0* é executado primeiro, pois tem o menor valor de **ExecOrder**. Em conclusão bem-sucedida (código de saída 0) do *InitCodePackage0,* *InitCodePackage1* é executado. Finalmente, após a conclusão bem-sucedida do *InitCodePackage1,* *workloadCodePackage* é executado.

```xml
<CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
  ...
</CodePackage>

<CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
  ...
</CodePackage>

<CodePackage Name="WorkloadCodePackage" Version="1.0">
  ...
</CodePackage>
```
## <a name="complete-example-using-initializer-codepackages"></a>Exemplo completo usando pacotes de código inicializador

Vamos olhar para um exemplo completo usando Initializer CodePackages.

> [!IMPORTANT]
> O exemplo a seguir assume a familiaridade com a criação de [aplicativos de contêiner do Windows usando O Fabric de Serviço e o Docker][containers-getting-started-link].
>
> Este exemplo faz referência mcr.microsoft.com/windows/nanoserver:1809. Os contêineres do Windows Server não são compatíveis em todas as versões de um sistema operacional do host. Para obter mais informações, consulte [Compatibilidade de versão de contêiner do Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

O seguinte ServiceManifest.xml se baseia no trecho servicemanifest descrito anteriormente. *InitCodePackage0,* *InitCodePackage1* e *WorkloadCodePackagesão* são Pacotes de código que representam contêineres. Após a ativação, *InitCodePackage0* é executado primeiro. Ele registra uma mensagem para um arquivo e sai. Em seguida, *o InitCodePackage1* é executado, que também registra uma mensagem em um arquivo e sai. Finalmente, o *WorkloadCodePackage* inicia a execução. Ele também registra uma mensagem em um arquivo, produz o conteúdo do arquivo para **stdout** e, em seguida, pings para sempre.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsInitCodePackageServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows Init CodePackage Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsInitCodePackageServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage0. &gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage1. &gt;&gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="WorkloadCodePackage" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from WorkloadCodePackage. &gt;&gt; C:\WorkspaceOnContainer\log.txt &amp;&amp; type C:\WorkspaceOnContainer\log.txt &amp;&amp; ping -t 127.0.0.1 &gt; nul</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

O aplicativo manifesto.xml a seguir descreve um aplicativo baseado no ServiceManifest.xml discutido acima. Observe que ele especifica o mesmo suporte **de volume** para todos os contêineres, ou seja, **C:\WorkspaceOnHost** é montado em **C:\WorkspaceOnContainer** em todos os três contêineres. O efeito líquido é que todos os contêineres escrevem para o mesmo arquivo de registro na ordem em que são ativados.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsInitCodePackageApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows Init CodePackage Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsInitCodePackageServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ContainerHostPolicies CodePackageRef="InitCodePackage0" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

     <ContainerHostPolicies CodePackageRef="InitCodePackage1" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

      <ContainerHostPolicies CodePackageRef="WorkloadCodePackage" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsInitCodePackageService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsInitCodePackageServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
Uma vez que o ServicePackage tenha sido ativado com sucesso, o conteúdo de **C:\WorkspaceOnHost\log.txt** deve ser o seguinte.

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

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
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md

