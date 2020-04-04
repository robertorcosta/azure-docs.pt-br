---
title: Adicionar armazenamento a um cache Azure HPC
description: Como definir alvos de armazenamento para que o cache Do Azure HPC possa usar seu sistema NFS no local ou contêineres Azure Blob para armazenamento de arquivos a longo prazo
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: 3fbc4e683c2b0e72c3a084a59793dbf9eb4b658c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657415"
---
# <a name="add-storage-targets"></a>Adicionar destinos de armazenamento

*Os alvos de armazenamento* são o armazenamento back-end para arquivos que são acessados através de um cache Azure HPC. Você pode adicionar armazenamento NFS (como um sistema de hardware no local) ou armazenar dados no Azure Blob.

Você pode definir até dez alvos de armazenamento diferentes para um cache. O cache apresenta todos os alvos de armazenamento em um namespace agregado.

Lembre-se que as exportações de armazenamento devem estar acessíveis a partir da rede virtual do cache. Para armazenamento de hardware no local, talvez seja necessário configurar um servidor DNS que possa resolver nomes de host para acesso ao armazenamento NFS. Leia mais em [acesso ao DNS](hpc-cache-prereqs.md#dns-access).

Adicione metas de armazenamento após a criação do cache. O procedimento é ligeiramente diferente dependendo se você está adicionando armazenamento Azure Blob ou uma exportação NFS. Os detalhes para cada um estão abaixo.

## <a name="open-the-storage-targets-page"></a>Abra a página de metas de armazenamento

No portal Azure, abra sua instância de cache e clique em **Alvos de armazenamento** na barra lateral esquerda. A página de destinos de armazenamento lista todos os alvos existentes e dá um link para adicionar um novo.

![captura de tela do link de alvos de armazenamento na barra lateral, sob o título Configurar, que está entre os títulos de categoria Configurações e Monitoramento](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Adicionar um novo alvo de armazenamento Azure Blob

Um novo alvo de armazenamento Blob precisa de um contêiner Blob vazio ou de um contêiner preenchido com dados no formato de sistema de arquivos em nuvem Do Cache Azure HPC. Leia mais sobre o pré-carregamento de um contêiner Blob em [dados move para armazenamento Azure Blob](hpc-cache-ingest.md).

Você pode criar um novo contêiner a partir desta página pouco antes de adicioná-lo.

![captura de tela da página de destino de armazenamento adicionar, preenchida com informações para um novo alvo de armazenamento Azure Blob](media/hpc-cache-add-blob.png)

Para definir um contêiner Azure Blob, digite essas informações.

* **Nome de destino de** armazenamento - Defina um nome que identifique esse destino de armazenamento no cache Do Azure HPC.
* **Tipo de destino** - Escolha **Blob**.
* **Conta de armazenamento** - Selecione a conta que deseja usar.

  Você precisará autorizar a instância de cache para acessar a conta de armazenamento conforme descrito em [Adicionar as funções de acesso](#add-the-access-control-roles-to-your-account).

  Para obter informações sobre o tipo de conta de armazenamento que você pode usar, leia [os requisitos de armazenamento Blob](hpc-cache-prereqs.md#blob-storage-requirements).

* **Contêiner de armazenamento** - Selecione o recipiente Blob para este destino ou clique **em Criar novo**.

  ![captura de tela de diálogo para especificar nome e nível de acesso (privado) para novo contêiner](media/add-blob-new-container.png)

* **Caminho de namespace virtual** - Defina o caminho do arquivo voltado para o cliente para esse destino de armazenamento. Leia [Configure namespace agregado](hpc-cache-namespace.md) para saber mais sobre o recurso de namespace virtual.

Quando terminar, clique em **OK** para adicionar o alvo de armazenamento.

> [!NOTE]
> Se o firewall da conta de armazenamento estiver definido para restringir o acesso apenas a "redes selecionadas", use a solução temporária documentada em Trabalhar em [torno das configurações de firewall da conta de armazenamento Blob](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>Adicione as funções de controle de acesso à sua conta

O Azure HPC Cache usa [o RBAC (Role-Based Access Control, controle de acesso baseado em função)](https://docs.microsoft.com/azure/role-based-access-control/index) para autorizar o serviço de cache a acessar sua conta de armazenamento para alvos de armazenamento Do Azure Blob.

O proprietário da conta de armazenamento deve adicionar explicitamente as funções [Storage Account Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) e Storage [Blob Data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) para o usuário "HPC Cache Resource Provider".

Você pode fazer isso com antecedência ou clicando em um link na página onde você adiciona um alvo de armazenamento Blob. Tenha em mente que pode levar até cinco minutos para que as configurações de função se propaguem através do ambiente Azure, então você deve esperar alguns minutos depois de adicionar as funções antes de criar um alvo de armazenamento.

Etapas para adicionar as funções RBAC:

1. Abra a página **de controle de acesso (IAM)** para a conta de armazenamento. (O link na página **de destino Adicionar armazenamento** abre automaticamente esta página para a conta selecionada.)

1. Clique **+** na parte superior da página e escolha **Adicionar uma atribuição de função**.

1. Selecione a função "Contribuinte da conta de armazenamento" na lista.

1. No **campo Atribuir acesso ao** campo, deixe o valor padrão selecionado ("Usuário, grupo ou principal de serviço do Azure AD").  

1. No campo **Selecionar,** procure por "hpc".  Essa seqüência deve corresponder a um princípio de serviço, chamado "Provedor de recursos de cache HPC". Clique nesse princípio para selecioná-lo.

   > [!NOTE]
   > Se uma pesquisa por "hpc" não funcionar, tente usar a string "storagecache" em vez disso. Os usuários que participaram de pré-visualizações (antes da GA) podem precisar usar o nome mais antigo para o principal do serviço.

1. Clique no botão **Salvar** na parte inferior.

1. Repita este processo para atribuir a função "Storage Blob Data Contributor".  

![captura de tela de adicionar GUI de atribuição de função](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Adicionar um novo alvo de armazenamento NFS

Um alvo de armazenamento NFS tem mais campos do que o alvo de armazenamento Blob. Esses campos especificam como alcançar a exportação de armazenamento e como armazenar seus dados de forma eficiente. Além disso, um destino de armazenamento NFS permite criar vários caminhos de namespace se o host NFS tiver mais de uma exportação disponível.

![Captura de tela de adicionar página-alvo de armazenamento com destino NFS definido](media/add-nfs-target.png)

> [!NOTE]
> Antes de criar um destino de armazenamento NFS, certifique-se de que seu sistema de armazenamento esteja acessível a partir do cache Do Azure HPC e atenda aos requisitos de permissão. A criação do destino de armazenamento falhará se o cache não puder acessar o sistema de armazenamento. Leia [os requisitos de armazenamento NFS](hpc-cache-prereqs.md#nfs-storage-requirements) e [problemas de configuração nas e armazenamento NFS para obter detalhes.](troubleshoot-nas.md)

Forneça essas informações para um alvo de armazenamento com backup do NFS:

* **Nome de destino de** armazenamento - Defina um nome que identifique esse destino de armazenamento no cache Do Azure HPC.

* **Tipo de destino** - Escolha **NFS**.

* **Nome do host** - Digite o endereço IP ou o nome de domínio totalmente qualificado para o seu sistema de armazenamento NFS. (Use um nome de domínio somente se o cache tiver acesso a um servidor DNS que possa resolver o nome.)

* **Modelo de uso** - Escolha um dos perfis de cache de dados com base no seu fluxo de trabalho, descrito em [Escolha um modelo de uso](#choose-a-usage-model), abaixo.

### <a name="nfs-namespace-paths"></a>Caminhos de namespace do NFS

Um destino de armazenamento NFS pode ter vários caminhos virtuais, desde que cada caminho represente uma exportação ou subdiretório diferente no mesmo sistema de armazenamento.

Crie todos os caminhos a partir de um alvo de armazenamento.

Você pode [adicionar e editar caminhos de namespace](hpc-cache-edit-storage.md) em um alvo de armazenamento a qualquer momento.

Preencha esses valores para cada caminho de namespace:

* **Caminho de namespace virtual** - Defina o caminho do arquivo voltado para o cliente para esse destino de armazenamento. Leia [Configure namespace agregado](hpc-cache-namespace.md) para saber mais sobre o recurso de namespace virtual.

<!--  The virtual path should start with a slash ``/``. -->

* **Caminho de exportação NFS** - Digite o caminho para a exportação nfs.

* **Caminho de subdiretório** - Se você quiser montar um subdiretório específico da exportação, insira-o aqui. Se não, deixe este campo em branco.

Quando terminar, clique em **OK** para adicionar o alvo de armazenamento.

### <a name="choose-a-usage-model"></a>Escolha um modelo de uso
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Quando você cria um alvo de armazenamento que aponta para um sistema de armazenamento NFS, você precisa escolher o modelo de uso para esse destino. Este modelo determina como seus dados são armazenados em cache.

Há três opções:

* **Leia gravações pesadas e pouco frequentes** - Use esta opção se quiser acelerar o acesso à leitura de arquivos que são estáticos ou raramente alterados.

  Essa opção armazena arquivos que os clientes lêem, mas passa gravações para o armazenamento back-end imediatamente. Os arquivos armazenados no cache nunca são comparados com os arquivos no volume de armazenamento NFS.

  Não use essa opção se houver o risco de que um arquivo possa ser modificado diretamente no sistema de armazenamento sem antes escrevê-lo no cache. Se isso acontecer, a versão armazenada em cache do arquivo nunca será atualizada com alterações do back-end, e o conjunto de dados pode se tornar inconsistente.

* **Maior que 15% de gravações** - Esta opção acelera o desempenho de leitura e gravação. Ao usar essa opção, todos os clientes devem acessar arquivos através do Cache Azure HPC em vez de montar o armazenamento back-end diretamente. Os arquivos armazenados em cache terão alterações recentes que não são armazenadas na parte traseira.

  Neste modelo de uso, os arquivos no cache não são verificados contra os arquivos no armazenamento back-end. A versão armazenada em cache do arquivo é considerada mais atual. Um arquivo modificado no cache é gravado no sistema de armazenamento back-end depois de estar no cache por uma hora sem alterações adicionais.

* **Os clientes escrevem no destino NFS, ignorando o cache** - Escolha essa opção se algum cliente em seu fluxo de trabalho gravar dados diretamente no sistema de armazenamento sem antes escrever para o cache. Os arquivos que os clientes solicitam são armazenados em cache, mas quaisquer alterações nesses arquivos do cliente são repassadas imediatamente para o sistema de armazenamento back-end.

  Com este modelo de uso, os arquivos no cache são frequentemente verificados em relação às versões back-end para atualizações. Essa verificação permite que os arquivos sejam alterados fora do cache, mantendo a consistência dos dados.

Esta tabela resume as diferenças do modelo de uso:

| Modelo de uso | Modo de cache | Verificação back-end | Atraso máximo de gravação |
| ---- | ---- | ---- | ---- |
| Leia gravações pesadas e pouco freqüentes | Ler | Never | Nenhum |
| Maior que 15% escreve | Leitura/gravação | Never | 1 hora |
| Os clientes contornam o cache | Ler | 30 segundos | Nenhum |

## <a name="next-steps"></a>Próximas etapas

Depois de criar metas de armazenamento, considere uma dessas tarefas:

* [Montar o Azure HPC Cache](hpc-cache-mount.md)
* [Mova dados para o armazenamento Do Azure Blob](hpc-cache-ingest.md)

Se você precisar atualizar qualquer configuração, você pode [editar um alvo de armazenamento](hpc-cache-edit-storage.md).
