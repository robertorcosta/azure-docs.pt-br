---
title: Otimizar cargas de trabalho do Azure usando a Pontuação do supervisor
description: Use a Pontuação do supervisor para aproveitar ao máximo o Azure
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 29d8480f501a78c1668b52034f439f998419f9d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335612"
---
# <a name="optimize-azure-workloads-using-advisor-score"></a>Otimizar cargas de trabalho do Azure usando a Pontuação do supervisor

## <a name="introduction-to-advisor-score"></a>Introdução à Pontuação do supervisor

O Azure Advisor fornece recomendações de práticas recomendadas para suas cargas de trabalho. Essas recomendações são personalizadas e acionáveis para ajudá-lo a:
* Melhorar a postura de suas cargas de trabalho e otimizar suas implantações do Azure
* Impeça proativamente os principais problemas seguindo as práticas recomendadas
* Avalie suas cargas de trabalho do Azure em relação aos cinco pilares do [Microsoft Azure Well-Architected Framework](https://docs.microsoft.com/azure/architecture/framework/)

Como um recurso fundamental do Advisor, a **Pontuação do consultor** é criada para ajudá-lo a atingir essas metas com eficiência e eficiência. 

Para tirar o máximo proveito do Azure, é crucial entender onde você está em sua jornada de otimização da carga de trabalho, quais serviços/recursos são consumidos bem e quais não são. Além disso, você desejará saber como priorizar suas ações, com base nas recomendações, para maximizar o resultado. Também é importante acompanhar e relatar o progresso que você está fazendo nesta jornada de otimização. Com a **Pontuação do supervisor**, você pode facilmente fazer todas essas coisas com nossa nova experiência de Gamificação. Como seu consultor de nuvem personalizado, o Azure Advisor avalia continuamente a telemetria de uso e a configuração de recursos para verificar as práticas recomendadas do setor. O Advisor, em seguida, agrega suas descobertas em uma única Pontuação para que você possa perceber, rapidamente, se você está realizando as etapas necessárias para criar soluções confiáveis, seguras e econômicas. A Pontuação do supervisor consiste em uma pontuação geral, que pode ser dividida em cinco pontuações de categoria, uma para cada categoria do supervisor do Azure que representa os cinco pilares da estrutura de Well-Architected. Você pode acompanhar o progresso feito ao longo do tempo exibindo a pontuação geral e a pontuação da categoria com a tendência diária, semanal e mensal, e pode definir benchmarks para ajudá-lo a atingir suas metas. 

 ![Experiência de Pontuação do supervisor](./media/advisor-score-1.png)

## <a name="how-to-consume-advisor-score"></a>Como consumir a Pontuação do supervisor
O Advisor exibe a pontuação geral do consultor e a divisão para categorias do supervisor, em percentuais. Uma pontuação de 100% em qualquer categoria significa que todos os seus recursos avaliados pelo Advisor seguem as práticas recomendadas que o Advisor recomenda. Na outra extremidade do espectro, uma pontuação de 0% significa que nenhum dos seus recursos avaliados pelo Advisor seguem as recomendações do Advisor. Usando esses detalhamentos de pontuação, você pode facilmente alcançar o fluxo abaixo:
* A **Pontuação do supervisor** para ajudá-lo a fazer uma linha de base de como sua carga de trabalho/assinaturas está fazendo com base na pontuação do supervisor. Você também pode ver as tendências históricas para entender o que é sua tendência.
* As **pontuações de categoria do Advisor** de cada recomendação informarão quais recomendações pendentes melhorarão a sua pontuação mais. Esses valores refletem o peso da recomendação e nossa facilidade prevista de implementação para garantir que você possa obter o máximo de valor com seu tempo e ajuda com a priorização
* **Impacto da Pontuação de categoria** de cada recomendação para ajudá-lo a priorizar suas ações de correção para cada categoria

A contribuição de cada recomendação para sua pontuação de categoria é mostrada claramente na página de Pontuação do supervisor na portal do Azure. Você pode aumentar cada Pontuação de categoria pelo ponto de porcentagem listado na coluna impacto da pontuação da categoria. Esse valor reflete o peso da recomendação dentro da categoria e a facilidade de implementação prevista para resolver os frutas potencialmente de baixa interrupções. Concentrar-se nas recomendações com o maior impacto de Pontuação ajudará você a aumentar o progresso com o tempo.  

![Impacto da Pontuação do supervisor](./media/advisor-score-2.png)

Caso qualquer uma das recomendações do Advisor não seja relevante para um recurso individual, você pode adiar ou ignorar essas recomendações e elas serão excluídas do cálculo de pontuação da próxima atualização. O Advisor também usará essa entrada como comentários adicionais para melhorar o modelo.

## <a name="how-is-advisor-score-calculated"></a>Como a Pontuação do supervisor é calculada?
O Advisor exibe as pontuações de categoria e sua pontuação geral do consultor como porcentagens. Uma pontuação de 100% em qualquer categoria significa todos os seus recursos, *avaliados pelo Advisor*, siga as práticas recomendadas que o Advisor recomenda. Na outra extremidade do espectro, uma pontuação de 0% significa que nenhum dos seus recursos, avaliados pelo Advisor, seguem as recomendações do Advisor. 
**Cada uma das cinco categorias tem uma pontuação potencial mais alta de 100.** A pontuação geral do consultor é calculada como uma soma de cada Pontuação de categoria aplicável, dividida pela soma da Pontuação potencial mais alta de todas as categorias aplicáveis. Para a maioria das assinaturas, isso significa que o Advisor soma a pontuação de cada categoria e divide por 500. No entanto, **cada Pontuação de categoria é calculada somente se você usar os recursos que são avaliados pelo Advisor.**

**Exemplo de cálculo de Pontuação do Advisor**
* Pontuação de assinatura única: essa é a média simples de todas as pontuações de categoria do Advisor para sua assinatura. Se as pontuações de categoria do supervisor forem de custo = 73, Reliabilit = 85, excelência operacional = 77, desempenho = 100; a **Pontuação do supervisor** seria (73 + 85 + 77 + 100)/(4x100) = 0,84 ou 84%.
* Pontuação de várias assinaturas: quando várias assinaturas são selecionadas, a pontuação geral do consultor que geramos são pontuações de categoria agregadas ponderadas. Aqui, cada Pontuação de categoria do supervisor é agregada com base nos recursos consumidos pelas assinaturas. Assim que tivermos as pontuações de categoria agregadas ponderadas, faremos uma média simples de fornecer uma pontuação geral para assinaturas. 


### <a name="scoring-methodology"></a>Metodologia de Pontuação: 
O cálculo da Pontuação do supervisor pode ser resumido em quatro etapas:
1. O Advisor calcula o **custo de varejo dos recursos afetados**, que são os recursos em suas assinaturas que têm pelo menos uma recomendação no Advisor.
2. O Advisor calcula o **custo de varejo dos recursos avaliados**, que são os recursos monitorados pelo Advisor, independentemente de terem ou não recomendações. 
3. Para cada tipo de recomendação, o Advisor calcula a **taxa de recursos íntegros**, que é o custo de varejo dos recursos afetados divididos pelo custo de varejo dos recursos avaliados.
4. O Advisor aplica três pesos adicionais à taxa de recursos íntegros em cada categoria:
  * As recomendações com maior impacto são ponderadas mais pesadas do que aquelas com menor impacto.
  * Os recursos com recomendações duradouras conterão mais em relação à sua pontuação.
  * Os recursos que você adiar ou ignorar no Advisor serão removidos inteiramente do seu cálculo de pontuação. 
    
O Advisor aplica esse modelo a um nível de categoria do Advisor (a segurança usa o modelo de [Pontuação segura](https://docs.microsoft.com/azure/security-center/secure-score-security-controls#introduction-to-secure-score) ), fornecendo a Pontuação do consultor para cada categoria e uma média simples produz a pontuação final do consultor.


## <a name="advisor-score-faq"></a>Perguntas frequentes sobre a Pontuação do supervisor
* **Com que frequência a minha pontuação é atualizada?**
Sua pontuação é atualizada pelo menos uma vez por dia. 
* **Por que algumas recomendações têm um valor "-" vazio na coluna impacto da pontuação da categoria?** O Advisor não inclui imediatamente novas recomendações ou aquelas com alterações recentes no modelo de pontuação. Após um curto período de avaliação, normalmente algumas semanas, elas serão incluídas na pontuação. 
* **Por que o impacto da Pontuação de custo é maior para algumas recomendações, mesmo que elas tenham menor economia potencial?**
Sua pontuação de custo reflete a economia potencial de recursos subutilizados e a facilidade prevista de implementar essas recomendações. Por exemplo, o peso extra é aplicado aos recursos afetados que ficam ociosos por um tempo maior, mesmo que a economia potencial seja menor. 
* **Por que não tenho uma pontuação para uma ou mais categorias ou assinaturas?**
O Advisor gerará uma pontuação apenas para as categorias e assinaturas que têm recursos que são avaliados pelo Advisor.
* **E se uma recomendação não for relevante?**
Se você ignorar uma recomendação do Advisor, ele será omitido do cálculo da sua pontuação. Ignorar recomendações também ajudará o Advisor a melhorar a qualidade das recomendações.
* **Por que minha pontuação foi alterada?** A classificação pode ser alterada se você corrigir recursos impactados adotando as práticas recomendadas que o Advisor recomenda. Se você ou qualquer pessoa com permissões em sua assinatura tiver modificado ou criado novos recursos, você também poderá ver flutuações na sua pontuação, pois sua pontuação é baseada em uma taxa de recursos impactados pelo custo em relação ao custo total de todos os recursos.
* **Como o Advisor calcula o custo de varejo dos recursos em uma assinatura?**
O Advisor usa as tarifas de pagamento conforme o uso publicadas na página de preços do Azure.com, que não reflete nenhum desconto aplicável, multiplicado pela quantidade de utilização no último dia em que o recurso foi alocado. Omitir os descontos do cálculo do custo do recurso torna a Pontuação do supervisor comparável entre assinaturas, locatários e registros onde os descontos podem variar. 
* **É necessário exibir as recomendações no Advisor para obter pontos para minha Pontuação?** Não. Sua pontuação reflete se você adotar práticas recomendadas que o Advisor recomenda, mesmo se você adotar essas práticas recomendadas proativamente e nunca exibir suas recomendações no Advisor.
* **A metodologia de Pontuação diferencia as cargas de trabalho de produção e desenvolvimento/teste?**
Não, não por enquanto, mas você pode ignorar recomendações em recursos individuais se esses recursos forem usados para desenvolvimento e teste e a recomendação não se aplicar.
* **Posso comparar pontuações entre uma assinatura com recursos 100 e uma assinatura com recursos de 100.000?**
A metodologia de pontuação foi projetada para controlar o número de recursos em uma combinação de assinatura e de serviço, portanto, as assinaturas com menos recursos podem ter pontuações maiores ou menores do que as assinaturas com mais recursos. 
* **O que isso significa quando vejo "em breve" na coluna de impacto de Pontuação?**
Isso significa que essa é uma nova recomendação e ainda estamos trabalhando para colocá-la em nosso modelo de Pontuação do Advisor. Depois que essa nova recomendação for considerada no cálculo de pontuação, você verá o valor de impacto de Pontuação para sua recomendação.  
* **Minha pontuação depende de quanto eu gasto no Azure?**
Não, sua pontuação não é necessariamente uma reflexão de quanto você gasta, e gastos desnecessários resultarão em uma pontuação de custo menor.

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
