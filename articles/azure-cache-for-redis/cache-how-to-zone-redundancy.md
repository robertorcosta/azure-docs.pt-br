---
title: Habilitar a redundância de zona para o cache do Azure para Redis (versão prévia)
description: Saiba como configurar a redundância de zona para o cache do Azure da camada Premium para instâncias de Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 3c396d6d5b9da9a48e0d68a2d7d49561d6f688de
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91341222"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis-preview"></a>Habilitar a redundância de zona para o cache do Azure para Redis (versão prévia)
Neste artigo, você aprenderá a configurar uma instância de cache do Azure com redundância de zona usando o portal do Azure.

O cache do Azure para as camadas Standard e Premium do Redis fornecem redundância interna por meio da Hospedagem de cada cache em duas VMs (máquinas virtuais) dedicadas. Embora essas VMs estejam localizadas em domínios separados de [falha e atualização do Azure](/azure/virtual-machines/windows/manage-availability) e altamente disponíveis, elas estão suscetíveis a falhas no nível do datacenter. O cache do Azure para Redis também dá suporte à redundância de zona em sua camada Premium. Um cache com redundância de zona é executado em VMs distribuídas em várias [zonas de disponibilidade](/azure/virtual-machines/windows/manage-availability#use-availability-zones-to-protect-from-datacenter-level-failures). Ele fornece maior resiliência e disponibilidade.

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
    | **Zonas de Disponibilidade** | Menu suspenso e selecione quais zonas usar. | As VMs para seu cache serão distribuídas pelas zonas selecionadas o mais uniforme possível. Por exemplo, se o cache tiver três réplicas e usar duas zonas, haverá duas VMs em cada zona. |
   
1. Depois de selecionar um cache de camada Premium, você será perguntado se deseja habilitar o clustering Redis ou não. Deixe o **clustering** como *desabilitado*. 
   
    :::image type="content" source="media/cache-how-to-premium-clustering/redis-clustering-disabled.png" alt-text="Configurar o cluster Redis.":::

    > [!NOTE]
    > O suporte à redundância de zona só funciona com caches não clusterizados e não geograficamente replicados no momento. Além disso, ele não dá suporte a link privado, dimensionamento, persistência de dados ou importação/exportação.
    >

1. Clique em **Criar**. 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="Crie o cache do Azure para Redis.":::
   
    A criação do cache demora um pouco. Monitore o progresso na página **Visão Geral** do Cache do Azure para Redis. Quando o **Status** for mostrado como **Em execução**, o cache estará pronto para uso.

    > [!NOTE]
    > As zonas de disponibilidade não podem ser alteradas após a criação de um cache.
    >

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o cache do Azure para recursos do Redis.

> [!div class="nextstepaction"]
> [Cache do Azure para camadas de serviço do Redis Premium](cache-overview.md#service-tiers)
