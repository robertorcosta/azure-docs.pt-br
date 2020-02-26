---
title: Planejando uma implantação de Sincronização de Arquivos do Azure | Microsoft Docs
description: Saiba o que considerar ao planejar uma implantação de Arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0684f626553946619a0db2cd895df39576bd17b9
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598248"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planejando uma implantação da Sincronização de Arquivos do Azure
Os [arquivos do Azure](storage-files-introduction.md) podem ser implantados de duas maneiras principais: montando diretamente os compartilhamentos de arquivos do Azure sem servidor ou armazenando em cache os compartilhamentos de arquivos do Azure no local usando sincronização de arquivos do Azure. A opção de implantação escolhida altera as coisas que você precisa considerar ao planejar sua implantação. 

- **Montagem direta de um compartilhamento de arquivos do Azure**: como os arquivos do Azure fornecem acesso SMB, você pode montar compartilhamentos de arquivos do Azure localmente ou na nuvem usando o cliente SMB padrão disponível no Windows, no MacOS e no Linux. Como os compartilhamentos de arquivos do Azure são sem servidor, a implantação para cenários de produção não requer o gerenciamento de um servidor de arquivos ou dispositivo NAS. Isso significa que você não precisa aplicar patches de software nem trocar discos físicos. 

- **Armazenar em cache o compartilhamento de arquivos do Azure local com o sincronização de arquivos do Azure**: sincronização de arquivos do Azure permite centralizar os compartilhamentos de arquivos da sua organização em arquivos do Azure, mantendo, ao mesmo tempo, a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos local. Sincronização de Arquivos do Azure transforma um Windows Server local (ou de nuvem) em um cache rápido do seu compartilhamento de arquivos do Azure. 

Este artigo aborda principalmente as considerações de implantação para a implantação de Sincronização de Arquivos do Azure. Para planejar uma implantação de compartilhamentos de arquivos do Azure para ser diretamente montado por um cliente local ou em nuvem, consulte [planejando uma implantação de arquivos do Azure](storage-files-planning.md).

## <a name="management-concepts"></a>Conceitos de gerenciamento
Uma implantação de Sincronização de Arquivos do Azure tem três objetos de gerenciamento fundamentais:

- **Compartilhamento de arquivos do Azure**: um compartilhamento de arquivos do Azure é um compartilhamento de arquivos de nuvem sem servidor, que fornece o *ponto de extremidade de nuvem* de uma relação de sincronização de sincronização de arquivos do Azure. Os arquivos em um compartilhamento de arquivos do Azure podem ser acessados diretamente com o protocolo SMB ou filerest, Embora recomendemos que você acesse principalmente os arquivos por meio do cache do Windows Server quando o compartilhamento de arquivos do Azure está sendo usado com Sincronização de Arquivos do Azure. Isso ocorre porque os arquivos do Azure atualmente não têm um mecanismo de detecção de alteração eficiente, como o Windows Server, portanto, as alterações feitas diretamente no compartilhamento de arquivos do Azure levarão tempo para serem propagadas de volta para os pontos de extremidade do servidor.
- **Ponto de extremidade do servidor**: o caminho no Windows Server que está sendo sincronizado com um compartilhamento de arquivos do Azure. Isso pode ser uma pasta específica em um volume ou a raiz do volume. Vários pontos de extremidade de servidor podem existir no mesmo volume se seus namespaces não se sobrepõem.
- **Grupo de sincronização**: o objeto que define a relação de sincronização entre um **ponto de extremidade de nuvem**, o compartilhamento de arquivos do Azure e um ponto de extremidade do servidor. Os pontos de extremidade em um grupo de sincronização são mantidos em sincronização entre si. Se, por exemplo, você tiver dois conjuntos distintos de arquivos que deseja gerenciar com Sincronização de Arquivos do Azure, você criaria dois grupos de sincronização e adicionará pontos de extremidade diferentes a cada grupo de sincronização.

### <a name="azure-file-share-management-concepts"></a>Conceitos de gerenciamento de compartilhamento de arquivos do Azure
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Conceitos de gerenciamento de Sincronização de Arquivos do Azure
Os grupos de sincronização são implantados nos **serviços de sincronização de armazenamento**, que são objetos de nível superior que registram servidores para uso com sincronização de arquivos do Azure e contêm as relações do grupo de sincronização. O recurso Serviço de Sincronização de Armazenamento é um par do recurso de conta de armazenamento e pode, de maneira semelhante, ser implantado em grupos de recursos do Azure. Um serviço de sincronização de armazenamento pode criar grupos de sincronização que contêm compartilhamentos de arquivos do Azure entre várias contas de armazenamento e vários servidores Windows registrados.

Antes de criar um grupo de sincronização em um serviço de sincronização de armazenamento, primeiro você deve registrar um Windows Server com o serviço de sincronização de armazenamento. Isso cria um objeto de **servidor registrado** , que representa uma relação de confiança entre o servidor ou o cluster e o serviço de sincronização de armazenamento. Para registrar um serviço de sincronização de armazenamento, você deve primeiro instalar o agente de Sincronização de Arquivos do Azure no servidor. Um servidor ou cluster individual pode ser registrado com apenas um serviço de sincronização de armazenamento por vez.

