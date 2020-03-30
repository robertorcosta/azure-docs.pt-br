---
title: Migrar para compartilhamentos de arquivos do Azure
description: Saiba mais sobre migrações para compartilhamentos de arquivos do Azure e encontre seu guia de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 903ce52120fce7c23c6a3754498b81fc6fc2430f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247309"
---
# <a name="migrate-to-azure-file-shares"></a>Migrar para compartilhamentos de arquivos do Azure

Este artigo abrange os aspectos básicos de uma migração para compartilhamentos de arquivos Do Zure.

Ao lado dos fundamentos de migração, este artigo contém uma lista de guias de migração existentes e individualizados. Esses guias de migração ajudam você a mover seus arquivos para compartilhamentos de arquivos do Azure e são organizados por onde seus dados residem hoje e para qual modelo de implantação (somente na nuvem ou híbrido) você planeja se mudar.

## <a name="migration-basics"></a>Noções básicas de migração

Existem vários tipos diferentes de armazenamento em nuvem disponíveis no Azure. Um aspecto fundamental de uma migração de arquivos para o Azure é determinar qual opção de armazenamento do Azure é certa para seus dados.

Os compartilhamentos de arquivos do Azure são ótimos para dados de arquivos de propósito geral. Realmente qualquer coisa que você use um smb ou nfs no local para. Com [o Azure File Sync,](storage-sync-files-planning.md)você pode, opcionalmente, armazenar o conteúdo de vários compartilhamentos de arquivos do Azure em vários servidores windows no local.

Se você tiver um aplicativo atualmente em execução em um servidor local, então armazenar arquivos em compartilhamentos de arquivos do Azure pode ser o certo para você, dependendo do aplicativo. Você pode levantar o aplicativo para executar no Azure e usar os compartilhamentos de arquivos do Azure como armazenamento compartilhado. Você também pode considerar [discos Azure](../../virtual-machines/windows/managed-disks-overview.md) para este cenário. Para aplicativos nascidos na nuvem que não dependem do SMB ou do acesso local à máquina aos seus dados ou acesso compartilhado, o armazenamento de [objetos, como blobs do Azure,](../blobs/storage-blobs-overview.md)é muitas vezes a melhor escolha.

A chave em qualquer migração é capturar toda a fidelidade de arquivo aplicável ao migrar seus arquivos do local de armazenamento atual para o Azure. Uma ajuda na escolha do armazenamento azure certo também é o aspecto de quanta fidelidade suportada pela opção de armazenamento Azure e é exigida pelo seu cenário. Os dados de arquivo de uso geral tradicionalmente dependem de metadados de arquivos. Os dados do aplicativo podem não. Existem dois componentes básicos para um arquivo:

- **Fluxo de dados**: O fluxo de dados de um arquivo armazena o conteúdo do arquivo.
- **Metadados de arquivo**: Os meta dados do arquivo têm vários subcomponentes:
   * Atributos do arquivo: somente leitura, por exemplo.
   * Permissões de arquivo: Referidas como *permissões NTFS* ou *ACLs de arquivo e pasta*.
   * Carimbos de tempo: Mais notavelmente os carimbos de tempo de *criação* e *última modificação.*
   * Fluxo de dados alternativo: um espaço para armazenar quantidades maiores de propriedades não padronizadas.

A fidelidade de arquivos, em uma migração, pode, portanto, ser definida como a capacidade de armazenar todas as informações de arquivo aplicáveis na fonte, a capacidade de transferi-las com a ferramenta de migração e a capacidade de armazená-las no armazenamento alvo da migração.

