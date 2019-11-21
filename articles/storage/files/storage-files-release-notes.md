---
title: Notas de versão para o agente de Sincronização de Arquivos do Azure | Microsoft Docs
description: Notas de versão para o agente de Sincronização de Arquivos do Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 10/8/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 8328170d73d75e8e2eb1a84881375ce21c72a61b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227844"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Notas de versão para o agente de Sincronização de Arquivos do Azure
A Sincronização de Arquivos do Azure permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. As instalações do Windows Server são transformadas em um cache rápido do seu compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS). Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo fornece as notas de versão para versões com suporte do agente de Sincronização de arquivos do Azure.

## <a name="supported-versions"></a>Versões com suporte
As seguintes versões têm suporte pela Sincronização de arquivos do Azure:

| Marco | Número de versão do agente | Data do lançamento | Status |
|----|----------------------|--------------|------------------|
| V8 Release - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | October 8, 2019 | Com suporte |
| July 2019 update rollup - [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 de julho de 2019 | Com suporte |
| July 2019 update rollup - [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | July 12, 2019 | Com suporte |
| V7 Release - [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | June 19, 2019 | Com suporte |
| June 2019 update rollup - [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | June 27, 2019 | Com suporte |
| June 2019 update rollup - [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | June 13, 2019 | Com suporte |
| May 2019 update rollup - [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | May 7, 2019 | Com suporte |
| V6 Release - [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | April 21, 2019 | Com suporte |
| April 2019 update rollup - [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | April 4, 2019 | Com suporte |
| March 2019 update rollup - [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | March 7, 2019 | Com suporte |
| Versão V5 – [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 12 de fevereiro de 2019 | Com suporte |
| Lançamento V4 | 4.0.1.0 - 4.3.0.0 | N/D | Not Supported - Agent versions expired on November 6, 2019 |
| V3 Release | 3.1.0.0 - 3.4.0.0 | N/D | Not Supported - Agent versions expired on August 19, 2019 |
| Pre-GA agents | 1.1.0.0 – 3.0.13.0 | N/D | Sem suporte – versão de agente expiradas em 1 de outubro de 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente de Sincronização de Arquivo do Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-8000"></a>Agent version 8.0.0.0
The following release notes are for version 8.0.0.0 of the Azure File Sync agent (released October 8, 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Restore performance Improvements
    - Faster recovery times for recovery done through Azure Backup. Restored files will sync back down to Azure File Sync servers much faster. 
- Improved cloud tiering portal experience  
    - If you have tiered files that are failing to recall, you can now view the recall errors in the server endpoint properties. Also, the server endpoint health will now show an error and mitigation steps if the cloud tiering filter driver is not loaded on the server.
- Simpler agent installation
    - The Az\AzureRM PowerShell module is no longer required to register the server making installation simpler and fast.
- Miscellaneous performance and reliability improvements

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão de sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- The agent is not supported on Nano Server deployment option.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- Running sysprep on a server that has the Azure File Sync agent installed is not supported and can lead to unexpected results. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

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
- Os arquivos em camadas ficarão inacessíveis se os arquivos não forem chamados antes da exclusão do ponto de extremidade do servidor. Para restaurar o acesso aos arquivos, recrie o ponto de extremidade do servidor. Se 30 dias se passaram desde que o ponto de extremidade do servidor foi excluído ou se o ponto de extremidade da nuvem foi excluído, os arquivos em camadas que não foram recuperados ficarão inutilizáveis. To learn more, see [Tiered files are not accessible on the server after deleting a server endpoint](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um arquivo de paginação de aplicativo ou SO em um local do ponto de extremidade do servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. To immediately sync files that are changed in the Azure file share, the [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet can be used to manually initiate the detection of changes in the Azure file share. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Sincronização de Arquivos do Azure não oferece suporte para mover a assinatura para um locatário do Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.

## <a name="agent-version-7200"></a>Agent version 7.2.0.0
The following release notes are for version 7.2.0.0 of the Azure File Sync agent released July 24, 2019. These notes are in addition to the release notes listed for version 7.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- Storage Sync Agent (FileSyncSvc) crashes if the proxy configuration is null.
- Server endpoint will start BCDR (error 0x80c80257 - ECS_E_BCDR_IN_PROGRESS) if multiple endpoints on the server have the same name.
- Cloud tiering reliability improvements.

## <a name="agent-version-7100"></a>Agent version 7.1.0.0
The following release notes are for version 7.1.0.0 of the Azure File Sync agent released July 12, 2019. These notes are in addition to the release notes listed for version 7.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- Accessing or browsing a server endpoint location over SMB is slow on Windows Server 2012 R2. 
- Increased CPU utilization after installing the Azure File Sync v6 agent.
- Cloud tiering telemetry improvements.
- Diversas melhorias de confiabilidade para camada e sincronização de nuvem.

## <a name="agent-version-7000"></a>Agent version 7.0.0.0
The following release notes are for version 7.0.0.0 of the Azure File Sync agent (released June 19, 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Support for larger file share sizes
    - With the preview of larger Azure file shares, we are increasing our support limits for file sync as well. In this first step, Azure File Sync now supports up to 25 TB and 50 million files in a single, syncing namespace. To apply for the large file share preview, fill in this form https://aka.ms/azurefilesatscalesurvey. 
- Support for firewall and virtual network setting on storage accounts
    - Azure File Sync now supports the firewall and virtual network setting on storage accounts. To configure your deployment to work with the firewall and virtual network setting, see [Configure firewall and virtual network settings](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
- PowerShell cmdlet to immediately sync files changed in the Azure file share
    - To immediately sync files that are changed in the Azure file share, the Invoke-AzStorageSyncChangeDetection PowerShell cmdlet can be used to manually initiate the detection of changes in the Azure file share. This cmdlet is intended for scenarios where some type of automated process is making changes in the Azure file share or the changes are done by an administrator (like moving files and directories into the share). For end-user changes, the recommendation is to install the Azure File Sync agent in an IaaS VM and have end users access the file share through the IaaS VM. This way all changes will quickly sync to other agents without the need to use the Invoke-AzStorageSyncChangeDetection cmdlet. To learn more, see the [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) documentation.
- Improved portal experience if you encounter files that are not syncing
    - If you have files that are failing to sync, we now differentiate between transient and persistent errors in the portal. Transient errors usually resolve themselves without the need for admin action. For example, a file that is currently in use will not sync until the file handle is closed. For persistent errors, we now show the number of files impacted by each error. The persistent error count is also displayed in the files not syncing column of all server endpoints in a sync group.
- Improved Azure Backup file-level restore
    - Individual files restored using Azure Backup are now detected and synced to the server endpoint faster.
- Improved cloud tiering recall cmdlet reliability 
    - The Invoke-StorageSyncFileRecall cmdlet now allows customers to specify per file retry count and per file retry delay similar to robocopy. Previously, this cmdlet would recall all tiered files under a given path in random order. With the new -Order parameter, this cmdlet will recall the hottest data first and honor the cloud tiering policy (stop recalling if the date policy is met or the volume free space is met; whichever happens first).
- Support for TLS 1.2 only (TLS 1.0 and 1.1 is disabled)
    - Azure File Sync now supports using TLS 1.2 only on servers that have TLS 1.0 and 1.1 disabled. Prior to this improvement, server registration would fail if TLS 1.0 and 1.1 was disabled on the server.
- Miscellaneous performance and reliability improvements for sync and cloud tiering
    - There are several reliability and performance improvements in this release. Some of them are targeted to make cloud tiering more efficient and Azure File Sync as a whole work better in those situations when you have a bandwidth throttling schedule set.

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão de sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- The agent is not supported on Nano Server deployment option.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- Running sysprep on a server that has the Azure File Sync agent installed is not supported and can lead to unexpected results. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

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

## <a name="agent-version-6300"></a>Agent version 6.3.0.0
The following release notes are for version 6.3.0.0 of the Azure File Sync agent released June 27, 2019. These notes are in addition to the release notes listed for version 6.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- Accessing or browsing a server endpoint location over SMB is slow on Windows Server 2012 R2 
- Increased CPU utilization after installing the Azure File Sync v6 agent
- Cloud tiering telemetry improvements

## <a name="agent-version-6200"></a>Agent version 6.2.0.0
The following release notes are for version 6.2.0.0 of the Azure File Sync agent released June 13, 2019. These notes are in addition to the release notes listed for version 6.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- After creating a server endpoint, High CPU usage may occur when background recall is downloading files to the server
- Sync and cloud tiering operations may fail with error ECS_E_SERVER_CREDENTIAL_NEEDED due to token expiration
- Recalling a file may fail if the URL to download the file contains reserved characters 

## <a name="agent-version-6100"></a>Agent version 6.1.0.0
The following release notes are for version 6.1.0.0 of the Azure File Sync agent released May 6, 2019. These notes are in addition to the release notes listed for version 6.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- Windows Admin Center fails to display the agent version and server endpoint configuration on servers which have Azure File Sync agent version 6.0 installed.

## <a name="agent-version-6000"></a>Agent version 6.0.0.0
The following release notes are for version 6.0.0.0 of the Azure File Sync agent (released April 22, 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Agent auto-update support
  - We have heard your feedback and added an auto-update feature into the Azure File Sync server agent. For more information, see [Azure File Sync agent update policy](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Support for Azure file share ACLs
  - Azure File Sync has always supported syncing ACLs between server endpoints but the ACLs were not synced to the cloud endpoint (Azure file share). This release adds support for syncing ACLs between server and cloud endpoints.
- Parallel upload and download sync sessions for a server endpoint 
  - Server endpoints now support uploading and downloading files at the same time. No more waiting for a download to complete so files can be uploaded to the Azure file share. 
- New Cloud Tiering cmdlets to get volume and tiering status
  - Two new, server-local PowerShell cmdlets can now be used to obtain cloud tiering and file recall information. They make logging information from two event channels on the server available:
    - Get-StorageSyncFileTieringResult will list all files and their paths that haven't tiered and reports on the reason why.
    - Get-StorageSyncFileRecallResult reports all file recall events. It lists every file recalled and its path as well as success or error for that recall.
  - By default, both event channels can store up to 1 MB each – you can increase the amount of files reported by increasing the event channel size.
- Support for FIPS mode
  - Azure File Sync now supports enabling FIPS mode on servers which have the Azure File Sync agent installed.
    - Prior to enabling FIPS mode on your server, install the Azure File Sync agent and [PackageManagement module](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) on your server. If FIPS is already enabled on the server, [manually download](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) the [PackageManagement module](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) to your server.
- Miscellaneous reliability improvements for cloud tiering and sync

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão de sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- The agent is not supported on Nano Server deployment option.
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

## <a name="agent-version-5200"></a>Agent version 5.2.0.0
The following release notes are for version 5.2.0.0 of the Azure File Sync agent released April 4, 2019. These notes are in addition to the release notes listed for version 5.0.2.0.

Lista dos problemas corrigidos nesta versão:  
- Reliability improvements for offline data transfer and data transfer resume features
- Sync telemetry improvements

## <a name="agent-version-5100"></a>Agent version 5.1.0.0
The following release notes are for version 5.1.0.0 of the Azure File Sync agent released March 7, 2019. These notes are in addition to the release notes listed for version 5.0.2.0.

Lista dos problemas corrigidos nesta versão:  
- Files may fail to sync with error 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) if change enumeration is failing on the server
- If a sync session or file receives an error 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE), sync will now retry the operation
- Files may fail to sync with error 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- High memory usage may occur when recalling files
- Cloud tiering telemetry improvements 

## <a name="agent-version-5020"></a>Versão do agente 5.0.2.0
As notas sobre a versão a seguir são para a versão 5.0.2.0 do agente de Sincronização de Arquivos do Azure (lançada em 12 de fevereiro de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Suporte para a nuvem do Azure Governamental
  - Adicionamos suporte à versão prévia para a nuvem do Azure Governamental. Isso exige uma assinatura inclusa na lista de permissão e download do agente especial da Microsoft. Para obter acesso à versão prévia, envie um email diretamente para [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com).
- Suporte para Eliminação de Duplicação de Dados
    - A Eliminação de Duplicação de Dados tem suporte completo com a camada de nuvem habilitada no Windows Server 2016 e no Windows Server 2019. Habilitar a eliminação de duplicação em um volume com camada de nuvem habilitada permite armazenar em cache mais arquivos localmente sem ter que provisionar mais armazenamento.
- Suporte para transferência de dados offline (por exemplo, por meio do Data Box)
    - Migre facilmente grandes quantidades de dados para a Sincronização de Arquivos do Azure do modo que escolher. You can choose Azure Data Box, AzCopy and even third-party migration services. Não é necessário usar grandes quantidades de largura de banda para colocar seus dados no Azure. No caso do Data Box, simplesmente envie-os por ele. Para obter mais informações, confira [Documentos de transferência de dados offline](https://aka.ms/AFS/OfflineDataTransfer).
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
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão de sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

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
