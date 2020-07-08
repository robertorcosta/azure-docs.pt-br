---
title: Notas de versão para o agente de Sincronização de Arquivos do Azure | Microsoft Docs
description: Notas de versão para o agente de Sincronização de Arquivos do Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 6/26/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 54a7f3f50de27747ab15f6895ebfb4f65faf5fdf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85484053"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Notas de versão para o agente de Sincronização de Arquivos do Azure
A Sincronização de Arquivos do Azure permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. As instalações do Windows Server são transformadas em um cache rápido do seu compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS). Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo fornece as notas de versão para versões com suporte do agente de Sincronização de arquivos do Azure.

## <a name="supported-versions"></a>Versões com suporte
As seguintes versões têm suporte pela Sincronização de arquivos do Azure:

| Marco | Número de versão do agente | Data de liberação | Status |
|----|----------------------|--------------|------------------|
| V 10.1 versão- [KB4522411](https://support.microsoft.com/en-us/help/4522411)| 10.1.0.0 | 5 de junho de 2020 | Com suporte-comprovante |
| Pacote cumulativo de atualizações de maio de 2020 – [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 de maio de 2020 | Com suporte |
| Versão V10 – [KB4522409](https://support.microsoft.com/en-us/help/4522409)| 10.0.0.0 | 9 de abril de 2020 | Com suporte |
| Pacote cumulativo de atualizações de dezembro de 2019 – [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 12 de dezembro de 2019 | Com suporte |
| Versão V9 – [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2 de dezembro de 2019 | Com suporte |
| Versão V8 – [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8 de outubro de 2019 | Com suporte |
| Pacote cumulativo de atualizações de julho de 2019 – [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 de julho de 2019 | Com suporte-a versão do agente irá expirar em 1º de setembro de 2020 |
| Pacote cumulativo de atualizações de julho de 2019 – [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12 de julho de 2019 | Com suporte-a versão do agente irá expirar em 1º de setembro de 2020 |
| Versão V7 – [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19 de junho de 2019 | Com suporte-a versão do agente irá expirar em 1º de setembro de 2020 |
| Versão V6 | 6.0.0.0 – 6.3.0.0 | N/D | Não compatível – As versões de agente expiraram em 21 de abril de 2020 |
| Versão V5 | 5.0.2.0 – 5.2.0.0 | N/D | Não compatível – As versões de agente expiraram em 18 de março de 2020 |
| Lançamento V4 | 4.0.1.0 – 4.3.0.0 | N/D | Não compatível – As versões de agente expiraram em 6 de novembro de 2019 |
| Versão V3 | 3.1.0.0 – 3.4.0.0 | N/D | Não compatível – As versões de agente expiraram em 19 de agosto de 2019 |
| Agentes pré-GA | 1.1.0.0 – 3.0.13.0 | N/D | Sem suporte – versão de agente expiradas em 1 de outubro de 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente de Sincronização de Arquivo do Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-10100"></a>Versão do agente 10.1.0.0
As notas de versão a seguir são para a versão 10.1.0.0 do agente de Sincronização de Arquivos do Azure lançado em 5 de junho de 2020. Essas observações são além das notas de versão listadas para a versão 10.0.0.0 e 10.0.2.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Suporte ao ponto de extremidade privado do Azure
    - O tráfego de sincronização para o serviço de sincronização de armazenamento agora pode ser enviado para um ponto de extremidade privado. Isso habilita o túnel em uma conexão de ExpressRoute ou VPN. Para saber mais, confira [Configurando pontos de extremidade de rede sincronização de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-networking-endpoints).
- Arquivos sincronizados a métrica agora exibirá o progresso enquanto uma sincronização grande estiver em execução, em vez de no final.
- Melhorias de confiabilidade diversas para a instalação do agente, camadas de nuvem, sincronização e telemetria

## <a name="agent-version-10020"></a>Versão do agente 10.0.2.0
As notas sobre a versão a seguir são da versão 10.0.2.0 do agente de Sincronização de Arquivos do Azure lançada em 19 de maio de 2020. Estas notas complementam as notas sobre a versão listadas para a versão 10.0.0.0.

Problema corrigido nessa versão:  
- O Agente de Sincronização de Armazenamento (FileSyncSvc) falha com frequência após a instalação do agente da Sincronização de Arquivos do Azure v10.

> [!Note]  
>Essa versão não foi habilitada para servidores configurados para atualizar automaticamente quando uma nova versão é disponibilizada. Para instalar essa atualização, use o Microsoft Update ou Catálogo de Atualizações da Microsoft (confira [KB4522412](https://support.microsoft.com/help/4522412) para ver as instruções de instalação).

## <a name="agent-version-10000"></a>Versão do agente 10.0.0.0
As notas sobre a versão a seguir são para a versão 10.0.0.0 do agente de Sincronização de Arquivos do Azure (lançada em 9 de abril de 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Progresso da sincronização aprimorado no portal
    - Com a versão do agente v10, o portal do Azure em breve começará a mostrar o tipo de sessão de sincronização em execução. Por ex.: download inicial, download regular, recall em segundo plano (casos rápidos de recuperação de desastre) e semelhantes. 

- Experiência aprimorada no portal de camada de nuvem
    - Se você tiver arquivos que estão falhando na camada ou no recall, agora você poderá exibir os erros de camadas nas propriedades do ponto de extremidade do servidor.
    - Informações adicionais de camada de nuvem estão disponíveis para um ponto de extremidade do servidor:
        - Tamanho do cache local
        - Eficiência de uso do cache
        - Detalhes da política de camada de nuvem: tamanho do volume, espaço livre atual ou hora do último acesso ao arquivo mais antigo no cache local.
    - Essas alterações serão disponibilizadas no portal do Azure logo após a versão inicial do agente v10.

- Compatibilidade para mover o Serviço de Sincronização de Armazenamento e/ou a conta de armazenamento para um locatário AAD (Azure Active Directory) diferente
    - A Sincronização de Arquivos do Azure agora é compatível com o recurso de mover o Serviço de Sincronização de Armazenamento e/ou a conta de armazenamento para um grupo de recursos, assinatura ou locatário do Azure AD.
    
- Diversos aprimoramentos de desempenho e confiabilidade
    - A detecção de alteração no compartilhamento de arquivo do Azure poderá falhar se a VNET (rede virtual) e as regras de firewall estiverem configuradas na conta de armazenamento.
    - Consumo de memória reduzido associado ao recall. 
    - Desempenho aprimorado ao usar o cmdlet [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection).
    - Outros aprimoramentos de confiabilidade. 
    
### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão do sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- O agente não é compatível com a opção de implantação do Nano Server.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- Não há compatibilidade com a execução do sysprep em um servidor que tenha o agente de Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

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
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Para sincronizar imediatamente os arquivos alterados no compartilhamento de arquivo do Azure, o cmdlet [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) do PowerShell pode ser usado para iniciar manualmente a detecção de alterações no compartilhamento de arquivo do Azure. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos, uma assinatura ou um locatário do Azure AD diferente. Depois que o serviço de sincronização de armazenamento ou a conta de armazenamento for movido, você precisará conceder ao aplicativo Microsoft. StorageSync acesso à conta de armazenamento (consulte [garantir que sincronização de arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Ao criar o ponto de extremidade na nuvem, o serviço de sincronização de armazenamento e a conta de armazenamento devem estar no mesmo locatário do Azure AD. Depois que o ponto de extremidade na nuvem é criado, o serviço de sincronização de armazenamento e a conta de armazenamento podem ser movidos para diferentes locatários do Azure AD.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.

## <a name="agent-version-9100"></a>Versão do agente 9.1.0.0
As notas sobre a versão a seguir são para a versão 9.1.0.0 do agente de Sincronização de Arquivos do Azure lançada em 12 de dezembro de 2019. Estas notas complementam as notas sobre a versão listadas para a versão 9.0.0.0.

Problema corrigido nessa versão:  
- A sincronização falha com um dos seguintes erros após a atualização para o agente 9.0 da Sincronização de Arquivos do Azure:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>Versão do agente 9.0.0.0
As notas sobre a versão a seguir são para a versão 9.0.0.0 do agente de Sincronização de Arquivos do Azure lançada em 2 de dezembro de 2019.

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Compatibilidade com a restauração de autoatendimento
    - Os usuários agora podem restaurar arquivos usando o recurso de versão anterior. Antes da versão v9, o recurso de versão anterior não era compatível com os volumes que tinham camada de nuvem habilitadas. Esse recurso deve ser habilitado para cada volume separadamente, em que um ponto de extremidade com camada de nuvem habilitada existe. Para obter mais informações, consulte:  
[A restauração de autoatendimento por meio de versões anteriores e do VSS (Serviço de Cópias de Sombra de Volume)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Compatibilidade com tamanhos maiores de compartilhamento de arquivo 
    - A Sincronização de Arquivos do Azure agora é compatível com até 64 TiB e 100 milhões de arquivos em um único namespace de sincronização.  
 
- Compatibilidade com a eliminação de duplicação de dados no Server 2019 
    - A Eliminação de Duplicação de Dados é compatível com a camada de nuvem habilitada no Windows Server 2019. Para ter compatibilidade com a Eliminação de Duplicação de Dados em volumes com camada de nuvem, a atualização do Windows [KB4520062](https://support.microsoft.com/help/4520062) precisa ser instalada. 
 
- Aumento do tamanho do arquivo mínimo para um arquivo ser colocado em camadas 
    - O tamanho do arquivo mínimo para um arquivo ser colocado em camadas agora é baseado no tamanho do cluster do sistema de arquivos (duas vezes o tamanho do cluster de sistema de arquivos). Por exemplo, por padrão, o tamanho do cluster do sistema de arquivos NTFS é de 4 KB, o tamanho mínimo resultante para um arquivo ser colocado em camadas é de 8 KB. 
 
- Cmdlet de teste da conectividade de rede 
    - Como parte da configuração da Sincronização de Arquivos do Azure, vários pontos de extremidade de serviço devem ser contatados. Cada um deles tem seu próprio nome DNS que precisa ser acessível ao servidor. Essas URLs também são específicas para a região em que um servidor está registrado. Depois que um servidor é registrado, o cmdlet de teste de conectividade (utilitário de registro do PowerShell e do servidor) pode ser usado para testar as comunicações com todas as URLs específicas desse servidor. Esse cmdlet pode ajudar a solucionar problemas quando a comunicação incompleta impede que o servidor trabalhe totalmente com Sincronização de Arquivos do Azure e pode ser usado para ajustar as configurações de proxy e firewall.  
 
        Para executar o teste de conectividade de rede, execute os seguintes comandos do PowerShell: 
 
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- Aprimoramento ao remover o ponto de extremidade do servidor quando a camada de nuvem está habilitada 
    - Como antes, remover um ponto de extremidade do servidor não resulta na remoção de arquivos no compartilhamento de arquivo do Azure. No entanto, o comportamento de pontos de nova análise no servidor local foi alterado. Pontos de nova análise (ponteiros para arquivos que não são locais no servidor) agora são excluídos durante a remoção de um ponto de extremidade do servidor. Os arquivos totalmente armazenados em cache permanecerão no servidor. Esse aprimoramento foi feito para impedir [arquivos em camadas órfãos](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) ao remover um ponto de extremidade do servidor. Se o ponto de extremidade do servidor for recriado, os pontos de nova análise para os arquivos em camadas serão recriados no servidor.  
 
- Aprimoramento de desempenho e confiabilidade 
    - Redução nas falhas de recall. O tamanho de recall agora é ajustado automaticamente com base na largura de banda da rede. 
    - Melhor desempenho de download ao adicionar um novo servidor a um grupo de sincronização. 
    - Arquivos reduzidos não sincronizam devido a conflitos de restrição. 
    - Os arquivos falharão na camada ou terão um recall inesperado em determinados cenários se o caminho do ponto de extremidade do servidor for um ponto de montagem de volume.
    
### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão do sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- O agente não é compatível com a opção de implantação do Nano Server.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- Não há compatibilidade com a execução do sysprep em um servidor que tenha o agente de Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

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
- Os arquivos em camadas ficarão inacessíveis se os arquivos não forem chamados antes da exclusão do ponto de extremidade do servidor. Para restaurar o acesso aos arquivos, recrie o ponto de extremidade do servidor. Se 30 dias se passaram desde que o ponto de extremidade do servidor foi excluído ou se o ponto de extremidade da nuvem foi excluído, os arquivos em camadas que não foram recuperados ficarão inutilizáveis. Para saber mais, confira [Arquivos em camadas não podem ser acessados no servidor depois da exclusão de um ponto de extremidade do servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um arquivo de paginação de aplicativo ou SO em um local do ponto de extremidade do servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Para sincronizar imediatamente os arquivos alterados no compartilhamento de arquivo do Azure, o cmdlet [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) do PowerShell pode ser usado para iniciar manualmente a detecção de alterações no compartilhamento de arquivo do Azure. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Sincronização de Arquivos do Azure não oferece suporte para mover a assinatura para um locatário do Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.
- Os arquivos podem falhar na camada se pagefile.sys estiver localizado em um volume que tenha a camada de nuvem habilitada. O pagefile.sys deve estar localizado em um volume que tenha a camada de nuvem desabilitada.

## <a name="agent-version-8000"></a>Versão do agente 8.0.0.0
As notas sobre a versão a seguir são referentes à versão 8.0.0.0 do agente de Sincronização de Arquivos do Azure lançada em 8 de outubro de 2019.

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Aprimoramentos de desempenho de restauração
    - Tempos mais rápidos para a recuperação feita por meio do Backup do Azure. Os arquivos restaurados serão sincronizados novamente com os servidores da Sincronização de Arquivos do Azure muito mais rápido. 
- Experiência aprimorada no portal de camada de nuvem  
    - Se você tiver arquivos em camadas que estão falhando no recall, agora você poderá ver os erros de recall nas propriedades do ponto de extremidade do servidor. Além disso, a integridade do ponto de extremidade do servidor agora mostrará um erro e as etapas de mitigação se o driver do filtro de camada de nuvem não estiver carregado no servidor.
- Instalação de agente mais simples
    - O módulo do PowerShell Az\AzureRM não é mais necessário para registrar o servidor, tornando a instalação mais simples e rápida.
- Diversos aprimoramentos de desempenho e confiabilidade

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão do sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- O agente não é compatível com a opção de implantação do Nano Server.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- Não há compatibilidade com a execução do sysprep em um servidor que tenha o agente de Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

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
- Os arquivos em camadas ficarão inacessíveis se os arquivos não forem chamados antes da exclusão do ponto de extremidade do servidor. Para restaurar o acesso aos arquivos, recrie o ponto de extremidade do servidor. Se 30 dias se passaram desde que o ponto de extremidade do servidor foi excluído ou se o ponto de extremidade da nuvem foi excluído, os arquivos em camadas que não foram recuperados ficarão inutilizáveis. Para saber mais, confira [Arquivos em camadas não podem ser acessados no servidor depois da exclusão de um ponto de extremidade do servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um arquivo de paginação de aplicativo ou SO em um local do ponto de extremidade do servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Para sincronizar imediatamente os arquivos alterados no compartilhamento de arquivo do Azure, o cmdlet [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) do PowerShell pode ser usado para iniciar manualmente a detecção de alterações no compartilhamento de arquivo do Azure. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Sincronização de Arquivos do Azure não oferece suporte para mover a assinatura para um locatário do Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.

## <a name="agent-version-7200"></a>Versão do agente 7.2.0.0
As notas sobre a versão a seguir são para a versão 7.2.0.0 do agente de Sincronização de Arquivos do Azure (lançada em 24 de julho de 2019). Estas notas complementam as notas sobre a versão listadas para a versão 7.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- O Agente de Sincronização de Armazenamento (FileSyncSvc) falhará se a configuração de proxy for nula.
- O ponto de extremidade do servidor iniciará BCDR (erro 0x80c80257-ECS_E_BCDR_IN_PROGRESS) se vários pontos de extremidades no servidor tiverem o mesmo nome.
- Aprimoramentos de confiabilidade na camada de nuvem.

## <a name="agent-version-7100"></a>Versão do agente 7.1.0.0
As notas sobre a versão a seguir são para a versão 7.1.0.0 do agente de Sincronização de Arquivos do Azure (lançada em 12 de julho de 2019). Estas notas complementam as notas sobre a versão listadas para a versão 7.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- O acesso ou a navegação em um local de ponto de extremidade do servidor no SMB é lento no Windows Server 2012 R2. 
- Maior utilização da CPU após a instalação do agente v6 da Sincronização de Arquivos do Azure.
- Melhorias de telemetria na camada de nuvem.
- Diversas melhorias de confiabilidade para camada e sincronização de nuvem.

## <a name="agent-version-7000"></a>Versão do agente 7.0.0.0
As notas sobre a versão a seguir são para a versão 7.0.0.0 do agente de Sincronização de Arquivos do Azure lançada em 19 de junho de 2019.

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Compatibilidade com tamanhos maiores de compartilhamento de arquivo
    - Com a visualização de compartilhamentos de arquivos grandes do Azure, estamos aumentando nossos limites de suporte para sincronização de arquivos também. Nesta primeira etapa, a Sincronização de Arquivos do Azure agora é compatível com até 25 TB e 50 milhões de arquivos em um único namespace de sincronização. Para aplicar a visualização de compartilhamento de arquivo grande, preencha este formulário https://aka.ms/azurefilesatscalesurvey. 
- Compatibilidade com a configuração de firewall e rede virtual em contas de armazenamento
    - O Sincronização de Arquivos do Azure agora é compatível com a configuração de firewall e rede virtual em contas de armazenamento. Para configurar a implantação para funcionar com a configuração de firewall e rede virtual, confira [Configurar as configurações de firewall e rede virtual](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
- Cmdlet do PowerShell para sincronizar imediatamente os arquivos alterados no compartilhamento de arquivo do Azure
    - Para sincronizar imediatamente os arquivos alterados no compartilhamento de arquivo do Azure, o cmdlet Invoke-AzStorageSyncChangeDetection do PowerShell pode ser usado para iniciar manualmente a detecção de alterações no compartilhamento de arquivo do Azure. Esse cmdlet pode ser usado em cenários em que algum tipo de processo automatizado faz alterações no compartilhamento de arquivo do Azure ou as alterações são feitas por um administrador (como mover arquivos e diretórios para o compartilhamento). Para alterações do usuário final, a recomendação é instalar o agente de Sincronização de Arquivos do Azure em uma VM IaaS e fazer com que os usuários finais acessem o compartilhamento de arquivo por meio da VM IaaS. Dessa forma, todas as alterações serão rapidamente sincronizadas com outros agentes sem a necessidade de usar o cmdlet Invoke-AzStorageSyncChangeDetection. Para saber mais, confira a documentação [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection).
- Experiência de portal aprimorada quando você encontra arquivos que não estão sincronizando
    - Se você tiver arquivos que estão falhando na sincronização, agora diferenciaremos os erros transitórios e persistentes no portal. Normalmente, os erros transitórios se resolvem sem a necessidade de ação do administrador. Por exemplo, um arquivo que está em uso no momento não será sincronizado até que o identificador de arquivo seja fechado. Para erros persistentes, agora mostramos o número de arquivos afetados por todos os erros. A contagem de erros persistentes também é exibida na coluna arquivos não sincronizados de todos os pontos de extremidade do servidor em um grupo de sincronização.
- Aprimoramento da restauração no nível do arquivo no Backup do Azure
    - Arquivos individuais restaurados com o Backup do Azure agora são detectados e sincronizados com o ponto de extremidade do servidor mais rapidamente.
- Aprimoramento na confiabilidade do cmdlet de recall de camada de nuvem 
    - O cmdlet Invoke-StorageSyncFileRecall agora permite que os clientes especifiquem a contagem de repetições e o atraso na repetição por arquivo, semelhante ao robocopy. Anteriormente, esse cmdlet fará um recall de todos os arquivos em camadas em um determinado caminho em ordem aleatória. Com o novo parâmetro -Order, esse cmdlet fará um recall dos dados de acesso frequente primeiro e honrará a política de camada de nuvem (pare de chamar novamente se a política de data for atendida ou se o espaço livre do volume for atendido; o que acontecer primeiro).
- Compatibilidade somente com TLS 1.2 (TLS 1.0 e 1.1 estão desabilitados)
    - A Sincronização de Arquivos do Azure agora é compatível somente com o uso do TLS 1.2 em servidores com o TLS 1.0 e 1.1 desabilitados. Antes desse aprimoramento, o registro do servidor falharia se o TLS 1.0 e 1.1 estivessem desabilitados no servidor.
- Diversos aprimoramentos de desempenho e confiabilidade para sincronização e camada de nuvem
    - Há várias melhorias de confiabilidade e desempenho nesta versão. Algumas delas são destinadas a tornar a camada de nuvem mais eficiente e a Sincronização de Arquivos do Azure um trabalho completo nas situações em que você tem um agendamento de limitação de largura de banda definido.

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão do sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- O agente não é compatível com a opção de implantação do Nano Server.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- Não há compatibilidade com a execução do sysprep em um servidor que tenha o agente de Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

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

## <a name="agent-version-6300"></a>Versão do agente 6.3.0.0
As notas sobre a versão a seguir são para a versão 6.3.0.0 do agente de Sincronização de Arquivos do Azure lançada em 27 de maio de 2019. Estas notas complementam as notas sobre a versão listadas para a versão 6.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- O acesso ou a navegação em um local de ponto de extremidade do servidor no SMB é lento no Windows Server 2012 R2 
- Maior utilização da CPU após a instalação do agente v6 da Sincronização de Arquivos do Azure
- Aprimoramentos de telemetria na camada de nuvem

## <a name="agent-version-6200"></a>Versão do agente 6.2.0.0
As notas sobre a versão a seguir são para a versão 6.2.0.0 do agente de Sincronização de Arquivos do Azure lançada em 13 de maio de 2019. Estas notas complementam as notas sobre a versão listadas para a versão 6.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- Depois de criar um ponto de extremidade do servidor, pode ocorrer um alto uso da CPU quando o recall em segundo plano baixa arquivos para o servidor
- As operações de sincronização e camada de nuvem podem falhar com o erro ECS_E_SERVER_CREDENTIAL_NEEDED devido à expiração do token
- Poderá haver falha no recall de um arquivo se a URL para baixá-lo contiver caracteres reservados 

## <a name="agent-version-6100"></a>Versão do agente 6.1.0.0
As notas sobre a versão a seguir são da versão 6.1.0.0 do agente de Sincronização de Arquivos do Azure lançada em 6 de maio de 2019. Estas notas complementam as notas sobre a versão listadas para a versão 6.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- O Centro de Administração do Windows falha ao exibir a versão do agente e a configuração do ponto de extremidade do servidor em servidores que têm o agente de Sincronização de Arquivos do Azure versão 6.0 instalado.

## <a name="agent-version-6000"></a>Versão do agente 6.0.0.0
As notas sobre a versão a seguir são para a versão 6.0.0.0 do agente de Sincronização de Arquivos do Azure (lançada em 22 de abril de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Compatibilidade com a atualização automática do agente
  - Seus comentários foram ouvidos. Adicionamos um recurso de atualização automática ao agente do servidor da Sincronização de Arquivos do Azure. Para saber mais, confira [Política de atualização do agente da Sincronização de Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Compatibilidade com as ACLs de compartilhamento de arquivo do Azure
  - A Sincronização de Arquivos do Azure sempre teve compatibilidade com a sincronização de ACLs entre pontos de extremidade do servidor. No entanto, as ACLs não eram sincronizadas com o ponto de extremidade na nuvem (compartilhamento de arquivo do Azure). Esta versão tem compatibilidade com a sincronização de ACLs entre servidores e pontos de extremidade de nuvem.
- Sessões de sincronização de upload e download paralelos para um ponto de extremidade de servidor 
  - Os pontos de extremidade do servidor agora são compatíveis com o carregamento e o download de arquivos ao mesmo tempo. Não é mais preciso aguardar a conclusão de um download para fazer upload dos arquivos para o compartilhamento de arquivo do Azure. 
- Novos cmdlets de camada de nuvem para obter o volume e o status de camadas
  - Dois novos cmdlets do PowerShell de servidor local agora podem ser usados para obter informações sobre camada de nuvem e recall de arquivos. Eles disponibilizam informações de log de dois canais de eventos no servidor disponível:
    - Get-StorageSyncFileTieringResult listará todos os arquivos e seus caminhos que não estão em camadas e gera relatórios sobre o motivo.
    - Get-StorageSyncFileRecallResult relata todos os eventos de recall de arquivo. Ele lista todos os arquivos de recall e o caminho, bem como êxito ou erro para o recall.
  - Por padrão, ambos os canais de eventos podem armazenar até 1 MB cada – você pode aumentar a quantidade de arquivos relatados ampliando o tamanho do canal de evento.
- Compatibilidade com o modo FIPS
  - A Sincronização de Arquivos do Azure agora é compatível com a habilitação do modo FIPS em servidores que têm o agente de Sincronização de Arquivos do Azure instalado.
    - Antes de habilitar o modo FIPS no servidor, instale o agente de Sincronização de Arquivos do Azure e o [módulo PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) no servidor. Se o FIPS já estiver habilitado no servidor, [baixe manualmente](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) o módulo [PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) para o servidor.
- Diversos aprimoramentos de confiabilidade para camada de nuvem e sincronização

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão do sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- O agente não é compatível com a opção de implantação do Nano Server.
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
As notas sobre a versão a seguir são para a versão 5.2.0.0 do agente de Sincronização de Arquivos do Azure lançada em 4 de abril de 2019. Estas notas complementam as notas sobre a versão listadas para a versão 5.0.2.0.

Lista dos problemas corrigidos nesta versão:  
- Aprimoramentos de confiabilidade para transferência de dados offline e recursos de retomada de transferência de dados
- Aprimoramentos de telemetria de sincronização

## <a name="agent-version-5100"></a>Versão do agente 5.1.0.0
As notas sobre a versão a seguir são para a versão 5.1.0.0 do agente de Sincronização de Arquivos do Azure lançada em 7 de março de 2019. Estas notas complementam as notas sobre a versão listadas para a versão 5.0.2.0.

Lista dos problemas corrigidos nesta versão:  
- Os arquivos podem falhar ao serem sincronizados com o erro 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) se a enumeração de alteração estiver falhando no servidor
- Se uma sessão ou arquivo de sincronização receber um erro 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE), agora a sincronização tentará a operação novamente
- Os arquivos podem falhar ao serem sincronizados com o erro 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- Pode ocorrer alto uso de memória no recall dos arquivos
- Aprimoramentos de telemetria na camada de nuvem 

## <a name="agent-version-5020"></a>Versão do agente 5.0.2.0
As notas sobre a versão a seguir são para a versão 5.0.2.0 do agente de Sincronização de Arquivos do Azure (lançada em 12 de fevereiro de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Suporte para a nuvem do Azure Governamental
  - Adicionamos suporte à versão prévia para a nuvem do Azure Governamental. Isso exige uma assinatura inclusa na lista de permissão e download do agente especial da Microsoft. Para obter acesso à versão prévia, envie um email diretamente para [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com).
- Suporte para Eliminação de Duplicação de Dados
    - A Eliminação de Duplicação de Dados tem suporte completo com a camada de nuvem habilitada no Windows Server 2016 e no Windows Server 2019. Habilitar a eliminação de duplicação em um volume com camada de nuvem habilitada permite armazenar em cache mais arquivos localmente sem ter que provisionar mais armazenamento.
- Suporte para transferência de dados offline (por exemplo, por meio do Data Box)
    - Migre facilmente grandes quantidades de dados para a Sincronização de Arquivos do Azure do modo que escolher. Você pode escolher o Azure Data Box, o AzCopy e até mesmo serviços de migração de terceiros. Não é necessário usar grandes quantidades de largura de banda para colocar seus dados no Azure. No caso do Data Box, simplesmente envie-os por ele. Para obter mais informações, confira [Documentos de transferência de dados offline](https://aka.ms/AFS/OfflineDataTransfer).
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
