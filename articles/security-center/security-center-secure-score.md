---
title: Pontuação segura no Azure Security Center | Microsoft Docs
description: " Priorize suas recomendações de segurança usando o Secure Score no Azure Security Center. "
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415761"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Melhorar sua pontuação segura na Central de segurança do Azure

> [!NOTE]
> Este artigo é sobre a versão anterior da pontuação segura. Esta experiência de pontuação segura ainda está disponível na ui, mas será eliminada gradualmente ao longo do tempo. As duas experiências de pontuação segura estão correndo lado a lado para permitir uma transição mais suave.
>
> Para obter detalhes sobre a pontuação mais nova e segura, consulte [aqui](secure-score-security-controls.md).
>

Com tantos serviços oferecendo os benefícios de segurança, muitas vezes é difícil saber quais etapas seguir primeiro para proteger e otimizar sua carga de trabalho. O Secure Score revisa suas recomendações de segurança e as prioriza para você, para que você saiba quais recomendações executar primeiro. Isso ajuda a encontrar as vulnerabilidades de segurança mais sérias para que você possa priorizar a investigação. Secure Score é uma ferramenta que ajuda você a avaliar sua postura de segurança de carga de trabalho.

## <a name="secure-score-calculation"></a>Cálculo de Pontuação Segura

A Central de Segurança reproduz o trabalho de um analista de segurança, revisa suas recomendações de segurança e aplica algoritmos avançados para determinar a importância de cada recomendação.
O Azure Security center constantemente revisa suas recomendações ativas e calcula seu Secure Score com base neles, a pontuação de uma recomendação é derivada de sua gravidade e práticas recomendadas de segurança que afetarão mais a segurança da sua carga de trabalho.

O Security Center também fornece uma **pontuação geral segura**. 

**O Score Geral seguro** é um acúmulo de todas as suas pontuações de recomendação. Você pode visualizar seu Score seguro geral em suas assinaturas ou grupos de gerenciamento, dependendo do que você selecionar. A pontuação varia de acordo com a assinatura selecionada e as recomendações ativas nessas assinaturas.

Para verificar quais recomendações impactam mais o seu Secure Score, você pode visualizar as três recomendações mais impactantes no painel do Security Center ou pode classificar as recomendações na lâmina da lista de recomendações usando a coluna **de impacto Secure Score.**

Para ver sua pontuação geral de segurança:

1. No painel do Azure, clique **em Security Center** e clique em Pontuação **segura**.

2. No topo você pode ver os destaques do Secure Score:
   - A **pontuação geral segura** representa a pontuação por política, por assinatura selecionada
   - **Secure Score por categoria** mostra quais recursos precisam de mais atenção
   - **As principais recomendações do secure score de impacto** fornecem uma lista das recomendações que melhorarão mais o seu Secure Score se você implementá-las.
 
   ![Pontuação segura](./media/security-center-secure-score/secure-score-dashboard.png)

3. Na tabela abaixo você pode ver cada uma de suas assinaturas e a pontuação geral do Secure Score para cada um.

   > [!NOTE]
   > A soma da Pontuação Segura de cada assinatura não é igual à pontuação geral do Secure Score. O Secure Score é um cálculo baseado na razão entre seus recursos saudáveis e seus recursos totais por recomendação, não uma soma de Pontuações Seguras em suas assinaturas. 
   >
4. Clique **em Exibir recomendações** para ver as recomendações para essa assinatura que você pode corrigir para melhorar seu Secure Score.
4. Na lista de recomendações, você pode ver que para cada recomendação há uma coluna que representa o **impacto secure score**. Este número representa o quanto o seu Score seguro geral irá melhorar se você seguir as recomendações. Por exemplo, na tela abaixo, se você **remediar vulnerabilidades nas configurações de segurança de contêineres,** o Secure Score aumentará em 35 pontos.

   ![Pontuação segura](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Pontuação segura individual

Além disso, para visualizar pontuações seguras individuais, você pode encontrá-las dentro da lâmina de recomendação individual.  

O **Índice de Recomendação Seguro** é um cálculo baseado na razão entre seus recursos saudáveis e seus recursos totais. Se o número de recursos saudáveis for igual ao número total de recursos, você terá a pontuação máxima da recomendação de 50. Para tentar obter seu Secure Score mais perto da pontuação máxima, corrija os recursos insalubres seguindo as recomendações.

O **impacto da recomendação** permite que você saiba o quanto seu Secure Score melhora se você aplicar as etapas de recomendação. Por exemplo, se o seu Secure Score for 42 e o impacto da **Recomendação** for +3, realizar as etapas descritas na recomendação melhore sua pontuação para se tornar 45.

A recomendação mostra quais ameaças sua carga de trabalho está exposta se as etapas de correção não forem executadas.

![pontuação de recomendação individual seguro](./media/security-center-secure-score/indiv-recommendation-secure-score.png)


## <a name="next-steps"></a>Próximas etapas
Este artigo mostrou como melhorar sua postura de segurança usando **o Secure Score** no Azure Security Center. Para saber mais sobre a Central de Segurança, confira:

* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre perguntas frequentes sobre como usar o serviço.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md)– saiba como monitorar a integridade dos recursos do Azure.
