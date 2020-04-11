---
title: Notas de versão para o agente de Sincronização de Arquivos do Azure | Microsoft Docs
description: Notas de versão para o agente de Sincronização de Arquivos do Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/16/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: c6455930f88d02e11cb0c45d29594ae40eaad9e3
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113285"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Notas de versão para o agente de Sincronização de Arquivos do Azure
A Sincronização de Arquivos do Azure permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. As instalações do Windows Server são transformadas em um cache rápido do seu compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS). Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo fornece as notas de versão para versões com suporte do agente de Sincronização de arquivos do Azure.

## <a name="supported-versions"></a>Versões com suporte
As seguintes versões têm suporte pela Sincronização de arquivos do Azure:

| Marco | Número de versão do agente | Data de liberação | Status |
|----|----------------------|--------------|------------------|
| Versão V10 - [KB4522359](https://support.microsoft.com/en-us/help/4522409/azure-file-sync-agent-v10-release-march-2020)| 10.0.0.0 | 9 de abril de 2020 | Em vôo |
| Rollup de atualização de dezembro de 2019 - [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 12 de dezembro de 2019 | Com suporte |
| Versão V9 - [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2 de dezembro de 2019 | Com suporte |
| Versão V8 - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8 de outubro de 2019 | Com suporte |
| Rollup de atualização de julho de 2019 - [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 de julho de 2019 | Com suporte |
| Rollup de atualização de julho de 2019 - [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12 de julho de 2019 | Com suporte |
| Versão V7 - [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19 de junho de 2019 | Com suporte |
| Rollup de atualização de junho de 2019 - [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 27 de junho de 2019 | Suportado - A versão do agente expirará em 21 de abril de 2020 |
| Rollup de atualização de junho de 2019 - [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 13 de junho de 2019 | Suportado - A versão do agente expirará em 21 de abril de 2020 |
| Rollup de atualização de maio de 2019 - [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 7 de maio de 2019 | Suportado - A versão do agente expirará em 21 de abril de 2020 |
| Versão V6 - [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 21 de abril de 2019 | Suportado - A versão do agente expirará em 21 de abril de 2020 |
| Versão V5 | 5.0.2.0 - 5.2.0.0 | N/D | Não Suportado - Versões do agente expiradas em 18 de março de 2020 |
| Lançamento V4 | 4.0.1.0 - 4.3.0.0 | N/D | Não Suportado - Versões do agente expiradas em 6 de novembro de 2019 |
| Versão V3 | 3.1.0.0 - 3.4.0.0 | N/D | Não Suportado - Versões do agente expiradas em 19 de agosto de 2019 |
| Agentes pré-GA | 1.1.0.0 – 3.0.13.0 | N/D | Sem suporte – versão de agente expiradas em 1 de outubro de 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente de Sincronização de Arquivo do Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-10000"></a>Versão do agente 10.0.0.0
As seguintes notas de versão são para a versão 10.0.0.0 do agente Azure File Sync (lançado em 9 de abril de 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Progresso de sincronização melhorado no portal
    - Com o lançamento do agente V10, o portal Azure começará em breve a mostrar o tipo de sessão de sincronização que está sendo executado. Por ex.: download inicial, download regular, recall em segundo plano (casos rápidos de recuperação de desastres) e similares. 

- Experiência aprimorada de portal de hierarçamento em nuvem
    - Se você tiver arquivos que não estão falhando em tier ou recall, agora você pode visualizar os erros de hierarquamento nas propriedades do ponto final do servidor.
    - Informações adicionais de hierarquidamento na nuvem estão disponíveis para um ponto final do servidor:
        - Tamanho do cache local
        - Eficiência de uso de cache
        - Detalhes da política de hierarquamento na nuvem: tamanho do volume, espaço livre atual ou o último tempo acessado do arquivo mais antigo no cache local.
    - Essas mudanças serão lançadas no portal Azure logo após o lançamento inicial do agente V10.

- Suporte para mover a conta de sincronização de armazenamento e/ou armazenamento para um inquilino AAD (AAD) diferente do Azure Active Directory (AAD)
    - O Azure File Sync agora suporta a movimentação da conta de sincronização de armazenamento e/ou armazenamento para um grupo de recursos diferente, assinatura ou inquilino Azure AD.
    
- Ferramenta de avaliação agora identifica arquivos ou diretórios que terminam com um período
    - A [ferramenta Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) foi atualizada para identificar arquivos ou diretórios que terminam com um período. Arquivos ou diretórios que terminam com um período não são suportados atualmente pelo Azure File Sync. Para usar a versão atualizada da [ferramenta Avaliação,](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)instale a versão mais recente do [módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).
 
- Melhorias de desempenho e confiabilidade diversas
    - A detecção de alterações no compartilhamento de arquivos do Azure pode falhar se as regras de rede virtual (VNET) e firewall estiverem configuradas na conta de armazenamento.
    - Consumo de memória reduzido associado ao recall. 
    - Desempenho melhorado ao usar o [cmdlet Invoke-AzStorageSyncDetecção.](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)
    - Outras melhorias diversas de confiabilidade. 
    
### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão do sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- O agente não é suportado na opção de implantação do Nano Server.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- A execução do sysprep em um servidor que tenha o agente Azure File Sync instalado não é suportada e pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os seguintes itens não são sincronizados, mas o restante do sistema continua a operar normalmente:
- Arquivos com caracteres sem suporte. Consulte [Guia de solução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter uma lista de caracteres sem suporte.
- Arquivos ou diretórios que encerram com um período.
- Caminhos com mais de 2.048 caracteres.
- A parte da SACL (lista de controle de acesso) do sistema de um descritor de segurança que é usada para fazer a auditoria.
- Atributos estendidos.
- Fluxos de dados alternativos.
- Pontos de nova análise.
- Links físicos.
- A compactação (se definida em um arquivo do servidor) não é preservada quando as alterações são sincronizadas para o arquivo de outros pontos de extremidade.
- Todos os arquivos criptografados com EFS (ou outra criptografia de modo de usuário) que impedem o serviço de ler os dados.

    > [!Note]  
    > A Sincronização de arquivos do Azure sempre criptografa os dados em trânsito. Os dados sempre são criptografados em repouso no Azure.
 
### <a name="server-endpoint"></a>Ponto de extremidade do servidor
- Um ponto de extremidade do servidor só pode ser criado em um volume NTFS. ReFS, FAT, FAT32 e outros sistemas de arquivos não têm suporte pela Sincronização de arquivos do Azure no momento.
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um arquivo de paginação de aplicativo ou SO em um local do ponto de extremidade do servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Para sincronizar imediatamente os arquivos que são alterados no compartilhamento de arquivos do Azure, o [cmdlet Invoke-AzStorageSyncDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell pode ser usado para iniciar manualmente a detecção de alterações no compartilhamento de arquivos do Azure. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Ao criar o ponto final da nuvem, o serviço de sincronização de armazenamento e a conta de armazenamento devem estar no mesmo inquilino do Azure AD. Uma vez criado o ponto final da nuvem, o serviço de sincronização de armazenamento e a conta de armazenamento podem ser movidos para diferentes inquilinos do Azure AD.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.

## <a name="agent-version-9100"></a>Agente versão 9.1.0.0
As seguintes notas de versão são para a versão 9.1.0.0 do agente Azure File Sync lançado em 12 de dezembro de 2019. Essas notas são adicionais às notas de versão listadas para a versão 9.0.0.0.

Problema corrigido nesta versão:  
- O sincronizar falha com um dos seguintes erros após a atualização para o azure File Sync agent versão 9.0:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>Agente versão 9.0.0.0
As seguintes notas de versão são para a versão 9.0.0.0 do agente Azure File Sync (lançado em 2 de dezembro de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Suporte de restauração de autoatendimento
    - Os usuários agora podem restaurar seus arquivos usando o recurso de versão anterior. Antes do lançamento do v9, o recurso da versão anterior não era suportado em volumes que tinham hierarquiagem em nuvem ativada. Esse recurso deve ser ativado para cada volume separadamente, no qual existe um ponto final com hierarquididade na nuvem ativado. Para obter mais informações, consulte:  
[Restauração de autoatendimento através de versões anteriores e VSS (Volume Shadow Copy Service)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Suporte para tamanhos maiores de compartilhamento de arquivos 
    - O Azure File Sync agora suporta até 64TiB e 100 milhões de arquivos em um único, sincronizando o namespace.  
 
- Suporte à duplicação de dados no Servidor 2019 
    - A deduplicação de dados agora é suportada com hierárquico em nuvem ativado no Windows Server 2019. Para suportar a deduplicação de dados em volumes com hierarcada na nuvem, a atualização do Windows [KB4520062](https://support.microsoft.com/help/4520062) deve ser instalada. 
 
- Tamanho mínimo de arquivo melhorado para um arquivo para tier 
    - O tamanho mínimo do arquivo para um arquivo para nível é agora baseado no tamanho do cluster do sistema de arquivos (o dobro do tamanho do cluster do sistema de arquivos). Por exemplo, por padrão, o tamanho do cluster do sistema de arquivos NTFS é 4KB, o tamanho mínimo de arquivo resultante para um arquivo para o nível é 8KB. 
 
- Cmdlet de teste de conectividade de rede 
    - Como parte da configuração Azure File Sync, vários pontos finais de serviço devem ser contatados. Cada um deles tem seu próprio nome DNS que precisa ser acessível ao servidor. Esses URLs também são específicos para a região para a região em que um servidor está registrado. Uma vez que um servidor é registrado, o cmdlet de teste de conectividade (PowerShell e Server Registration Utility) pode ser usado para testar comunicações com todas as URLs específicas para este servidor. Este cmdlet pode ajudar a solucionar problemas quando a comunicação incompleta impede que o servidor funcione totalmente com o Azure File Sync e pode ser usado para ajustar as configurações de proxy e firewall.  
 
        Para executar o teste de conectividade de rede, execute os seguintes comandos PowerShell: 
 
        Módulo de importação "C:\Arquivos do programa\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Armazenamento de testesSyncConectividade de rede
 
- Remova a melhoria do ponto final do servidor quando o hierarquidade da nuvem estiver ativado 
    - Como antes, a remoção de um ponto final do servidor não resulta na remoção de arquivos no compartilhamento de arquivos do Azure. No entanto, o comportamento dos pontos de reparso no servidor local mudou. Os pontos de reparse (ponteiros para arquivos que não estão locais no servidor) são agora excluídos ao remover um ponto final do servidor. Os arquivos totalmente armazenados permanecerão no servidor. Essa melhoria foi feita para evitar [arquivos hierárquicos órfãos](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) ao remover um ponto final do servidor. Se o ponto final do servidor for recriado, os pontos de reparsos para os arquivos hierárquicos serão recriados no servidor.  
 
- Aprimoramento de desempenho e confiabilidade 
    - Falhas de recall reduzidas. O tamanho do recall agora é ajustado automaticamente com base na largura de banda da rede. 
    - Melhor desempenho de download ao adicionar um novo servidor a um grupo de sincronização. 
    - Arquivos reduzidos não sincronizados devido a conflitos de restrição. 
    - Os arquivos falham em ser superiores ou são chamados inesperadamente em determinados cenários se o caminho do ponto final do servidor for um ponto de montagem de volume.
    
### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão do sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- O agente não é suportado na opção de implantação do Nano Server.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- A execução do sysprep em um servidor que tenha o agente Azure File Sync instalado não é suportada e pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os seguintes itens não são sincronizados, mas o restante do sistema continua a operar normalmente:
- Arquivos com caracteres sem suporte. Consulte [Guia de solução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter uma lista de caracteres sem suporte.
- Arquivos ou diretórios que encerram com um período.
- Caminhos com mais de 2.048 caracteres.
- A parte discricionária da DACL (lista de controle de acesso) de um descritor de segurança se for maior que 2 KB. (Esse problema se aplica somente quando você tem mais de 40 ACEs (entradas de controle de acesso) em um único item.)
- A parte da SACL (lista de controle de acesso) do sistema de um descritor de segurança que é usada para fazer a auditoria.
- Atributos estendidos.
- Fluxos de dados alternativos.
- Pontos de nova análise.
- Links físicos.
- A compactação (se definida em um arquivo do servidor) não é preservada quando as alterações são sincronizadas para o arquivo de outros pontos de extremidade.
- Todos os arquivos criptografados com EFS (ou outra criptografia de modo de usuário) que impedem o serviço de ler os dados.

    > [!Note]  
    > A Sincronização de arquivos do Azure sempre criptografa os dados em trânsito. Os dados sempre são criptografados em repouso no Azure.
 
### <a name="server-endpoint"></a>Ponto de extremidade do servidor
- Um ponto de extremidade do servidor só pode ser criado em um volume NTFS. ReFS, FAT, FAT32 e outros sistemas de arquivos não têm suporte pela Sincronização de arquivos do Azure no momento.
- Os arquivos em camadas ficarão inacessíveis se os arquivos não forem chamados antes da exclusão do ponto de extremidade do servidor. Para restaurar o acesso aos arquivos, recrie o ponto de extremidade do servidor. Se 30 dias se passaram desde que o ponto de extremidade do servidor foi excluído ou se o ponto de extremidade da nuvem foi excluído, os arquivos em camadas que não foram recuperados ficarão inutilizáveis. Para saber mais, consulte [arquivos hierárquicos não estão acessíveis no servidor depois de excluir um ponto final do servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um arquivo de paginação de aplicativo ou SO em um local do ponto de extremidade do servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Para sincronizar imediatamente os arquivos que são alterados no compartilhamento de arquivos do Azure, o [cmdlet Invoke-AzStorageSyncDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell pode ser usado para iniciar manualmente a detecção de alterações no compartilhamento de arquivos do Azure. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Sincronização de Arquivos do Azure não oferece suporte para mover a assinatura para um locatário do Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.
- Os arquivos podem falhar no nível se o pagefile.sys estiver localizado em um volume que tenha o hierarquidamento na nuvem ativado. O pagefile.sys deve estar localizado em um volume que tenha o hierárquico da nuvem desativado.

## <a name="agent-version-8000"></a>Agente versão 8.0.0.0
As seguintes notas de versão são para a versão 8.0.0.0 do agente Azure File Sync (lançado em 8 de outubro de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Restaurar melhorias de desempenho
    - Tempos de recuperação mais rápidos para recuperação feitos através do Azure Backup. Os arquivos restaurados serão sincronizados de volta para servidores Azure File Sync muito mais rápido. 
- Experiência aprimorada de portal de hierarçamento em nuvem  
    - Se você tiver arquivos hierárquicos que não estão sendo lembrados, agora você pode visualizar os erros de recall nas propriedades do ponto final do servidor. Além disso, a saúde do ponto final do servidor agora mostrará um erro e etapas de mitigação se o driver de filtro de hierarcada na nuvem não estiver carregado no servidor.
- Instalação de agente mais simples
    - O módulo Az\AzureRM PowerShell não é mais necessário para registrar o servidor tornando a instalação mais simples e rápida.
- Melhorias de desempenho e confiabilidade diversas

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão do sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- O agente não é suportado na opção de implantação do Nano Server.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- A execução do sysprep em um servidor que tenha o agente Azure File Sync instalado não é suportada e pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os seguintes itens não são sincronizados, mas o restante do sistema continua a operar normalmente:
- Arquivos com caracteres sem suporte. Consulte [Guia de solução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter uma lista de caracteres sem suporte.
- Arquivos ou diretórios que encerram com um período.
- Caminhos com mais de 2.048 caracteres.
- A parte discricionária da DACL (lista de controle de acesso) de um descritor de segurança se for maior que 2 KB. (Esse problema se aplica somente quando você tem mais de 40 ACEs (entradas de controle de acesso) em um único item.)
- A parte da SACL (lista de controle de acesso) do sistema de um descritor de segurança que é usada para fazer a auditoria.
- Atributos estendidos.
- Fluxos de dados alternativos.
- Pontos de nova análise.
- Links físicos.
- A compactação (se definida em um arquivo do servidor) não é preservada quando as alterações são sincronizadas para o arquivo de outros pontos de extremidade.
- Todos os arquivos criptografados com EFS (ou outra criptografia de modo de usuário) que impedem o serviço de ler os dados.

    > [!Note]  
    > A Sincronização de arquivos do Azure sempre criptografa os dados em trânsito. Os dados sempre são criptografados em repouso no Azure.
 
### <a name="server-endpoint"></a>Ponto de extremidade do servidor
- Um ponto de extremidade do servidor só pode ser criado em um volume NTFS. ReFS, FAT, FAT32 e outros sistemas de arquivos não têm suporte pela Sincronização de arquivos do Azure no momento.
- Os arquivos em camadas ficarão inacessíveis se os arquivos não forem chamados antes da exclusão do ponto de extremidade do servidor. Para restaurar o acesso aos arquivos, recrie o ponto de extremidade do servidor. Se 30 dias se passaram desde que o ponto de extremidade do servidor foi excluído ou se o ponto de extremidade da nuvem foi excluído, os arquivos em camadas que não foram recuperados ficarão inutilizáveis. Para saber mais, consulte [arquivos hierárquicos não estão acessíveis no servidor depois de excluir um ponto final do servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um arquivo de paginação de aplicativo ou SO em um local do ponto de extremidade do servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Para sincronizar imediatamente os arquivos que são alterados no compartilhamento de arquivos do Azure, o [cmdlet Invoke-AzStorageSyncDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell pode ser usado para iniciar manualmente a detecção de alterações no compartilhamento de arquivos do Azure. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Sincronização de Arquivos do Azure não oferece suporte para mover a assinatura para um locatário do Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.

## <a name="agent-version-7200"></a>Agente versão 7.2.0.0
As seguintes notas de versão são para a versão 7.2.0.0 do agente Azure File Sync lançado em 24 de julho de 2019. Essas notas são adicionais às notas de versão listadas para a versão 7.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- O Agente de Sincronização de Armazenamento (FileSyncSvc) falha se a configuração do proxy for nula.
- O ponto final do servidor iniciará o BCDR (erro 0x80c80257 - ECS_E_BCDR_IN_PROGRESS) se vários pontos finais no servidor tiverem o mesmo nome.
- Melhorias na confiabilidade do hierarquidamento na nuvem.

## <a name="agent-version-7100"></a>Agente versão 7.1.0.0
As seguintes notas de versão são para a versão 7.1.0.0 do agente Azure File Sync lançado em 12 de julho de 2019. Essas notas são adicionais às notas de versão listadas para a versão 7.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- Acessar ou navegar um local de ponto final do servidor sobre SMB é lento no Windows Server 2012 R2. 
- Aumento da utilização da CPU após a instalação do agente Azure File Sync v6.
- Melhorias na telemetria de hierarquidade em nuvem.
- Diversas melhorias de confiabilidade para camada e sincronização de nuvem.

## <a name="agent-version-7000"></a>Agente versão 7.0.0.0
As seguintes notas de versão são para a versão 7.0.0.0 do agente Azure File Sync (lançado em 19 de junho de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Suporte para tamanhos maiores de compartilhamento de arquivos
    - Com a visualização de compartilhamentos maiores de arquivos Azure, estamos aumentando nossos limites de suporte para sincronização de arquivos também. Nesta primeira etapa, o Azure File Sync agora suporta até 25 TB e 50 milhões de arquivos em um único, sincronizando o namespace. Para solicitar a pré-visualização de https://aka.ms/azurefilesatscalesurveycompartilhamento de arquivos em grande parte, preencha este formulário . 
- Suporte para configuração de firewall e rede virtual em contas de armazenamento
    - O Azure File Sync agora suporta a configuração de firewall e rede virtual em contas de armazenamento. Para configurar sua implantação para trabalhar com a configuração de firewall e rede virtual, consulte [Configurar configurações de firewall e rede virtual](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
- Cmdlet do PowerShell para sincronizar imediatamente arquivos alterados no compartilhamento de arquivos do Azure
    - Para sincronizar imediatamente os arquivos que são alterados no compartilhamento de arquivos do Azure, o cmdlet Invoke-AzStorageSyncDetection PowerShell pode ser usado para iniciar manualmente a detecção de alterações no compartilhamento de arquivos do Azure. Este cmdlet destina-se a cenários em que algum tipo de processo automatizado está fazendo alterações no compartilhamento de arquivos do Azure ou as alterações são feitas por um administrador (como mover arquivos e diretórios para o compartilhamento). Para alterações no usuário final, a recomendação é instalar o agente Azure File Sync em uma VM IaaS e fazer com que os usuários finais acessem o compartilhamento de arquivos através do VM IaaS. Desta forma, todas as alterações serão sincronizadas rapidamente com outros agentes sem a necessidade de usar o cmdlet Invoke-AzStorageSyncDetecção. Para saber mais, consulte a documentação [Invoke-AzStorageSyncChangeDetection.](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)
- Experiência de portal melhorada se você encontrar arquivos que não estão sincronizando
    - Se você tem arquivos que estão falhando em sincronizar, agora diferenciamos entre erros transitórios e persistentes no portal. Erros transitórios geralmente se resolvem sem a necessidade de ação de admin. Por exemplo, um arquivo que está atualmente em uso não será sincronizado até que a alça do arquivo seja fechada. Para erros persistentes, agora mostramos o número de arquivos impactados por cada erro. A contagem de erros persistentes também é exibida nos arquivos que não sincronizam a coluna de todos os pontos finais do servidor em um grupo de sincronização.
- Restauração aprimorada do nível de arquivo do Azure Backup
    - Arquivos individuais restaurados usando o Azure Backup são agora detectados e sincronizados com o ponto final do servidor mais rapidamente.
- Melhor nível de hierarquiagem da nuvem capacidade de recuperação cmdlet 
    - O cmdlet Invoke-StorageSyncRecall agora permite que os clientes especifiquem por contagem de repetição de arquivo e por atraso de tentativa de arquivo semelhante à robocopy. Anteriormente, este cmdlet recordaria todos os arquivos hierárquicos sob um determinado caminho em ordem aleatória. Com o novo parâmetro -Order, este cmdlet lembrará os dados mais quentes primeiro e honrará a política de hierarquamento na nuvem (pare de lembrar se a política de data é atendida ou o espaço livre de volume saturado; o que acontecer primeiro).
- Suporte apenas para TLS 1.2 (TLS 1.0 e 1.1 está desativado)
    - O Azure File Sync agora suporta o uso do TLS 1.2 apenas em servidores que possuem TLS 1.0 e 1.1 desativados. Antes dessa melhoria, o registro do servidor falharia se o TLS 1.0 e o 1.1 fossem desativados no servidor.
- Melhorias de desempenho e confiabilidade diversas para sincronização e hierquiagem em nuvem
    - Existem várias melhorias de confiabilidade e desempenho nesta versão. Alguns deles são direcionados para tornar a hierarquiagem em nuvem mais eficiente e o Azure File Sync como um todo funciona melhor nessas situações quando você tem um cronograma de estrangulamento de largura de banda definido.

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão do sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- O agente não é suportado na opção de implantação do Nano Server.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- A execução do sysprep em um servidor que tenha o agente Azure File Sync instalado não é suportada e pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os seguintes itens não são sincronizados, mas o restante do sistema continua a operar normalmente:
- Arquivos com caracteres sem suporte. Consulte [Guia de solução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter uma lista de caracteres sem suporte.
- Arquivos ou diretórios que encerram com um período.
- Caminhos com mais de 2.048 caracteres.
- A parte discricionária da DACL (lista de controle de acesso) de um descritor de segurança se for maior que 2 KB. (Esse problema se aplica somente quando você tem mais de 40 ACEs (entradas de controle de acesso) em um único item.)
- A parte da SACL (lista de controle de acesso) do sistema de um descritor de segurança que é usada para fazer a auditoria.
- Atributos estendidos.
- Fluxos de dados alternativos.
- Pontos de nova análise.
- Links físicos.
- A compactação (se definida em um arquivo do servidor) não é preservada quando as alterações são sincronizadas para o arquivo de outros pontos de extremidade.
- Todos os arquivos criptografados com EFS (ou outra criptografia de modo de usuário) que impedem o serviço de ler os dados.

    > [!Note]  
    > A Sincronização de arquivos do Azure sempre criptografa os dados em trânsito. Os dados sempre são criptografados em repouso no Azure.
 
### <a name="server-endpoint"></a>Ponto de extremidade do servidor
- Um ponto de extremidade do servidor só pode ser criado em um volume NTFS. ReFS, FAT, FAT32 e outros sistemas de arquivos não têm suporte pela Sincronização de arquivos do Azure no momento.
- Os arquivos em camadas ficarão inacessíveis se os arquivos não forem chamados antes da exclusão do ponto de extremidade do servidor. Para restaurar o acesso aos arquivos, recrie o ponto de extremidade do servidor. Se 30 dias se passaram desde que o ponto de extremidade do servidor foi excluído ou se o ponto de extremidade da nuvem foi excluído, os arquivos em camadas que não foram recuperados ficarão inutilizáveis.
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um arquivo de paginação de aplicativo ou SO em um local do ponto de extremidade do servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Sincronização de Arquivos do Azure não oferece suporte para mover a assinatura para um locatário do Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.

## <a name="agent-version-6300"></a>Agente versão 6.3.0.0
As seguintes notas de versão são para a versão 6.3.0.0 do agente Azure File Sync lançado em 27 de junho de 2019. Essas notas são adicionais às notas de versão listadas para a versão 6.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- Acessar ou navegar um local de ponto final do servidor sobre SMB é lento no Windows Server 2012 R2 
- Aumento da utilização da CPU após a instalação do agente Azure File Sync v6
- Melhorias na telemetria de hierarquidade em nuvem

## <a name="agent-version-6200"></a>Agente versão 6.2.0.0
As seguintes notas de versão são para a versão 6.2.0.0 do agente Azure File Sync lançado em 13 de junho de 2019. Essas notas são adicionais às notas de versão listadas para a versão 6.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- Depois de criar um ponto final do servidor, o uso de CPU alta pode ocorrer quando o recall em segundo plano está baixando arquivos para o servidor
- As operações de sincronização e hierarquiagem na nuvem podem falhar com ECS_E_SERVER_CREDENTIAL_NEEDED de erro devido à expiração do token
- A recuperação de um arquivo pode falhar se a URL para baixar o arquivo contiver caracteres reservados 

## <a name="agent-version-6100"></a>Agente versão 6.1.0.0
As seguintes notas de versão são para a versão 6.1.0.0 do agente Azure File Sync lançado em 6 de maio de 2019. Essas notas são adicionais às notas de versão listadas para a versão 6.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- O Windows Admin Center falha ao exibir a configuração da versão do agente e do ponto final do servidor em servidores que têm o agente Azure File Sync versão 6.0 instalado.

## <a name="agent-version-6000"></a>Agente versão 6.0.0.0
As seguintes notas de versão são para a versão 6.0.0.0 do agente Azure File Sync (lançado em 22 de abril de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Suporte de atualização automática do agente
  - Ouvimos seu feedback e adicionamos um recurso de atualização automática no agente de servidor Azure File Sync. Para obter mais informações, consulte [a política de atualização do agente Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Suporte para ACLs de compartilhamento de arquivos Azure
  - O Azure File Sync sempre fez suporte para sincronizar ACLs entre pontos finais do servidor, mas as ACLs não foram sincronizadas com o ponto final da nuvem (compartilhamento de arquivos Do Zure). Esta versão adiciona suporte para sincronizar ACLs entre pontos finais de servidor e nuvem.
- Sessões de sincronização de upload e download paralelas para um ponto final do servidor 
  - Os pontos finais do servidor agora suportam o upload e o download de arquivos ao mesmo tempo. Não há mais espera por um download para que os arquivos possam ser carregados para o compartilhamento de arquivos do Azure. 
- Novos cmdlets de hierarçamento de nuvem para obter status de volume e hierarquiagem
  - Dois novos cmdlets PowerShell locais do servidor agora podem ser usados para obter informações de hierarquidamento na nuvem e recall de arquivos. Eles disponibilizam informações de registro de dois canais de eventos no servidor:
    - Get-StorageSyncTieringResult listará todos os arquivos e seus caminhos que não foram hierarquizados e informa o motivo.
    - Get-StorageSyncRecallRecallResultado relata todos os eventos de recall de arquivos. Ele lista todos os arquivos recuperados e seu caminho, bem como sucesso ou erro para esse recall.
  - Por padrão, ambos os canais de eventos podem armazenar até 1 MB cada um – você pode aumentar a quantidade de arquivos relatados aumentando o tamanho do canal de eventos.
- Suporte para o modo FIPS
  - O Azure File Sync agora suporta a ativação do modo FIPS em servidores que têm o agente Azure File Sync instalado.
    - Antes de ativar o modo FIPS em seu servidor, instale o agente Azure File Sync e [o módulo PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) em seu servidor. Se o FIPS já estiver habilitado no servidor, [baixe manualmente](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) o [módulo PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) para o seu servidor.
- Melhorias de confiabilidade diversas para hierárquico e sincronização de nuvens

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão do sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- O agente não é suportado na opção de implantação do Nano Server.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- Não há suporte à execução de sysprep em um servidor que possua o agente Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os seguintes itens não são sincronizados, mas o restante do sistema continua a operar normalmente:
- Arquivos com caracteres sem suporte. Consulte [Guia de solução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter uma lista de caracteres sem suporte.
- Arquivos ou diretórios que encerram com um período.
- Caminhos com mais de 2.048 caracteres.
- A parte discricionária da DACL (lista de controle de acesso) de um descritor de segurança se for maior que 2 KB. (Esse problema se aplica somente quando você tem mais de 40 ACEs (entradas de controle de acesso) em um único item.)
- A parte da SACL (lista de controle de acesso) do sistema de um descritor de segurança que é usada para fazer a auditoria.
- Atributos estendidos.
- Fluxos de dados alternativos.
- Pontos de nova análise.
- Links físicos.
- A compactação (se definida em um arquivo do servidor) não é preservada quando as alterações são sincronizadas para o arquivo de outros pontos de extremidade.
- Todos os arquivos criptografados com EFS (ou outra criptografia de modo de usuário) que impedem o serviço de ler os dados.

    > [!Note]  
    > A Sincronização de arquivos do Azure sempre criptografa os dados em trânsito. Os dados sempre são criptografados em repouso no Azure.
 
### <a name="server-endpoint"></a>Ponto de extremidade do servidor
- Um ponto de extremidade do servidor só pode ser criado em um volume NTFS. ReFS, FAT, FAT32 e outros sistemas de arquivos não têm suporte pela Sincronização de arquivos do Azure no momento.
- Os arquivos em camadas ficarão inacessíveis se os arquivos não forem chamados antes da exclusão do ponto de extremidade do servidor. Para restaurar o acesso aos arquivos, recrie o ponto de extremidade do servidor. Se 30 dias se passaram desde que o ponto de extremidade do servidor foi excluído ou se o ponto de extremidade da nuvem foi excluído, os arquivos em camadas que não foram recuperados ficarão inutilizáveis.
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um arquivo de paginação de aplicativo ou SO em um local do ponto de extremidade do servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Sincronização de Arquivos do Azure não oferece suporte para mover a assinatura para um locatário do Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.
- Ao exibir as propriedades de arquivo de um cliente SMB, o atributo offline poderá parecer estar definido incorretamente devido ao cache SMB de metadados do arquivo.

## <a name="agent-version-5200"></a>Versão do agente 5.2.0.0
As seguintes notas de versão são para a versão 5.2.0.0 do agente Azure File Sync lançado em 4 de abril de 2019. Essas notas são adicionais às notas de versão listadas para a versão 5.0.2.0.

Lista dos problemas corrigidos nesta versão:  
- Melhorias de confiabilidade para recursos de transferência de dados offline e transferência de dados
- Melhorias na telemetria de sincronização

## <a name="agent-version-5100"></a>Versão do agente 5.1.0.0
As seguintes notas de versão são para a versão 5.1.0.0 do agente Azure File Sync lançado em 7 de março de 2019. Essas notas são adicionais às notas de versão listadas para a versão 5.0.2.0.

Lista dos problemas corrigidos nesta versão:  
- Os arquivos podem não sincronizar com o erro 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) se a enumeração da alteração estiver falhando no servidor
- Se uma sessão de sincronização ou arquivo receber um erro 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE), a sincronização agora tentará novamente a operação
- Os arquivos podem não sincronizar com o erro 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- O uso de memória elevado pode ocorrer ao recuperar arquivos
- Melhorias na telemetria de hierarquidade em nuvem 

## <a name="agent-version-5020"></a>Versão do agente 5.0.2.0
As notas sobre a versão a seguir são para a versão 5.0.2.0 do agente de Sincronização de Arquivos do Azure (lançada em 12 de fevereiro de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Suporte para a nuvem do Azure Governamental
  - Adicionamos suporte à versão prévia para a nuvem do Azure Governamental. Isso exige uma assinatura inclusa na lista de permissão e download do agente especial da Microsoft. Para ter acesso à pré-visualização, envie um e-mail diretamente para [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com).
- Suporte para Eliminação de Duplicação de Dados
    - A Eliminação de Duplicação de Dados tem suporte completo com a camada de nuvem habilitada no Windows Server 2016 e no Windows Server 2019. Habilitar a eliminação de duplicação em um volume com camada de nuvem habilitada permite armazenar em cache mais arquivos localmente sem ter que provisionar mais armazenamento.
- Suporte para transferência de dados offline (por exemplo, por meio do Data Box)
    - Migre facilmente grandes quantidades de dados para a Sincronização de Arquivos do Azure do modo que escolher. Você pode escolher a Caixa de Dados Azure, o AzCopy e até mesmo os serviços de migração de terceiros. Não é necessário usar grandes quantidades de largura de banda para colocar seus dados no Azure. No caso do Data Box, simplesmente envie-os por ele. Para obter mais informações, confira [Documentos de transferência de dados offline](https://aka.ms/AFS/OfflineDataTransfer).
- Desempenho de sincronização aprimorado
    - Os clientes com vários pontos de extremidade do servidor no mesmo volume podem ter passado por sincronização lenta antes dessa versão. O Azure File Sync cria um instantâneo temporário do VSS uma vez por dia no servidor para sincronizar arquivos que tenham identificadores abertos. A Sincronização agora dá suporte a vários pontos de extremidade de servidor sincronizando em um volume quando uma sessão de sincronização do VSS estiver ativa. Não é mais necessário esperar a conclusão de uma sessão de sincronização do VSS para retomar a sincronização em outros pontos de extremidade do servidor no volume.
- Monitoramento aprimorado no portal
    - Gráficos foram adicionados no portal do Serviço de Sincronização de Armazenamento para exibir:
        - Número de arquivos sincronizados
        - Tamanho dos dados transferidos
        - Número de arquivos não sincronizados
        - Tamanho dos dados em recall
        - Status da conectividade do servidor
    - Para saber mais, confira [Monitorar a Sincronização de Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Confiabilidade e escalabilidade aprimoradas
    - O número máximo de objetos do sistema de arquivos (diretórios e arquivos) em um diretório aumentou para 1 milhão. O limite anterior era de 200.000.
    - A Sincronização tentará retomar a transferência de dados em vez de retransmitir quando uma transferência for interrompida para arquivos grandes 

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão do sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- O agente não tem suporte nas opções de implantação do Windows Server Core ou Nano Server.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.
- O modo FIPS não tem suporte e deve ser desabilitado. 

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- Não há suporte à execução de sysprep em um servidor que possua o agente Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

### <a name="sync-limitations"></a>Limitações de sincronização
Os seguintes itens não são sincronizados, mas o restante do sistema continua a operar normalmente:
- Arquivos com caracteres sem suporte. Consulte [Guia de solução de problemas](storage-sync-files-troubleshoot.md#handling-unsupported-characters) para obter uma lista de caracteres sem suporte.
- Arquivos ou diretórios que encerram com um período.
- Caminhos com mais de 2.048 caracteres.
- A parte discricionária da DACL (lista de controle de acesso) de um descritor de segurança se for maior que 2 KB. (Esse problema se aplica somente quando você tem mais de 40 ACEs (entradas de controle de acesso) em um único item.)
- A parte da SACL (lista de controle de acesso) do sistema de um descritor de segurança que é usada para fazer a auditoria.
- Atributos estendidos.
- Fluxos de dados alternativos.
- Pontos de nova análise.
- Links físicos.
- A compactação (se definida em um arquivo do servidor) não é preservada quando as alterações são sincronizadas para o arquivo de outros pontos de extremidade.
- Todos os arquivos criptografados com EFS (ou outra criptografia de modo de usuário) que impedem o serviço de ler os dados.

    > [!Note]  
    > A Sincronização de arquivos do Azure sempre criptografa os dados em trânsito. Os dados sempre são criptografados em repouso no Azure.
 
### <a name="server-endpoint"></a>Ponto de extremidade do servidor
- Um ponto de extremidade do servidor só pode ser criado em um volume NTFS. ReFS, FAT, FAT32 e outros sistemas de arquivos não têm suporte pela Sincronização de arquivos do Azure no momento.
- Os arquivos em camadas ficarão inacessíveis se os arquivos não forem chamados antes da exclusão do ponto de extremidade do servidor. Para restaurar o acesso aos arquivos, recrie o ponto de extremidade do servidor. Se 30 dias se passaram desde que o ponto de extremidade do servidor foi excluído ou se o ponto de extremidade da nuvem foi excluído, os arquivos em camadas que não foram recuperados ficarão inutilizáveis.
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um arquivo de paginação de aplicativo ou SO em um local do ponto de extremidade do servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Sincronização de Arquivos do Azure não oferece suporte para mover a assinatura para um locatário do Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.
- Ao exibir as propriedades de arquivo de um cliente SMB, o atributo offline poderá parecer estar definido incorretamente devido ao cache SMB de metadados do arquivo.
