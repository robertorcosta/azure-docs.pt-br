---
title: Perguntas comuns sobre o Microsoft Azure Service Fabric | Microsoft Docs
description: Perguntas frequentes e respostas sobre o Service Fabric
services: service-fabric
documentationcenter: .net
author: chackdan
manager: chackdan
editor: ''
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: pepogors
ms.openlocfilehash: 23479692e815b5dda010ec2035c206df15715347
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167422"
---
# <a name="commonly-asked-service-fabric-questions"></a>Perguntas frequentes sobre o Service Fabric

Há muitas perguntas frequentes sobre o que o Service Fabric pode fazer e como ele deve ser usado. Este documento aborda muitas dessas perguntas comuns e suas respostas.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Gerenciamento e instalação de cluster

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Como faço para reverter meu certificado de cluster do Service Fabric?

Reverter qualquer atualização para o seu aplicativo requer detecção de falha de integridade antes da confirmação de alteração do quórum do cluster do Service Fabric; as alterações confirmadas somente podem passar por roll forward. Engenheiros de escalação nos Serviços de Atendimento ao Cliente podem ser solicitados a recuperar seu cluster, se uma alteração de certificado interrompido não monitorado tiver sido introduzido.  A [Atualização do aplicativo do Service Fabric](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade?branch=master) aplica [parâmetros de atualização de aplicativo](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master) e não oferece promessa de atualização de tempo de inatividade.  Seguindo o nosso modo monitorado de atualização de aplicativo recomendado, o andamento automática através de domínios de atualização se baseia nas verificações de integridade sem interrupção, revertendo automaticamente se a atualização de um serviço padrão falhar.
 
Se o seu cluster ainda estiver aproveitando a clássica propriedade Thumbprint do Certificado em seu modelo do Resource Manager, é recomendável [Alterar o cluster da impressão digital do certificado para o nome comum](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn), para aproveitar recursos modernos de gerenciamento de segredos.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>É possível criar um cluster que abranja várias regiões do Azure ou meus próprios data centers?

Sim. 

A principal tecnologia de clustering do Service Fabric pode ser usada para combinar máquinas em execução em qualquer lugar do mundo, desde que elas tenham conectividade de rede entre si. No entanto, criar e executar um cluster desse tipo pode ser complicado.

