---
title: Criar um cluster autônomo do Azure Service Fabric | Microsoft Docs
description: Crie um cluster de Service Fabric do Azure em qualquer computador (físico ou virtual) executando o Windows Server, seja no local ou em qualquer nuvem.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/21/2019
ms.author: dekapur
ms.openlocfilehash: 6fce1957101050c6ff3a2c3aba2b4b87d4f66f1d
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554657"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Criar um cluster autônomo em execução no Windows Server
Você pode usar o Azure Service Fabric para criar clusters Service Fabric em máquinas virtuais ou computadores que executam o Windows Server. Isso significa que você pode implantar e executar Service Fabric aplicativos em qualquer ambiente que contenha um conjunto de computadores Windows Server interconectados, seja no local ou em qualquer provedor de nuvem. O Service Fabric fornece um pacote de instalação para criar os clusters do Service Fabric denominado pacote do Windows Server autônomo. Os clusters tradicionais de Service Fabric no Azure estão disponíveis como um serviço gerenciado, enquanto os clusters de Service Fabric autônomos são de autoatendimento.

Este artigo orienta você pelas etapas para criar um Cluster Service Fabric autônomo.

> [!NOTE]
> Este pacote autônomo do Windows Server está comercialmente disponível sem custos e pode ser usado para implantações de produção. Este pacote pode conter novos recursos de Service Fabric que estão em "versão prévia". Role para baixo até "[recursos de visualização incluídos neste pacote](#previewfeatures_anchor)". seção para obter a lista dos recursos de visualização. Você pode [baixar uma cópia do EULA](https://go.microsoft.com/fwlink/?LinkID=733084) agora.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Obter suporte para o Service Fabric para o pacote do Windows Server
* Pergunte à comunidade sobre o pacote autônomo do Service Fabric para Windows Server no [Fórum do Service Fabric do Azure](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Abra um tíquete de [Suporte Profissional para o Service Fabric](https://support.microsoft.com/oas/default.aspx?prid=16146).  Saiba mais sobre o suporte profissional da Microsoft [aqui](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Você também pode obter suporte para esse pacote como parte do [Microsoft suporte Premier](https://support.microsoft.com/en-us/premier).
* Para obter mais detalhes, consulte as [Opções de suporte do Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
* Para coletar logs para fins de suporte, execute o [coletor de logs autônomo Service Fabric](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Baixar o Service Fabric para o pacote do Windows Server
Para criar o cluster, use o Service Fabric para o pacote do Windows Server (Windows Server 2012 R2 e mais recente) encontrado aqui: <br>
[Link de download-Service Fabric pacote autônomo-Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

Encontre detalhes sobre o conteúdo do pacote [aqui](service-fabric-cluster-standalone-package-contents.md).

O pacote de tempo de execução Service Fabric é baixado automaticamente no momento da criação do cluster. Se estiver implantando de um computador não conectado à Internet, baixe o pacote de tempo de execução fora de banda aqui: <br>
[Link de download-tempo de execução de Service Fabric-Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Encontre exemplos de configuração de cluster autônomo em: <br>
[Exemplos de configuração de cluster autônomo](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Criar o cluster
Vários arquivos de configuração de cluster de exemplo são instalados com o pacote de instalação. *ClusterConfig.Unsecure.DevCluster.json* é a configuração de cluster mais simples: um cluster não seguro de três nós em execução em um único computador.  Outros arquivos de configuração descrevem clusters únicos ou vários computadores protegidos com certificados x. 509 ou com a segurança do Windows.  Você não precisa modificar nenhuma das configurações de configuração padrão para este tutorial, mas examine o arquivo de configuração e familiarize-se com as configurações.  A seção **nós** descreve os três nós no cluster: nome, endereço IP, [tipo de nó, domínio de falha e domínio de atualização](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  A seção de **Propriedades** define a [segurança, o nível de confiabilidade, a coleta de diagnóstico e os tipos de nós](service-fabric-cluster-manifest.md#cluster-properties) para o cluster.

O cluster criado neste artigo não é seguro.  Qualquer pessoa pode se conectar anonimamente e executar operações de gerenciamento, de modo que os clusters de produção sempre devem ser protegidos usando certificados X. 509 ou a segurança do Windows.  A segurança só é configurada no momento da criação do cluster e não é possível habilitar a segurança após a criação do cluster. Atualize o arquivo de configuração Habilitar [segurança de certificado](service-fabric-windows-cluster-x509-security.md) ou segurança do [Windows](service-fabric-windows-cluster-windows-security.md). Leia [Proteger um cluster](service-fabric-cluster-security.md) para saber mais sobre a segurança do cluster do Service Fabric.

### <a name="step-1-create-the-cluster"></a>Etapa 1: criar o cluster

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>Cenário A: criar um cluster de desenvolvimento local não seguro
Service Fabric pode ser implantado em um cluster de desenvolvimento de uma máquina usando o arquivo *ClusterConfig. Unsecure. DevCluster. JSON* incluído em [exemplos](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Descompacte o pacote autônomo em seu computador, copie o arquivo de configuração de exemplo para o computador local e, em seguida, execute o script *CreateServiceFabricCluster. ps1* por meio de uma sessão do PowerShell de administrador, na pasta do pacote autônomo.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Consulte a seção configuração do ambiente em [planejar e preparar a implantação do cluster](service-fabric-cluster-standalone-deployment-preparation.md) para obter detalhes de solução de problemas.

Se terminar de executar cenários de desenvolvimento, você poderá remover o Service Fabric cluster do computador fazendo referência a etapas na seção "[remover um cluster](#removecluster_anchor)". 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>Cenário B: criar um cluster de vários computadores
Depois de ter passado pelas etapas de planejamento e preparação detalhadas em [planejar e preparar a implantação do cluster](service-fabric-cluster-standalone-deployment-preparation.md), você estará pronto para criar seu cluster de produção usando o arquivo de configuração do cluster.

O administrador de cluster que está implantando e Configurando o cluster deve ter privilégios de administrador no computador. Não é possível instalar Service Fabric em um controlador de domínio.

1. O script *TestConfiguration. ps1* no pacote autônomo é usado como um analisador de práticas recomendadas para validar se um cluster pode ser implantado em um determinado ambiente. [Preparação da implantação](service-fabric-cluster-standalone-deployment-preparation.md) lista os pré-requisitos e requisitos de ambiente. Execute o script para verificar se você pode criar o cluster de desenvolvimento:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Você deve ver saídas semelhantes às seguintes. Se o campo inferior "Passed" é retornado como "True", as verificações de integridade são aprovadas e o cluster parece estar pronto para implantação com base na configuração de entrada.

    ```powershell
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. Criar o cluster: execute o script *CreateServiceFabricCluster. ps1* para implantar o Cluster Service Fabric em cada computador na configuração. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Rastreamentos de implantação são gravados na VM/computador em que você executou o script do PowerShell CreateServiceFabricCluster.ps1. Eles podem ser encontrados na subpasta DeploymentTraces, com base no diretório do qual o script foi executado. Para ver se Service Fabric foi implantada corretamente em um computador, localize os arquivos instalados no diretório FabricDataRoot, conforme detalhado na seção FabricSettings do arquivo de configuração de cluster (por padrão c:\ProgramData\SF). Assim, os processos FabricHost. exe e Fabric. exe podem ser vistos em execução no Gerenciador de tarefas.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>Cenário C: criar um cluster offline (desconectado da Internet)
O pacote de tempo de execução Service Fabric é baixado automaticamente na criação do cluster. Ao implantar um cluster em computadores que não estão conectados à Internet, você precisará baixar o pacote de tempo de execução Service Fabric separadamente e fornecer o caminho para ele na criação do cluster.
O pacote de tempo de execução pode ser baixado separadamente, de outro computador conectado à Internet, em [Download Link-Service Fabric Runtime-Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Copie o pacote de tempo de execução no qual você está implantando o cluster offline e crie o cluster executando `CreateServiceFabricCluster.ps1` com o parâmetro `-FabricRuntimePackagePath` incluído, conforme mostrado neste exemplo: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.JSON* e *.\MicrosoftAzureServiceFabric.cab* são os caminhos para a configuração de cluster e o arquivo Runtime. cab, respectivamente.

### <a name="step-2-connect-to-the-cluster"></a>Etapa 2: conectar-se ao cluster
Conecte-se ao cluster para verificar se o cluster está em execução e disponível. O módulo do PowerShell do infabric é instalado com o tempo de execução.  Você pode se conectar ao cluster de um dos nós de cluster ou de um computador remoto com o tempo de execução Service Fabric.  O cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) estabelece uma conexão com o cluster.

Para se conectar a um cluster não seguro, execute o seguinte comando do PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

Por exemplo:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```

Consulte [conectar-se a um cluster seguro](service-fabric-connect-to-secure-cluster.md) para obter outros exemplos de conexão a um cluster. Depois de se conectar ao cluster, use o cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) para exibir uma lista de nós no cluster e informações de status para cada nó. O **HealthState** deve ser *OK* para cada nó.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>Etapa 3: Visualizar o cluster usando o Service Fabric Explorer
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) é uma boa ferramenta para visualizar o cluster e gerenciar aplicativos.  Service Fabric Explorer é um serviço executado no cluster, que você acessa usando um navegador navegando até [http://localhost:19080/Explorer](http://localhost:19080/Explorer).

O painel do cluster fornece uma visão geral do cluster, incluindo um resumo da integridade do aplicativo e do nó. A exibição de nó mostra o layout físico do cluster. Para um determinado nó, você pode inspecionar quais aplicativos têm o código implantado nesse nó.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Adicionar e remover nós
Você pode adicionar ou remover os nós do cluster do Service Fabric autônomo quando seu negócio precisa mudar. Confira [Adicionar ou remover nós de um cluster do Service Fabric autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md) para ver as etapas detalhadas.

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Remover um cluster
Para remover um cluster, execute o script do PowerShell *RemoveServiceFabricCluster.ps1* da pasta do pacote e transmita o caminho para o arquivo de configuração JSON. Opcionalmente, você pode especificar um local para o log da exclusão.

Esse script pode ser executado em qualquer computador que tenha o acesso de administrador para todos os computadores listados como nós no arquivo de configuração do cluster. O computador no qual este script é executado não precisa fazer parte do cluster.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Dados de telemetria coletados e como recusá-los
Como padrão, o produto coleta a telemetria no uso de Service Fabric para melhorar o produto. O analisador de práticas recomendadas que é executado como parte da instalação do verifica a conectividade com [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Se não estiver acessível, a instalação falhará, a menos que você recuse a telemetria.

1. O pipeline de telemetria tenta carregar os dados a seguir para [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) uma vez por dia. É um upload de melhor esforço e não tem impacto sobre a funcionalidade do cluster. A telemetria é enviada somente do nó que executa o Gerenciador de failover primário. Nenhum outro nó envia telemetria.
2. A telemetria consiste no seguinte:

* Número de serviços
* Número de ServiceTypes
* Número de aplicativos
* Número de ApplicationUpgrades
* Número de FailoverUnits
* Número de InBuildFailoverUnits
* Número de UnhealthyFailoverUnits
* Número de réplicas
* Número de InBuildReplicas
* Número de StandByReplicas
* Número de OfflineReplicas
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Número de nós
* IsContextComplete: true/false
* Clusterid: é um GUID gerado aleatoriamente para cada cluster
* ServiceFabricVersion
* Endereço IP da máquina virtual ou máquina a partir da qual a telemetria é carregada

Para desabilitar a telemetria, adicione o seguinte ao elemento *propriedades* em sua configuração de cluster: *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Recursos de visualização incluídos neste pacote
Nenhum.


> [!NOTE]
> A partir da nova [versão ga do cluster autônomo para o Windows Server (versão 5.3.204. x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), você pode atualizar seu cluster para versões futuras, manual ou automaticamente. Consulte a [atualização de um documento de versão do cluster Service Fabric autônomo](service-fabric-cluster-upgrade-windows-server.md) para obter detalhes.
> 
> 

## <a name="next-steps"></a>Próximos passos
* [Implantar e remover aplicativos usando o PowerShell](service-fabric-deploy-remove-applications.md)
* [Definições de configuração para o cluster autônomo no Windows](service-fabric-cluster-manifest.md)
* [Adicionar ou remover nós de um cluster do Service Fabric autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Atualizar uma versão de Cluster Service Fabric autônoma](service-fabric-cluster-upgrade-windows-server.md)
* [Criar um cluster do Service Fabric autônomo com VMs do Azure executando o Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Proteger um cluster autônomo no Windows usando a segurança](service-fabric-windows-cluster-windows-security.md)
* [Proteger um cluster autônomo no Windows usando os certificados X509](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png
