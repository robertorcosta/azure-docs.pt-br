---
title: Migrar dados para o Azure File Sync com a Caixa de Dados Do Azure
description: Migre os dados em massa de uma forma compatível com o Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d0331419de89775062f1309c5d854cd7325c68e4
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656761"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Migrar dados em massa para Sincronização de Arquivos do Azure com o Azure Data Box
Você pode migrar dados em massa para o Azure File Sync de duas maneiras:

* **Faça upload de seus arquivos usando o Azure File Sync.** Este é o método mais simples. Mova seus arquivos localmente para o Windows Server 2012 R2 ou posterior, e instale o agente Azure File Sync. Depois de configurar a sincronização, seus arquivos serão carregados do servidor. (Nossos clientes atualmente experimentam uma velocidade média de upload de 1 TiB a cada dois dias.) Para garantir que seu servidor não use muito da largura de banda para o seu data center, você pode querer configurar um [cronograma de estrangulamento da largura de banda](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Transfira seus arquivos off-line.** Se você não tiver largura de banda suficiente, você pode não ser capaz de carregar arquivos para o Azure em um período razoável de tempo. O desafio é a sincronização inicial de todo o conjunto de arquivos. Para superar esse desafio, use ferramentas de migração em massa offline, como a [família Azure Data Box](https://azure.microsoft.com/services/storage/databox). 

Este artigo explica como migrar arquivos offline de uma maneira compatível com o Azure File Sync. Siga estas instruções para evitar conflitos de arquivos e para preservar suas listas de controle de acesso de arquivos e pastas (ACLs) e carimbos de tempo após ativar a sincronização.

## <a name="migration-tools"></a>Ferramentas de migração
O processo que descrevemos neste artigo funciona não apenas para data box, mas também para outras ferramentas de migração offline. Também funciona para ferramentas como AzCopy, Robocopy ou ferramentas e serviços parceiros que funcionam diretamente pela internet. No entanto, para superar o desafio inicial de upload, siga os passos deste artigo para usar essas ferramentas de uma maneira compatível com o Azure File Sync.

Em alguns casos, você precisa passar de um Servidor Windows para outro Servidor Windows antes de adotar o Azure File Sync. [O SMS (Storage Migration Service, serviço de migração](https://aka.ms/storagemigrationservice) de armazenamento) pode ajudar com isso. Se você precisa migrar para uma versão do Sistema Operacional do Servidor que é suportada pelo Azure File Sync (Windows Server 2012R2 ou superior) ou simplesmente precisa migrar porque está comprando um novo sistema para o Azure File Sync, o SMS tem inúmeros recursos e vantagens que ajudarão a fazer sua migração sem problemas.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Benefícios de usar uma ferramenta para transferir dados offline
Aqui estão os principais benefícios de usar uma ferramenta de transferência como data box para migração offline:

- Você não tem que carregar todos os seus arquivos pela rede. Para grandes espaços de nome, esta ferramenta poderia economizar largura de banda de rede significativa e tempo.
- Quando você usa o Azure File Sync, não importa qual ferramenta de transferência você usa (Data Box, azure Import/Export service, e assim por diante), seu servidor vivo carrega apenas os arquivos que mudam depois que você move os dados para o Azure.
- O Azure File Sync sincroniza as ACLs de arquivo e pasta, mesmo que a ferramenta de migração em massa offline não transporte ACLs.
- Data Box e Azure File Sync não requerem tempo de inatividade. Quando você usa a Data Box para transferir dados para o Azure, você usa a largura de banda da rede de forma eficiente e preserva a fidelidade do arquivo. Você também mantém seu namespace atualizado, carregando apenas os arquivos que mudam depois de mover os dados para o Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Pré-requisitos para transferência de dados offline
Você não deve habilitar a sincronização no servidor que está migrando antes de concluir sua transferência de dados off-line. Outras coisas a considerar antes de começar são as seguintes:

- Se você planeja usar a Data Box para sua migração em massa: Revise os [pré-requisitos de implantação para Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planeje sua topologia final do Azure File Sync: [Planeje uma implantação do Azure File Sync](storage-sync-files-planning.md)
- Escolha a conta de armazenamento do Azure que conterá os compartilhamentos de arquivos com os quais você deseja sincronizar. Assegure-se de que sua migração em massa ocorra nos compartilhamentos de preparo temporários da mesma Conta de Armazenamento. A migração em massa só poderá ser ativada utilizando um compartilhamento final e um compartilhamento de preparo que residem na mesma conta de armazenamento.
- Uma migração em massa só pode ser utilizada quando você cria uma nova relação de sincronização com um local do servidor. Você não pode habilitar uma migração em massa com uma relação de sincronização existente.


## <a name="process-for-offline-data-transfer"></a>Processo para transferência de dados offline
Veja como configurar o Azure File Sync de uma maneira compatível com ferramentas de migração em massa, como o Azure Data Box:

![Diagrama mostrando como configurar o Azure File Sync](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Etapa | Detalhes |
|---|---------------------------------------------------------------------------------------|
| ![Etapa 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Solicite o Data Box](../../databox/data-box-deploy-ordered.md). A família Data Box oferece [vários produtos](https://azure.microsoft.com/services/storage/databox/data) para atender às suas necessidades. Ao receber sua Data Box, siga sua [documentação para copiar seus dados](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) para este caminho UNC na Caixa de Dados: * \\<DeviceIPAddres\>\<StorageAccountName_AzFile\>\<ShareName\>*. Aqui, *ShareName* é o nome da parte de encenação. Envie o Data Box para o Azure. |
| ![Etapa 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Aguarde até que seus arquivos apareçam nos compartilhamentos de arquivos do Azure que você escolheu como ações temporárias de encenação. *Não habilite a sincronização com essas ações.* |
| ![Etapa 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | <ul><li>Crie um novo compartilhamento vazio para cada compartilhamento de arquivo que a Data Box criou para você. Essa nova ação deve estar na mesma conta de armazenamento que o compartilhamento data box. [Como criar um novo compartilhamento de arquivos do Azure](storage-how-to-create-file-share.md).</li><li>[Crie um grupo de sincronização](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) em um serviço de sincronização de armazenamento. Refira a parte vazia como um ponto final de nuvem. Repita essa etapa para cada compartilhamento de arquivos do Data Box. [Configure o Azure File Sync](storage-sync-files-deployment-guide.md).</li></ul> |
| ![Etapa 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Adicione seu diretório do servidor ao vivo como um ponto de extremidade do servidor](storage-sync-files-deployment-guide.md#create-a-server-endpoint). No processo, especifique que você moveu os arquivos para o Azure e faça referência às partes de encenação. Você pode ativar ou desativar o hierárquico da nuvem conforme necessário. Ao criar um ponto final do servidor em seu servidor ao vivo, consulte o compartilhamento de encenação. Na **lâmina de ponto final adicionar servidor,** em **Transferência de dados offline,** selecione **Ativado**e selecione o compartilhamento de staging que deve estar na mesma conta de armazenamento que o ponto final da nuvem. Aqui, a lista de ações disponíveis é filtrada por conta de armazenamento e ações que ainda não estão sincronizadas. A captura de tela a seguir desta tabela mostra como referenciar o compartilhamento do DataBox durante a criação do ponto final do servidor no portal Azure. |
| ![Etapa 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | Depois de adicionar o ponto final do servidor na etapa anterior, os dados começam a fluir automaticamente da fonte certa. A [seção Sincronizar a divisão](#syncing-the-share) explica quando os dados fluem do compartilhamento databox ou do Windows Server |
| |

![Captura de tela da interface de usuário do portal Azure, mostrando como ativar a transferência de dados offline ao criar um novo ponto final do servidor](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Sincronização do compartilhamento
Depois de criar o ponto final do servidor, a sincronização será iniciada. O processo de sincronização determina se cada arquivo no servidor também existe no compartilhamento de estadiamento onde a Data Box depositou os arquivos. Se o arquivo existir lá, o processo de sincronização copia o arquivo do compartilhamento de encenação em vez de carregá-lo do servidor. Se o arquivo não existir no compartilhamento de encenação ou se uma versão mais recente estiver disponível no servidor local, o processo de sincronização faz upload do arquivo do servidor local.

Ao sincronizar o compartilhamento, a sincronização irá mesclar quaisquer atributos de arquivo ausentes, permissões ou carimbos de tempo das variantes de arquivo no servidor local, combinando-os com seus contrapartes de arquivo do compartilhamento DataBox. Isso garante que cada arquivo e pasta chegue com toda a fidelidade possível de arquivos no compartilhamento de arquivos do Azure.

> [!IMPORTANT]
> Você pode habilitar o modo de migração em massa apenas enquanto estiver criando um ponto final do servidor. Depois de estabelecer um ponto final do servidor, você não pode integrar dados migrados em massa de um servidor já sincronizado no namespace.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACLs e carimbos de data/hora em arquivos e pastas
O Azure File Sync garante que as ACLs de arquivo e pasta sejam sincronizadas a partir do servidor vivo, mesmo que a ferramenta de migração em massa que você usou não tenha inicialmente transportado ACLs. Por causa disso, o compartilhamento de encenação não precisa conter Quaisquer ACLs para arquivos e pastas. Quando você habilita o recurso de migração de dados offline à medida que cria um novo ponto final do servidor, todas as ACLs de arquivo são sincronizadas no servidor. Os carimbos de tempo recém-criados e modificados também são sincronizados.

## <a name="shape-of-the-namespace"></a>Forma do namespace
Quando você habilita a sincronização, o conteúdo do servidor determina a forma do namespace. Se os arquivos forem excluídos do servidor local após o snapshot e o término da migração da Data Box, esses arquivos não se moverão para o espaço de nome ao vivo e sincronizando. Eles ficam na parte da encenação, mas não são copiados. Isso é necessário porque a sincronização mantém o namespace de acordo com o servidor ao vivo. O instantâneo data *box* é apenas um campo de preparação para uma cópia de arquivo eficiente. Não é a autoridade para a forma do espaço de nomes ao vivo.

## <a name="cleaning-up-after-bulk-migration"></a>Limpeza após migração em massa 
À medida que o servidor completa sua sincronização inicial do namespace, os arquivos migrados em massa da Data Box usam o compartilhamento de arquivos de staging. Na lâmina **Propriedades de ponto final** do servidor no portal Azure, na seção Transferência de Dados **Offline,** o status muda de **Em andamento** para **concluído**. 

![Captura de tela da lâmina Server Endpoint Properties, onde os controles de status e desativação para transferência de dados off-line estão localizados](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Agora você pode limpar a parte de encenação para economizar custos:

1. Na lâmina Propriedades de ponto final do **servidor,** quando o status estiver **concluído,** **selecione Desativar transferência de dados off-line**.
2. Considere excluir a parte de encenação para economizar custos. A parte de encenação provavelmente não contém ACLs de arquivo e pasta, por isso é improvável que seja útil. Para fins pontuais de backup, crie um instantâneo real [do compartilhamento de arquivos Azure sincronizado](storage-snapshots-files.md). Você pode [configurar o Azure Backup para tirar instantâneos]( ../../backup/backup-afs.md) em um cronograma.

Desative o modo de transferência de dados offline somente quando o estado estiver **concluído** ou quando você quiser cancelar por causa de uma configuração errada. Se você desativar o modo durante uma implantação, os arquivos começarão a ser carregados do servidor mesmo que seu compartilhamento de encenação ainda esteja disponível.

> [!IMPORTANT]
> Depois de desativar o modo de transferência de dados offline, você não poderá habilitá-lo novamente, mesmo que o compartilhamento de staging da migração em massa ainda esteja disponível.

## <a name="next-steps"></a>Próximas etapas
- [Planejar uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
