---
title: Migrar para compartilhamentos de Arquivos do Azure
description: Saiba mais sobre migrações para compartilhamentos de arquivos do Azure e encontre seu guia de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d6141d48d67dd44c348961c6e09acf4e2531a61e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685981"
---
# <a name="migrate-to-azure-file-shares"></a>Migrar para compartilhamentos de Arquivos do Azure

Este artigo abrange os aspectos básicos de uma migração para compartilhamentos de arquivos Do Zure.

Este artigo contém noções básicas de migração e uma tabela de guias de migração. Esses guias ajudam você a mover seus arquivos para compartilhamentos de arquivos do Azure. Os guias são organizados com base em onde seus dados estão e em que modelo de implantação (somente em nuvem ou híbrido) você está se movendo.

## <a name="migration-basics"></a>Noções básicas de migração

O Azure tem vários tipos disponíveis de armazenamento em nuvem. Um aspecto fundamental das migrações de arquivos para o Azure é determinar qual opção de armazenamento do Azure é certa para seus dados.

[Os compartilhamentos de arquivos Azure](storage-files-introduction.md) são adequados para dados de arquivos de uso geral. Esses dados incluem qualquer coisa para a sua utilização de smb ou NFS no local. Com [o Azure File Sync,](storage-sync-files-planning.md)você pode armazenar o conteúdo de vários compartilhamentos de arquivos do Azure em servidores que executam o Windows Server no local.

Para um aplicativo que atualmente é executado em um servidor local, armazenar arquivos em um compartilhamento de arquivos do Azure pode ser uma boa escolha. Você pode mover o aplicativo para o Azure e usar os compartilhamentos de arquivos do Azure como armazenamento compartilhado. Você também pode considerar [discos Azure](../../virtual-machines/windows/managed-disks-overview.md) para este cenário.

Alguns aplicativos em nuvem não dependem de SMB ou de acesso a dados locais de máquina ou acesso compartilhado. Para esses aplicativos, o armazenamento de objetos como [blobs Do Azure](../blobs/storage-blobs-overview.md) é muitas vezes a melhor escolha.

A chave em qualquer migração é capturar toda a fidelidade de arquivo aplicável ao mover seus arquivos do local de armazenamento atual para o Azure. Quanta fidelidade a opção de armazenamento do Azure suporta e o quanto seu cenário requer também ajuda você a escolher o armazenamento azure certo. Os dados de arquivo de uso geral dependem tradicionalmente de metadados de arquivos. Os dados do aplicativo podem não.

Aqui estão os dois componentes básicos de um arquivo:

- **Fluxo de dados**: O fluxo de dados de um arquivo armazena o conteúdo do arquivo.
- **Metadados de arquivo**: Os metadados do arquivo têm esses subcomponentes:
   * Atributos de arquivo como somente leitura
   * Permissões de arquivo, que podem ser referidas como *permissões NTFS* ou *ACLs de arquivo e pasta*
   * Carimbos de tempo, mais notavelmente a criação e os carimbos de tempo última sumidantes
   * Um fluxo de dados alternativo, que é um espaço para armazenar quantidades maiores de propriedades fora do padrão

A fidelidade de arquivos em uma migração pode ser definida como a capacidade de:

- Armazene todas as informações de arquivo aplicáveis na fonte.
- Transferir arquivos com a ferramenta de migração.
- Armazene arquivos no armazenamento alvo da migração.

