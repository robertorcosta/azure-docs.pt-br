---
title: incluir arquivo
description: incluir arquivo
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 10/06/2020
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: 07a80fcd1ec62b051a660bdac696f89e3b7c42b7
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93354116"
---
1. Para criar um cache, entre no [portal do Azure](https://portal.azure.com) e selecione **Criar um recurso**.

    :::image type="content" source="media/redis-cache-create/create-resource.png" alt-text="Selecionar o Cache do Azure para Redis.":::

   
1. Na página **Novo** , selecione **Bancos de dados** e, em seguida, **Cache do Azure para Redis**.

    :::image type="content" source="media/redis-cache-create/select-cache.png" alt-text="Selecionar o Cache do Azure para Redis.":::
   
1. Na página **Novo Cache Redis** , defina as configurações para o novo cache.
   
   | Configuração      | Valor sugerido  | DESCRIÇÃO |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Insira um nome global exclusivo. | O nome de cache precisa ser uma cadeia de caracteres com 1 a 63 caracteres que contém somente números, letras ou hifens. O nome precisa começar e terminar com um número ou uma letra e não pode conter hifens consecutivos. O *nome do host* de sua instância de cache será *\<DNS name>.redis.cache.windows.net*. | 
   | **Assinatura** | Clique na lista suspensa e selecione sua assinatura. | A assinatura na qual essa nova instância do Cache do Azure para Redis será criada. | 
   | **Grupo de recursos** | Clique na lista suspensa e selecione um grupo de recursos ou selecione **Criar** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual o cache e outros recursos serão criados. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
   | **Localidade** | Clique na lista suspensa e selecione uma localização. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão o cache. |
   | **Tipo de preços** | Clique na lista suspensa e selecione um [Tipo de preço](https://azure.microsoft.com/pricing/details/cache/). |  O tipo de preço determina o tamanho, o desempenho e os recursos disponíveis para o cache. Para obter mais informações, confira [Visão geral do Cache do Azure para Redis](../articles/azure-cache-for-redis/cache-overview.md). |

1. Selecione a guia **Rede** ou clique no botão **Rede** na parte inferior da página.

1. Na guia **Rede** , escolha o método de conectividade.

1. Selecione **Próximo: Avançado** ou clique no botão **Próximo: Avançado** na parte inferior da página.

1. Na guia **Avançado** de uma instância de cache Básico ou Standard, selecione a alternância Habilitar se desejar habilitar uma porta não TLS. Também é possível selecionar qual versão do Redis você gostaria de usar, a 4 ou a (versão prévia) 6.

    :::image type="content" source="media/redis-cache-create/redis-version.png" alt-text="Redis versão 4 ou 6.":::

1. Na guia **Avançado** de uma instância de cache Premium, defina as configurações da porta não TLS, do clustering e da persistência de dados. Também é possível selecionar qual versão do Redis você gostaria de usar, a 4 ou a (versão prévia) 6. 

1. Selecione **Próximo: Marcas** ou clique no botão **Próximo: Botão** Categorias na parte inferior da página.

1. Opcionalmente, na guia **Marcas** , insira o nome e o valor caso deseje categorizar o recurso. 

1. Selecione **Examinar + criar**. Você será levado para a guia Examinar + criar, na qual o Azure validará a configuração.

1. Depois que a mensagem em verde Validação aprovada for exibida, selecione **Criar**.

A criação do cache demora um pouco. Monitore o progresso na página **Visão Geral** do Cache do Azure para Redis. Quando o **Status** for mostrado como **Em execução** , o cache estará pronto para uso. 