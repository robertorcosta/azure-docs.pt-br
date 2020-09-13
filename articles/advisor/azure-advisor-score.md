---
title: Otimizar cargas de trabalho do Azure usando a Pontuação do supervisor
description: Use a Pontuação do supervisor para aproveitar ao máximo o Azure
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: c9e6a13b39ac3436a71b6cecee4c91c609589378
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90038959"
---
# <a name="advisor-score-to-get-the-most-out-of-azure"></a>Pontuação do supervisor para obter o máximo do Azure

## <a name="introduction-to-advisor-score"></a>Introdução à Pontuação do supervisor

O Azure Advisor fornece recomendações de práticas recomendadas para suas cargas de trabalho. Essas recomendações são personalizadas e acionáveis para ajudá-lo a:
* Melhorar a postura de suas cargas de trabalho e otimizar suas implantações do Azure
* Impeça proativamente os principais problemas seguindo as práticas recomendadas
* Avalie suas cargas de trabalho do Azure em relação aos cinco pilares da [estrutura de Microsoft Azure bem projetada](https://docs.microsoft.com/azure/architecture/framework/)

Como um recurso fundamental do Advisor, a **Pontuação do consultor** é criada para ajudá-lo a atingir essas metas com eficiência e eficiência. 

Para tirar o máximo proveito do Azure, é crucial entender onde você está em sua jornada de otimização da carga de trabalho, quais serviços/recursos são consumidos bem e quais não são. Além disso, você desejará saber como priorizar suas ações, com base nas recomendações, para maximizar o resultado. Também é importante acompanhar e relatar o progresso que você está fazendo nesta jornada de otimização. Com a **Pontuação do supervisor**, você pode facilmente fazer todas essas coisas com nossa nova experiência de Gamificação. Como seu consultor de nuvem personalizado, o Azure Advisor avalia continuamente a telemetria de uso e a configuração de recursos para verificar as práticas recomendadas do setor. O Advisor, em seguida, agrega suas descobertas em uma única Pontuação para que você possa perceber, rapidamente, se você está realizando as etapas necessárias para criar soluções confiáveis, seguras e econômicas. A Pontuação do supervisor consiste em uma pontuação geral, que pode ser dividida em cinco pontuações de categoria, uma para cada categoria do Azure Advisor que representa os cinco pilares da estrutura bem arquitetada. Você pode acompanhar o progresso feito ao longo do tempo exibindo a pontuação geral e a pontuação da categoria com a tendência diária, semanal e mensal, e pode definir benchmarks para ajudá-lo a atingir suas metas. 

 ![Experiência de Pontuação do supervisor](./media/advisor-score-1.png)

## <a name="how-to-consume-advisor-score"></a>Como consumir a Pontuação do supervisor
O Advisor exibe a pontuação geral do consultor e a divisão para categorias do supervisor, em percentuais. Uma pontuação de 100% em qualquer categoria significa que todos os seus recursos avaliados pelo Advisor seguem as práticas recomendadas que o Advisor recomenda. Na outra extremidade do espectro, uma pontuação de 0% significa que nenhum dos seus recursos avaliados pelo Advisor seguem as recomendações do Advisor. Usando esses detalhamentos de pontuação, você pode facilmente alcançar o fluxo abaixo:
* A **Pontuação do supervisor** para ajudá-lo a fazer uma linha de base de como sua carga de trabalho/assinaturas está fazendo com base na pontuação do supervisor. Você também pode ver as tendências históricas para entender o que é sua tendência.
* **Pontuações de categoria do Advisor** para ajudá-lo a entender quais categorias precisam de mais atenção e ajuda com a priorização
* **Possível aumento da Pontuação** de cada recomendação para ajudá-lo a priorizar suas ações de correção para cada categoria

A contribuição de cada recomendação para sua pontuação é mostrada claramente na página Visão geral do portal do Azure. Você pode aumentar sua pontuação adotando as práticas recomendadas, e você pode priorizar as recomendações que têm maior **Pontuação potencial aumentar** para fazer o progresso mais rápido com o tempo que você tem.  

![Impacto da Pontuação do supervisor](./media/advisor-score-2.png)

Como a metodologia de Pontuação do Advisor aplica peso extra a recursos mais caros com recomendações demoradas, você pode aumentar o progresso corrigindo os recursos com o maior custo de varejo primeiro. Caso qualquer recomendação do Advisor não seja relevante para um recurso individual, você pode ignorar essas recomendações para excluí-las do cálculo de Pontuação e enviar comentários para o Advisor para melhorar suas recomendações. 

## <a name="how-is-advisor-score-calculated"></a>Como a Pontuação do supervisor é calculada?
O Advisor exibe as pontuações de categoria e sua pontuação geral do consultor como porcentagens. Uma pontuação de 100% em qualquer categoria significa todos os seus recursos, *avaliados pelo Advisor*, siga as práticas recomendadas que o Advisor recomenda. Na outra extremidade do espectro, uma pontuação de 0% significa que nenhum dos seus recursos, avaliados pelo Advisor, seguem as recomendações do Advisor. 
**Cada uma das cinco categorias tem uma pontuação potencial mais alta de 100.** A pontuação geral do consultor é calculada como uma soma de cada Pontuação de categoria aplicável, dividida pela soma da Pontuação potencial mais alta de todas as categorias aplicáveis. Para a maioria das assinaturas, isso significa que o Advisor soma a pontuação de cada categoria e divide por 500. No entanto, **cada Pontuação de categoria é calculada somente se você usar os recursos que são avaliados pelo Advisor.**

### <a name="scoring-methodology"></a>Metodologia de Pontuação: 
O cálculo da Pontuação do supervisor pode ser resumido em quatro etapas:
1. O Advisor calcula o **custo diário de varejo dos recursos afetados**, que são os recursos em suas assinaturas que têm pelo menos uma recomendação no Advisor.
2. O Advisor calcula o **custo diário de varejo dos recursos avaliados**, que são os recursos monitorados pelo Advisor, independentemente de terem alguma recomendação ou não. 
3. Para cada tipo de recomendação, o Advisor calcula a **taxa de recursos íntegros**, que é o custo dos recursos afetados divididos pelo custo dos recursos avaliados.
4. O Advisor aplica três pesos adicionais à taxa de recursos íntegros em cada categoria:
* As recomendações com maior impacto são ponderadas mais pesadas do que aquelas com menor impacto.
* Os recursos com recomendações duradouras conterão mais em relação à sua pontuação.
* Os recursos que você ignorar no Advisor serão removidos inteiramente do seu cálculo de pontuação. 
    
O Advisor aplica esse modelo a um nível de categoria do Advisor (a segurança usa o modelo de [Pontuação segura](https://docs.microsoft.com/azure/security-center/secure-score-security-controls#introduction-to-secure-score) ), fornecendo a Pontuação do consultor para cada categoria e uma média simples produz a pontuação final do consultor.


## <a name="advisor-score-faq"></a>Perguntas frequentes sobre a Pontuação do supervisor
* **Com que frequência a minha pontuação é atualizada?**
Sua pontuação é atualizada pelo menos uma vez por dia. 
* **É necessário exibir as recomendações no Advisor para obter um ponto para a minha Pontuação?**
Não. Sua pontuação reflete se você adotar práticas recomendadas que o Advisor recomenda, mesmo se você nunca exibir essas recomendações no Advisor e adotar práticas recomendadas proativamente.  
* **Como o Advisor calcula o custo diário de varejo dos recursos em uma assinatura?**
O Advisor usa as tarifas de *pagamento conforme* o uso publicadas na página de preços do Azure.com, que não reflete nenhum desconto aplicável, multiplicado pela quantidade de utilização no último dia em que o recurso foi alocado. Omitir os descontos do cálculo do custo do recurso torna a Pontuação do supervisor comparável entre assinaturas, locatários e registros onde os descontos podem variar. 
* **E se uma recomendação não for relevante?**
Se você ignorar uma recomendação do Advisor, ele será omitido do cálculo da sua pontuação. Ignorar recomendações também ajudará o Advisor a melhorar a qualidade das recomendações.
* **Por que minha pontuação foi alterada?** A classificação pode ser alterada se você corrigir recursos impactados adotando as práticas recomendadas que o Advisor recomenda. Se você ou qualquer pessoa com permissões em sua assinatura tiver modificado ou criado novos recursos, você também poderá ver flutuações na sua pontuação, pois sua pontuação é baseada em uma taxa de recursos impactados pelo custo em relação ao custo total de todos os recursos.
* **Minha pontuação depende de quanto eu gasto no Azure?**
Não. A pontuação foi projetada para controlar o tamanho de uma combinação de assinatura e de serviço. 
* **A metodologia de Pontuação diferencia as cargas de trabalho de produção e desenvolvimento/teste?**
Não, não por enquanto, mas você pode ignorar recomendações em recursos individuais se esses recursos forem usados para desenvolvimento e teste e a recomendação não se aplicar.
* **Posso comparar pontuações entre uma assinatura com recursos 100 e uma assinatura com recursos de 100.000?**
A metodologia de pontuação foi projetada para controlar o número de recursos em uma combinação de assinatura e de serviço, portanto, as assinaturas com menos recursos podem ter pontuações maiores ou menores do que as assinaturas com mais recursos. 

## <a name="how-to-access-advisor"></a>Como acessar o Advisor
A Pontuação do supervisor está em visualização pública no portal do Azure. Você precisa ir para a seção do assistente e encontrar a Pontuação do supervisor como o segundo item de menu na barra de navegação à esquerda. 

![Ponto de entrada de Pontuação do supervisor](./media/advisor-score-3.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as recomendações do Assistente, consulte:
* [Introdução ao Advisor](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do Advisor](advisor-operational-excellence-recommendations.md)