Para garantir que sua migração prossiga sem problemas, identifique [a melhor ferramenta de cópia para suas necessidades](#migration-toolbox) e corresponda a um alvo de armazenamento à sua fonte.

Levando em conta as informações anteriores, você pode ver que o armazenamento de destino para arquivos de uso geral no Azure é [compartilhamento de arquivos Azure](storage-files-introduction.md).

Ao contrário do armazenamento de objetos em blobs do Azure, um compartilhamento de arquivos do Azure pode armazenar metadados de arquivos nativamente. Os compartilhamentos de arquivos do Azure também preservam a hierarquia de arquivos e pastas, atributos e permissões. As permissões NTFS podem ser armazenadas em arquivos e pastas porque estão no local.

Um usuário do Active Directory, que é seu controlador de domínio local, pode acessar nativamente um compartilhamento de arquivos Do Zure. Assim como um usuário do Azure Active Directory Domain Services (Azure AD DS). Cada um usa sua identidade atual para obter acesso com base em permissões de compartilhamento e em ACLs de arquivo e pasta. Esse comportamento é semelhante a um usuário que se conecta a um compartilhamento de arquivos no local.

O fluxo de dados alternativo é o aspecto principal da fidelidade de arquivos que atualmente não pode ser armazenado em um arquivo em um compartilhamento de arquivos Do Zure. É preservado no local quando o Azure File Sync é usado.

Saiba mais sobre [a autenticação azure AD](storage-files-identity-auth-active-directory-enable.md) e [autenticação Azure AD DS](storage-files-identity-auth-active-directory-domain-service-enable.md) para compartilhamentos de arquivos Azure.

## <a name="migration-guides"></a>Guias de migração

A tabela a seguir lista guias de migração detalhados.

Como usar a tabela:

1. Localize a linha para o sistema de origem em que seus arquivos estão armazenados no momento.

1. Escolha um desses alvos:

   - Uma implantação híbrida usando o Azure File Sync para armazenar o conteúdo dos compartilhamentos de arquivos do Azure no local
   - Ações de arquivos do Azure na nuvem

   Selecione a coluna de destino que corresponde à sua escolha.

1. Dentro da intersecção entre fonte e destino, uma célula de tabela lista cenários de migração disponíveis. Selecione um para vincular diretamente ao guia de migração detalhado.

Um cenário sem link ainda não tem um guia de migração publicado. Verifique esta tabela ocasionalmente para obter atualizações. Novos guias serão publicados quando estiverem disponíveis.

| Fonte | Destino: </br>Implantação híbrida | Destino: </br>Implantação somente em nuvem |
|:---|:--|:--|
| | Combinação de ferramentas:| Combinação de ferramentas: |
| Windows Server 2012 R2 e posterior | <ul><li>[Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)</li><li>[Sincronização de arquivos azure e caixa de dados do Azure](storage-sync-offline-data-transfer.md)</li><li>Serviço de migração de sincronização e armazenamento de arquivos do Azure</li></ul> | <ul><li>Sincronização de Arquivos do Azure</li><li>Sincronização de arquivos do Azure e caixa de dados</li><li>Serviço de migração de sincronização e armazenamento de arquivos do Azure</li><li>Robocopy</li></ul> |
| Windows Server 2012 e anterior | <ul><li>Sincronização de arquivos do Azure e caixa de dados</li><li>Serviço de migração de sincronização e armazenamento de arquivos do Azure</li></ul> | <ul><li>Serviço de migração de sincronização e armazenamento de arquivos do Azure</li><li>Robocopy</li></ul> |
| NAS (Network-attached storage, armazenamento conectado à rede) | <ul><li>[Sincronização de arquivos Azure e RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Linux ou Samba | <ul><li>[Sincronização de arquivos Azure e RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Microsoft Azure StorSimple Cloud Appliance 8100 ou StorSimple Cloud Appliance 8600 | <ul><li>[Sincronização de arquivos Azure e StorSimple Cloud Appliance 8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple Cloud Appliance 1200 | <ul><li>[Sincronização de Arquivos do Azure](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Caixa de ferramentas de migração

### <a name="file-copy-tools"></a>Ferramentas de cópia de arquivos

Existem várias ferramentas de cópia de arquivos disponíveis da Microsoft e outras. Para selecionar a ferramenta certa para o seu cenário de migração, você deve considerar essas perguntas fundamentais:

* A ferramenta suporta a origem e os locais de destino da sua cópia de arquivo?

* A ferramenta suporta seu caminho de rede ou protocolos disponíveis (como REST, SMB ou NFS) entre os locais de armazenamento de origem e de destino?

* A ferramenta preserva a fidelidade de arquivos necessária suportada por sua fonte e locais de destino?

    Em alguns casos, seu armazenamento de destino não suporta a mesma fidelidade que sua fonte. Se o armazenamento de destino for suficiente para suas necessidades, a ferramenta deve corresponder apenas aos recursos de fidelidade de arquivo do destino.

* A ferramenta possui recursos que permitem que ele se encaixe na sua estratégia de migração?

    Por exemplo, considere se a ferramenta permite minimizar seu tempo de inatividade.
    
    Quando uma ferramenta suporta uma opção para espelhar uma fonte para um alvo, muitas vezes você pode executá-la várias vezes na mesma fonte e no destino enquanto a fonte permanece acessível.

    A primeira vez que você executa a ferramenta, ela copia a maior parte dos dados. Esta corrida inicial pode durar um tempo. Muitas vezes dura mais do que você deseja para tirar a fonte de dados offline para seus processos de negócios.

    Ao espelhar uma fonte para um alvo (como com **a robocopia/MIR),** você pode executar a ferramenta novamente na mesma fonte e alvo. A corrida é muito mais rápida porque precisa transportar apenas mudanças de origem que ocorrem após a execução anterior. Reexecutar uma ferramenta de cópia desta forma pode reduzir significativamente o tempo de inatividade.

A tabela a seguir classifica as ferramentas da Microsoft e sua adequação atual para compartilhamentos de arquivos Do Zure:

| Recomendadas | Ferramenta | Suporte para compartilhamentos de arquivos Azure | Preservação da fidelidade de arquivos |
| :-: | :-- | :---- | :---- |
|![Sim, recomendado](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy |  Com suporte. As partes de arquivos do Azure podem ser montadas como unidades de rede. | Fidelidade total.* |
|![Sim, recomendado](media/storage-files-migration-overview/circle-green-checkmark.png)| Sincronização de Arquivos do Azure | Nativamente integrado aos compartilhamentos de arquivos do Azure. | Fidelidade total.* |
|![Sim, recomendado](media/storage-files-migration-overview/circle-green-checkmark.png)| Serviço de Migração de Armazenamento | Indiretamente apoiado. Os compartilhamentos de arquivos do Azure podem ser montados como unidades de rede em servidores-alvo de SMS. | Fidelidade total.* |
|![Não totalmente recomendado](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Data Box |  Com suporte. | Não copia metadados. [A caixa de dados pode ser usada com o Azure File Sync](storage-sync-offline-data-transfer.md). |
|![Não recomendável](media/storage-files-migration-overview/circle-red-x.png)| AzCopy |  Com suporte. | Não copia metadados. |
|![Não recomendável](media/storage-files-migration-overview/circle-red-x.png)| Gerenciador de Armazenamento do Azure |  Com suporte. | Não copia metadados. |
|![Não recomendável](media/storage-files-migration-overview/circle-red-x.png)| Fábrica de dados do Azure |  Com suporte. | Não copia metadados. |
|||||

*\*Fidelidade total: atende ou excede os recursos de compartilhamento de arquivos do Azure.*

### <a name="migration-helper-tools"></a>Ferramentas de ajuda à migração

Esta seção descreve ferramentas que ajudam você a planejar e executar migrações.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy da Microsoft Corporation

RoboCopy é uma das ferramentas mais aplicáveis às migrações de arquivos. Ele vem como parte do Windows. A [documentação principal do RoboCopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) é um recurso útil para muitas opções desta ferramenta.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize da JAM Software GmbH

O Azure File Sync é dimensionado principalmente com o número de itens (arquivos e pastas) e não com o valor total de armazenamento. A ferramenta TreeSize permite determinar o número de itens nos volumes do Servidor Windows.

Você pode usar a ferramenta para criar uma perspectiva antes de uma [implantação do Azure File Sync](storage-sync-files-deployment-guide.md). Você também pode usá-lo quando a hierarquiagem da nuvem estiver engajada após a implantação. Nesse cenário, você vê o número de itens e quais diretórios usam mais o cache do servidor.

A versão testada da ferramenta é a versão 4.4.1. É compatível com arquivos em camadas de nuvem. A ferramenta não causará o recall de arquivos hierárquicos durante seu funcionamento normal.

## <a name="next-steps"></a>Próximas etapas

1. Crie um plano para o qual a implantação de compartilhamentos de arquivos Do Zure (somente na nuvem ou híbrido) deseja.
1. Revise a lista de guias de migração disponíveis para encontrar o guia detalhado que corresponde à sua origem e implantação de compartilhamentos de arquivos Do Zure.

Aqui estão mais informações sobre as tecnologias de Arquivos Azure mencionadas neste artigo:

* [Visão geral do compartilhamento de arquivos do Azure](storage-files-introduction.md)
* [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
* [Sincronização de arquivos Azure: hierarquiagem na nuvem](storage-sync-cloud-tiering.md)
