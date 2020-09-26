---
title: Adicionar réplicas ao cache do Azure para Redis (versão prévia)
description: Saiba como adicionar mais réplicas ao seu cache do Azure da camada Premium para instâncias de Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: a747cf8e1713eb905aee02af95c568a448b47f05
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91341148"
---
# <a name="add-replicas-to-azure-cache-for-redis-preview"></a>Adicionar réplicas ao cache do Azure para Redis (versão prévia)
Neste artigo, você aprenderá a configurar uma instância de cache do Azure com réplicas adicionais usando o portal do Azure.

O cache do Azure para as camadas Standard e Premium do Redis oferece redundância ao hospedar cada cache em duas VMs (máquinas virtuais) dedicadas. Essas VMs são configuradas como primária e réplica. Quando a VM primária torna-se indisponível, a réplica detecta que e assume como a nova primária automaticamente. Agora você pode aumentar o número de réplicas em um cache Premium até três, dando a você um total de quatro VMs fazendo backup de um cache. Ter várias réplicas resulta em maior resiliência do que a que uma única réplica pode fornecer.

> [!IMPORTANT]
> Essa visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [termos de uso suplementares para visualizações de Microsoft Azure.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 
> 

## <a name="prerequisites"></a>Pré-requisitos
* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)

> [!NOTE]
> Este recurso está atualmente em visualização- [Fale conosco](mailto:azurecache@microsoft.com) se você estiver interessado.
>

## <a name="create-a-cache"></a>Criar um cache
Para criar um cache, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Criar um recurso**.
  
1. Na página **Novo**, selecione **Bancos de dados** e, em seguida, **Cache do Azure para Redis**.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Selecione cache do Azure para Redis.":::
   
1. Na página **Novo Cache Redis**, defina as configurações para o novo cache.
   
    | Configuração      | Valor sugerido  | DESCRIÇÃO |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome DNS** | Insira um nome global exclusivo. | O nome de cache precisa ser uma cadeia de caracteres com 1 a 63 caracteres que contém somente números, letras ou hifens. O nome precisa começar e terminar com um número ou uma letra e não pode conter hifens consecutivos. O *nome do host* de sua instância de cache será *\<DNS name>.redis.cache.windows.net*. | 
    | **Assinatura** | Clique na lista suspensa e selecione sua assinatura. | A assinatura na qual essa nova instância do Cache do Azure para Redis será criada. | 
    | **Grupo de recursos** | Clique na lista suspensa e selecione um grupo de recursos ou selecione **Criar** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual o cache e outros recursos serão criados. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
    | **Localidade** | Clique na lista suspensa e selecione uma localização. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão o cache. |
    | **Tipo de preços** | Menu suspenso e selecione um cache de [camada Premium](https://azure.microsoft.com/pricing/details/cache/) . |  O tipo de preço determina o tamanho, o desempenho e os recursos disponíveis para o cache. Para obter mais informações, confira [Visão geral do Cache do Azure para Redis](cache-overview.md). |
    | **Contagem de réplicas** | Deslize para escolher o número de réplicas. | O padrão é UTF-1. |
   
1. Depois de selecionar um cache de camada Premium, você será perguntado se deseja habilitar o clustering Redis ou não. Deixe o **clustering** como *desabilitado*. 
   
    :::image type="content" source="media/cache-how-to-premium-clustering/redis-clustering-disabled.png" alt-text="Configurar o cluster Redis.":::

    > [!NOTE]
    > O suporte a várias réplicas funciona apenas com caches não clusterizados no momento.
    >

1. Clique em **Criar**. 
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Crie o cache do Azure para Redis.":::
   
    A criação do cache demora um pouco. Monitore o progresso na página **Visão Geral** do Cache do Azure para Redis. Quando o **Status** for mostrado como **Em execução**, o cache estará pronto para uso.

    > [!NOTE]
    > O número de réplicas em um cache não pode ser alterado depois de ser criado.
    >

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o cache do Azure para recursos do Redis.

> [!div class="nextstepaction"]
> [Cache do Azure para camadas de serviço do Redis Premium](cache-overview.md#service-tiers)
