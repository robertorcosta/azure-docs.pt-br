---
title: Orientação de recuperação de desastres para Avere vFXT para Azure
description: Como proteger dados no Avere vFXT para o Azure contra exclusões acidentais ou paralisações
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 28278f76497d6e9d0fee221bb4ef32fe6d369db0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966662"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Orientação de recuperação de desastres para Avere vFXT para Azure

Este artigo descreve estratégias para proteger seu Avere vFXT para o fluxo de trabalho do Azure e dá orientações para o backup de dados para que você possa se recuperar de acidentes ou paralisações.

Avere vFXT for Azure armazena temporariamente dados em seu cache. Os dados são armazenados a longo prazo em sistemas de armazenamento back-end - sistemas de hardware no local, contêineres de armazenamento Azure Blob ou ambos.

Para se proteger contra paralisações e possíveis perdas de dados, considere essas quatro áreas:

* Proteção contra tempo de inatividade se um sistema Avere vFXT for Azure ficar indisponível
* Proteção de dados no cache de cluster
* Proteção de dados no armazenamento de hardware NAS back-end
* Proteção de dados no armazenamento em nuvem Azure Blob back-end

Cada cliente Avere vFXT para cliente Azure deve criar seu próprio plano abrangente de recuperação de desastres que inclua planos para esses itens. Você também pode construir resiliência em aplicativos que você usa com o cluster vFXT. Leia os links em [Próximos passos](#next-steps) para obter ajuda.

## <a name="protect-against-downtime"></a>Proteger contra o tempo de inatividade

A redundância é incorporada ao produto Avere vFXT para azure:

* O cluster está altamente disponível, e os nós de cluster individuais podem falhar com uma interrupção mínima.
* Os dados alterados no cache são gravados regularmente em arquivos de núcleo back-end (hardware NAS ou Azure Blob) para armazenamento a longo prazo.

Cada cluster Avere vFXT for Azure deve estar localizado em uma única zona de disponibilidade, mas você pode usar clusters redundantes localizados em diferentes zonas ou regiões diferentes para fornecer acesso rapidamente no caso de uma paralisação regional.

Você também pode posicionar contêineres de armazenamento em várias regiões se estiver preocupado em perder o acesso aos dados. No entanto, tenha em mente que as transações entre regiões têm maior latência e custo mais alto do que as transações que ficam dentro de uma região.

## <a name="protect-data-in-the-cluster-cache"></a>Proteger dados no cache de cluster

Os dados armazenados em cache são sempre gravados nos arquivos principais antes de um desligamento regular, mas em um desligamento descontrolado, os dados alterados no cache podem ser perdidos.

Se você usar o cluster para otimizar apenas as leituras de arquivos, não haverá alterações a perder. Se você também usar o cluster para cache alterações de arquivo (gravações), considere se deve ou não ajustar as políticas de [cache](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html) dos arquivos principais<!-- link to legacy doc --> para personalizar a freqüência com que os dados são gravados para armazenamento a longo prazo.

Em geral, seu plano de recuperação deve se concentrar no backup dos sistemas de armazenamento back-end, que possuem mais dados e normalmente são mais importantes para restabelecer seu fluxo de trabalho após uma falha.

## <a name="protect-data-in-nas-core-filers"></a>Proteger dados em arquivos núcleo NAS

Use métodos aceitos para proteger os dados armazenados em um arquivo núcleo de hardware NAS no local, incluindo instantâneos e backups completos, conforme recomendado pelo provedor NAS. A recuperação de desastres para esses filers principais está além do escopo deste artigo.

## <a name="protect-data-in-azure-blob-storage"></a>Proteger dados no armazenamento Azure Blob

O Avere vFXT for Azure usa o armazenamento localmente redundante (LRS) para filers principais Do Azure Blob. Isso significa que os dados em seus contêineres Blob são automaticamente copiados para proteção contra falhas transitórias de hardware dentro de um data center.

Esta seção dá dicas de como proteger ainda mais seus dados no armazenamento Blob contra raras paralisações em toda a região ou exclusões acidentais.

As práticas recomendadas para proteger dados no armazenamento Do Azure Blob incluem:

* Copie seus dados críticos para outra conta de armazenamento em outra região com freqüência (tão frequentemente quanto determinado pelo seu plano de recuperação de desastres).
* Controle o acesso aos dados em todos os sistemas de destino para evitar exclusão acidental ou corrupção. Considere usar [bloqueios de recursos](../azure-resource-manager/management/lock-resources.md) no armazenamento de dados.
* Habilite o recurso de snapshot de nuvem Avere vFXT para [azure](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) para os filers principais do Blob.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Copiar dados do arquivo principal do Avere vFXT para uma conta de backup

Siga essas etapas para estabelecer um backup de dados em outra conta.

1. Se necessário, gere uma nova chave de criptografia e armazene-a com segurança fora dos sistemas afetados.

   Se seus dados forem criptografados pelo cluster Avere vFXT for Azure, você deve gerar uma nova chave de criptografia antes de copiar os dados para outra conta de armazenamento. Armazene essa chave e senha com segurança em uma instalação segura e que não será impactada por uma falha regional.

   Você deve fornecer esta chave ao adicionar o contêiner a um cluster - mesmo se você estiver adicionando-o ao seu cluster original.

   Leia [configurações de criptografia na nuvem](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> para informações detalhadas.

   Se o seu contêiner usar apenas a criptografia incorporada do Azure, você pode pular este passo.

1. Remova o arquivo principal do sistema. Isso força o cluster a gravar todos os dados alterados para o armazenamento back-end.

   Embora você tenha que adicionar o arquivo principal após o backup, removê-lo é a melhor maneira de garantir que todos os dados sejam completamente escritos para o back-end. (A opção "suspender" às vezes pode deixar dados alterados no cache.) <!-- xxx true? or just metadata? -->

   Anote o nome do arquivo principal e as informações de junção (listadas na página **Namespace** no painel de controle) para que você possa replicá-lo quando você adicionar o contêiner após o backup.

   Use o painel de controle de cluster para remover o arquivo principal. [Abra o painel de controle de cluster](avere-vfxt-cluster-gui.md) e escolha Core **filer** > **Gerenciar arquivos principais**. Encontre o sistema de armazenamento que deseja fazer backup e use o botão **Remover** para excluí-lo do cluster.

1. Crie um novo e vazio recipiente de armazenamento Blob em outra conta de armazenamento em outra região.

1. Use qualquer ferramenta de cópia conveniente para copiar os dados do arquivo principal para o novo contêiner. A cópia deve replicar os dados sem alterações e sem interromper o formato proprietário do sistema de arquivos em nuvem usado pelo Avere vFXT para o Azure. As ferramentas baseadas no Azure incluem [AzCopy,](../storage/common/storage-use-azcopy-v10.md) [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)e [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

1. Depois de copiar os dados para o contêiner de backup, adicione o contêiner original de volta ao cluster conforme descrito no [armazenamento Configure](avere-vfxt-add-storage.md).

   * Use o mesmo nome do arquivo principal e as informações de junção para que os fluxos de trabalho do cliente não precisem ser alterantes.
   * Defina o valor do **conteúdo bucket** para a opção de dados existente.
   * Se o contêiner foi criptografado pelo cluster, você deve inserir a chave de criptografia atual para o seu conteúdo. (Esta é a chave que você atualizou na primeira etapa.)

Para backups após o primeiro, você não precisa criar um novo recipiente de armazenamento. No entanto, considere gerar uma nova chave de criptografia toda vez que você fizer um backup para ter certeza de que você tem a chave atual armazenada em um lugar que você se lembra.

### <a name="access-a-backup-data-source-during-an-outage"></a>Acesse uma fonte de dados de backup durante uma paralisação

Para acessar o contêiner de backup de um cluster Avere vFXT for Azure, siga este processo:

1. Se necessário, crie um novo cluster Avere vFXT para Azure em uma região não afetada.

   > [!TIP]
   > Quando você cria um cluster Avere vFXT para Azure, você pode salvar uma cópia de seu modelo de criação e parâmetros. Se você salvar essas informações ao criar seu cluster principal, poderá usá-la para criar um cluster de substituição com as mesmas propriedades. Na página [de resumo,](avere-vfxt-deploy.md#validation-and-purchase) clique no **link Baixar modelo e parâmetros.** Salve as informações em um arquivo antes de criar o cluster.

1. Adicione um novo arquivo núcleo de nuvem que aponta para o recipiente Blob duplicado.

   Certifique-se de especificar que o contêiner de destino já contém dados na configuração de **conteúdo bucket** do assistente de criação do arquivo principal. (O sistema deve alertá-lo se você acidentalmente deixar este conjunto para **Esvaziar**.)  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. Se necessário, atualize os clientes para que eles montem o novo cluster ou novo arquivo principal em vez do original. (Se você adicionar o arquivo de núcleo de substituição com o mesmo nome e caminho de junção do contêiner original, você não precisará atualizar os processos do cliente a menos que você precise montar o novo cluster em um novo endereço IP.)

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a personalização das configurações do Avere vFXT para Azure, leia [ajuste de cluster](avere-vfxt-tuning.md).
* Saiba mais sobre recuperação de desastres e construção de aplicativos resilientes no Azure:

  * [Orientações técnicas de resiliência do Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
  * [Recuperar-se de uma interrupção do serviço em toda a região](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Recuperação de desastre e alta disponibilidade para aplicativos do Azure](<https://docs.microsoft.com/azure/resiliency/resiliency-disaster-recovery-high-availability-azure-applications>)
  <!-- can't find these in the source tree to use relative links -->
