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
ms.openlocfilehash: 1b11c6beda5b261d1edc77dcbb365d8d8df8bd09
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176719"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Notas de versão para o agente de Sincronização de Arquivos do Azure
A Sincronização de Arquivos do Azure permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. As instalações do Windows Server são transformadas em um cache rápido do seu compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS). Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo fornece as notas de versão para versões com suporte do agente de Sincronização de arquivos do Azure.

## <a name="supported-versions"></a>Versões com suporte
As seguintes versões têm suporte pela Sincronização de arquivos do Azure:

| Marco | Número de versão do agente | Data de liberação | Status |
|----|----------------------|--------------|------------------|
| V8 versão – [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8 de outubro de 2019 | Suportado |
| Pacote cumulativo de atualizações de julho de 2019- [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 de julho de 2019 | Suportado |
| Pacote cumulativo de atualizações de julho de 2019- [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12 de julho de 2019 | Com suporte |
| Versão v7- [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19 de junho de 2019 | Com suporte |
| Pacote cumulativo de atualizações de junho de 2019- [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 27 de junho de 2019 | Com suporte |
| Pacote cumulativo de atualizações de junho de 2019- [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 13 de junho de 2019 | Com suporte |
| Pacote cumulativo de atualizações 2019 de maio- [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 7 de maio de 2019 | Com suporte |
| Versão V6- [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 21 de abril de 2019 | Com suporte |
| Pacote cumulativo de atualizações de abril de 2019- [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | 4 de abril de 2019 | Com suporte |
| Pacote cumulativo de atualizações de março de 2019- [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | 7 de março de 2019 | Com suporte |
| Versão V5 – [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 12 de fevereiro de 2019 | Com suporte |
| Pacote cumulativo de atualizações de janeiro de 2019 – [KB4481059](https://support.microsoft.com/help/4481059)| 4.3.0.0 | 14 de janeiro de 2019 | Com suporte-a versão do agente expirará em 5 de novembro de 2019 |
| Pacote cumulativo de atualizações de dezembro de 2018 – [KB4459990](https://support.microsoft.com/help/4459990)| 4.2.0.0 | 10 de dezembro de 2018 | Com suporte-a versão do agente expirará em 5 de novembro de 2019 |
| Pacote cumulativo de atualizações de dezembro de 2018 | 4.1.0.0 | 4 de dezembro de 2018 | Com suporte-a versão do agente expirará em 5 de novembro de 2019 |
| Lançamento V4 | 4.0.1.0 | 13 de novembro de 2018 | Com suporte-a versão do agente expirará em 5 de novembro de 2019 |
| Versão v3 | 3.1.0.0-3.4.0.0 | Sem Suporte | Sem suporte-as versões do agente expiraram em 19 de agosto de 2019 |
| Agentes de pré-lançamento | 1.1.0.0 – 3.0.13.0 | N/D | Sem suporte – versão de agente expiradas em 1 de outubro de 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente de Sincronização de Arquivo do Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-8000"></a>Versão do agente 8.0.0.0
As notas de versão a seguir são para a versão 8.0.0.0 do agente de Sincronização de Arquivos do Azure (lançado em 8 de outubro de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Aprimoramentos de desempenho de restauração
    - Tempos de recuperação mais rápidos para recuperação feita por meio do backup do Azure. Os arquivos restaurados serão sincronizados novamente para Sincronização de Arquivos do Azure servidores muito mais rapidamente. 
- Experiência aprimorada no portal de camadas de nuvem  
    - Se você tiver arquivos em camadas que estão falhando na recuperação, agora você poderá exibir os erros de recuperação nas propriedades do ponto de extremidade do servidor. Além disso, a integridade do ponto de extremidade do servidor agora mostrará um erro e as etapas de mitigação se o driver do filtro de camadas de nuvem não estiver carregado no servidor.
- Instalação do agente mais simples
    - O módulo do PowerShell do Az\AzureRM não é mais necessário para registrar o servidor, tornando a instalação mais simples e rápida.
- Melhorias de desempenho e confiabilidade diversas

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão de sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- Não há suporte para o agente na opção de implantação do nano Server.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- Não há suporte para executar o Sysprep em um servidor com o agente de Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

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
- Os arquivos em camadas ficarão inacessíveis se os arquivos não forem chamados antes da exclusão do ponto de extremidade do servidor. Para restaurar o acesso aos arquivos, recrie o ponto de extremidade do servidor. Se 30 dias se passaram desde que o ponto de extremidade do servidor foi excluído ou se o ponto de extremidade da nuvem foi excluído, os arquivos em camadas que não foram recuperados ficarão inutilizáveis. Para saber mais, consulte [arquivos em camadas não podem ser acessados no servidor após a exclusão de um ponto de extremidade do servidor](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- A camada de nuvem não tem suporte no volume do sistema. Para criar um ponto de extremidade do servidor no volume do sistema, desabilite a disposição em camadas da nuvem ao criar o ponto de extremidade do servidor.
- O Clustering de Failover só tem suporte com discos de cluster, não com CSVs (Volumes Compartilhados Clusterizados).
- Um ponto de extremidade de servidor não pode estar aninhado. Ele pode coexistir no mesmo volume em paralelo com outro ponto de extremidade.
- Não armazene um arquivo de paginação de aplicativo ou SO em um local do ponto de extremidade do servidor.
- O nome do servidor no portal não será atualizado se o servidor for renomeado.

### <a name="cloud-endpoint"></a>Ponto de extremidade da nuvem
- A Sincronização de Arquivos do Azure dá suporte a alterações diretamente no compartilhamento de arquivos do Azure. No entanto, as alterações feitas no compartilhamento de arquivos do Azure precisam primeiro ser descobertas por um trabalho de detecção de alteração da Sincronização de Arquivos do Azure. Um trabalho de detecção de alterações é iniciado para um ponto de extremidade da nuvem uma vez a cada 24 horas. Para sincronizar imediatamente os arquivos que são alterados no compartilhamento de arquivos do Azure, o cmdlet [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) do PowerShell pode ser usado para iniciar manualmente a detecção de alterações no compartilhamento de arquivos do Azure. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure através do protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração de sincronização.
- O serviço de sincronização de armazenamento e/ou a conta de armazenamento podem ser movidos para um grupo de recursos ou assinatura diferente dentro do locatário do Azure AD existente. Se a conta de armazenamento for movida, você precisará conceder o acesso do Serviço de Sincronização de Arquivos Híbrido para a conta de armazenamento (consulte [Certifique-se de que a Sincronização de Arquivos do Azure tenha acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Sincronização de Arquivos do Azure não oferece suporte para mover a assinatura para um locatário do Azure AD diferente.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.

## <a name="agent-version-7200"></a>Versão do agente 7.2.0.0
As notas de versão a seguir são para a versão 7.2.0.0 do agente de Sincronização de Arquivos do Azure lançado em 24 de julho de 2019. Essas notas são além das notas de versão listadas para a versão 7.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- O agente de sincronização de armazenamento (FileSyncSvc) falhará se a configuração de proxy for nula.
- O ponto de extremidade do servidor iniciará BCDR (erro 0x80c80257-ECS_E_BCDR_IN_PROGRESS) se vários pontos de extremidades no servidor tiverem o mesmo nome.
- Melhorias de confiabilidade em camadas de nuvem.

## <a name="agent-version-7100"></a>Versão do agente 7.1.0.0
As notas de versão a seguir são para a versão 7.1.0.0 do agente de Sincronização de Arquivos do Azure lançado em 12 de julho de 2019. Essas notas são além das notas de versão listadas para a versão 7.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- O acesso ou a navegação de um local de ponto de extremidade do servidor sobre o SMB é lento no Windows Server 2012 R2. 
- Maior utilização da CPU após a instalação do agente Sincronização de Arquivos do Azure v6.
- Aprimoramentos de telemetria em camadas de nuvem.
- Diversas melhorias de confiabilidade para camada e sincronização de nuvem.

## <a name="agent-version-7000"></a>Versão do agente 7.0.0.0
As notas de versão a seguir são para a versão 7.0.0.0 do agente de Sincronização de Arquivos do Azure (lançada em 19 de junho de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Suporte para tamanhos maiores de compartilhamento de arquivos
    - Com a visualização de grandes compartilhamentos de arquivos do Azure, estamos aumentando nossos limites de suporte para sincronização de arquivos também. Nesta primeira etapa, Sincronização de Arquivos do Azure agora dá suporte a até 25 TB e 50 milhões arquivos em um único namespace de sincronização. Para aplicar a visualização de compartilhamento de arquivos grandes, preencha este formulário https://aka.ms/azurefilesatscalesurvey. 
- Suporte para configuração de rede virtual e firewall em contas de armazenamento
    - O Sincronização de Arquivos do Azure agora dá suporte à configuração de firewall e rede virtual em contas de armazenamento. Para configurar sua implantação para trabalhar com a configuração de rede virtual e firewall, consulte [definir configurações de firewall e rede virtual](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
- Cmdlet do PowerShell para sincronizar imediatamente os arquivos alterados no compartilhamento de arquivos do Azure
    - Para sincronizar imediatamente os arquivos que são alterados no compartilhamento de arquivos do Azure, o cmdlet Invoke-AzStorageSyncChangeDetection do PowerShell pode ser usado para iniciar manualmente a detecção de alterações no compartilhamento de arquivos do Azure. Esse cmdlet destina-se a cenários em que algum tipo de processo automatizado está fazendo alterações no compartilhamento de arquivos do Azure ou as alterações são feitas por um administrador (como mover arquivos e diretórios para o compartilhamento). Para alterações do usuário final, a recomendação é instalar o agente de Sincronização de Arquivos do Azure em uma VM IaaS e fazer com que os usuários finais acessem o compartilhamento de arquivos por meio da VM IaaS. Dessa forma, todas as alterações serão rapidamente sincronizadas com outros agentes sem a necessidade de usar o cmdlet Invoke-AzStorageSyncChangeDetection. Para saber mais, confira a documentação do [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) .
- Experiência de portal aprimorada se você encontrar arquivos que não estão sincronizando
    - Se você tiver arquivos que estão falhando na sincronização, agora diferenciaremos os erros transitórios e persistentes no Portal. Normalmente, os erros transitórios se resolvem sem a necessidade de ação de administrador. Por exemplo, um arquivo que está em uso no momento não será sincronizado até que o identificador do arquivo seja fechado. Para erros persistentes, agora mostramos o número de arquivos afetados por cada erro. A contagem de erros persistentes também é exibida na coluna arquivos não Sincronizando de todos os pontos de extremidade do servidor em um grupo de sincronização.
- Restauração em nível de arquivo de backup do Azure aprimorada
    - Arquivos individuais restaurados usando o backup do Azure agora são detectados e sincronizados com o ponto de extremidade do servidor mais rapidamente.
- Melhoria na confiabilidade do cmdlet de recuperação de camadas de nuvem 
    - O cmdlet Invoke-StorageSyncFileRecall agora permite que os clientes especifiquem a contagem de repetições por arquivo e o atraso de repetição de arquivo semelhante ao Robocopy. Anteriormente, esse cmdlet rechamaria todos os arquivos em camadas em um determinado caminho em ordem aleatória. Com o novo parâmetro de ordem, esse cmdlet rechamará os dados mais interessantes primeiro e honrará a política de camadas de nuvem (Pare de se rechamar se a política de data for atendida ou se o espaço livre do volume for atendido; o que acontecer primeiro).
- Suporte para TLS 1,2 somente (o TLS 1,0 e 1,1 está desabilitado)
    - Sincronização de Arquivos do Azure agora dá suporte ao uso do TLS 1,2 somente em servidores com TLS 1,0 e 1,1 desabilitados. Antes dessa melhoria, o registro do servidor falharia se o TLS 1,0 e o 1,1 estivesse desabilitado no servidor.
- Melhorias de desempenho e confiabilidade diversas para a sincronização e a disposição em camadas de nuvem
    - Há várias melhorias de confiabilidade e desempenho nesta versão. Algumas delas são destinadas a tornar a camada de nuvem mais eficiente e Sincronização de Arquivos do Azure como um trabalho completo nessas situações quando você tem um agendamento de limitação de largura de banda definido.

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão de sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- Não há suporte para o agente na opção de implantação do nano Server.
- O agente tem suporte somente no Windows Server 2019, no Windows Server 2016 e no Windows Server 2012 R2.
- O agente requer pelo menos 2 GiB de memória. Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica ativada, a VM deverá ser configurada com um mínimo de 2048 MiB de memória.
- O serviço Agente de Sincronização de Armazenamento (FileSyncSvc) não dá suporte a pontos de extremidade do servidor localizados em um volume que tenha o diretório SVI (informações de volume do sistema) compactado. Essa configuração levará a resultados inesperados.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- Não há suporte para executar o Sysprep em um servidor com o agente de Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.

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
As notas de versão a seguir são para a versão 6.3.0.0 do agente de Sincronização de Arquivos do Azure lançada em 27 de junho de 2019. Essas notas são além das notas de versão listadas para a versão 6.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- O acesso ou a navegação em um local de ponto de extremidade do servidor sobre o SMB é lento no Windows Server 2012 R2 
- Maior utilização da CPU após a instalação do agente Sincronização de Arquivos do Azure V6
- Aprimoramentos de telemetria em camadas de nuvem

## <a name="agent-version-6200"></a>Versão do agente 6.2.0.0
As notas de versão a seguir são para a versão 6.2.0.0 do agente de Sincronização de Arquivos do Azure lançado em 13 de junho de 2019. Essas notas são além das notas de versão listadas para a versão 6.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- Depois de criar um ponto de extremidade do servidor, o alto uso da CPU pode ocorrer quando o recall em segundo plano está baixando arquivos para o servidor
- As operações de camadas de sincronização e de nuvem podem falhar com o erro ECS_E_SERVER_CREDENTIAL_NEEDED devido à expiração do token
- A rechamada de um arquivo poderá falhar se a URL para baixar o arquivo contiver caracteres reservados 

## <a name="agent-version-6100"></a>Versão do agente 6.1.0.0
As notas de versão a seguir são para a versão 6.1.0.0 do agente de Sincronização de Arquivos do Azure lançado em 6 de maio de 2019. Essas notas são além das notas de versão listadas para a versão 6.0.0.0.

Lista dos problemas corrigidos nesta versão:  
- O centro de administração do Windows falha ao exibir a versão do agente e a configuração do ponto de extremidade do servidor em servidores que têm o agente Sincronização de Arquivos do Azure versão 6,0 instalado.

## <a name="agent-version-6000"></a>Versão do agente 6.0.0.0
As notas de versão a seguir são para a versão 6.0.0.0 do agente de Sincronização de Arquivos do Azure (lançada em 22 de abril de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Suporte à atualização automática do agente
  - Nós ouvimos seus comentários e adicionamos um recurso de atualização automática no agente do Sincronização de Arquivos do Azure Server. Para obter mais informações, consulte [sincronização de arquivos do Azure política de atualização do agente](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Suporte para ACLs de compartilhamento de arquivos do Azure
  - O Sincronização de Arquivos do Azure sempre tem suporte para sincronizar ACLs entre os pontos de extremidade do servidor, mas as ACLs não foram sincronizadas com o ponto final da nuvem (compartilhamento de arquivos do Azure). Esta versão adiciona suporte para a sincronização de ACLs entre servidores e pontos de extremidade de nuvem.
- Fazer upload paralelo e baixar sessões de sincronização para um ponto de extremidade do servidor 
  - Os pontos de extremidade do servidor agora dão suporte ao carregamento e ao download de arquivos ao mesmo tempo. Não está mais aguardando a conclusão de um download para que os arquivos possam ser carregados no compartilhamento de arquivos do Azure. 
- Novos cmdlets de camadas de nuvem para obter o volume e o status de camadas
  - Dois novos cmdlets do PowerShell de servidor local agora podem ser usados para obter informações sobre camadas de nuvem e recuperação de arquivos. Eles disponibilizam informações de log de dois canais de eventos no servidor disponíveis:
    - Get-StorageSyncFileTieringResult listará todos os arquivos e seus caminhos que não estão em camadas e que se relatam sobre o motivo.
    - Get-StorageSyncFileRecallResult relata todos os eventos de recuperação de arquivo. Ele lista todos os arquivos recuperados e seu caminho, bem como êxito ou erro para essa recuperação.
  - Por padrão, ambos os canais de eventos podem armazenar até 1 MB cada – você pode aumentar a quantidade de arquivos relatados aumentando o tamanho do canal de evento.
- Suporte para o modo FIPS
  - O Sincronização de Arquivos do Azure agora dá suporte à habilitação do modo FIPS em servidores que têm o agente de Sincronização de Arquivos do Azure instalado.
    - Antes de habilitar o modo FIPS no seu servidor, instale o agente de Sincronização de Arquivos do Azure e o [módulo PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) no servidor. Se o FIPS já estiver habilitado no servidor, [Baixe manualmente](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download) o [módulo PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) para o servidor.
- Aprimoramentos de confiabilidade diversos para a nuvem e a sincronização em camadas

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implantar a Sincronização de Arquivos do Azure, você precisa avaliar se ela é compatível com seu sistema usando a ferramenta de avaliação da Sincronização de Arquivos do Azure. Essa ferramenta é um cmdlet do Azure PowerShell que verifica se há possíveis problemas com seu sistema de arquivos e conjunto de dados, como caracteres sem suporte ou uma versão de sistema operacional sem suporte. Para obter instruções sobre instalação e uso, consulte a seção [Ferramenta de Avaliação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) no guia de planejamento. 

### <a name="agent-installation-and-server-configuration"></a>Instalação do agente e configuração do servidor
Para saber mais sobre como instalar e configurar o agente de Sincronização de Arquivos do Azure com o Windows Server, confira [Planejamento de uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md) e [Como implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md).

- O pacote de instalação do agente deve ser instalado com permissões de privilégios elevados (admin).
- Não há suporte para o agente na opção de implantação do nano Server.
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
As notas de versão a seguir são para a versão 5.2.0.0 do agente de Sincronização de Arquivos do Azure lançado em 4 de abril de 2019. Essas notas são além das notas de versão listadas para a versão 5.0.2.0.

Lista dos problemas corrigidos nesta versão:  
- Aprimoramentos de confiabilidade para transferência de dados offline e recursos de retomada de transferência de dados
- Aprimoramentos de telemetria de sincronização

## <a name="agent-version-5100"></a>Versão do agente 5.1.0.0
As notas de versão a seguir são para a versão 5.1.0.0 do agente de Sincronização de Arquivos do Azure lançado em 7 de março de 2019. Essas notas são além das notas de versão listadas para a versão 5.0.2.0.

Lista dos problemas corrigidos nesta versão:  
- Os arquivos podem falhar ao serem sincronizados com o erro 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) se a enumeração de alteração estiver falhando no servidor
- Se uma sessão ou arquivo de sincronização receber um erro 0x80072F78 (WININET_E_INVALID_SERVER_RESPONSE), a sincronização agora tentará a operação novamente
- Os arquivos podem falhar ao serem sincronizados com o erro 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- O uso de memória alta pode ocorrer ao rechamar arquivos
- Aprimoramentos de telemetria em camadas de nuvem 

## <a name="agent-version-5020"></a>Versão do agente 5.0.2.0
As notas sobre a versão a seguir são para a versão 5.0.2.0 do agente de Sincronização de Arquivos do Azure (lançada em 12 de fevereiro de 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Problemas corrigidos e aprimoramentos

- Suporte para a nuvem do Azure Governamental
  - Adicionamos suporte à versão prévia para a nuvem do Azure Governamental. Isso exige uma assinatura inclusa na lista de permissão e download do agente especial da Microsoft. Para obter acesso à versão prévia, envie um email diretamente para [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com).
- Suporte para Eliminação de Duplicação de Dados
    - A Eliminação de Duplicação de Dados tem suporte completo com a camada de nuvem habilitada no Windows Server 2016 e no Windows Server 2019. Habilitar a eliminação de duplicação em um volume com camada de nuvem habilitada permite armazenar em cache mais arquivos localmente sem ter que provisionar mais armazenamento.
- Suporte para transferência de dados offline (por exemplo, por meio do Data Box)
    - Migre facilmente grandes quantidades de dados para a Sincronização de Arquivos do Azure do modo que escolher. Você pode escolher Azure Data Box, AzCopy e até mesmo serviços de migração de terceiros. Não é necessário usar grandes quantidades de largura de banda para colocar seus dados no Azure. No caso do Data Box, simplesmente envie-os por ele. Para obter mais informações, confira [Documentos de transferência de dados offline](https://aka.ms/AFS/OfflineDataTransfer).
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

## <a name="agent-version-4300"></a>Versão do agente 4.3.0.0
As notas sobre a versão a seguir são referentes à versão 4.3.0.0 do agente de Sincronização de Arquivos do Azure lançada em 14 de janeiro de 2019. Estas notas complementam as notas sobre a versão listadas para a versão 4.0.1.0.

Lista dos problemas corrigidos nesta versão:  
- Arquivos não são organizadas em camadas depois de atualizar o agente de Sincronização de Arquivos do Azure para a versão 4.x.
- Agora, AfsUpdater.exe tem suporte no Windows Server 2019.
- Diversas melhorias de confiabilidade para sincronização. 

## <a name="agent-version-4200"></a>Versão do agente 4.2.0.0
As notas sobre a versão a seguir são para a versão 4.2.0.0 do agente de Sincronização de Arquivos do Azure lançada em 10 de dezembro de 2018. Estas notas complementam as notas sobre a versão listadas para a versão 4.0.1.0.

Lista dos problemas corrigidos nesta versão:  
- Um erro de parada 0x3B ou erro de parada 0x1E pode ocorrer quando um instantâneo do VSS é criado.  
- Pode ocorrer uma perda de memória quando a camada de nuvem está habilitada  

## <a name="agent-version-4100"></a>Versão do agente 4.1.0.0
As notas sobre a versão a seguir são para a versão 4.1.0.0 do agente de Sincronização de Arquivos do Azure lançada em 4 de dezembro de 2018. Estas notas complementam as notas sobre a versão listadas para a versão 4.0.1.0.

Lista dos problemas corrigidos nesta versão:  
- O servidor pode ficar sem resposta devido a uma perda de memória da camada de nuvem.  
- A instalação do agente falha com o seguinte erro: Erro 1921. Não foi possível parar o serviço de 'agente de sincronização de armazenamento’ (FileSyncSvc).  Verifique se você tem privilégios suficientes para parar serviços do sistema.  
- O serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) pode falhar quando o uso de memória é alto.  
- Diversas melhorias de confiabilidade para camada e sincronização de nuvem.

## <a name="agent-version-4010"></a>Versão de agente 4.0.1.0
As notas sobre a versão a seguir são para a versão 4.0.1.0 do agente de Sincronização de Arquivos do Azure (lançada em 13 de novembro de 2018).

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
- Um erro de parada 0x3B ou erro de parada 0x1E pode ocorrer quando um instantâneo do VSS é criado.

### <a name="interoperability"></a>Interoperabilidade
- Antivírus, backup e outros aplicativos que acessam arquivos em camadas podem causar recalls indesejados quando não respeitam o atributo offline e ignoram a leitura do conteúdo desses arquivos. Para saber mais, consulte [Solução de problemas de Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md).
- As triagens de arquivo do FSRM (Gerenciador de Recursos de Servidor de Arquivos) podem causar falhas de sincronização intermináveis quando os arquivos são bloqueados devido à triagem de arquivo.
- Não há suporte à execução de sysprep em um servidor que possua o agente Sincronização de Arquivos do Azure instalado e isso pode levar a resultados inesperados. O agente de Sincronização de Arquivos do Azure deverá ser instalado após a implantação da imagem do servidor e a conclusão da mini-instalação do sysprep.
- A Eliminação de duplicação de dados e as camadas de nuvem não têm suporte no mesmo volume.

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
- A configuração de política de camada de nuvem baseada em data é usada para especificar arquivos que devem ser armazenados em cache, se acessados em um número de dias especificado. Para saber mais, consulte [Visão geral da camada de nuvem](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering#afs-force-tiering).
- Se um arquivo em camadas é copiado em outro local usando o Robocopy, o arquivo resultante não fica em camadas. O atributo offline pode ser definido porque o Robocopy inclui esse atributo nas operações de cópia incorretamente.
- Ao copiar arquivos usando robocopy, use a opção /MIR para preservar os carimbos de data/hora dos arquivos. Isso garantirá que os arquivos mais antigos sejam colocados em camadas antes dos arquivos acessados recentemente.
- Ao exibir as propriedades de arquivo de um cliente SMB, o atributo offline poderá parecer estar definido incorretamente devido ao cache SMB de metadados do arquivo.
