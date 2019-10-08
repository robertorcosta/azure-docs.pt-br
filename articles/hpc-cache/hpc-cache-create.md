---
title: Criar um Azure HPC Cache (versão prévia)
description: Como criar uma instância do Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: rohogue
ms.openlocfilehash: 7052b88a24ff5353656a71a7bfb044922ae1415c
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709991"
---
# <a name="create-an-azure-hpc-cache-preview"></a>Criar um Azure HPC Cache (versão prévia)

Use o portal do Azure para criar seu cache.

![captura de tela de uma visão geral do cache no portal do Azure, com botão de criação na parte inferior](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definir os detalhes básicos

![captura de tela da página de detalhes do projeto no portal do Azure](media/hpc-cache-create-basics.png)

Em **Detalhes do Projeto**, selecione a assinatura e o grupo de recursos que hospedarão o cache. Verifique se a assinatura está na lista de [acesso de visualização](hpc-cache-prereqs.md#azure-subscription).

Em **Detalhes do Serviço**, defina o nome do cache e estes outros atributos:

* Localização – selecione uma das [regiões com suporte](hpc-cache-overview.md#region-availability).
* Rede virtual – você pode selecionar uma existente ou criar uma nova rede virtual.
* Sub-rede – escolha ou crie uma sub-rede com pelo menos 64 endereços IP (/24) que serão usados somente para esta instância do Azure HPC Cache.

## <a name="set-cache-capacity"></a>Definir a capacidade de cache
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na página **Cache**, você precisa definir a capacidade de seu cache. Os valores definidos aqui determinam a quantidade de dados que o cache pode armazenar e a rapidez com que ele pode atender às solicitações do cliente.

Após o período de versão prévia pública, a capacidade também afetará o custo do cache.

Escolha a capacidade definindo estes dois valores:

* A taxa máxima de transferência de dados para o cache (taxa de transferência), em GB/segundo
* A quantidade de armazenamento alocada para dados armazenados em cache, em TB

Escolha um dos valores de taxa de transferência disponíveis e tamanhos de armazenamento em cache.

Tenha em mente que a taxa de transferência de dados real depende da carga de trabalho, das velocidades de rede e do tipo dos destinos de armazenamento. Os valores escolhidos definem a taxa de transferência máxima para todo o sistema de cache, mas uma parte dela é usada para tarefas de sobrecarga. Por exemplo, se um cliente solicitar um arquivo que ainda não está armazenado no cache ou se o arquivo estiver marcado como obsoleto, seu cache usará parte da taxa de transferência para obtê-lo arquivo do armazenamento de back-end.

O Azure HPC Cache gerencia quais arquivos são armazenados em cache e pré-carregados para maximizar as tarifas de acesso ao cache. O conteúdo do cache é avaliado continuamente e os arquivos são movidos para o armazenamento de longo prazo quando são acessados com menos frequência. Escolha um tamanho de armazenamento de cache que possa manter confortavelmente o conjunto ativo de arquivos de trabalho com espaço adicional para metadados e outras sobrecargas.

![captura de tela da página de dimensionamento do cache](media/hpc-cache-create-capacity.png)

## <a name="add-resource-tags-optional"></a>Adicionar marcas de recurso (opcional)

A página **Marcas** permite que você adicione [tags de recurso](https://go.microsoft.com/fwlink/?linkid=873112) à sua instância do Azure HPC Cache.

## <a name="finish-creating-the-cache"></a>Concluir a criação do cache

Depois de configurar o novo cache, clique na guia **Examinar + criar**. O portal valida suas seleções e permite que você examine suas escolhas. Se tudo estiver correto, clique em **Criar**.

A criação do cache leva cerca de 10 minutos. Você pode acompanhar o progresso no painel de notificações do portal do Azure.

![captura de tela das páginas "implantação em andamento" e "notificações" da criação de cache no portal](media/hpc-cache-deploy-status.png)

Quando a criação for concluída, uma notificação será exibida com um link para a nova instância do Azure HPC Cache e o cache será exibido na lista **Recursos** de sua assinatura.

![captura de tela da instância do Azure HPC Cache no portal do Azure](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Próximas etapas

Depois que o cache for exibido na lista **Recursos**, defina os destinos de armazenamento para dar ao cache o acesso às fontes de dados.

* [Adicionar destinos de armazenamento](hpc-cache-add-storage.md)
