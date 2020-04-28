---
title: Pacote autônomo do Azure Service Fabric para Windows Server
description: Descrição e o conteúdo do pacote Autônomo do Azure Service Fabric para Windows Server.
author: maburlik
ms.topic: conceptual
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: 1bb73fa69717f067139067f127a0d50af4878d6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75451841"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Conteúdo do pacote Autônomo do Service Fabric para Windows Server
No pacote Autônomo do Service Fabric [baixado](https://go.microsoft.com/fwlink/?LinkId=730690), você encontrará os seguintes arquivos:

| **Nome do arquivo** | **Descrição resumida** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Um script do PowerShell que cria o cluster usando as configurações em ClusterConfig.json. |
| RemoveServiceFabricCluster.ps1 |Um script do PowerShell que remove um cluster usando as configurações em ClusterConfig.json. |
| AddNode.ps1 |Um script do PowerShell para adicionar um nó a um cluster implantado existente na máquina atual. |
| RemoveNode.ps1 |Um script do PowerShell para remover um nó de um cluster implantado existente da máquina atual. |
| CleanFabric.ps1 |Um script do PowerShell para limpeza de uma instalação autônoma do Service Fabric do computador atual. As instalações anteriores do MSI devem ser removidas usando seus próprios desinstaladores associados. |
| TestConfiguration.ps1 |Um script do PowerShell para analisar a infraestrutura conforme especificado no Cluster.json. |
| DownloadServiceFabricRuntimePackage.ps1 |Um script do PowerShell usado para baixar o pacote mais recente do runtime fora de banda, para cenários nos quais a máquina de implantação não está conectada à internet. |
| DeploymentComponentsAutoextractor.exe |Arquivo compactado de extração automática que contém os componentes de implantação usados pelos scripts do pacote autônomo. |
| EULA_ENU.txt |Os termos de licença para uso do pacote autônomo do Windows Server do Microsoft Azure Service Fabric. Você pode [baixar uma cópia do EULA](https://go.microsoft.com/fwlink/?LinkID=733084) agora. |
| Readme.txt |Um link para as notas de versão e instruções básicas de instalação. É um subconjunto das instruções neste documento. |
| ThirdPartyNotice.rtf |Aviso de software de terceiros que está no pacote. |
| Tools\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |StandaloneLogCollector.exe, que é executado sob demanda para coletar e carregar os logs de rastreamento para a Microsoft para fins de suporte. |
| Tools\ServiceFabricUpdateService.zip |Uma ferramenta usada para habilitar a atualização automática de código para clusters que não têm acesso à Internet. Encontre mais detalhes [aqui](service-fabric-cluster-upgrade-windows-server.md)|

**Modelos** 

| **Nome do arquivo** | **Descrição resumida** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |Um arquivo de exemplo de configuração do cluster, que contém as configurações para um cluster de desenvolvimento de um único computador (ou máquina virtual) com três nós, não protegido, incluindo as informações para cada nó no cluster. |
| ClusterConfig.Unsecure.MultiMachine.json |Um arquivo de exemplo de configuração do cluster, que contém as configurações para um cluster de vários computadores (ou máquinas virtuais), não protegido, incluindo as informações para cada computador no cluster. |
| ClusterConfig.Windows.DevCluster.json |Um arquivo de exemplo de configuração do cluster que contém todas as configurações para um cluster de desenvolvimento de um único computador (ou máquina virtual) com três nós, protegido, incluindo as informações para cada nó que está no cluster. O cluster é protegido usando as [identidades do Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |Um arquivo de exemplo de configuração do cluster, que contém todas as configurações para um cluster de vários computadores (ou máquinas virtuais), protegido, que use a segurança do Windows, incluindo as informações para cada computador no cluster protegido. O cluster é protegido usando as [identidades do Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |Um arquivo de exemplo de configuração do cluster, que contém todas as configurações para um cluster de desenvolvimento de um único computador (ou máquina virtual), com três nós, protegido, incluindo as informações para cada nó no cluster. O cluster é protegido usando certificados x509. |
| ClusterConfig.x509.MultiMachine.json |Um arquivo de exemplo de configuração do cluster, que contém todas as configurações para um cluster de vários computadores (ou máquinas virtuais), protegido, incluindo as informações para cada nó no cluster. O cluster é protegido usando certificados x509. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |Um arquivo de exemplo de configuração do cluster, que contém todas as configurações para um cluster de vários computadores (ou máquinas virtuais), protegido, incluindo as informações para cada nó no cluster. O cluster é protegido usando [Contas de Serviço Gerenciado por Grupo](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Exemplos da configuração do cluster
As versões mais recentes dos modelos de configuração de cluster podem ser encontradas na página do GitHub: [exemplos de configuração de cluster autônomo](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Pacote de Runtime Independente
O pacote de runtime mais recente é baixado automaticamente durante a implantação de cluster de [Link de Download - Runtime do Service Fabric - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Relacionado
* [Criar um cluster autônomo do Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md)
* [Cenários de segurança do cluster do Service Fabric](service-fabric-windows-cluster-windows-security.md)
