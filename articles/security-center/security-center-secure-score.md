---
title: Pontuação segura na central de segurança do Azure | Microsoft Docs
description: " Priorize suas recomendações de segurança usando a pontuação segura na central de segurança do Azure. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/15/2019
ms.author: memildin
ms.openlocfilehash: 30405ce5cc875144fcd1cf83d4a3f883a0304989
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79415761"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Melhorar sua pontuação segura na Central de segurança do Azure

> [!NOTE]
> Este artigo é sobre a versão anterior da Pontuação segura. Essa experiência de Pontuação segura ainda está disponível na interface do usuário, mas será dividida em fases ao longo do tempo. As duas experiências de Pontuação segura estão sendo executadas lado a lado para habilitar uma transição mais suave.
>
> Para obter detalhes sobre a pontuação segura mais recente, consulte [aqui](secure-score-security-controls.md).
>

Com tantos serviços oferecendo os benefícios de segurança, muitas vezes é difícil saber quais etapas seguir primeiro para proteger e otimizar sua carga de trabalho. A pontuação segura revisa suas recomendações de segurança e as prioriza para você, de modo que você saiba quais recomendações executar primeiro. Isso ajuda a encontrar as vulnerabilidades de segurança mais sérias para que você possa priorizar a investigação. A pontuação segura é uma ferramenta que ajuda a avaliar sua postura de segurança de carga de trabalho.

## <a name="secure-score-calculation"></a>Cálculo de Pontuação segura

A Central de Segurança reproduz o trabalho de um analista de segurança, revisa suas recomendações de segurança e aplica algoritmos avançados para determinar a importância de cada recomendação.
A central de segurança do Azure revisa constantemente suas recomendações ativas e calcula sua pontuação segura com base nelas, a pontuação de uma recomendação é derivada de suas práticas recomendadas de gravidade e segurança que afetarão a segurança da sua carga de trabalho.

A central de segurança também fornece uma **Pontuação segura geral**. 

A **Pontuação de segurança geral** é um acúmulo de todas as suas pontuações de recomendação. Você pode exibir sua pontuação de segurança geral em suas assinaturas ou grupos de gerenciamento, dependendo do que você selecionar. A pontuação varia de acordo com a assinatura selecionada e as recomendações ativas nessas assinaturas.

Para verificar quais recomendações afetam a sua pontuação segura, você pode exibir as três principais recomendações mais impactantes no painel da central de segurança ou pode classificar as recomendações na folha da lista de recomendações usando a coluna de **impacto de Pontuação segura** .

Para exibir sua pontuação de segurança geral:

1. No painel do Azure, clique em **central de segurança** e em **classificação segura**.

2. Na parte superior, você pode ver os destaques de Pontuação segura:
   - A **Pontuação de segurança geral** representa a pontuação por política, por assinatura selecionada
   - A **classificação segura por categoria** mostra quais recursos precisam mais de atenção
   - As **principais recomendações por impacto de Pontuação segura** fornecem uma lista das recomendações que melhorarão sua pontuação segura mais se você implementá-las.
 
   ![Pontuação segura](./media/security-center-secure-score/secure-score-dashboard.png)

3. Na tabela abaixo, você pode ver cada uma de suas assinaturas e a pontuação de segurança geral de cada uma.

   > [!NOTE]
   > A soma da Pontuação segura de cada assinatura não é igual à pontuação de segurança geral. A pontuação segura é um cálculo com base na proporção entre seus recursos íntegros e o total de recursos por recomendação, não uma soma de pontuações seguras em suas assinaturas. 
   >
4. Clique em **Exibir recomendações** para ver as recomendações para a assinatura que você pode corrigir para melhorar sua pontuação segura.
4. Na lista de recomendações, você pode ver que, para cada recomendação, há uma coluna que representa o **impacto de Pontuação segura**. Esse número representa o quanto sua pontuação de segurança geral será aprimorada se você seguir as recomendações. Por exemplo, na tela abaixo, se você **corrigir vulnerabilidades em configurações de segurança de contêiner**, sua pontuação segura aumentará em 35 pontos.

   ![Pontuação segura](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Pontuação segura individual

Além disso, para exibir pontuações seguras individuais, você pode encontrá-las na folha de recomendação individual.  

A **Pontuação segura de recomendação** é um cálculo com base na proporção entre seus recursos íntegros e o total de recursos. Se o número de recursos íntegros for igual ao número total de recursos, você obterá a pontuação de segurança máxima da recomendação de 50. Para tentar obter sua pontuação segura mais próximo da pontuação máxima, corrija os recursos não íntegros seguindo as recomendações.

O **impacto da recomendação** permite que você saiba quanto sua pontuação segura melhora se você aplicar as etapas de recomendação. Por exemplo, se sua pontuação segura for 42 e o **impacto de recomendação** for + 3, executar as etapas descritas na recomendação melhorará sua pontuação para se tornar 45.

A recomendação mostra quais ameaças sua carga de trabalho está exposta se as etapas de correção não forem executadas.

![pontuação de recomendação individual seguro](./media/security-center-secure-score/indiv-recommendation-secure-score.png)


## <a name="next-steps"></a>Próximas etapas
Este artigo mostrou como melhorar sua postura de segurança usando a **Pontuação segura** na central de segurança do Azure. Para saber mais sobre a Central de Segurança, confira:

* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre perguntas frequentes sobre como usar o serviço.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md)– saiba como monitorar a integridade dos recursos do Azure.
