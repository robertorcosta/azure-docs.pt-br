---
title: Planejando uma implantação de Sincronização de Arquivos do Azure | Microsoft Docs
description: Saiba o que considerar ao planejar uma implantação de Arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8666f51b88d2a70a2cb27e3606f24010771c8017
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460691"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planejando uma implantação da Sincronização de Arquivos do Azure

:::row:::
    :::column:::
        [![Entrevista e demonstração apresentando Azure File Sync - clique para reproduzir!](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        O Azure File Sync é um serviço que permite fazer um cache de vários compartilhamentos de arquivos do Azure em um Windows Server ou VM na nuvem no local. 
        
        Este artigo apresenta conceitos e recursos do Azure File Sync. Uma vez que você esteja familiarizado com o Azure File Sync, considere seguir o [guia de implantação do Azure File Sync](storage-sync-files-deployment-guide.md) para testar este serviço.        
    :::column-end:::
:::row-end:::

Os arquivos serão armazenados na nuvem em [compartilhamentos de arquivos do Azure](storage-files-introduction.md). Os compartilhamentos de arquivos do Azure podem ser usados de duas maneiras: montando diretamente esses compartilhamentos de arquivos Azure sem servidor (SMB) ou arquivar compartilhamentos de arquivos do Azure no local usando o Azure File Sync. Qual opção de implantação você escolhe altera os aspectos que você precisa considerar como você planeja para sua implantação. 

- **Montagem direta de um compartilhamento de arquivos Azure**: Como o Azure Files fornece acesso ao SMB, você pode montar compartilhamentos de arquivos Do Zure no local ou na nuvem usando o cliente SMB padrão disponível no Windows, macOS e Linux. Como os compartilhamentos de arquivos do Azure não têm servidor, a implantação para cenários de produção não requer o gerenciamento de um servidor de arquivos ou dispositivo NAS. Isso significa que você não precisa aplicar patches de software ou trocar discos físicos. 

- **O compartilhamento de arquivos do Cache Azure no local com o Azure File Sync**: O Azure File Sync permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos Azure, mantendo a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos no local. O Azure File Sync transforma um Windows Server no local (ou na nuvem) em um cache rápido do seu compartilhamento de arquivos Azure. 

## <a name="management-concepts"></a>Conceitos de gerenciamento
Uma implantação do Azure File Sync tem três objetos de gerenciamento fundamentais:

- **Compartilhamento de arquivos Azure**: Um compartilhamento de arquivos Azure é um compartilhamento de arquivos na nuvem sem servidor, que fornece o *ponto final* da nuvem de uma relação de sincronização de sincronia de arquivos Do Azure. Os arquivos em um compartilhamento de arquivos do Azure podem ser acessados diretamente com o SMB ou o protocolo FileREST, embora o encorajemos a acessar principalmente os arquivos através do cache do Windows Server quando o compartilhamento de arquivos do Azure estiver sendo usado com o Azure File Sync. Isso porque o Azure Files hoje não possui um mecanismo eficiente de detecção de alterações como o Windows Server tem, então as alterações no compartilhamento de arquivos do Azure diretamente levarão tempo para se propagar de volta aos pontos finais do servidor.
- **Ponto final do servidor**: O caminho no Servidor Windows que está sendo sincronizado com um compartilhamento de arquivos Do Zure. Esta pode ser uma pasta específica em um volume ou na raiz do volume. Vários pontos finais do servidor podem existir no mesmo volume se seus namespaces não se sobreporem.
- **Grupo de sincronização**: O objeto que define a relação de sincronização entre um **ponto final de nuvem**ou compartilhamento de arquivo Azure e um ponto final do servidor. Os pontos de extremidade em um grupo de sincronização são mantidos em sincronização entre si. Se, por exemplo, você tiver dois conjuntos distintos de arquivos que deseja gerenciar com o Azure File Sync, você criará dois grupos de sincronização e adicionará pontos finais diferentes a cada grupo de sincronização.

### <a name="azure-file-share-management-concepts"></a>Conceitos de gerenciamento de compartilhamento de arquivos do Azure
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Conceitos de gerenciamento do Azure File Sync
Os grupos de sincronização são implantados no **Storage Sync Services**, que são objetos de nível superior que registram servidores para uso com o Azure File Sync e contêm as relações de grupo de sincronização. O recurso Serviço de Sincronização de Armazenamento é um par do recurso de conta de armazenamento e pode, de maneira semelhante, ser implantado em grupos de recursos do Azure. Um Serviço de Sincronização de Armazenamento pode criar grupos de sincronização que contenham compartilhamentos de arquivos Do Zure em várias contas de armazenamento e vários servidores Windows registrados.

Antes de criar um grupo de sincronização em um Serviço de Sincronização de Armazenamento, primeiro você deve registrar um Servidor Windows com o Serviço de Sincronização de Armazenamento. Isso cria um objeto **de servidor registrado,** que representa uma relação de confiança entre seu servidor ou cluster e o Serviço de Sincronização de Armazenamento. Para registrar um Serviço de Sincronização de Armazenamento, primeiro você deve instalar o agente Azure File Sync no servidor. Um servidor ou cluster individual pode ser registrado com apenas um Serviço de Sincronização de Armazenamento por vez.

Um grupo de sincronização contém um ponto final na nuvem ou compartilhamento de arquivos Do Zure e pelo menos um ponto final do servidor. O objeto ponto final do servidor contém as configurações que configuram o recurso **de hierarquida de nuvem,** que fornece o recurso de cache do Azure File Sync. Para sincronizar com um compartilhamento de arquivos Do Zure, a conta de armazenamento que contém o compartilhamento de arquivos Do Zure deve estar na mesma região do Azure que o Serviço de Sincronização de Armazenamento.

### <a name="management-guidance"></a>Orientação gerencial
Ao implantar o Azure File Sync, recomendamos:

- Implantando compartilhamentos de arquivos Azure 1:1 com compartilhamentos de arquivos do Windows. O objeto ponto final do servidor oferece um grande grau de flexibilidade sobre como você configura a topologia de sincronização no lado do servidor da relação de sincronização. Para simplificar o gerenciamento, faça com que o caminho do ponto final do servidor corresponda ao caminho do compartilhamento de arquivos do Windows. 

- Use o menor número possível de serviços de sincronização de armazenamento. Isso simplificará o gerenciamento quando você tiver grupos de sincronização que contenham vários pontos finais do servidor, uma vez que um Servidor Windows só pode ser registrado em um Serviço de Sincronização de Armazenamento por vez. 

- Prestando atenção às limitações de IOPS de uma conta de armazenamento ao implantar compartilhamentos de arquivos DoZure. Idealmente, você mapearia compartilhamentos de arquivos 1:1 com contas de armazenamento, no entanto, isso pode nem sempre ser possível devido a vários limites e restrições, tanto da sua organização quanto do Azure. Quando não for possível ter apenas um compartilhamento de arquivo implantado em uma conta de armazenamento, considere quais ações serão altamente ativas e quais ações serão menos ativas para garantir que as ações de arquivo mais quentes não sejam colocadas na mesma conta de armazenamento juntas.

## <a name="windows-file-server-considerations"></a>Considerações do servidor de arquivos do Windows
Para habilitar o recurso de sincronização no Windows Server, você deve instalar o agente para download do Azure File Sync. O agente Azure File Sync fornece `FileSyncSvc.exe`dois componentes principais: o serviço de fundo do Windows que é `StorageSync.sys`responsável por monitorar alterações nos pontos finais do servidor e início de sessões de sincronização, e , um filtro de sistema de arquivos que permite hierarquiagem na nuvem e recuperação rápida de desastres.  

### <a name="operating-system-requirements"></a>Requisitos do sistema operacional
O Azure File Sync é suportado com as seguintes versões do Windows Server:

| Versão | SKUs com suporte | Opções de implantação com suporte |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter, Standard e IoT | Full and Core |
| Windows Server 2016 | Servidor de datacenter, padrão e armazenamento | Full and Core |
| Windows Server 2012 R2 | Servidor de datacenter, padrão e armazenamento | Full and Core |

Versões futuras do Windows Server serão adicionadas à medida que forem liberadas.

> [!Important]  
> Recomendamos manter todos os servidores usados com a Sincronização de Arquivos do Azure atualizados com as últimas atualizações do Windows Update. 

### <a name="minimum-system-resources"></a>Recursos mínimos do sistema
O Azure File Sync requer um servidor, físico ou virtual, com pelo menos uma CPU e um mínimo de 2 GiB de memória.

> [!Important]  
> Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deve ser configurada com um mínimo de 2048 MiB de memória.

Para a maioria das cargas de trabalho de produção, não recomendamos configurar um servidor de sincronização de arquivos Azure com apenas os requisitos mínimos. Consulte [os recursos recomendados do sistema](#recommended-system-resources) para obter mais informações.

### <a name="recommended-system-resources"></a>Recursos recomendados do sistema
Assim como qualquer recurso ou aplicativo do servidor, os requisitos de recursos do sistema para o Azure File Sync são determinados pela escala da implantação; implantações maiores em um servidor requerem maiores recursos do sistema. Para o Azure File Sync, a escala é determinada pelo número de objetos nos pontos finais do servidor e pelo churn no conjunto de dados. Um único servidor pode ter pontos finais do servidor em vários grupos de sincronização e o número de objetos listados na tabela a seguir conta para o namespace completo ao que um servidor está conectado. 

Por exemplo, ponto final do servidor A com 10 milhões de objetos + ponto final do servidor B com 10 milhões de objetos = 20 milhões de objetos. Para essa implantação por exemplo, recomendamos 8 CPUs, 16 GiB de memória para estado estável e (se possível) 48 GiB de memória para a migração inicial.
 
Os dados do namespace são armazenados na memória por razões de desempenho. Por causa disso, espaços de nomes maiores requerem mais memória para manter um bom desempenho, e mais churn requer mais CPU para processar. 
 
Na tabela a seguir, fornecemos tanto o tamanho do namespace como uma conversão para capacidade para compartilhamentos típicos de arquivos de propósito geral, onde o tamanho médio do arquivo é de 512 KiB. Se os tamanhos dos arquivos forem menores, considere adicionar memória adicional para a mesma quantidade de capacidade. Baseie sua configuração de memória no tamanho do namespace.

| Tamanho do namespace - arquivos & diretórios (milhões)  | Capacidade típica (TiB)  | Núcleos de CPU  | Memória recomendada (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (sincronização inicial)/ 2 (churn típico)      |
| 5        | 2.3     | 2        | 16 (sincronização inicial)/ 4 (churn típico)    |
| 10       | 4.7     | 4        | 32 (sincronização inicial)/ 8 (churn típico)   |
| 30       | 14.0    | 8        | 48 (sincronização inicial)/ 16 (churn típico)   |
| 50       | 23.3    | 16       | 64 (sincronização inicial)/ 32 (churn típico)  |
| 100*     | 46.6    | 32       | 128 (sincronização inicial)/ 32 (churn típico)  |

\*Sincronizar mais de 100 milhões de arquivos & diretórios não é recomendado no momento. Este é um limite suave baseado em nossos limiares testados. Para obter mais informações, consulte [as metas de escalabilidade e desempenho do Azure Files](storage-files-scale-targets.md#azure-file-sync-scale-targets).

> [!TIP]
> A sincronização inicial de um namespace é uma operação intensiva e recomendamos alocar mais memória até que a sincronização inicial seja concluída. Isso não é necessário, mas.pode acelerar a sincronização inicial. 
> 
> Churn típico é 0,5% da alteração do namespace por dia. Para níveis mais altos de churn, considere adicionar mais CPU. 

- Um volume anexado localmente formatado com o sistema de arquivos NTFS.

### <a name="evaluation-cmdlet"></a>Cmdlet de avaliação
Antes de implantar o Azure File Sync, você deve avaliar se ele é compatível com o seu sistema usando o cmdlet de avaliação do Azure File Sync. Este cmdlet verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão do sistema operacional sem suporte. Suas verificações cobrem a maioria, mas não todas as características mencionadas abaixo; recomendamos que você leia o resto desta seção cuidadosamente para garantir que sua implantação vá sem problemas. 

O cmdlet de avaliação pode ser instalado instalando o módulo Az PowerShell, que pode ser instalado seguindo as instruções aqui: [Instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

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
O Azure File Sync só é suportado diretamente em volumes NTFS. Armazenamento conectado direto, ou DAS, no Windows Server significa que o sistema operacional Windows Server possui o sistema de arquivos. O DAS pode ser fornecido através da fixação física de discos ao servidor de arquivos, anexando discos virtuais a uma VM do servidor de arquivos (como uma VM hospedada pelo Hyper-V), ou mesmo através do ISCSI.

Apenas os volumes NTFS são suportados; ReFS, FAT, FAT32 e outros sistemas de arquivos não são suportados.

A tabela a seguir mostra o estado interop dos recursos do sistema de arquivos NTFS: 

| Recurso | Status de suporte | Observações |
|---------|----------------|-------|
| ACLs (listas de controle de acesso) | suporte completo | As listas de controle de acesso discricionárias no estilo Windows são preservadas pelo Azure File Sync e são aplicadas pelo Windows Server em pontos finais do servidor. As ACLs também podem ser aplicadas ao montar diretamente o compartilhamento de arquivos Do Zure, no entanto, isso requer configuração adicional. Consulte a [seção Identidade](#identity) para obter mais informações. |
| Links físicos | Ignorado | |
| Links simbólicos | Ignorado | |
| Pontos de montagem | Suporte parcial | Pontos de montagem podem ser a raiz de um Ponto de Extremidade de Servidor, mas serão ignorados se estiverem contidos no namespace de um ponto de extremidade de servidor. |
| Junções | Ignorado | Por exemplo, as pastas DFSRoots e DfrsrPrivate do Sistema de Arquivos Distribuído. |
| Pontos de nova análise | Ignorado | |
| Compactação NTFS | suporte completo | |
| Arquivos esparsos | suporte completo | Os arquivos esparsos são sincronizados (não são bloqueados), mas são sincronizados com a nuvem como um arquivo completo. Se o conteúdo do arquivo for alterado na nuvem (ou em outro servidor), o arquivo não será mais esparso quando a alteração for baixada. |
| ADS (Fluxos de Dados Alternativos) | Preservados, mas não sincronizados | Por exemplo, as marcas de classificação criadas pela Infraestrutura de classificação de arquivos não são sincronizadas. As marcas de classificação em arquivos existentes em cada um dos pontos de extremidade do servidor são mantidas. |

<a id="files-skipped"></a>O Azure File Sync também pulará certos arquivos temporários e pastas do sistema:

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
**Windows Server 2016 e Windows Server 2019**   
A eliminação de duplicação de dados tem suporte em volumes com a camada de nuvem habilitada no Windows Server 2016 e Windows Server 2019. A habilitação da duplicação de dados em um volume com hierarquização na nuvem habilitada permite que você faça cache de mais arquivos no local sem armazenar mais armazenamento. 

Quando a deduplicação de dados estiver ativada em um volume com o hierarquidamento em nuvem ativado, os arquivos otimizados do Dedup dentro do local do ponto final do servidor serão hierarquizados semelhantes a um arquivo normal com base nas configurações de diretiva de hierarnada na nuvem. Uma vez que os arquivos otimizados do Dedup tenham sido hierarquizados, o trabalho de coleta de lixo de eliminação de dados será executado automaticamente para recuperar o espaço do disco, removendo pedaços desnecessários que não são mais referenciados por outros arquivos no volume.

Observe que a economia de volume só se aplica ao servidor; seus dados no compartilhamento de arquivos do Azure não serão dissérterios.

> [!Note]  
> Para suportar a deduplicação de dados em volumes com hierarquantes na nuvem ativados no Windows Server 2019, a atualização do Windows [KB4520062](https://support.microsoft.com/help/4520062) deve ser instalada e o agente azure File Sync versão 9.0.0.0 ou mais recente é necessário.

**Windows Server 2012 R2**  
O Azure File Sync não suporta duplicação de dados e hierarquididade na nuvem no mesmo volume no Windows Server 2012 R2. Se a deduplicação de dados estiver ativada em um volume, a hierarquida de nuvem deve ser desativada. 

**Observações**
- Se a deduplicação de dados for instalada antes de instalar o agente Azure File Sync, será necessária uma reinicialização para suportar a deduplicação de dados e a hierarquiçação da nuvem no mesmo volume.
- Se a deduplicação de dados estiver ativada em um volume após a hierarquiagem na nuvem ser habilitada, o trabalho inicial de otimização de duplicação otimizará arquivos no volume que ainda não estão hierárquicos e terá o seguinte impacto na hierarquiagem da nuvem:
    - A política de espaço livre continuará a tier files de acordo com o espaço livre no volume usando o mapa de calor.
    - A política de data salta a hierarquica dos arquivos que podem ter sido elegíveis para hierarquiagem devido ao trabalho de otimização de duplicação acessando os arquivos.
- Para trabalhos contínuos de otimização de duplicação, a hierarcada em nuvem com a política de datas será adiada pela configuração [Minimum Deduplication MinimumFileAgeDays,](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) se o arquivo ainda não estiver hierárquico. 
    - Exemplo: Se a configuração MinimumFileAgeDays for de sete dias e a política de data de hierarcada na nuvem for de 30 dias, a diretiva de data irá fazer camadas de arquivos após 37 dias.
    - Nota: Uma vez que um arquivo é hierarquizado pelo Azure File Sync, o trabalho de otimização de duplicação pulará o arquivo.
- Se um servidor executando o Windows Server 2012 R2 com o agente Azure File Sync instalado for atualizado para o Windows Server 2016 ou o Windows Server 2019, as seguintes etapas devem ser executadas para suportar a duplicação de dados e a hierarquação na nuvem no mesmo volume:  
    - Desinstale o agente Azure File Sync para O Windows Server 2012 R2 e reinicie o servidor.
    - Baixe o agente Azure File Sync para a nova versão do sistema operacional do servidor (Windows Server 2016 ou Windows Server 2019).
    - Instale o agente Azure File Sync e reinicie o servidor.  
    
    Nota: As configurações de sincronização de arquivos do Azure no servidor são retidas quando o agente é desinstalado e reinstalado.

### <a name="distributed-file-system-dfs"></a>DFS (Sistema de Arquivos Distribuído)
A Sincronização de Arquivos do Azure fornece suporte para interoperabilidade com Namespaces de DFS (DFS-N) e Replicação do DFS (DFS-R).

**DFS-N (Namespaces de DFS)**: a Sincronização de arquivos do Azure é totalmente suportada em servidores DFS-N. É possível instalar o agente Sincronização de arquivos do Azure em um ou mais membros DFS-N para sincronizar dados entre os pontos de extremidade de servidor e o ponto de extremidade da nuvem. Para obter mais informações, consulte [visão geral de Namespaces de DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**DFS Replicação (DFS-R)**: Uma vez que o DFS-R e o Azure File Sync são ambas soluções de replicação, na maioria dos casos, recomendamos substituir o DFS-R pelo Azure File Sync. Existem, no entanto, vários cenários em que você gostaria de usar o DFS-R e o Azure File Sync juntos:

- Você está migrando de uma implantação de DFS-R para uma implantação de Sincronização de arquivos do Azure. Para obter mais informações, consulte [Migrar uma implantação de DFS-R (Replicação do DFS) para Sincronização de arquivos do Azure](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nem todo servidor local que precisa de uma cópia dos dados do seu arquivo pode ser conectado diretamente à internet.
- Os servidor de ramificação consolidam dados em um servidor de hub único, para o qual você gostaria de usar a Sincronização de arquivos do Azure.

Para que o Azure File Sync e o DFS-R funcionem lado a lado:

1. A camada de nuvem da Sincronização de arquivos do Azure deve ser desabilitada em volumes com pastas replicadas DFS-R.
2. Os pontos de extremidade de servidor não devem ser configurados em pastas de replicação somente leitura do DFS-R.

Para obter mais informações, consulte [Visão geral da Replicação do DFS](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
O uso do sysprep em um servidor que tenha o agente Azure File Sync instalado não é suportado e pode levar a resultados inesperados. A instalação do agente e o registro do servidor devem ocorrer depois da implantação da imagem do servidor e da conclusão da mini-instalação do sysprep.

### <a name="windows-search"></a>Windows Search
Se a opção de camadas em nuvem estiver habilitada em um ponto de extremidade do servidor, os arquivos que estão em camadas serão ignorados e não serão indexados pelo Windows Search. Arquivos sem camadas são indexados corretamente.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Outras soluções de HSM (Gerenciamento de Armazenamento Hierárquico)
Nenhuma outra solução de HSM deve ser usada com a Sincronização de Arquivos do Azure.

## <a name="identity"></a>Identidade
O Azure File Sync funciona com sua identidade padrão baseada em Anúncios sem qualquer configuração especial além da configuração de sincronização. Quando você está usando o Azure File Sync, a expectativa geral é que a maioria dos acessos passe pelos servidores de cache do Azure File Sync, em vez de através do compartilhamento de arquivos do Azure. Como os pontos finais do servidor estão localizados no Windows Server, e o Windows Server tem suportado AD e ACLs no estilo Windows por muito tempo, nada é necessário além de garantir que os servidores de arquivos do Windows registrados no Serviço de Sincronização de Armazenamento sejam unidos por domínio. O Azure File Sync armazenará ACLs nos arquivos no compartilhamento de arquivos do Azure e os replicará em todos os pontos finais do servidor.

Mesmo que as alterações feitas diretamente no compartilhamento de arquivos do Azure levem mais tempo para sincronizar com os pontos finais do servidor no grupo de sincronização, você também pode querer garantir que você possa impor suas permissões de AD no seu compartilhamento de arquivos diretamente na nuvem também. Para fazer isso, você deve participar de sua conta de armazenamento para o Seu AD no local, assim como seus servidores de arquivos do Windows são unidos por domínio. Para saber mais sobre o domínio que adere sua conta de armazenamento a um Active Directory de propriedade do cliente, consulte [a visão geral do Azure Files Active Directory](storage-files-active-directory-overview.md).

> [!Important]  
> O domínio que a de juntar sua conta de armazenamento ao Active Directory não é necessário para implantar com sucesso o Azure File Sync. Esta é uma etapa estritamente opcional que permite que o compartilhamento de arquivos Do Azure aplique ACLs no local quando os usuários montam o compartilhamento de arquivos Do Zure diretamente.

## <a name="networking"></a>Rede
O agente Azure File Sync se comunica com o seu serviço de sincronização de armazenamento e o compartilhamento de arquivos Azure usando o protocolo Azure File Sync REST e o protocolo FileREST, ambos sempre usam HTTPS sobre a porta 443. O SMB nunca é usado para carregar ou baixar dados entre o Windows Server e o compartilhamento de arquivos do Azure. Como a maioria das organizações permite o tráfego HTTPS sobre a porta 443, como um requisito para visitar a maioria dos sites, a configuração de rede especial geralmente não é necessária para implantar o Azure File Sync.

Com base na política da sua organização ou nos requisitos regulamentares exclusivos, você pode exigir uma comunicação mais restritiva com o Azure e, portanto, o Azure File Sync fornece vários mecanismos para configurar a rede. Com base em suas necessidades, você pode:

- Sincronização de túneis e tráfego de upload/download de arquivos sobre seu ExpressRoute ou Azure VPN. 
- Faça uso de arquivos Azure e recursos de rede do Azure, como pontos finais de serviço e pontos finais privados.
- Configure o Azure File Sync para suportar seu proxy no ambiente.
- Acelera a atividade da rede do Azure File Sync.

Para saber mais sobre a configuração da funcionalidade de rede do Azure File Sync, consulte:
- [Configurações de proxy e firewall da Sincronização de Arquivos do Azure](storage-sync-files-firewall-and-proxy.md)
- [Garantindo que a Sincronização de arquivos do Azure seja uma boa vizinha no seu data center](storage-sync-files-server-registration.md)

## <a name="encryption"></a>Criptografia
Ao usar o Azure File Sync, existem três camadas diferentes de criptografia a considerar: criptografia no armazenamento em repouso do Windows Server, criptografia em trânsito entre o agente Azure File Sync e o Azure e a criptografia no resto de seus dados no compartilhamento de arquivos do Azure. 

### <a name="windows-server-encryption-at-rest"></a>Criptografia do Windows Server em repouso 
Existem duas estratégias para criptografar dados no Windows Server que funcionam geralmente com o Azure File Sync: criptografia sob o sistema de arquivos, de tal forma que o sistema de arquivos e todos os dados gravados para ele sejam criptografados e criptografia dentro do próprio formato de arquivo. Esses métodos não são mutuamente exclusivos; eles podem ser usados juntos, se desejado, uma vez que o propósito da criptografia é diferente.

Para fornecer criptografia abaixo do sistema de arquivos, o Windows Server fornece a caixa de entrada BitLocker. O BitLocker é totalmente transparente para o Azure File Sync. A principal razão para usar um mecanismo de criptografia como o BitLocker é evitar a exfiltração física de dados do seu datacenter local por alguém que rouba os discos e evitar que o carregamento lateral de um sistema operacional não autorizado realize leituras/gravações não autorizadas aos seus dados. Para saber mais sobre o BitLocker, consulte [visão geral do BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview).

Produtos de terceiros que funcionam de forma semelhante ao BitLocker, na qual se sentam abaixo do volume NTFS, devem funcionar de forma totalmente transparente com o Azure File Sync. 

O outro método principal para criptografar dados é criptografar o fluxo de dados do arquivo quando o aplicativo salva o arquivo. Alguns aplicativos podem fazer isso nativamente, no entanto, este geralmente não é o caso. Um exemplo de método para criptografar o fluxo de dados do arquivo é o AIP (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS. A principal razão para usar um mecanismo de criptografia como o AIP/RMS é impedir a exfiltração de dados do seu compartilhamento de arquivos por pessoas que os copiam para locais alternativos, como uma unidade flash ou e-mail para uma pessoa não autorizada. Quando o fluxo de dados de um arquivo é criptografado como parte do formato do arquivo, este arquivo continuará a ser criptografado no compartilhamento de arquivos do Azure. 

O Azure File Sync não interopera com o NTFS Encrypted File System (NTFS EFS) ou com soluções de criptografia de terceiros que estão acima do sistema de arquivos, mas abaixo do fluxo de dados do arquivo. 

### <a name="encryption-in-transit"></a>Criptografia em trânsito
O agente Azure File Sync se comunica com o seu Serviço de Sincronização de Armazenamento e o compartilhamento de arquivos Azure Sync RESTANTE e o protocolo FileREST, ambos sempre usam HTTPS sobre a porta 443. O Azure File Sync não envia solicitações não criptografadas pelo HTTP. 

As contas de armazenamento do Azure contêm um switch para exigir criptografia em trânsito, que é habilitado por padrão. Mesmo que o switch no nível da conta de armazenamento esteja desativado, o que significa que conexões não criptografadas aos seus compartilhamentos de arquivos do Azure são possíveis, o Azure File Sync ainda usará apenas canais criptografados para acessar seu compartilhamento de arquivos.

A principal razão para desativar a criptografia em trânsito para a conta de armazenamento é suportar um aplicativo legado que deve ser executado em um sistema operacional mais antigo, como o Windows Server 2008 R2 ou a distribuição Linux mais antiga, falando diretamente com um compartilhamento de arquivos do Azure. Se o aplicativo legado for conversado com o cache do Windows Server do compartilhamento de arquivos, a configuração não terá efeito. 

Recomendamos fortemente garantir que a criptografia de dados em trânsito seja ativada.

Para obter mais informações sobre criptografia em trânsito, consulte [Exigir transferência segura no armazenamento do Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-file-share-encryption-at-rest"></a>Criptografia de compartilhamento de arquivos Do Zure em repouso
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Camadas de armazenamento
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Permitir que as ações de arquivo padrão abrangem até 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Disponibilidade regional
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Disponibilidade da região de sincronização de arquivos do Azure
O Azure File Sync está disponível nas seguintes regiões:

| Nuvem do Azure | Região geográfica | Região do Azure | Código da região |
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
| Público | França | França Sul* | `francesouth` |
| Público | Índia | Índia Central | `centralindia` |
| Público | Índia | Sul da Índia | `southindia` |
| Público | Japão | Leste do Japão | `japaneast` |
| Público | Japão | Oeste do Japão | `japanwest` |
| Público | Coreia do Sul | Coreia Central | `koreacentral` |
| Público | Coreia do Sul | Sul da Coreia | `koreasouth` |
| Público | África do Sul | Norte da África do Sul | `southafricanorth` |
| Público | África do Sul | Sudoeste da África do Sul* | `southafricawest` |
| Público | EAU | Central dos Emirados Dos Emirados; | `uaecentral` |
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

Para as regiões marcadas com asteriscos, você deve entrar em contato com o Suporte Do Azure para solicitar acesso ao Armazenamento Azure nessas regiões. O processo está descrito [neste documento.](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="redundancy"></a>Redundância
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> O armazenamento redundante geo-redundante e geo-zona tem a capacidade de fazer o armazenamento de failover manualmente para a região secundária. Recomendamos que você não faça isso fora de um desastre quando estiver usando o Azure File Sync devido à maior probabilidade de perda de dados. No caso de um desastre em que você gostaria de iniciar um failover manual de armazenamento, você precisará abrir um caso de suporte com a Microsoft para que o Azure File Sync retome a sincronização com o ponto final secundário.

## <a name="migration"></a>Migração
Se você tiver um servidor de arquivos Windows existente, o Azure File Sync pode ser instalado diretamente no local, sem a necessidade de mover dados para um novo servidor. Se você está planejando migrar para um novo servidor de arquivos do Windows como parte da adoção do Azure File Sync, existem várias abordagens possíveis para mover dados:

- Crie pontos finais do servidor para o seu compartilhamento de arquivos antigo e seu novo compartilhamento de arquivos e deixe o Azure File Sync sincronizar os dados entre os pontos finais do servidor. A vantagem dessa abordagem é que torna muito fácil subscrever o armazenamento em seu novo servidor de arquivos, já que o Azure File Sync está ciente do hierárquico na nuvem. Quando estiver pronto, você pode cortar os usuários finais para o compartilhamento de arquivos no novo servidor e remover o ponto final do servidor do antigo compartilhamento de arquivos.

- Crie um ponto final do servidor apenas no novo servidor de `robocopy`arquivos e copie dados do compartilhamento de arquivos antigo usando . Dependendo da topologia dos compartilhamentos de arquivos em seu novo servidor (quantas ações você tem em cada volume, quão livre cada `robocopy` volume é, etc.), você pode precisar providenciar temporariamente armazenamento adicional, pois é esperado que do seu servidor antigo para o seu novo servidor dentro do seu datacenter local complete mais rápido do que o Azure File Sync irá mover dados para o Azure.

Também é possível usar a Data Box para migrar dados para uma implantação do Azure File Sync. Na maioria das vezes, quando os clientes querem usar o Data Box para ingerir dados, eles o fazem porque acham que aumentará a velocidade de sua implantação ou porque ajudará com cenários de largura de banda restrita. Embora seja verdade que o uso de uma Caixa de Dados para ingerir dados na implantação do Azure File Sync diminuirá a utilização da largura de banda, provavelmente será mais rápido para a maioria dos cenários buscar um upload de dados on-line através de um dos métodos descritos acima. Para saber mais sobre como usar a Data Box para ingerir dados na implantação do Azure File Sync, consulte [Migrar dados para o Azure File Sync com o Azure Data Box](storage-sync-offline-data-transfer.md).

Um erro comum que os clientes cometem ao migrar dados para sua nova implantação do Azure File Sync é copiar dados diretamente no compartilhamento de arquivos do Azure, em vez de em seus servidores de arquivos Windows. Embora o Azure File Sync identifique todos os novos arquivos no compartilhamento de arquivos do Azure e os sincronize de volta aos seus compartilhamentos de arquivos do Windows, isso geralmente é consideravelmente mais lento do que o carregamento de dados através do servidor de arquivos do Windows. Muitas ferramentas de cópia do Azure, como o AzCopy, têm a desvantagem adicional de não copiar todos os metadados importantes de um arquivo, como carimbos de tempo e ACLs.

## <a name="antivirus"></a>Antivírus
Como os antivírus funcionam com o exame de arquivos em busca de códigos mal-intencionados conhecidos, um antivírus pode causar o recall de arquivos em camadas. Nas versões 4.0 e acima do agente de Sincronização de Arquivo do Azure, arquivos em camadas têm o conjunto FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS de atributo seguro do Windows. Recomendamos consultar o fornecedor do software para saber como configurar a solução para ignorar a leitura de arquivos com esse conjunto de atributos (muitos fazem isso automaticamente). 

As soluções antivírus internas da Microsoft, o Windows Defender e o System Center Endpoint Protection (SCEP), ignoram automaticamente a leitura de arquivos que possuem esse atributo definido. Nós os testamos e identificamos um problema menor: quando você adiciona um servidor a um grupo de sincronização existente, os arquivos com menos de 800 bytes são recuperados (feitos o download) no novo servidor. Esses arquivos permanecerão no novo servidor e não serão colocados em camadas, pois não atendem ao requisito de tamanho em camadas (> 64kb).

> [!Note]  
> Os fornecedores de antivírus podem verificar a compatibilidade entre seu produto e o Azure File Sync usando o [Azure File Sync Antivirus Compatibility Test Suite](https://www.microsoft.com/download/details.aspx?id=58322), que está disponível para download no Microsoft Download Center.

## <a name="backup"></a>Backup 
Como as soluções de antivírus, as soluções de backup podem causar o recall de arquivos em camadas. Recomendamos o uso de uma solução de backup de nuvem para fazer backup do compartilhamento do arquivos do Azure, em vez de um produto de backup local.

Se você estiver usando uma solução de backup no local, os backups devem ser realizados em um servidor no grupo de sincronização que tem o hierárquico na nuvem desativado. Ao executar uma restauração, use as opções de restauração no nível do volume ou no nível do arquivo. Os arquivos restaurados usando a opção de restauração no nível do arquivo serão sincronizados com todos os pontos de extremidade no grupo de sincronização e os arquivos existentes serão substituídos pela versão restaurada do backup.  As restaurações no nível de volume não substituirão as versões de arquivo mais recentes no compartilhamento de arquivos do Azure ou em outros pontos de extremidade do servidor.

> [!Note]  
> A restauração bare-metal (BMR) pode causar resultados inesperados e não é atualmente suportada.

> [!Note]  
> Com a versão 9 do agente Azure File Sync, os instantâneos VSS (incluindo a guia Versões Anteriores) agora são suportados em volumes que têm hierarquica na nuvem ativada. No entanto, você deve habilitar a compatibilidade da versão anterior através do PowerShell. [Saiba como](storage-files-deployment-guide.md).

## <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente de Sincronização de Arquivo do Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Próximas etapas
* [Considere as configurações de firewall e proxy](storage-sync-files-firewall-and-proxy.md)
* [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)
* [Implantar os Arquivos do Azure](storage-files-deployment-guide.md)
* [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
* [Monitorar a Sincronização de Arquivos do Azure](storage-sync-files-monitoring.md)