Se você está interessado nesse cenário, é recomendável entrar em contato por meio da [Lista de problemas do Service Fabric no GitHub](https://github.com/azure/service-fabric-issues) ou de seu representante de suporte para obter orientações adicionais. A equipe do Service Fabric está trabalhando para fornecer informações, orientações e recomendações adicionais para esse cenário. 

Veja a seguir alguns itens que você deve levar em consideração: 

1. Atualmente, o recurso de cluster do Service Fabric no Azure é regional, assim como os conjuntos de dimensionamento de máquinas virtuais nos quais o cluster se baseia. Isso significa que, em caso de falha regional, você pode perder a capacidade de gerenciar o cluster por meio do Azure Resource Manager ou do Portal do Azure. Isso pode acontecer mesmo que o cluster permaneça em execução e que você possa interagir diretamente com ele. Além disso, atualmente o Azure não possibilita ter uma única rede virtual que possa ser usada em várias regiões. Isso significa que um cluster de várias regiões do Azure requer [Endereços IP Públicos para cada VM nos Conjuntos de Dimensionamento de VMs](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) ou [Gateways de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md). Essas opções de rede têm impactos diferentes sobre os custos, o desempenho e, até certo ponto, o design do aplicativo, de modo que análise e planejamento cuidadosos são necessários antes de manter um ambiente desse tipo.
2. A manutenção, o gerenciamento e o monitoramento dessas máquinas podem ficar complicados, especialmente quando distribuídas em _tipos_ de ambientes, como entre provedores de nuvem diferentes ou entre recursos locais e o Azure. É necessário ter cuidado para garantir que as atualizações, o monitoramento, o gerenciamento e o diagnóstico sejam compreendidos para o cluster e para os aplicativos antes de executar cargas de trabalho de produção em um ambiente desse tipo. Se você já tiver experiência solucionando esses problemas no Azure ou em seus próprios datacenters, é provável que as mesmas soluções possam ser aplicadas ao compilar ou executar seu cluster do Service Fabric. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Os nós do Service Fabric recebem as atualizações do sistema operacional automaticamente?

Você pode usar [o recurso Atualização geral de imagem do sistema operacional em escala de computador virtual](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) disponível hoje em dia.

Para clusters que NÃO são executados no Azure, [fornecemos um aplicativo ](service-fabric-patch-orchestration-application.md) para corrigir os sistemas operacionais abaixo dos nós do Service Fabric.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Posso usar conjuntos de dimensionamento de máquinas virtuais grandes no meu cluster do SF? 

**Resposta curta** – Não. 

**Resposta longa** – embora os conjuntos de dimensionamento de máquinas virtuais grandes permitam que você dimensione um conjunto de dimensionamento de máquinas virtuais até 1.000 instâncias de VM, isso é feito com o uso de PGs (Grupos de Posicionamento). FDs (domínios de falha) e UDs (domínios de atualização) só são consistentes dentro de um grupo de posicionamento. O Service Fabric usa UDs e FDs para tomar decisões de posicionamento de suas instâncias de serviço/réplicas de serviço. Já que UDs e FDs são comparáveis somente dentro de um grupo de posicionamento, o SF não pode usá-los. Por exemplo, se VM1 no PG1 tem uma topologia de FD=0 e VM9 em PG2 tem uma topologia de FD=4, isso não significa que VM1 e VM2 estejam em dois Racks de hardwares diferentes, portanto, os SFs não podem usar os valores de FD nesse caso para tomar decisões de posicionamento.

Atualmente, há outros problemas com conjuntos de dimensionamento de máquinas virtuais grandes, como a falta de suporte para o balanceamento de carga de nível 4. Consulte para obter [detalhes sobre grandes conjuntos de dimensionamento](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Qual o tamanho mínimo de um cluster do Service Fabric? Por que ele não pode ser menor?

O tamanho mínimo com suporte para um cluster do Service Fabric que execute cargas de trabalho de produção é de cinco nós. Para cenários de desenvolvimento, damos suporte a um nó (otimizado para experiência de desenvolvimento rápido no Visual Studio) e cinco clusters de nó.

Exigimos um cluster de produção para ter pelo menos cinco nós devido aos três motivos a seguir:
1. Mesmo quando nenhum serviço de usuário estiver em execução, um cluster do Service Fabric executa um conjunto de serviços com sistema de estado, incluindo o serviço de nomes e o serviço de gerenciador de failover. Esses serviços do sistema são essenciais para o cluster permanecer operacional.
2. Sempre colocamos uma réplica de um serviço por nó, de modo que o tamanho do cluster é o limite superior para o número de réplicas que um serviço (na verdade, uma partição) pode ter.
3. Como uma atualização de cluster desligará pelo menos um nó, queremos ter um buffer de pelo menos um nó; portanto, queremos que um cluster de produção tenha pelo menos dois nós *além* do mínimo necessário. O mínimo necessário é o tamanho do quorum de um serviço de sistema, conforme explicado abaixo.  

Queremos que o cluster esteja disponíveis se houver falha simultânea de dois nós. Para que um cluster do Service Fabric esteja disponível, os serviços do sistema deverão estar disponíveis. Serviços de sistema com estado como serviço de nomenclatura e serviço de gerenciamento de failover, que controlam quais serviços foram implantados no cluster e onde eles estão hospedados atualmente, dependem de uma consistência forte. Essa consistência forte, por sua vez, depende da capacidade de adquirir um *quorum* para qualquer atualização para o estado desses serviços, onde um quorum representa a maioria estrita das réplicas (N/2 + 1) para um determinado serviço. Portanto, se quisermos ser resilientes em relação à perda simultânea de dois nós (portanto, perda simultânea de duas réplicas de um serviço de sistema), precisamos ter ClusterSize - QuorumSize > = 2, que força o tamanho mínimo para ser cinco. Para ver isso, considere que o cluster tem N nós e há N réplicas de um serviço de sistema, uma em cada nó. O tamanho do quorum para um serviço do sistema é (N/2 + 1). O operador de desigualdade acima se parece com N - (N/2 + 1) > = 2. Há dois casos a serem considerados: quando N for par e quando N for ímpar. Se N for par, digamos, N = 2\*m onde m >= 1, a desigualdade parece 2\*m - (2\*m/2 + 1) >= 2 ou m >= 3. O requisito mínimo para N é 6 e isso é obtido quando m = 3. Por outro lado, se N for ímpar, digamos, N = 2\*m + 1, onde m > = 1, a desigualdade parece 2\*m + 1 - ((2\*m + 1) / 2 + 1) > = 2 ou 2\*m + 1 - (m + 1) > = 2 ou m > = 2. O requisito mínimo para N é 5 e isso é obtido quando m = 2. Portanto, entre todos os valores de N que satisfazem a desigualdade ClusterSize - QuorumSize > = 2, o mínimo é 5.

No argumento acima, pressupomos que cada nó tenha uma réplica de um serviço de sistema, portanto, o tamanho do quorum é calculado com base no número de nós no cluster. No entanto, se alterássemos *TargetReplicaSetSize*, poderíamos deixar o tamanho do quorum menor que (N/2+1) que pode dar a impressão de que poderíamos ter um cluster menor do que 5 nós e ainda ter 2 nós extras acima do tamanho do quorum. Por exemplo, em um cluster de 4 nós, se definirmos o TargetReplicaSetSize como 3, o tamanho do quorum baseado em TargetReplicaSetSize será (3/2 + 1) ou 2 e, portanto, teremos ClusterSize - QuorumSize = 4-2 >= 2. No entanto, não podemos garantir que o serviço de sistema será exatamente o valor ou acima do quorum se perdermos qualquer par de nós simultaneamente. Os dois nós perdidos poderiam estar hospedando duas réplicas, portanto, o serviço do sistema entraria em perda de quorum (tendo apenas uma única réplica sobrando) e ficaria indisponível.

Com esse plano de fundo, vamos examinar algumas configurações de cluster possíveis:

**Um nó**: essa opção não oferece alta disponibilidade, já que a perda do nó único por qualquer motivo significa a perda de todo o cluster.

**Dois nós**: um quorum para um serviço implantado entre dois nós (N = 2) é 2 (2/2 + 1 = 2). Quando uma única réplica é perdida, é impossível criar um quorum. Como a execução de uma atualização de serviço requer desativar temporariamente uma réplica, essa não é uma configuração útil.

**Três nós**: com três nós (N=3), o requisito para criar um quorum ainda é dois nós (3/2 + 1 = 2). Isso significa que você pode perder um nó individual e ainda manter o quorum, mas a falha simultânea de dois nós fará os serviços do sistema entrar em perda de quorum e fará o cluster se tornar indisponível.

**Quatro nós**: com quatro nós (N=4), o requisito para criar um quorum é três nós (4/2 + 1 = 3). Isso significa que você pode perder um nó individual e ainda manter o quorum, mas a falha simultânea de dois nós fará os serviços do sistema entrar em perda de quorum e fará o cluster se tornar indisponível.

**Cinco nós**: com cinco nós (N=5), o requisito para criar um quorum ainda é três nós (5/2 + 1 = 3). Isso significa que você pode perder dois nós ao mesmo tempo e ainda manter o quorum para os serviços do sistema.

Para cargas de trabalho de produção, você deve ser resiliente a falhas simultâneas de pelo menos dois nós (por exemplo, um devido à atualização de cluster, um por outros motivos), portanto, cinco nós são necessários.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Posso desativar meu cluster à noite/aos finais de semana para reduzir custos?

Em geral, não. O Service Fabric armazena o estado em discos locais, efêmeros, o que significa que, se a máquina virtual for movida para um host diferente, os dados não serão movidos com ela. Na operação normal, isso não é um problema, já que o novo nó é atualizado por outros nós. No entanto, se você parar todos os nós e reiniciá-los mais tarde, haverá uma possibilidade significativa de a maioria dos nós iniciar em novos hosts e impossibilitar a recuperação do sistema.

Se você quiser criar clusters para testar seu aplicativo antes da implantação, é recomendável criar dinamicamente os clusters como parte de sua [integração contínua/pipeline de implantação contínuo](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Como atualizo meu sistema operacional (por exemplo, do Windows Server 2012 para o Windows Server 2016)?

Embora estejamos trabalhando em uma experiência aprimorada, no momento você é responsável pela atualização. Você deve atualizar a imagem do sistema operacional nas máquinas virtuais do cluster em uma VM por vez. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Posso criptografar discos de dados anexados em um tipo de nó de cluster (conjunto de dimensionamento de máquinas virtuais)?
Sim.  Para obter mais informações, consulte [Criar um cluster com discos de dados anexados](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks), [Criptografar discos (PowerShell)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md), e [Criptografar discos (CLI) ](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-cli.md).

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Posso usar VMs de baixa prioridade em um tipo de nó de cluster (conjunto de escala de máquina virtual)?
Nº Não há suporte para VMs de baixa prioridade. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Quais são os diretórios e os processos que preciso excluir ao executar um programa antivírus no meu cluster?

| **Diretórios de Antivírus excluídos** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (da configuração de cluster) |
| FabricLogRoot (da configuração de cluster) |

| **Processos do antivírus excluídos** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |
 
### <a name="how-can-my-application-authenticate-to-keyvault-to-get-secrets"></a>Como meu aplicativo pode autenticar obter segredos no cofre de chaves?
A seguir, os meios para o seu aplicativo obter credenciais para autenticação no KeyVault:

A. Durante o trabalho de criação / empacotamento de aplicativos, você pode inserir um certificado no pacote de dados do aplicativo SF e usá-lo para autenticar no KeyVault.
B. Para hosts habilitados para MSI do conjunto de dimensionamento de máquinas virtuais, você pode desenvolver um SetupEntryPoint simples do PowerShell para seu aplicativo de it para obter [um token de acesso do ponto de extremidade do MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)e, em seguida, [recuperar seus segredos do keyvault](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret).

## <a name="application-design"></a>Design do aplicativo

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Qual a melhor maneira de consultar dados ao longo das partições de uma Coleção Confiável?

Coleções Confiáveis normalmente são [particionadas](service-fabric-concepts-partitioning.md) para permitir a escalação horizontal para um melhor desempenho e resultado. Isso significa que o estado de um determinado serviço pode ser espalhado por dezenas ou centenas de máquinas. Para executar operações no conjunto de dados completo, você tem algumas opções:

- Criar um serviço que consulte todas as partições de outro serviço para obter os dados necessários.
- Criar um serviço que possa receber dados de todas as partições de outro serviço.
- Enviar dados por push periodicamente de cada serviço para um repositório externo. Essa abordagem é apropriada apenas se as consultas que você estiver fazendo não fizerem parte da lógica comercial principal.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Qual a melhor maneira de consultar dados entre os meus atores?

Os atores são projetados para serem unidades independentes de estado e computação, portanto, não é recomendável executar consultas amplas de estado do ator no tempo de execução. Se você precisar fazer uma consulta em todo o conjunto de um estado do ator, deve considerar:

- Substituir seus serviços de ator por serviços confiáveis com estado, assim como o número de solicitações de rede para reunir todos os dados do número de atores para o número de partições do seu serviço.
- Projetar seus atores para enviar seu estado por push a um repositório externo periodicamente para facilitar a consulta. Como acima, essa abordagem só é viável se as consultas que você estiver executando não forem necessárias para o comportamento do seu tempo de execução.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Quantos dados posso armazenar em uma Coleção Confiável?

Serviços confiáveis normalmente são particionados, de forma que o valor que você pode armazenar é limitado apenas pelo número de máquinas que tem no cluster e a quantidade de memória disponível nessas máquinas.

Como exemplo, suponha que você tenha uma coleção confiável em um serviço com 100 partições e 3 réplicas, armazenando objetos com uma média de 1 KB de tamanho. Agora, suponha que você tem um cluster de 10 máquinas com 16 gb de memória por máquina. Para simplificar e sendo cauteloso, suponha que o sistema operacional e os serviços do sistema, o tempo de execução do Service Fabric e seus serviços consomem 6 GB disso, deixando 10 GB disponíveis por computador, ou 100 GB para o cluster.

Tendo em mente que cada objeto deve ser armazenado três vezes (uma primária e duas réplicas), você teria memória suficiente para cerca de 35 milhões de objetos na sua coleção ao operar com capacidade total. No entanto, recomendamos ser resiliente à perda simultânea de um domínio de falha e um domínio de atualização, que representa aproximadamente 1/3 da capacidade e reduziria o número a aproximadamente 23 milhões.

Observe que esse cálculo também pressupõe que:

- A distribuição de dados entre as partições é aproximadamente uniforme ou que você está relatando métricas de carga para o Gerenciador de Recursos de Cluster. Por padrão, o Service Fabric executa balanceamento de carga com base na contagem de réplicas. No exemplo anterior, isso colocaria 10 réplicas primárias e 20 réplicas secundárias em cada nó no cluster. Isso funciona bem para cargas distribuídas uniformemente entre as partições. Se a carga não for uniforme, você deverá relatá-la para que o Resource Manager possa empacotar réplicas menores juntas e permitir que as réplicas maiores consumam mais memória em um nó individual.

- O serviço confiável em questão é o único estado de armazenamento no cluster. Como é possível implantar vários serviços em um cluster, é necessário estar atento aos recursos que cada um precisa para executar e gerenciar seu estado.

- O cluster em si não está aumentando ou diminuindo. Se você adicionar mais máquinas, o Service Fabric irá rebalancear as réplicas para aproveitar a capacidade adicional até que o número de máquinas ultrapasse o número de partições no seu serviço, já que uma réplica individual não pode abranger máquinas. Em contrapartida, se você reduz o tamanho do cluster removendo computadores, suas réplicas são empacotadas mais próximas e têm menos capacidade geral.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Quantos dados posso armazenar em um ator?

Assim como acontece com os serviços confiáveis, a quantidade de dados que você pode armazenar em um serviço de ator é limitada apenas pelo espaço em disco total e a memória disponível entre os nós no cluster. No entanto, os atores individuais são mais eficazes quando são usados para encapsular uma pequena quantidade de estado e a lógica comercial associada. Como regra geral, um ator individual deve ter um estado que seja medido em quilobytes.

## <a name="other-questions"></a>Outras perguntas

### <a name="how-does-service-fabric-relate-to-containers"></a>Como o Service Fabric se relaciona com os contêineres?

Os contêineres oferecem uma maneira simples de empacotar serviços e suas dependências, de modo que executam consistentemente em todos os ambientes e podem operar de maneira isolada em uma única máquina. O Service Fabric oferece uma maneira de implantar e gerenciar serviços, incluindo [serviços que foram empacotados em um contêiner](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Vocês pretendem abrir o código do Service Fabric?

Temos partes de código aberto do Service Fabric ([estrutura de serviços confiáveis](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [estrutura de atores confiáveis](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [bibliotecas de integração do ASP.NET Core](https://github.com/Azure/service-fabric-aspnetcore), [Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer) e [CLI do Service Fabric](https://github.com/Azure/service-fabric-cli)) no GitHub e aceitamos contribuições da comunidade para esses projetos. 

Nós [anunciamos recentemente](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/) que estamos planejando tornar o código-fonte aberto do tempo de execução do Service Fabric. No momento, estamos com o [repositório do Service Fabric](https://github.com/Microsoft/service-fabric/) no GitHub com build do Linux e ferramentas de teste, o que significa que você pode clonar o repositório, criar o Service Fabric para Linux, executar testes básicos, indicar problemas e enviar solicitações de pull. Estamos trabalhando duro para migrar o ambiente de build do Windows junto com um ambiente completo de CI.

Siga o [Blog do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) para obter mais detalhes à medida que eles forem anunciados.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [conceitos principais de Service Fabric](service-fabric-technical-overview.md) e [as práticas recomendadas](service-fabric-best-practices-overview.md) conceitos de malha Ice] (Service-Fabric-Technical-Overview.MD) e [as práticas recomendadas](service-fabric-best-practices-overview.md)
