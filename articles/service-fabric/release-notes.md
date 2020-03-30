---
title: Lançamentos de Fabric de Serviço Azure
description: Notas de versão para a malha de serviço do Azure. Inclui informações sobre os recursos mais recentes e melhorias no Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: cb4fdd56e9cf67c71ac690d423499929167f8977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064234"
---
# <a name="service-fabric-releases"></a>Lançamentos de Service Fabric

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Guias de solução de problemas Problemas</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">Opções</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">de suporte</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">opções de código de versões</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">suportadas</a>

Este artigo fornece mais informações sobre as versões mais recentes e atualizações para o tempo de execução do Service Fabric e SDKs.

## <a name="whats-new-in-service-fabric"></a>O que há de novo em Malha de Serviço

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
| 18 de novembro de 2019 | [Tecido de serviço Azure 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 de janeiro de 2020 | [Azure Service Fabric 7.0 Liberação de atualização](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 de fevereiro de 2020 | [Azure Service Fabric 7.0 Liberação de atualização](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|

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
| 11 de junho de 2019 | [Tecido de serviço Azure 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 de julho de 2019 | [Azure Service Fabric 6.5 Liberação de atualização](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 de julho de 2019 | [Azure Service Fabric 6.5 Liberação de atualização](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Aug 23, 2019 | [Azure Service Fabric 6.5 Liberação de atualização](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 de outubro de 2019 | [Azure Service Fabric 6.5 Liberação de atualização](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Notas de versão](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


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