Um grupo de sincronização contém um ponto de extremidade de nuvem, ou compartilhamento de arquivos do Azure, e pelo menos um ponto de extremidade do servidor. O objeto de ponto de extremidade do servidor contém as configurações que configuram o recurso de **camadas de nuvem** , que fornece o recurso de cache do sincronização de arquivos do Azure. Para sincronizar com um compartilhamento de arquivos do Azure, a conta de armazenamento que contém o compartilhamento de arquivos do Azure deve estar na mesma região do Azure que o serviço de sincronização de armazenamento.

### <a name="management-guidance"></a>Diretrizes de gerenciamento
Ao implantar Sincronização de Arquivos do Azure, recomendamos:

- Implantação de compartilhamentos de arquivos do Azure 1:1 com compartilhamentos de arquivos do Windows. O objeto de ponto de extremidade do servidor oferece um grande grau de flexibilidade na forma como você configura a topologia de sincronização no lado do servidor da relação de sincronização. Para simplificar o gerenciamento, faça com que o caminho do ponto de extremidade do servidor corresponda ao caminho do compartilhamento de arquivos do Windows. 

- Use o mínimo possível de serviços de sincronização de armazenamento. Isso simplificará o gerenciamento quando você tiver grupos de sincronização que contêm vários pontos de extremidade de servidor, já que um servidor Windows só pode ser registrado em um serviço de sincronização de armazenamento por vez. 

- Prestar atenção às limitações de IOPS de uma conta de armazenamento ao implantar compartilhamentos de arquivos do Azure. O ideal é que você mapeie os compartilhamentos de arquivos 1:1 com contas de armazenamento. no entanto, isso nem sempre pode ser possível devido a vários limites e restrições, tanto da sua organização quanto do Azure. Quando não for possível ter apenas um compartilhamento de arquivos implantado em uma conta de armazenamento, considere quais compartilhamentos serão altamente ativos e quais compartilhamentos serão menos ativas para garantir que os compartilhamentos de arquivos mais interessantes não sejam colocados na mesma conta de armazenamento.

## <a name="windows-file-server-considerations"></a>Considerações sobre o servidor de arquivos do Windows
Para habilitar o recurso de sincronização no Windows Server, você deve instalar o Sincronização de Arquivos do Azure agente baixável. O agente de Sincronização de Arquivos do Azure fornece dois componentes principais: `FileSyncSvc.exe`, o serviço Windows em segundo plano que é responsável por monitorar alterações nos pontos de extremidade do servidor e iniciar sessões de sincronização, e `StorageSync.sys`, um filtro do sistema de arquivos que habilita a camada de nuvem e a recuperação rápida de desastres.  

### <a name="operating-system-requirements"></a>Requisitos do sistema operacional
Há suporte para Sincronização de Arquivos do Azure com as seguintes versões do Windows Server:

| Versão | SKUs com suporte | Opções de implantação com suporte |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter, padrão e IoT | Completo e central |
| Windows Server 2016 | Datacenter, padrão e servidor de armazenamento | Completo e central |
| Windows Server 2012 R2 | Datacenter, padrão e servidor de armazenamento | Completo e central |

Versões futuras do Windows Server serão adicionadas à medida que forem liberadas.

> [!Important]  
> Recomendamos manter todos os servidores usados com a Sincronização de Arquivos do Azure atualizados com as últimas atualizações do Windows Update. 

### <a name="minimum-system-resources"></a>Recursos mínimos do sistema
O Sincronização de Arquivos do Azure requer um servidor, físico ou virtual, com pelo menos uma CPU e um mínimo de 2 GiB de memória.

> [!Important]  
> Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica habilitada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.

