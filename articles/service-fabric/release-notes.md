---
title: Lançamentos de Fabric de Serviço Azure
description: Notas de versão para a malha de serviço do Azure. Inclui informações sobre os recursos mais recentes e melhorias no Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 3e0f6c78b6e5dd066cbfbac6805bb3c42068e66a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729590"
---
# <a name="service-fabric-releases"></a>Lançamentos de Service Fabric

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Guias de solução de problemas Problemas</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">Opções</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">de suporte</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">opções de código de versões</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">suportadas</a>

Este artigo fornece mais informações sobre as versões mais recentes e atualizações para o tempo de execução do Service Fabric e SDKs.

## <a name="whats-new-in-service-fabric"></a>O que há de novo em Malha de Serviço

### <a name="service-fabric-71"></a>Malha de serviço 7.1
Devido à atual crise do COVID-19, e levando em consideração os desafios enfrentados por nossos clientes, estamos disponibilizando o 7.1, mas não atualizaremos automaticamente os clusters definidos para receber upgrades automáticos. Estamos pausando atualizações automáticas até segunda ordem para garantir que os clientes possam aplicar upgrades quando mais apropriadopara eles, para evitar interrupções inesperadas.

Você poderá atualizar para 7.1 através do [Portal Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) ou através de uma [implantação do Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#set-the-upgrade-mode-using-a-resource-manager-template).

Os clusters de malha de serviço com upgrades automáticos ativados começarão a receber a atualização 7.1 automaticamente assim que retomarmos o procedimento padrão de implantação. Forneceremos outro anúncio antes do lançamento padrão começar no [Service Fabric Tech Community Site](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).
Também publicamos atualizações para o fim da data de suporte para grandes lançamentos a partir de 6.5 até 7.1 [aqui](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-versions). 

## <a name="what-is-new-in-service-fabric-71"></a>O que é o novo tecido em serviço 7.1?
Estamos animados para anunciar o próximo lançamento de Service Fabric. Esta versão é carregada com os principais recursos e melhorias. Algumas das principais características são destacadas abaixo:
## <a name="key-announcements"></a>Principais Anúncios
- **Disponibilidade geral** do tecido de [ **serviço suseias identidades gerenciadas para aplicativos de malha de serviço**](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)
- [**Suporte para Ubuntu 1804**](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-create-vnet-and-linux-cluster)
 - [**Visualização: Suporte ao disco efêmero VMSS**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-azure-deployment-preparation#use-ephemeral-os-disks-for-virtual-machine-scale-sets)**: Os discos do Sistema Operacional Efêmero são armazenados na máquina virtual local e não salvos no armazenamento remoto do Azure. Eles são recomendados para todos os tipos de nó de malha de serviço (principal e secundário), porque em comparação com discos tradicionais persistentes do sistema operacional, discos efêmeros do sistema operacional:
      -  Reduza a latência de leitura/gravação para o disco DO SO
      -  Habilite operações mais rápidas de gerenciamento de nó de reset/re-imagem
      -  Reduza os custos gerais (os discos são gratuitos e não incorrem em custo adicional de armazenamento)
- Suporte para declaração de [**certificados de ponto final de serviço de aplicativos de malha de serviço por nome comum de assunto**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources).
- [**Suporte para sondas de saúde para serviços em contêineres**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage): Suporte ao mecanismo Liveness Probe para aplicações em contêineres. Liveness Probe ajuda a anunciar a vida do aplicativo contêiner e quando eles não respondem em tempo hábil, isso resultará em uma reinicialização. 
- [**Suporte para pacotes de código inicializador**](https://docs.microsoft.com/azure/service-fabric/initializer-codepackages) para [contêineres](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-containers-overview) e aplicativos [executáveis de hóspedes.](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-guest-executables-introduction) Isso permite a execução de Pacotes de Código (por exemplo, contêineres), em um pedido especificado, para executar a inicialização do Pacote de Serviços.
- **FabricObserver e ClusterObserver** são aplicativos apátridas que capturam telemetria de malha de serviço relacionada a diferentes aspectos de um cluster SF. Ambos os aplicativos estão prontos para serem implantados em clusters de produção do Windows para capturar telemetria rica com suporte implementado para ApplicationInsights, EventSource e LogAnalytics.
    - [**FabricObserver (FO) 2.0**](https://github.com/microsoft/service-fabric-observer)- é executado em todos os nós, gera eventos de saúde, emite telemetria quando os limites de uso de recursos configurados pelo usuário são atingidos. Esta versão contém vários aprimoramentos em monitoramento, gerenciamento de dados, detalhes de eventos de saúde, telemetria estruturada.
     - [**ClusterObserver (CO) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) - é executado em um nó, captura telemetria de saúde em nível de cluster. Nesta versão, o ClusterObserver também monitora o status do nó e emite telemetria quando o nó está desativado/desabilitando/desativado por mais tempo do que o período de tempo especificado pelo usuário.

### <a name="improve-application-life-cycle-experience"></a>Melhorar a experiência do ciclo de vida do aplicativo

- **[Visualização:Drenagem de solicitação](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-advanced#avoid-connection-drops-during-planned-downtime-of-stateless-services)**: Durante a manutenção planejada do serviço, como upgrades de serviço ou desativação do nó, você gostaria de permitir que os serviços drenem graciosamente as conexões. Esse recurso adiciona uma ocorrência de atraso de fechamento na configuração do serviço. Durante as operações planejadas, o SF removerá o endereço do Serviço da descoberta e, em seguida, esperará essa duração antes de desligar o serviço.
- **[Detecção e balanceamento automáticos de subcluster](https://docs.microsoft.com/azure/service-fabric/cluster-resource-manager-subclustering )**: O subclustering acontece quando serviços com diferentes restrições de colocação têm uma métrica de [carga](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-metrics)comum . Se a carga nos diferentes conjuntos de nós difere significativamente, o Service Fabric Cluster Resource Manager acredita que o cluster está desequilibrado, mesmo quando tem o melhor equilíbrio possível devido às restrições de colocação. Como resultado, ele tenta reequilibrar o cluster, potencialmente causando movimentos de serviço desnecessários (uma vez que o "desequilíbrio" não pode ser substancialmente melhorado). Começando com essa versão, o Cluster Resource Manager agora tentará detectar automaticamente esses tipos de configurações e entender quando o desequilíbrio pode ser corrigido através do movimento e, em vez disso, deve deixar as coisas em paz, já que nenhuma melhoria substancial pode ser feita.  
- [**Custo de movimento diferente para réplicas secundárias**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost): Introduzimos um novo valor de custo de movimento VeryHigh que fornece flexibilidade adicional em alguns cenários para definir se um custo de movimento separado deve ser usado para réplicas secundárias.
- Mecanismo de [**sonda liveness**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage ) habilitado para aplicações contêinerizadas. Liveness Probe ajuda a anunciar a vida do aplicativo contêiner e quando eles não respondem em tempo hábil, isso resultará em uma reinicialização.
- [**Executar até a conclusão/uma vez para serviços**](https://docs.microsoft.com/azure/service-fabric/run-to-completion)**

### <a name="image-store-improvements"></a>Melhorias na Loja de Imagens
 - O Service Fabric 7.1 usa **transporte personalizado para garantir a transferência de arquivos entre nós por padrão**. A dependência do compartilhamento de arquivos SMB é removida da versão 7.1. Os compartilhamentos de arquivos SMB protegidos ainda existem em nós que contêm réplica do Image Store Service para a escolha do cliente de optar por sair do padrão e para atualizar e fazer downgrade para a versão antiga.
       
 ### <a name="reliable-collections-improvements"></a>Melhorias de coleções confiáveis

- [**Na memória, apenas armazenar suporte para serviços estaduais usando Coleções Confiáveis**](https://docs.microsoft.com/azure/service-fabric/service-fabric-work-with-reliable-collections#volatile-reliable-collections): Coleções confiáveis voláteis permite que os dados sejam percorridos em disco para durabilidade contra paralisações em larga escala, podendo ser usado para cargas de trabalho como cache replicado, por exemplo, onde a perda de dados ocasional pode ser tolerada. Com base nas [limitações e restrições das Coleções Voláteis Confiáveis,](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections-guidelines#volatile-reliable-collections)recomendamos isso para cargas de trabalho que não precisam de persistência, para serviços que lidam com as raras ocasiões de Perda de Quórum.
- [**Visualização: Service Fabric Backup Explorer**](https://github.com/microsoft/service-fabric-backup-explorer): Para facilitar o gerenciamento de backups de coleções confiáveis para aplicativos de malha de serviço, o Service Fabric Backup Explorer permite que os usuários
    - Audite e revise o conteúdo das Coleções Confiáveis,
    - Atualize o estado atual para uma visão consistente
    - Criar backup do instantâneo atual das Coleções Confiáveis
    - Corrigir a corrupção de dados
                 
### <a name="service-fabric-71-releases"></a>Versões do Service Fabric 7.1
| Data de liberação | Versão | Obter mais informações |
|---|---|---|
| 20 de abril de 2020 | [Tecido de serviço Azure 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Notas de versão](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|


### <a name="service-fabric-70"></a>Malha de serviço 7.0

O Azure Service Fabric 7.0 já está disponível! Você poderá atualizar para o 7.0 através do portal Azure ou através de uma implantação do Azure Resource Manager. Devido ao feedback dos clientes sobre os lançamentos durante o período de férias, não começaremos a atualizar automaticamente os clusters definidos para receber upgrades automáticos até janeiro.
Em janeiro, retomaremos o procedimento padrão de implantação e os clusters com upgrades automáticos habilitados começarão a receber a atualização 7.0 automaticamente. Faremos outro anúncio antes do lançamento começar.
Também atualizaremos nossas datas de lançamento planejadas para indicar que levamos esta política em consideração. Veja aqui as atualizações sobre [nossos futuros cronogramas de lançamento.](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)
 
Esta é a versão mais recente do Service Fabric e está carregada com os principais recursos e melhorias.

### <a name="key-announcements"></a>Principais Anúncios
 - [**KeyVaultSuporte de suporte para segredos de aplicativos (Preview)**](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references): Os aplicativos de malha de serviço que habilitaram [identidades gerenciadas](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) agora podem referenciar diretamente uma URL secreta do Key Vault como uma variável de ambiente, parâmetro de aplicativo ou credencial de repositório de contêiner. O Service Fabric resolverá automaticamente o segredo usando a identidade gerenciada do aplicativo. 
     
- **Segurança de upgrade melhorada para serviços apátridas**: Para garantir a disponibilidade durante uma atualização de aplicativo, introduzimos novas configurações para definir o [número mínimo de instâncias para serviços apátridas](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) a serem considerados disponíveis. Anteriormente, esse valor era de 1 para todos os serviços e não era mutável. Com esta nova verificação de segurança por serviço, você pode garantir que seus serviços mantenham um número mínimo de instâncias de up durante upgrades de aplicativos, upgrades de cluster e outras manutenções que dependem das verificações de saúde e segurança da Service Fabric.
  
- [**Limites de recursos para serviços de usuário**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services): Os usuários podem configurar limites de recursos para os serviços do usuário em um nó para evitar cenários como o esgotamento de recursos dos serviços do sistema Service Fabric. 
  
- [**Muito alto custo de movimentação de serviço**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) para um tipo de réplica. As réplicas com custo de movimento muito alto serão movidas somente se houver uma violação de restrição no cluster que não possa ser corrigida de outra forma. Consulte os docs para obter informações adicionais sobre quando o uso de um custo de movimento "Muito Alto" é razoável e para considerações adicionais.
  
-  **Verificações adicionais**de segurança de cluster : Nesta versão, introduzimos uma verificação de segurança de quórum de nó de sementes configurável. Isso permite personalizar quantos nós de sementes devem estar disponíveis durante o ciclo de vida do cluster e cenários de gerenciamento. As operações que levariam o cluster abaixo do valor configurado estão bloqueadas. Hoje o valor padrão é sempre um quórum dos nódulos de sementes, por exemplo, se você tiver 7 nódulos de sementes, uma operação que o levaria abaixo de 5 nós de sementes seria bloqueado por padrão. Com essa mudança, você poderia fazer o valor mínimo seguro 6, o que permitiria que apenas um nó de sementes fosse para baixo de cada vez.
   
- Adicionado suporte para [**gerenciar o serviço de backup e restauração no Service Fabric Explorer**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster). Isso torna possível as seguintes atividades diretamente de dentro do SFX: descobrir o serviço de backup e restaurar, criar política de backup, habilitar backups automáticos, fazer backups adhoc, ativar operações de restauração e navegar backups existentes.

- Anunciando a disponibilidade do [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool): Esta ferramenta ajuda a validar que os tipos usados em coleções confiáveis são compatíveis para a frente e para trás durante uma atualização de aplicativo de rolamento. Isso ajuda a evitar falhas de atualização ou perda de dados e corrupção de dados devido a tipos ausentes ou incompatíveis.

- [**Habilitar leituras estáveis em réplicas secundárias**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1):Leituras estáveis restringirão réplicas secundárias a valores de retorno que foram saqueados.

Além disso, esta versão contém outros novos recursos, correções de bugs e suporte, confiabilidade e melhorias de desempenho. Para obter a lista completa de alterações, consulte as notas de [versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

### <a name="service-fabric-70-releases"></a>Lançamentos do Service Fabric 7.0

| Data de liberação | Versão | Obter mais informações |
|---|---|---|
| 18 de novembro de 2019 | [Tecido de serviço Azure 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 de janeiro de 2020 | [Azure Service Fabric 7.0 Liberação de atualização](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 de fevereiro de 2020 | [Azure Service Fabric 7.0 Liberação de atualização](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2 de março de 2020 | [Azure Service Fabric 7.0 Liberação de atualização](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)

### <a name="service-fabric-65"></a>Malha de serviço 6.5

Essa versão inclui suporte, confiabilidade e melhorias de desempenho, novos recursos, correções de bugs e aprimoramentos para facilitar o gerenciamento do ciclo de vida do cluster e do aplicativo.

> [!IMPORTANT]
> Service Fabric 6.5 é o lançamento final com suporte a ferramentas Service Fabric no Visual Studio 2015. Os clientes são aconselhados a se mudar para [o Visual Studio 2019](https://visualstudio.microsoft.com/vs/) daqui para frente.

Aqui está o que há de novo no Service Fabric 6.5:

- O Service Fabric Explorer inclui um [Visualizador de Armazenamento de Imagens](service-fabric-visualizing-your-cluster.md#image-store-viewer) para inspecionar aplicativos que você carregou na loja de imagens.

- A versão [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) [do Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) inclui muitas melhorias de autodiagnóstico. Recomenda-se que os clientes de POA se movam para esta versão.

- [EventStore Service é habilitado por padrão](service-fabric-visualizing-your-cluster.md#event-store) para clusters Service Fabric 6.5, a menos que você tenha optado por sair.

- Adicionado [seleção de eventos do ciclo de vida de réplicas](service-fabric-diagnostics-event-generation-operational.md#replica-events) para serviços estaduais.

- [Melhor visibilidade do status do nó de sementes,](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)incluindo avisos em nível de cluster se um nó de sementes não for saudável *(Para baixo,* *removido* ou *desconhecido).*

- [A ferramenta de recuperação de desastres do aplicativo de malha de](https://github.com/Microsoft/Service-Fabric-AppDRTool) serviço permite que os serviços de malha de serviço se recuperem rapidamente quando o cluster principal encontrar um desastre. Os dados do cluster primário são continuamente sincronizados no aplicativo de espera secundário usando backup e restauração periódicas.

- Suporte ao Visual Studio para [publicação de aplicativos .NET Core em clusters baseados em Linux](service-fabric-how-to-publish-linux-app-vs.md).

- [O Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) será instalado automaticamente para as versões Service Fabric 6.5 (e posteriores) quando você atualizar ou criar um novo cluster Linux no Azure.

- [O SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) é instalado por padrão nos clusters MacOS/Linux OneBox.

Para obter mais detalhes, consulte as notas de versão do [Service Fabric 6.5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Versões de Service Fabric 6.5

| Data de liberação | Versão | Obter mais informações |
|---|---|---|
| 11 de junho de 2019 | [Tecido de serviço Azure 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 de julho de 2019 | [Azure Service Fabric 6.5 Liberação de atualização](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 de julho de 2019 | [Azure Service Fabric 6.5 Liberação de atualização](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Aug 23, 2019 | [Azure Service Fabric 6.5 Liberação de atualização](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Notas de versão](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 de outubro de 2019 | [Azure Service Fabric 6.5 Liberação de atualização](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Notas de lançamento] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


## <a name="previous-versions"></a>Versões anteriores

### <a name="service-fabric-64-releases"></a>Versões de Service Fabric 6.4

| Data de liberação | Versão | Obter mais informações |
|---|---|---|
| 30 de novembro de 2018 | [Tecido de serviço Azure 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 de dezembro de 2018 | [Azure Service Fabric 6.4 Liberação de atualização para clusters Windows](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 de fevereiro de 2019 | [Azure Service Fabric 6.4 Liberação de atualização](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 de março de 2019 | [Azure Service Fabric 6.4 Liberação de atualização](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 de abril de 2019 | [Azure Service Fabric 6.4 Liberação de atualização](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 de maio de 2019 | [Azure Service Fabric 6.4 Liberação de atualização](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 de maio de 2019 | [Azure Service Fabric 6.4 Liberação de atualização](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Notas de versão](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
