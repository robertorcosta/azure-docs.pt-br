---
title: Criar um Azure HPC Cache
description: Como criar uma instância do Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 06/01/2020
ms.author: v-erkel
ms.openlocfilehash: 894595ee3660532bf046a39e994fa669f7c6b002
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84434082"
---
# <a name="create-an-azure-hpc-cache"></a>Criar um Azure HPC Cache

Use o portal do Azure para criar seu cache.

![captura de tela de uma visão geral do cache no portal do Azure, com botão de criação na parte inferior](media/hpc-cache-home-page.png)

Clique na imagem abaixo para assistir a uma [demonstração em vídeo](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) da criação de um cache e da adição de um destino de armazenamento.

[![miniatura de vídeo: cache do HPC do Azure: instalação (clique para visitar a página de vídeo)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="define-basic-details"></a>Definir os detalhes básicos

![captura de tela da página de detalhes do projeto no portal do Azure](media/hpc-cache-create-basics.png)

Em **Detalhes do Projeto**, selecione a assinatura e o grupo de recursos que hospedarão o cache. Verifique se a assinatura está na lista de [acesso](hpc-cache-prereqs.md#azure-subscription).

Em **Detalhes do Serviço**, defina o nome do cache e estes outros atributos:

* Localização – selecione uma das [regiões com suporte](hpc-cache-overview.md#region-availability).
* Rede virtual – você pode selecionar uma existente ou criar uma nova rede virtual.
* Sub-rede – escolha ou crie uma sub-rede com pelo menos 64 endereços IP (/24). Essa sub-rede deve ser usada somente para esta instância de cache do Azure HPC.

## <a name="set-cache-capacity"></a>Definir a capacidade de cache
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na página **Cache**, você precisa definir a capacidade de seu cache. Os valores definidos aqui determinam a quantidade de dados que o cache pode armazenar e a rapidez com que ele pode atender às solicitações do cliente.

A capacidade também afeta o custo do cache.

Escolha a capacidade definindo estes dois valores:

* A taxa máxima de transferência de dados para o cache (taxa de transferência), em GB/segundo
* A quantidade de armazenamento alocada para dados armazenados em cache, em TB

Escolha um dos valores de taxa de transferência disponíveis e tamanhos de armazenamento em cache.

Tenha em mente que a taxa de transferência de dados real depende da carga de trabalho, das velocidades de rede e do tipo dos destinos de armazenamento. Os valores escolhidos definem a taxa de transferência máxima para todo o sistema de cache, mas uma parte dela é usada para tarefas de sobrecarga. Por exemplo, se um cliente solicitar um arquivo que ainda não esteja armazenado no cache, ou se o arquivo estiver marcado como obsoleto, seu cache usará parte de sua taxa de transferência para obtê-lo do armazenamento de back-end.

O Azure HPC Cache gerencia quais arquivos são armazenados em cache e pré-carregados para maximizar as tarifas de acesso ao cache. O conteúdo do cache é avaliado continuamente e os arquivos são movidos para o armazenamento de longo prazo quando são acessados com menos frequência. Escolha um tamanho de armazenamento de cache que possa manter confortavelmente o conjunto ativo de arquivos de trabalho, além de espaço adicional para metadados e outras sobrecargas.

![captura de tela da página de dimensionamento do cache](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Habilitar criptografia de Azure Key Vault (opcional)

Se o cache estiver em uma região que dá suporte a chaves de criptografia gerenciadas pelo cliente, a página **chaves de criptografia de disco** aparecerá entre as guias **cache** e **marcas** . No momento da publicação, essa opção tem suporte no leste dos EUA, no Sul EUA Central e no oeste dos EUA 2.

Se você quiser gerenciar as chaves de criptografia usadas com o armazenamento de cache, forneça suas Azure Key Vault informações na página **chaves de criptografia de disco** . O cofre de chaves deve estar na mesma região e na mesma assinatura que o cache.

Você poderá ignorar esta seção se não precisar de chaves gerenciadas pelo cliente. Por padrão, o Azure criptografa dados com chaves gerenciadas pela Microsoft. Leia [criptografia de armazenamento do Azure](../storage/common/storage-service-encryption.md) para saber mais.

> [!NOTE]
>
> * Você não pode alterar entre chaves gerenciadas pela Microsoft e chaves gerenciadas pelo cliente depois de criar o cache.
> * Depois que o cache é criado, você deve autorizá-lo a acessar o cofre de chaves. Clique no botão **habilitar criptografia** na página **visão geral** do cache para ativar a criptografia. Siga esta etapa dentro de 90 minutos da criação do cache.
> * Os discos de cache são criados após essa autorização. Isso significa que o tempo de criação do cache inicial é curto, mas o cache não estará pronto para ser usado por dez minutos ou mais depois que você autorizar o acesso.

Para obter uma explicação completa do processo de criptografia de chave gerenciada pelo cliente, leia [usar chaves de criptografia gerenciadas pelo cliente para o cache do Azure HPC](customer-keys.md).

![captura de tela da página chaves de criptografia com os campos "gerenciado pelo cliente" selecionados e cofre de chaves mostrando](media/create-encryption.png)

Selecione **cliente gerenciado** para escolher criptografia de chave gerenciada pelo cliente. Os campos de especificação do cofre de chaves são exibidos. Selecione a Azure Key Vault a ser usada e, em seguida, selecione a chave e a versão a serem usadas para esse cache. A chave deve ser uma chave RSA de 2048 bits. Você pode criar um novo cofre de chaves, chave ou versão de chave nesta página.

Depois de criar o cache, você deve autorizá-lo a usar o serviço de cofre de chaves. Leia [autorizar Azure Key Vault criptografia do cache](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) para obter detalhes.

## <a name="add-resource-tags-optional"></a>Adicionar marcas de recurso (opcional)

A página **Marcas** permite que você adicione [tags de recurso](https://go.microsoft.com/fwlink/?linkid=873112) à sua instância do Azure HPC Cache.

## <a name="finish-creating-the-cache"></a>Concluir a criação do cache

Depois de configurar o novo cache, clique na guia **revisar + criar** . O portal valida suas seleções e permite que você examine suas escolhas. Se tudo estiver correto, clique em **Criar**.

A criação do cache leva cerca de 10 minutos. Você pode acompanhar o progresso no painel de notificações do portal do Azure.

![captura de tela das páginas "implantação em andamento" e "notificações" da criação de cache no portal](media/hpc-cache-deploy-status.png)

Quando a criação for concluída, uma notificação será exibida com um link para a nova instância do Azure HPC Cache e o cache será exibido na lista **Recursos** de sua assinatura.

![captura de tela da instância do Azure HPC Cache no portal do Azure](media/hpc-cache-new-overview.png)

> [!NOTE]
> Se o cache usar chaves de criptografia gerenciadas pelo cliente, o cache poderá aparecer na lista de recursos antes que o status da implantação seja alterado para concluído. Assim que o status do cache estiver **aguardando a chave** , você poderá [autorizá-lo](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) a usar o cofre de chaves.

## <a name="next-steps"></a>Próximas etapas

Depois que o cache aparecer na lista de **recursos** , você poderá passar para a próxima etapa.

* [Defina os destinos de armazenamento](hpc-cache-add-storage.md) para dar acesso ao cache às suas fontes de dados.
* Se você usar chaves de criptografia gerenciadas pelo cliente, será necessário [autorizar Azure Key Vault criptografia](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) na página Visão geral do cache para concluir a configuração do cache. Você deve fazer essa etapa antes de poder adicionar armazenamento. Leia [usar chaves de criptografia gerenciadas pelo cliente](customer-keys.md) para obter detalhes.