Para a maioria das cargas de trabalho de produção, não recomendamos configurar um servidor de sincronização de Sincronização de Arquivos do Azure com apenas os requisitos mínimos. Consulte [recursos do sistema recomendados](#recommended-system-resources) para obter mais informações.

### <a name="recommended-system-resources"></a>Recursos de sistema recomendados
Assim como qualquer recurso de servidor ou aplicativo, os requisitos de recursos do sistema para Sincronização de Arquivos do Azure são determinados pela escala da implantação; implantações maiores em um servidor exigem mais recursos do sistema. Por Sincronização de Arquivos do Azure, a escala é determinada pelo número de objetos entre os pontos de extremidade do servidor e a rotatividade no conjunto de um. Um único servidor pode ter pontos de extremidade de servidor em vários grupos de sincronização e o número de objetos listados nas contas de tabela a seguir para o namespace completo ao qual um servidor está anexado. 

Por exemplo, ponto de extremidade do servidor A com 10 milhões objetos + ponto de extremidade do servidor B com 10 milhões objetos = 20 milhões objetos. Para essa implantação de exemplo, recomendamos 8 CPUs, 16 GiB de memória para o estado Steady e (se possível) 48 GiB de memória para a migração inicial.
 
Os dados de namespace são armazenados na memória por motivos de desempenho. Por causa disso, namespaces maiores exigem mais memória para manter o bom desempenho e mais rotatividade requer mais CPU para processar. 
 
Na tabela a seguir, fornecemos o tamanho do namespace, bem como uma conversão para capacidade para compartilhamentos de arquivos de uso geral típicos, em que o tamanho médio do arquivo é 512 KiB. Se os tamanhos de arquivo forem menores, considere adicionar memória adicional para a mesma quantidade de capacidade. Baseie sua configuração de memória no tamanho do namespace.

| Tamanho do namespace-arquivos & diretórios (milhões)  | Capacidade típica (TiB)  | Núcleos de CPU  | Memória recomendada (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (sincronização inicial)/2 (variação típica)      |
| 5        | 2.3     | 2        | 16 (sincronização inicial)/4 (rotatividade típica)    |
| 10       | 4.7     | 4        | 32 (sincronização inicial)/8 (variação típica)   |
| 30       | 14.0    | 8        | 48 (sincronização inicial)/16 (variação típica)   |
| 50       | 23,3    | 16       | 64 (sincronização inicial)/32 (variação típica)  |
| 100*     | 46,6    | 32       | 128 (sincronização inicial)/32 (variação típica)  |

\*a sincronização de mais de 100 milhões arquivos & diretórios não é recomendável no momento. Esse é um limite flexível com base em nossos limites testados. Para obter mais informações, consulte [escalabilidade e metas de desempenho dos arquivos do Azure](storage-files-scale-targets.md#azure-file-sync-scale-targets).

> [!TIP]
> A sincronização inicial de um namespace é uma operação intensiva e é recomendável alocar mais memória até que a sincronização inicial seja concluída. Isso não é necessário, mas pode acelerar a sincronização inicial. 
> 
> A rotatividade típica é de 0,5% da alteração do namespace por dia. Para níveis mais altos de variação, considere adicionar mais CPU. 

- Um volume anexado localmente formatado com o sistema de arquivos NTFS.

### <a name="evaluation-cmdlet"></a>Cmdlet de avaliação
Antes de implantar Sincronização de Arquivos do Azure, você deve avaliar se ele é compatível com seu sistema usando o cmdlet de avaliação Sincronização de Arquivos do Azure. Esse cmdlet verifica possíveis problemas com o seu sistema de arquivos e conjunto de banco de arquivo, como caracteres sem suporte ou uma versão do sistema operacional sem suporte. Suas verificações abordam a maioria dos recursos mencionados abaixo, mas não todos eles. Recomendamos que você leia o restante desta seção com cuidado para garantir que sua implantação seja tranqüila. 

O cmdlet Evaluation pode ser instalado instalando o módulo AZ PowerShell, que pode ser instalado seguindo as instruções aqui: [instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Uso  
Você pode invocar a ferramenta de avaliação de algumas maneiras diferentes: você pode executar verificações de sistema, verificações de conjunto de dados ou ambas. Para executar verificações de sistema e de conjunto de dados: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Para testar apenas o conjunto de dados:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Para testar somente os requisitos do sistema:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
Para exibir os resultados em CSV:
```powershell
$errors = Invoke-AzStorageSyncCompatibilityCheck […]
$errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="file-system-compatibility"></a>Compatibilidade do sistema de arquivos
Só há suporte para Sincronização de Arquivos do Azure em volumes NTFS anexados diretamente. O armazenamento com conexão direta, ou DAS, no Windows Server significa que o sistema operacional do Windows Server possui o sistema de arquivos. O DAS pode ser fornecido por meio da anexação física de discos ao servidor de arquivos, anexando discos virtuais a uma VM de servidor de arquivos (como uma VM hospedada pelo Hyper-V) ou até mesmo por meio de ISCSI.

Somente volumes NTFS têm suporte; Não há suporte para ReFS, FAT, FAT32 e outros sistemas de arquivos.

A tabela a seguir mostra o estado de interoperabilidade dos recursos do sistema de arquivos NTFS: 

| Recurso | Status de suporte | Observações |
|---------|----------------|-------|
| ACLs (listas de controle de acesso) | suporte completo | As listas de controle de acesso condicional de estilo do Windows são preservadas por Sincronização de Arquivos do Azure e são impostas pelo Windows Server em pontos de extremidade de servidor. As ACLs também podem ser impostas ao montar diretamente o compartilhamento de arquivos do Azure, no entanto, isso requer configuração adicional. Consulte a [seção identidade](#identity) para obter mais informações. |
| Links físicos | Ignorado | |
| Links simbólicos | Ignorado | |
| Pontos de montagem | Suporte parcial | Pontos de montagem podem ser a raiz de um Ponto de Extremidade de Servidor, mas serão ignorados se estiverem contidos no namespace de um ponto de extremidade de servidor. |
| Junções | Ignorado | Por exemplo, as pastas DFSRoots e DfrsrPrivate do Sistema de Arquivos Distribuído. |
| Pontos de nova análise | Ignorado | |
| Compactação NTFS | suporte completo | |
| Arquivos esparsos | suporte completo | Os arquivos esparsos são sincronizados (não são bloqueados), mas são sincronizados com a nuvem como um arquivo completo. Se o conteúdo do arquivo for alterado na nuvem (ou em outro servidor), o arquivo não será mais esparso quando a alteração for baixada. |
| ADS (Fluxos de Dados Alternativos) | Preservados, mas não sincronizados | Por exemplo, as marcas de classificação criadas pela Infraestrutura de classificação de arquivos não são sincronizadas. As marcas de classificação em arquivos existentes em cada um dos pontos de extremidade do servidor são mantidas. |

<a id="files-skipped"></a>Sincronização de Arquivos do Azure também irá ignorar determinados arquivos temporários e pastas do sistema:

| Arquivo/pasta | Observação |
|-|-|
| pagefile.sys | Arquivo específico do sistema |
| Desktop.ini | Arquivo específico do sistema |
| thumbs.db | Arquivo temporário para miniaturas |
| ehthumbs.db | Arquivo temporário para miniaturas de mídia |
| ~$\*.\* | Arquivo temporário do Office |
| \*.tmp | Arquivo temporário |
| \*.laccdb | Arquivo de bloqueio do banco de dados do Access|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Arquivo de sincronização interno|
| \\Informações de Volume do Sistema | Pasta específica do volume |
| $RECYCLE.BIN| Pasta |
| \\SyncShareState | Pasta para sincronização |

### <a name="failover-clustering"></a>Clustering de failover
O clustering de failover do Windows Server tem suporte pela Sincronização de Arquivo do Azure para a opção de implantação “Servidor de Arquivos de uso geral”. Não há suporte para o Clustering de Failover em “SOFS (Servidor de Arquivos de Escalabilidade Horizontal) para dados de aplicativos” ou CSVs (Volumes Compartilhados Clusterizados).

> [!Note]  
> O agente de Sincronização de Arquivos do Azure deve ser instalado em cada nó em um Cluster de Failover para a sincronização funcionar corretamente.

### <a name="data-deduplication"></a>Eliminação de duplicação de dados
**Windows server 2016 e Windows server 2019**   
A eliminação de duplicação de dados tem suporte em volumes com a camada de nuvem habilitada no Windows Server 2016 e Windows Server 2019. Habilitar a eliminação de duplicação de dados em um volume com camada de nuvem habilitada permite que você armazene em cache mais arquivos localmente sem provisionar mais armazenamento. 

Quando a eliminação de duplicação de dados está habilitada em um volume com disposição em camadas de nuvem habilitada, os arquivos otimizados para eliminação de duplicatas no local do ponto de extremidade do servidor serão em camadas semelhantes a um arquivo normal com base nas configurações de política de camadas de nuvem. Depois que os arquivos otimizados para eliminação de duplicação estiverem em camadas, o trabalho de coleta de lixo de eliminação de duplicatas de dados será executado automaticamente para recuperar espaço em disco removendo partes desnecessárias que não são mais referenciadas por outros arquivos no volume.

Observe que a economia de volume se aplica somente ao servidor; seus dados no compartilhamento de arquivos do Azure não serão eliminação de duplicação.

> [!Note]  
> Para dar suporte à eliminação de duplicação de dados em volumes com camadas de nuvem habilitadas no Windows Server 2019, o Windows Update [KB4520062](https://support.microsoft.com/help/4520062) deve ser instalado e sincronização de arquivos do Azure versão do agente 9.0.0.0 ou mais recente é necessária.

**Windows Server 2012 R2**  
Sincronização de Arquivos do Azure não dá suporte à eliminação de duplicação de dados e à camada de nuvem no mesmo volume no Windows Server 2012 R2. Se a eliminação de duplicação de dados estiver habilitada em um volume, a camada de nuvem deverá ser desabilitada. 

**Observações**
- Se a eliminação de duplicação de dados for instalada antes da instalação do agente de Sincronização de Arquivos do Azure, uma reinicialização será necessária para dar suporte à eliminação de duplicação de dados e à camada de nuvem no mesmo volume.
- Se a eliminação de duplicação de dados estiver habilitada em um volume depois que a disposição em camadas de nuvem estiver habilitada, o trabalho de otimização de eliminação de duplicação inicial otimizará os arquivos no volume que ainda não estão em camadas e terá o seguinte impacto na camada de nuvem:
    - A política de espaço livre continuará a hierarquizar arquivos de acordo com o espaço livre no volume usando o calor.
    - A política de data ignorará a camada de arquivos que, de outra forma, podem ter sido elegíveis para camadas devido ao trabalho de otimização de eliminação de duplicação que acessa os arquivos.
- Para trabalhos de otimização de eliminação de duplicação em andamento, as camadas de nuvem com a política de data serão atrasadas pela configuração [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) de eliminação de duplicação de dados, se o arquivo ainda não estiver em camadas. 
    - Exemplo: se a configuração MinimumFileAgeDays for de sete dias e a política de data de camadas de nuvem for de 30 dias, a política de data terá os arquivos de camada após 37 dias.
    - Observação: quando um arquivo estiver em camadas por Sincronização de Arquivos do Azure, o trabalho de otimização de eliminação de duplicação ignorará o arquivo.
- Se um servidor que executa o Windows Server 2012 R2 com o agente de Sincronização de Arquivos do Azure instalado for atualizado para o Windows Server 2016 ou o Windows Server 2019, as etapas a seguir deverão ser executadas para dar suporte à eliminação de duplicação de dados e à camada de nuvem no mesmo volume:  
    - Desinstale o agente de Sincronização de Arquivos do Azure para Windows Server 2012 R2 e reinicie o servidor.
    - Baixe o agente de Sincronização de Arquivos do Azure para a nova versão do sistema operacional do servidor (Windows Server 2016 ou Windows Server 2019).
    - Instale o agente de Sincronização de Arquivos do Azure e reinicie o servidor.  
    
    Observação: as definições de configuração do Sincronização de Arquivos do Azure no servidor são mantidas quando o agente é desinstalado e reinstalado.

### <a name="distributed-file-system-dfs"></a>DFS (Sistema de Arquivos Distribuído)
A Sincronização de Arquivos do Azure fornece suporte para interoperabilidade com Namespaces de DFS (DFS-N) e Replicação do DFS (DFS-R).

**DFS-N (Namespaces de DFS)** : a Sincronização de arquivos do Azure é totalmente suportada em servidores DFS-N. É possível instalar o agente Sincronização de arquivos do Azure em um ou mais membros DFS-N para sincronizar dados entre os pontos de extremidade de servidor e o ponto de extremidade da nuvem. Para obter mais informações, consulte [visão geral de Namespaces de DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replicação do DFS (DFS-r)** : como o DFS-r e sincronização de arquivos do Azure são soluções de replicação, na maioria dos casos, é recomendável substituir DFS-r por sincronização de arquivos do Azure. No entanto, há vários cenários em que você desejaria usar o DFS-R e Sincronização de Arquivos do Azure juntos:

- Você está migrando de uma implantação de DFS-R para uma implantação de Sincronização de arquivos do Azure. Para obter mais informações, consulte [Migrar uma implantação de DFS-R (Replicação do DFS) para Sincronização de arquivos do Azure](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nem todo servidor local que precisa de uma cópia dos dados do arquivo pode ser conectado diretamente à Internet.
- Os servidor de ramificação consolidam dados em um servidor de hub único, para o qual você gostaria de usar a Sincronização de arquivos do Azure.

Para Sincronização de Arquivos do Azure e o DFS-R funcionar lado a lado:

1. A camada de nuvem da Sincronização de arquivos do Azure deve ser desabilitada em volumes com pastas replicadas DFS-R.
2. Os pontos de extremidade de servidor não devem ser configurados em pastas de replicação somente leitura do DFS-R.

Para obter mais informações, consulte [Visão geral da Replicação do DFS](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
Não há suporte para o uso do Sysprep em um servidor com o agente de Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. A instalação do agente e o registro do servidor devem ocorrer depois da implantação da imagem do servidor e da conclusão da mini-instalação do sysprep.

### <a name="windows-search"></a>Windows Search
Se a opção de camadas em nuvem estiver habilitada em um ponto de extremidade do servidor, os arquivos que estão em camadas serão ignorados e não serão indexados pelo Windows Search. Arquivos sem camadas são indexados corretamente.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Outras soluções de HSM (Gerenciamento de Armazenamento Hierárquico)
Nenhuma outra solução de HSM deve ser usada com a Sincronização de Arquivos do Azure.

## <a name="identity"></a>Identidade
Sincronização de Arquivos do Azure trabalha com sua identidade padrão baseada em AD sem qualquer configuração especial além de configurar a sincronização. Quando você está usando Sincronização de Arquivos do Azure, a expectativa geral é que a maioria dos acessos percorra os servidores de cache Sincronização de Arquivos do Azure, em vez de usar o compartilhamento de arquivos do Azure. Como os pontos de extremidade do servidor estão localizados no Windows Server, e o Windows Server tem suporte para ACLs de estilo do AD e do Windows há muito tempo, nada é necessário além de garantir que os servidores de arquivos do Windows registrados com o serviço de sincronização de armazenamento estejam ingressados no domínio. Sincronização de Arquivos do Azure armazenará ACLs nos arquivos no compartilhamento de arquivos do Azure e os replicará para todos os pontos de extremidade do servidor.

Embora as alterações feitas diretamente no compartilhamento de arquivos do Azure demorem mais para serem sincronizadas com os pontos de extremidade do servidor no grupo de sincronização, você também pode querer garantir que você possa impor suas permissões do AD em seu compartilhamento de arquivos diretamente na nuvem. Para fazer isso, você deve ingressar o domínio em sua conta de armazenamento no AD local, assim como os servidores de arquivos do Windows são ingressados no domínio. Para saber mais sobre o ingresso no domínio de sua conta de armazenamento para um Active Directory de Propriedade do cliente, consulte [visão geral dos arquivos do Azure Active Directory](storage-files-active-directory-overview.md).

> [!Important]  
> Não é necessário ingressar no domínio em sua conta de armazenamento no Active Directory para implantar Sincronização de Arquivos do Azure com êxito. Essa é uma etapa estritamente opcional que permite que o compartilhamento de arquivos do Azure imponha ACLs locais quando os usuários montam o compartilhamento de arquivos do Azure diretamente.

## <a name="networking"></a>Rede
O agente de Sincronização de Arquivos do Azure se comunica com o serviço de sincronização de armazenamento e o compartilhamento de arquivos do Azure usando o protocolo REST Sincronização de Arquivos do Azure e o protocolo filerest, ambos sempre usam HTTPS pela porta 443. O SMB nunca é usado para carregar ou baixar dados entre o Windows Server e o compartilhamento de arquivos do Azure. Como a maioria das organizações permite o tráfego HTTPS pela porta 443, como um requisito para visitar a maioria dos sites, normalmente a configuração de rede especial não é necessária para implantar Sincronização de Arquivos do Azure.

Com base na política da sua organização ou em requisitos regulatórios exclusivos, você pode exigir comunicação mais restritiva com o Azure e, portanto, Sincronização de Arquivos do Azure fornece vários mecanismos para você configurar a rede. Com base em seus requisitos, você pode:

- Túnel de sincronização e carregamento de arquivos/download por meio de seu ExpressRoute ou VPN do Azure. 
- Faça uso de arquivos do Azure e recursos de rede do Azure, como pontos de extremidade de serviço e pontos de extremidade privados.
- Configure Sincronização de Arquivos do Azure para dar suporte ao seu proxy em seu ambiente.
- Limitar a atividade de rede de Sincronização de Arquivos do Azure.

Para saber mais sobre como configurar a funcionalidade de rede do Sincronização de Arquivos do Azure, consulte:
- [Configurações de proxy e firewall da Sincronização de Arquivos do Azure](storage-sync-files-firewall-and-proxy.md)
- [Garantindo Sincronização de Arquivos do Azure é um bom vizinho em seu datacenter](storage-sync-files-server-registration.md)

## <a name="encryption"></a>Criptografia
Ao usar Sincronização de Arquivos do Azure, há três camadas diferentes de criptografia a serem consideradas: criptografia no armazenamento em repouso do Windows Server, criptografia em trânsito entre o agente de Sincronização de Arquivos do Azure e o Azure, e a criptografia em repouso em seus dados no compartilhamento de arquivos do Azure. 

### <a name="windows-server-encryption-at-rest"></a>Criptografia em repouso do Windows Server 
Há duas estratégias para criptografar dados no Windows Server que funcionam geralmente com Sincronização de Arquivos do Azure: criptografia abaixo do sistema de arquivos, de modo que o sistema de arquivos e todos os dados gravados nele sejam criptografados e criptografia no próprio formato de arquivo. Esses métodos não são mutuamente exclusivos; Eles podem ser usados juntos, se desejado, uma vez que a finalidade da criptografia é diferente.

Para fornecer criptografia sob o sistema de arquivos, o Windows Server fornece a caixa de entrada do BitLocker. O BitLocker é totalmente transparente para Sincronização de Arquivos do Azure. O principal motivo para usar um mecanismo de criptografia como o BitLocker é impedir a vazamento física de dados do seu datacenter local por alguém roubar os discos e impedir o Sideload de um sistema operacional não autorizado para executar leituras/gravações não autorizadas em seus dados. Para saber mais sobre o BitLocker, confira [visão geral do BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview).

Os produtos de terceiros que funcionam de forma semelhante ao BitLocker, no que ficam sob o volume NTFS, devem funcionar de forma totalmente transparente com Sincronização de Arquivos do Azure. 

O outro método principal para criptografar dados é criptografar o fluxo de dados do arquivo quando o aplicativo salvar o arquivo. Alguns aplicativos podem fazer isso nativamente, no entanto, geralmente esse não é o caso. Um exemplo de um método para criptografar o fluxo de dados do arquivo é o Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS. O principal motivo para usar um mecanismo de criptografia como o AIP/RMS é impedir vazamento de dados de dados do seu compartilhamento de arquivos por pessoas que os copiam para locais alternativos, como em uma unidade flash ou enviá-los por email para uma pessoa não autorizada. Quando o fluxo de dados de um arquivo é criptografado como parte do formato de arquivo, esse arquivo continuará a ser criptografado no compartilhamento de arquivos do Azure. 

O Sincronização de Arquivos do Azure não interopera com o sistema de arquivos criptografados NTFS (NTFS) ou com soluções de criptografia de terceiros que ficam acima do sistema de arquivos, mas abaixo do fluxo de dados do arquivo. 

### <a name="encryption-in-transit"></a>Criptografia em trânsito
Sincronização de Arquivos do Azure agente se comunica com o serviço de sincronização de armazenamento e o compartilhamento de arquivos do Azure usando o protocolo REST Sincronização de Arquivos do Azure e o protocolo filerest, ambos sempre usam HTTPS pela porta 443. Sincronização de Arquivos do Azure não envia solicitações não criptografadas por HTTP. 

As contas de armazenamento do Azure contêm uma opção para exigir criptografia em trânsito, que é habilitada por padrão. Mesmo que a opção no nível da conta de armazenamento esteja desabilitada, o que significa que as conexões não criptografadas com seus compartilhamentos de arquivos do Azure são possíveis, Sincronização de Arquivos do Azure ainda usará canais criptografados para acessar o compartilhamento de arquivos.

O principal motivo para desabilitar a criptografia em trânsito para a conta de armazenamento é dar suporte a um aplicativo herdado que deve ser executado em um sistema operacional mais antigo, como o Windows Server 2008 R2 ou a distribuição mais antiga do Linux, conversando diretamente com um compartilhamento de arquivos do Azure. Se o aplicativo herdado se comunica com o cache do Windows Server do compartilhamento de arquivos, alternar essa configuração não terá nenhum efeito. 

É altamente recomendável garantir que a criptografia de dados em trânsito esteja habilitada.

Para obter mais informações sobre criptografia em trânsito, consulte [Exigir transferência segura no armazenamento do Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-file-share-encryption-at-rest"></a>Criptografia de compartilhamento de arquivos do Azure em repouso
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Camadas de armazenamento
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Habilitar compartilhamentos de arquivos padrão para abranger até 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Disponibilidade regional
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Disponibilidade da região de sincronização de arquivos do Azure
Sincronização de Arquivos do Azure está disponível nas seguintes regiões:

| Nuvem do Azure | Região geográfica | Região do Azure | Código de região |
|-------------|-------------------|--------------|-------------|
| Público | Ásia | Leste da Ásia | `eastasia` |
| Público | Ásia | Sudeste Asiático | `southeastasia` |
| Público | Austrália | Leste da Austrália | `australiaeast` |
| Público | Austrália | Sudeste da Austrália | `australiasoutheast` |
| Público | Brasil | Sul do Brasil | `brazilsouth` |
| Público | Canada | Canadá Central | `canadacentral` |
| Público | Canada | Leste do Canadá | `canadaeast` |
| Público | Europa | Norte da Europa | `northeurope` |
| Público | Europa | Europa Ocidental | `westeurope` |
| Público | França | França Central | `francecentral` |
| Público | França | Sul da França * | `francesouth` |
| Público | Índia | Índia Central | `centralindia` |
| Público | Índia | Sul da Índia | `southindia` |
| Público | Japão | Leste do Japão | `japaneast` |
| Público | Japão | Oeste do Japão | `japanwest` |
| Público | Coreia do Sul | Coreia Central | `koreacentral` |
| Público | Coreia do Sul | Sul da Coreia | `koreasouth` |
| Público | África do Sul | Norte da África do Sul | `southafricanorth` |
| Público | África do Sul | Oeste da África do Sul * | `southafricawest` |
| Público | EAU | EAU Central * | `uaecentral` |
| Público | EAU | Norte dos EAU | `uaenorth` |
| Público | Reino Unido | Sul do Reino Unido | `uksouth` |
| Público | Reino Unido | Oeste do Reino Unido | `ukwest` |
| Público | EUA | Centro dos EUA | `centralus` |
| Público | EUA | Leste dos EUA | `eastus` |
| Público | EUA | Leste dos EUA 2 | `eastus2` |
| Público | EUA | Centro-Norte dos EUA | `northcentralus` |
| Público | EUA | Centro-Sul dos Estados Unidos | `southcentralus` |
| Público | EUA | Centro-Oeste dos EUA | `westcentralus` |
| Público | EUA | Oeste dos EUA | `westus` |
| Público | EUA | Oeste dos EUA 2 | `westus2` |
| Gov dos EUA | EUA | Governo dos EUA do Arizona | `usgovarizona` |
| Gov dos EUA | EUA | Governo dos EUA do Texas | `usgovtexas` |
| Gov dos EUA | EUA | Gov. dos EUA – Virgínia | `usgovvirginia` |

A Sincronização de Arquivos do Azure é compatível apenas com um compartilhamento de arquivo do Azure que esteja na mesma região que o Serviço de Sincronização de Armazenamento.

Para as regiões marcadas com asteriscos, você deve contatar o suporte do Azure para solicitar acesso ao armazenamento do Azure nessas regiões. O processo é descrito neste [documento](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="redundancy"></a>Redundância
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> O armazenamento com redundância geográfica e de zona geográfica tem a capacidade de fazer failover manual do armazenamento para a região secundária. É recomendável que você não faça isso fora de um desastre quando estiver usando Sincronização de Arquivos do Azure devido à maior probabilidade de perda de dados. No caso de um desastre em que você gostaria de iniciar um failover manual de armazenamento, será necessário abrir um caso de suporte com a Microsoft para obter Sincronização de Arquivos do Azure para retomar a sincronização com o ponto de extremidade secundário.

## <a name="migration"></a>Migração
Se você tiver um servidor de arquivos do Windows existente, Sincronização de Arquivos do Azure poderá ser instalado diretamente no local, sem a necessidade de mover dados para um novo servidor. Se você estiver planejando migrar para um novo servidor de arquivos do Windows como parte da adoção de Sincronização de Arquivos do Azure, há várias abordagens possíveis para mover dados:

- Crie pontos de extremidade do servidor para o compartilhamento de arquivos antigo e o novo compartilhamento de arquivos e permita que Sincronização de Arquivos do Azure sincronize os dados entre os pontos de extremidade do servidor. A vantagem dessa abordagem é que ela facilita muito a assinatura do armazenamento em seu novo servidor de arquivos, pois Sincronização de Arquivos do Azure é um reconhecimento em camadas de nuvem. Quando estiver pronto, você poderá recortar os usuários finais para o compartilhamento de arquivos no novo servidor e remover o ponto de extremidade do servidor do compartilhamento de arquivos antigo.

- Crie um ponto de extremidade do servidor somente no novo servidor de arquivos e copie dados para o compartilhamento de arquivos antigo usando `robocopy`. Dependendo da topologia dos compartilhamentos de arquivos no novo servidor (Quantos compartilhamentos você tem em cada volume, como a liberação de cada volume é, etc.), talvez seja necessário provisionar temporariamente o armazenamento adicional, pois espera-se que `robocopy` de seu servidor antigo para o novo servidor no datacenter local será concluída mais rápido do que Sincronização de Arquivos do Azure moverá os dados para o Azure.

Também é possível usar Data Box para migrar dados para uma implantação Sincronização de Arquivos do Azure. Na maioria das vezes, quando os clientes desejam usar Data Box para ingerir dados, eles fazem isso porque acham que aumentarão a velocidade de sua implantação ou, pois isso ajudará com cenários de largura de banda restritas. Embora seja verdade que usar um Data Box para ingerir dados em sua implantação de Sincronização de Arquivos do Azure diminuirá a utilização da largura de banda, provavelmente será mais rápido para a maioria dos cenários buscar um carregamento de dados online por meio de um dos métodos descritos acima. Para saber mais sobre como usar Data Box para ingerir dados em sua implantação de Sincronização de Arquivos do Azure, consulte [migrar dados para sincronização de arquivos do Azure com Azure data Box](storage-sync-offline-data-transfer.md).

Um erro comum que os clientes fazem ao migrar dados para sua nova implantação de Sincronização de Arquivos do Azure é copiar dados diretamente para o compartilhamento de arquivos do Azure, em vez de em seus servidores de arquivos do Windows. Embora Sincronização de Arquivos do Azure identifique todos os novos arquivos no compartilhamento de arquivos do Azure e sincronize-os de volta para seus compartilhamentos de arquivos do Windows, isso geralmente é consideravelmente mais lento do que carregar dados por meio do servidor de arquivos do Windows. Muitas ferramentas de cópia do Azure, como AzCopy, têm a desvantagem adicional de não copiar todos os metadados importantes de um arquivo, como carimbos de data/hora e ACLs.

## <a name="antivirus"></a>Antivírus
Como os antivírus funcionam com o exame de arquivos em busca de códigos mal-intencionados conhecidos, um antivírus pode causar o recall de arquivos em camadas. Nas versões 4.0 e acima do agente de Sincronização de Arquivo do Azure, arquivos em camadas têm o conjunto FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS de atributo seguro do Windows. Recomendamos consultar o fornecedor do software para saber como configurar a solução para ignorar a leitura de arquivos com esse conjunto de atributos (muitos fazem isso automaticamente). 

As soluções antivírus internas da Microsoft, o Windows Defender e o System Center Endpoint Protection (SCEP), ignoram automaticamente a leitura de arquivos que possuem esse atributo definido. Nós os testamos e identificamos um problema menor: quando você adiciona um servidor a um grupo de sincronização existente, os arquivos com menos de 800 bytes são recuperados (feitos o download) no novo servidor. Esses arquivos permanecerão no novo servidor e não serão colocados em camadas, pois não atendem ao requisito de tamanho em camadas (> 64kb).

> [!Note]  
> Os fornecedores de antivírus podem verificar a compatibilidade entre seus produtos e Sincronização de Arquivos do Azure usando o [sincronização de arquivos do Azure antivírus Compatibility Suite](https://www.microsoft.com/download/details.aspx?id=58322), que está disponível para download no centro de download da Microsoft.

## <a name="backup"></a>Backup 
Como as soluções de antivírus, as soluções de backup podem causar o recall de arquivos em camadas. Recomendamos o uso de uma solução de backup de nuvem para fazer backup do compartilhamento do arquivos do Azure, em vez de um produto de backup local.

Se você estiver usando uma solução de backup local, os backups devem ser executados em um servidor no grupo de sincronização que tem a camada de nuvem desabilitada. Ao executar uma restauração, use as opções de restauração no nível do volume ou no nível do arquivo. Os arquivos restaurados usando a opção de restauração no nível do arquivo serão sincronizados com todos os pontos de extremidade no grupo de sincronização e os arquivos existentes serão substituídos pela versão restaurada do backup.  As restaurações no nível de volume não substituirão as versões de arquivo mais recentes no compartilhamento de arquivos do Azure ou em outros pontos de extremidade do servidor.

> [!Note]  
> A restauração bare-metal (BMR) pode causar resultados inesperados e não é atualmente suportada.

> [!Note]  
> Com a versão 9 do agente de Sincronização de Arquivos do Azure, os instantâneos do VSS (incluindo a guia versões anteriores) agora têm suporte em volumes que têm a camada de nuvem habilitada. No entanto, você deve habilitar a compatibilidade de versão anterior por meio do PowerShell. [Saiba como](storage-files-deployment-guide.md).

## <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente de Sincronização de Arquivo do Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Próximas etapas
* [Considere as configurações de firewall e proxy](storage-sync-files-firewall-and-proxy.md)
* [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)
* [Implantar os Arquivos do Azure](storage-files-deployment-guide.md)
* [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
* [Monitorar a Sincronização de Arquivos do Azure](storage-sync-files-monitoring.md)