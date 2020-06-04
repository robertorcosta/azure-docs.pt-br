---
title: Solicitação de aumento de cota de núcleo da CPU – Azure HDInsight
description: Conheça o processo para solicitar um aumento para os núcleos de CPU alocados para sua assinatura.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: a708ad9626f4d6750de64097a7e1e9177c350e5e
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83717735"
---
# <a name="requesting-quota-increases-for-azure-hdinsight"></a>Como solicitar aumentos de cota para o Azure HDInsight

As cotas de núcleo de CPU ajudam a garantir que o uso de recursos seja bem distribuído entre todos os clientes em uma determinada região do Azure. No entanto, em determinados casos, seus requisitos de negócios podem exigir mais recursos de cluster do que a sua cota atual permitirá. Nesses casos, você pode solicitar um aumento de cota de núcleo de CPU para que possa implantar clusters, o que corresponde aos seus requisitos de processamento de dados.

Ao atingir um limite de cota, você não pode implantar novos clusters ou escalar horizontalmente os clusters existentes, adicionando mais nós de trabalho. O único limite de cota é a cota de Núcleos de CPU que existe no nível da região para cada assinatura. Por exemplo, sua assinatura pode ter um limite de 30 núcleos de CPU na região leste dos EUA, com outros 30 núcleos de CPU permitidos no leste dos EUA 2.

## <a name="gather-required-information"></a>Coletar informações necessárias

Se você tiver recebido um erro indicando que atingiu um limite de cota, use o processo descrito nesta seção para reunir informações importantes e enviar uma solicitação de aumento de cota.

1. Determine o tamanho, a escala e o tipo da VM do cluster desejado.
1. Verifique os limites de capacidade da cota atual da sua assinatura. Para verificar os núcleos disponíveis, execute as seguintes etapas:

    1. Entre no [portal do Azure](https://portal.azure.com/).
    1. Navegue até a página da **Visão geral** do cluster HDInsight.
    1. No menu à esquerda, selecione **Limites de cota**. A página exibe o número de núcleos em uso, o número de núcleos disponíveis e o total de núcleos.

Para solicitar um aumento de cota, siga estes passos:

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Selecione **Ajuda + suporte** no canto inferior esquerdo da página.

    ![botão de ajuda e suporte](./media/quota-increase-request/help-support-button.png)

1. Selecione **Nova solicitação de suporte**.
1. Na página **Nova solicitação de suporte**, na guia **Noções básicas**, selecione as opções a seguir:

   - **Tipo de problema**: **Limites de serviço e assinatura (cotas)**
   - **Assinatura**: a assinatura que você quer modificar
   - **Tipo de cota**: **HDInsight**

     ![Crie uma solicitação de suporte para aumentar a cota de núcleos do HDInsight](./media/quota-increase-request/hdinsight-quota-support-request.png)

1. Selecione **Avançar: Soluções >>** .
1. Na página **Detalhes**, insira uma descrição do problema, selecione a gravidade do problema, o método de contato preferencial e outros campos obrigatórios. Use o modelo listado abaixo para garantir que você forneça as informações necessárias. As solicitações de aumento de cota são avaliadas pela equipe de capacidade do Azure, e não pela equipe de produto do HDInsight. Quanto mais completas forem as informações fornecidas, maior será a probabilidade de sua solicitação ser aprovada.

    ```
    I would like to request [SPECIFY DESIRED AMOUNT] on [DESIRED SKU] for [SUBSCRIPTION ID].
    
    My current quota on this subscription is [CURRENT QUOTA AMOUNT].
    
    I would like to use the extra cores for [DETAIL REASON].
    ```

    ![detalhes do problema](./media/quota-increase-request/problem-details.png)

1. Selecione **Avançar: Examinar + Criar >>** .
1. Na guia **Revisar + criar**, selecione **Criar**.

> [!NOTE]  
> Se precisar aumentar a cota de núcleos do HDInsight em uma região privada, [envie uma solicitação de lista de permissões](https://aka.ms/canaryintwhitelist).

Você pode [entrar em contato com o suporte para solicitar um aumento de cota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

Há alguns limites de cota fixos. Por exemplo, uma única assinatura do Azure pode ter no máximo 10 mil núcleos. Para detalhes sobre esses limites, confira [Assinatura do Azure e limites, cotas e restrições de serviços](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

## <a name="next-steps"></a>Próximas etapas

* [Configurar clusters no HDInsight com Apache Hadoop, Spark, Kafka e mais](hdinsight-hadoop-provision-linux-clusters.md): Saiba como instalar e configurar clusters no HDInsight.
* [Monitorar o desempenho do cluster](hdinsight-key-scenarios-to-monitor.md): saiba mais sobre os principais cenários a serem monitorados em seu cluster HDInsight e que podem afetar a capacidade do seu cluster.