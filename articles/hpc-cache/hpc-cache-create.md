---
title: Criar um Azure HPC Cache
description: Como criar uma instância do Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: befbe2435a518b82cf5a3ab12e6129aa3ce5c22b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537960"
---
# <a name="create-an-azure-hpc-cache"></a>Criar um Azure HPC Cache

Use o portal do Azure para criar seu cache.

![captura de tela de uma visão geral do cache no portal do Azure, com botão de criação na parte inferior](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definir os detalhes básicos

![captura de tela da página de detalhes do projeto no portal do Azure](media/hpc-cache-create-basics.png)

Em **Detalhes do Projeto**, selecione a assinatura e o grupo de recursos que hospedarão o cache.

Em **Detalhes do Serviço**, defina o nome do cache e estes outros atributos:

* Localização – selecione uma das [regiões com suporte](hpc-cache-overview.md#region-availability).
* Rede virtual – você pode selecionar uma existente ou criar uma nova rede virtual.
* Sub-rede – escolha ou crie uma sub-rede com pelo menos 64 endereços IP (/24) que serão usados somente para esta instância do Azure HPC Cache.

## <a name="set-cache-capacity"></a>Definir a capacidade de cache
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na página **Cache**, você precisa definir a capacidade de seu cache. Os valores definidos aqui determinam a quantidade de dados que o cache pode armazenar e a rapidez com que ele pode atender às solicitações do cliente.

A capacidade também afeta o custo do cache.

Escolha a capacidade definindo estes dois valores:

* A taxa máxima de transferência de dados para o cache (taxa de transferência), em GB/segundo
* A quantidade de armazenamento alocada para dados armazenados em cache, em TB

Escolha um dos valores de taxa de transferência disponíveis e tamanhos de armazenamento em cache.

Tenha em mente que a taxa de transferência de dados real depende da carga de trabalho, das velocidades de rede e do tipo dos destinos de armazenamento. Os valores escolhidos definem a taxa de transferência máxima para todo o sistema de cache, mas uma parte dela é usada para tarefas de sobrecarga. Por exemplo, se um cliente solicitar um arquivo que ainda não está armazenado no cache ou se o arquivo estiver marcado como obsoleto, seu cache usará parte da taxa de transferência para obtê-lo arquivo do armazenamento de back-end.

O Azure HPC Cache gerencia quais arquivos são armazenados em cache e pré-carregados para maximizar as tarifas de acesso ao cache. O conteúdo do cache é avaliado continuamente e os arquivos são movidos para o armazenamento de longo prazo quando são acessados com menos frequência. Escolha um tamanho de armazenamento de cache que possa manter confortavelmente o conjunto ativo de arquivos de trabalho com espaço adicional para metadados e outras sobrecargas.

![captura de tela da página de dimensionamento do cache](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Habilite a criptografia do Azure Key Vault (opcional)

Se o cache estiver em uma região que suporte as chaves de criptografia gerenciadas pelo cliente, a página **Chaves de criptografia de disco** será exibida entre as guias **Cache** e **Tags.** A partir do momento da publicação, esta opção é apoiada no Leste dos EUA, Centro-Sul dos EUA e Oeste dos EUA 2.

Se você quiser gerenciar as chaves de criptografia usadas com o armazenamento do cache, forneça as informações do Azure Key Vault na página **chaves de criptografia do Disco.** O cofre-chave deve estar na mesma região e na mesma assinatura do cache.

Você pode pular esta seção se não precisar de chaves gerenciadas pelo cliente. O Azure criptografa dados com chaves gerenciadas pela Microsoft por padrão. Leia [a criptografia de armazenamento do Azure](../storage/common/storage-service-encryption.md) para saber mais.

> [!NOTE]
>
> * Não é possível alterar entre chaves gerenciadas pela Microsoft e chaves gerenciadas pelo cliente após a criação do cache.
> * Depois que o cache for criado, você deve autorizá-lo para acessar o cofre de chaves. Clique no botão **Ativar criptografia** na página **Visão geral** do cache para ativar a criptografia. Dê este passo dentro de 90 minutos após a criação do cache.
> * Os discos de cache são criados após essa autorização. Isso significa que o tempo inicial de criação do cache é curto, mas o cache não estará pronto para usar por dez minutos ou mais depois de autorizar o acesso.

Para obter uma explicação completa do processo de criptografia de chaves gerenciado pelo cliente, leia [Usar chaves de criptografia gerenciadas pelo cliente para cache Azure HPC](customer-keys.md).

![captura de tela da página chaves de criptografia com campos de "gerenciados pelo cliente" selecionados e campos de cofre de chaves mostrando](media/create-encryption.png)

O Cliente selecionado **conseguiu** escolher a criptografia de chave gerenciada pelo cliente. Os campos de especificação do cofre chave aparecem. Selecione o Azure Key Vault para usar e selecione a chave e a versão a serem usados para este cache. A chave deve ser uma chave RSA de 2048 bits. Você pode criar uma nova versão de cofre, chave ou chave a partir desta página.

Depois de criar o cache, você deve autorizá-lo a usar o serviço de cofre de chaves. Leia [autorizar a criptografia do Azure Key Vault do cache](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) para obter detalhes.

## <a name="add-resource-tags-optional"></a>Adicionar marcas de recurso (opcional)

A página **Marcas** permite que você adicione [tags de recurso](https://go.microsoft.com/fwlink/?linkid=873112) à sua instância do Azure HPC Cache.

## <a name="finish-creating-the-cache"></a>Concluir a criação do cache

Depois de configurar o novo cache, clique na guia **'Revisar + criar'.** O portal valida suas seleções e permite que você revise suas escolhas. Se tudo estiver correto, clique em **Criar**.

A criação do cache leva cerca de 10 minutos. Você pode acompanhar o progresso no painel de notificações do portal do Azure.

![captura de tela das páginas "implantação em andamento" e "notificações" da criação de cache no portal](media/hpc-cache-deploy-status.png)

Quando a criação for concluída, uma notificação será exibida com um link para a nova instância do Azure HPC Cache e o cache será exibido na lista **Recursos** de sua assinatura.

![captura de tela da instância do Azure HPC Cache no portal do Azure](media/hpc-cache-new-overview.png)

> [!NOTE]
> Se o cache usar chaves de criptografia gerenciadas pelo cliente, o cache pode aparecer na lista de recursos antes que o status de implantação seja concluído. Assim que o status do cache estiver **esperando a chave,** você pode [autorizá-lo](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) a usar o cofre de chaves.

## <a name="next-steps"></a>Próximas etapas

Depois que seu cache aparecer na lista **Recursos,** você pode passar para a próxima etapa.

* [Defina metas de armazenamento](hpc-cache-add-storage.md) para dar ao seu cache acesso às suas fontes de dados.
* Se você usar chaves de criptografia gerenciadas pelo cliente, você precisa autorizar a [criptografia do Azure Key Vault](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) a partir da página de visão geral do cache para concluir sua configuração de cache. Você deve fazer este passo antes de adicionar armazenamento. Leia [Usar chaves de criptografia gerenciadas pelo cliente](customer-keys.md) para obter detalhes.