Para garantir que sua migração prossiga da forma mais suave possível, identifique [a melhor ferramenta de cópia para suas necessidades](#migration-toolbox) e corresponda a um alvo de armazenamento à sua fonte.

Levando em conta as informações anteriores, fica claro qual é o armazenamento de destino para arquivos de uso geral no Azure: [compartilhamentos de arquivos Azure](storage-files-introduction.md). Em comparação com o armazenamento de objetos em blobs do Azure, os metadados de arquivos podem ser armazenados nativamente em arquivos em um compartilhamento de arquivos Do Zure.

Os compartilhamentos de arquivos do Azure também preservam a hierarquia de arquivos e pastas. Adicionalmente:
* As permissões NTFS podem ser armazenadas em arquivos e pastas à medida que estiverem no local.
* Os usuários de AD (ou usuários do Azure AD DS) podem acessar nativamente um compartilhamento de arquivos Do Zure. 
    Eles usam sua identidade atual e obtêm acesso com base em permissões de compartilhamento, bem como ACLs de arquivos e pastas. Um comportamento não muito diferente quando os usuários se conectam a um compartilhamento de arquivos no local.
*  O fluxo de dados alternativo é o aspecto principal da fidelidade de arquivos que atualmente não pode ser armazenado em um arquivo em um compartilhamento de arquivos Do Zure.
   Ele é preservado no local quando o Azure File Sync está envolvido.

* [Saiba mais sobre autenticação de Anúncios para compartilhamentos de arquivos Do Zure](storage-files-identity-auth-active-directory-enable.md)
* [Saiba mais sobre a autenticação do Azure Active Directory Domain Services (AAD DS) para compartilhamentos de arquivos Do Zure](storage-files-identity-auth-active-directory-domain-service-enable.md)

## <a name="migration-guides"></a>Guias de migração

A tabela a seguir lista guias de migração detalhados.

Navegue por:
1. Localize a linha para o sistema de origem em que seus arquivos estão armazenados no momento.
2. Decida se você tem como alvo uma implantação híbrida onde você usa o Azure File Sync para armazenar o conteúdo de um ou mais compartilhamentos de arquivos do Azure no local ou se você gosta de usar compartilhamentos de arquivos do Azure diretamente na nuvem. Selecione a coluna de destino que reflete sua decisão.
3. Dentro da intersecção entre fonte e destino, uma célula de tabela lista cenários de migração disponíveis. Selecione um deles para vincular diretamente ao guia de migração detalhado.

Um cenário sem link ainda não tem um guia de migração publicado. Verifique esta tabela ocasionalmente para obter atualizações. Novos guias serão publicados quando disponíveis.

| Fonte | Destino: </br>Implantação híbrida | Destino: </br>Implantação somente em nuvem |
|:---|:--|:--|
| | Combinação de ferramentas:| Combinação de ferramentas: |
| Windows Server 2012 R2 e mais recente | <ul><li>[Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)</li><li>[Sincronização de arquivos Azure + DataBox](storage-sync-offline-data-transfer.md)</li><li>Serviço de migração de armazenamento + sincronização de arquivos do Azure</li></ul> | <ul><li>Sincronização de Arquivos do Azure</li><li>Sincronização de arquivos Azure + DataBox</li><li>Serviço de migração de armazenamento + sincronização de arquivos do Azure</li><li>Robocopy</li></ul> |
| Windows Server 2012 e mais antigo | <ul><li>Sincronização de arquivos Azure + DataBox</li><li>Serviço de migração de armazenamento + sincronização de arquivos do Azure</li></ul> | <ul><li>Serviço de migração de armazenamento + sincronização de arquivos do Azure</li><li>Robocopy</li></ul> |
| Nas (Network Attached Storage, armazenamento conectado à rede) | <ul><li>[Sincronização de arquivos Azure + RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| Linux / Samba | <ul><li>[RoboCopy + Sincronização de Arquivos Azure](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| StorSimple 8100 / 8600 | <ul><li>[Azure File Sync + 8020 Virtual Appliance](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple 1200 | <ul><li>[Sincronização de Arquivos do Azure](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Caixa de ferramentas de migração

### <a name="file-copy-tools"></a>Ferramentas de cópia de arquivos

Existem várias ferramentas de cópia de arquivos da Microsoft e não-Microsoft disponíveis. Para selecionar a ferramenta de cópia de arquivos certa para o seu cenário de migração, você deve considerar três questões fundamentais:

* A ferramenta de cópia suporta a origem e o local de destino de uma determinada cópia do arquivo? 
    * Ele suporta seu caminho de rede e/ou protocolos disponíveis (por exemplo, REST/SMB/NFS) para e a partir dos locais de armazenamento de origem e destino?
* A ferramenta de cópia preserva a fidelidade de arquivo necessária suportada pelo local de origem/destino? Em alguns casos, seu armazenamento de destino não suporta a mesma fidelidade que sua fonte. Você já tomou a decisão de que o armazenamento de destino é suficiente para suas necessidades, portanto, a ferramenta de cópia só precisa corresponder aos recursos de fidelidade de arquivos de alvos.
* A ferramenta de cópia tem recursos que a tornam adequada à minha estratégia de migração? 
    * Por exemplo, considere se ele tem opções que permitem minimizar seu tempo de inatividade. Uma boa pergunta é: posso executar essa cópia várias vezes no mesmo local, por usuários com acesso ativo? Se assim for, você pode reduzir significativamente a quantidade de tempo de inatividade. Compare isso com uma situação em que você só pode iniciar a cópia quando a fonte parar de mudar, para garantir uma cópia completa.

A tabela a seguir classifica as ferramentas da Microsoft e sua adequação atual para compartilhamentos de arquivos Do Zure:

| Recomendadas | Ferramenta | Suporta compartilhamentos de arquivos Azure | Preserva a fidelidade de arquivos |
| :-: | :-- | :---- | :---- |
|![Sim, recomendado.](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy |  Com suporte. As partes de arquivos do Azure podem ser montadas como unidades de rede. | Fidelidade total* |
|![Sim, recomendado.](media/storage-files-migration-overview/circle-green-checkmark.png)| Sincronização de Arquivos do Azure | Nativamente integrado aos compartilhamentos de arquivos do Azure. | Fidelidade total* |
|![Sim, recomendado.](media/storage-files-migration-overview/circle-green-checkmark.png)| Serviço de Migração de Armazenamento (SMS) | Indiretamente apoiado. Os compartilhamentos de arquivos do Azure podem ser montados como unidades de rede em um servidor de destino sMS. | Fidelidade total* |
|![Não totalmente recomendado.](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Caixa de dados do Azure |  Com suporte. | Não copia metadados. [Pode ser usado em combinação com o Azure File Sync](storage-sync-offline-data-transfer.md). |
|![Não recomendável.](media/storage-files-migration-overview/circle-red-x.png)| AzCopy |  Com suporte. | Não copia metadados. |
|![Não recomendável.](media/storage-files-migration-overview/circle-red-x.png)| Gerenciador de Armazenamento do Azure |  Com suporte. | Não copia metadados. |
|![Não recomendável.](media/storage-files-migration-overview/circle-red-x.png)| Fábrica de dados do Azure |  Com suporte. | Não copia metadados. |
|||||

*\*Fidelidade total: atende ou excede os recursos de compartilhamento de arquivos do Azure.*

### <a name="migration-helper-tools"></a>Ferramentas de ajuda à migração

Esta seção lista ferramentas que ajudam a planejar e executar migrações.

* **RoboCopy, da Microsoft Corporation**

    Uma das ferramentas de cópia mais aplicáveis para migrações de arquivos, vem como parte do Microsoft Windows. Devido às muitas opções nesta ferramenta, a documentação principal do [RoboCopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) é uma fonte útil.

* **TreeSize, da JAM Software GmbH**

    O Azure File Sync é dimensionado principalmente com o número de itens (arquivos e pastas) e menos ainda com o valor total do TiB. A ferramenta pode ser usada para determinar o número de arquivos e pastas nos volumes do Windows Server. Além disso, ele pode ser usado para criar uma perspectiva antes de uma [implantação do Azure File Sync](storage-sync-files-deployment-guide.md) - mas também depois, quando o hierarquismo na nuvem está ativado e você gosta de ver não apenas o número de itens, mas também em quais diretórios o cache do servidor é mais usado.
    Esta ferramenta (versão testada 4.4.1) é compatível com arquivos hierárquicos em nuvem. Não causará o recall de arquivos hierárquicos durante sua operação normal.


## <a name="next-steps"></a>Próximas etapas

1. Crie um plano para o qual a implantação de compartilhamentos de arquivos Azure (somente na nuvem ou híbrido) que você se esforça.
2. Revise a lista de guias de migração disponíveis para encontrar o guia detalhado que corresponde à sua origem e implantação de compartilhamentos de arquivos Do Zure.

Há mais informações disponíveis sobre as tecnologias do Azure Files mencionadas neste artigo:

* [Visão geral do compartilhamento de arquivos do Azure](storage-files-introduction.md)
* [Planejamento para uma implantação do Azure File Sync](storage-sync-files-planning.md)
* [Sincronização de arquivos Azure: hierarquiagem na nuvem](storage-sync-cloud-tiering.md)
