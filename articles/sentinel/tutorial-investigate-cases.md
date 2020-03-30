---
title: Investigue incidentes com o Azure Sentinel| Microsoft Docs
description: Use este tutorial para aprender como investigar incidentes com o Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: ecd8c508d05bfeb541a6cb5efbcdf2fffd3c78d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587185"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Tutorial: Investigue incidentes com o Azure Sentinel

> [!IMPORTANT]
> O gráfico de investigação está atualmente em visualização pública.
> Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Este tutorial ajuda você a investigar incidentes com o Azure Sentinel. Depois de conectar suas fontes de dados ao Azure Sentinel, você deseja ser notificado quando algo suspeito acontecer. Para permitir que você faça isso, o Azure Sentinel permite criar regras avançadas de alerta, que geram incidentes que você pode atribuir e investigar.

Este artigo cobre:
> [!div class="checklist"]
> * Investigar incidentes
> * Use o gráfico de investigação
> * Responda às ameaças

Um incidente pode incluir vários alertas. É uma agregação de todas as evidências relevantes para uma investigação específica. Um incidente é criado com base em regras analíticas que você criou na página **Analytics.** As propriedades relacionadas aos alertas, como gravidade e status, são definidas no nível do incidente. Depois de deixar o Azure Sentinel saber que tipo de ameaças você está procurando e como encontrá-las, você pode monitorar ameaças detectadas investigando incidentes.

## <a name="prerequisites"></a>Pré-requisitos
Você só poderá investigar o incidente se você usou os campos de mapeamento da entidade quando você configurar sua regra analítica. O gráfico de investigação exige que seu incidente original inclua entidades.

## <a name="how-to-investigate-incidents"></a>Como investigar incidentes

1. Selecione **Incidentes**. A página **Incidentes** permite que você saiba quantos incidentes você tem, quantos estão abertos, quantos você definiu para **Em andamento**e quantos estão fechados. Para cada incidente, você pode ver a hora que ocorreu, e o estado do incidente. Olhe para a gravidade para decidir quais incidentes lidar primeiro.

    ![Ver gravidade do incidente](media/tutorial-investigate-cases/incident-severity.png)

1. Você pode filtrar os incidentes conforme necessário, por exemplo, por status ou gravidade.

1. Para começar uma investigação, selecione um incidente específico. À direita, você pode ver informações detalhadas para o incidente, incluindo sua gravidade, resumo do número de entidades envolvidas, os eventos brutos que desencadearam este incidente, e a identificação única do incidente.

1. Para ver mais detalhes sobre os alertas e entidades no incidente, **selecione Exibir detalhes completos** na página do incidente e revise as guias relevantes que resumem as informações do incidente. Na guia **Alertas,** revise o alerta em si. Você pode ver todas as informações relevantes sobre o alerta – a consulta que acionou o alerta, o número de resultados retornados por consulta e a capacidade de executar playbooks nos alertas. Para aprofundar ainda mais o incidente, selecione o número de **Eventos**. Isso abre a consulta que gerou os resultados e os eventos que acionaram o alerta no Log Analytics. Na guia **Entidades,** você pode ver todas as entidades que mapeou como parte da definição da regra de alerta.

    ![Exibir detalhes do alerta](media/tutorial-investigate-cases/alert-details.png)

1. Se você está investigando ativamente um incidente, é uma boa idéia definir o status do incidente **para em andamento** até que você feche.

1. Incidentes podem ser atribuídos a um usuário específico. Para cada incidente, você pode atribuir um proprietário, definindo o campo **do proprietário do Incidente.** Todos os incidentes começam como não atribuídos. Você também pode adicionar comentários para que outros analistas sejam capazes de entender o que você investigou e quais são suas preocupações em torno do incidente.

    ![Atribuir incidente ao usuário](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Selecione **Investigar** para visualizar o mapa de investigação.

## <a name="use-the-investigation-graph-to-deep-dive"></a>Use o gráfico de investigação para mergulhar profundamente

O gráfico de investigação permite que os analistas façam as perguntas certas para cada investigação. O gráfico de investigação ajuda você a entender o escopo e identificar a causa raiz de uma potencial ameaça à segurança, correlacionando dados relevantes com qualquer entidade envolvida. Você pode mergulhar mais fundo e investigar qualquer entidade apresentada no gráfico selecionando-o e escolhendo entre diferentes opções de expansão.  
  
O gráfico de investigação fornece:

- **Contexto visual a partir de dados brutos**: O gráfico visual ao vivo exibe relações de entidades extraídas automaticamente dos dados brutos. Isso permite que você veja facilmente conexões entre diferentes fontes de dados.

- **Descoberta completa do escopo da investigação**: Expanda seu escopo de investigação usando consultas de exploração incorporadas para superficialmente o escopo completo de uma violação.

- **Etapas de investigação incorporadas**: Use opções de exploração predefinidas para garantir que você esteja fazendo as perguntas certas em face de uma ameaça.

Para usar o gráfico de investigação:

1. Selecione um incidente e selecione **Investigar**. Isso te leva ao gráfico da investigação. O gráfico fornece um mapa ilustrativo das entidades diretamente ligadas ao alerta e a cada recurso conectado posteriormente.

   > [!IMPORTANT] 
   > Você só poderá investigar o incidente se você usou os campos de mapeamento da entidade quando você configurar sua regra analítica. O gráfico de investigação exige que seu incidente original inclua entidades.

   ![Exibir mapa](media/tutorial-investigate-cases/map1.png)

1. Selecione uma entidade para abrir o painel **Entidades** para que você possa revisar informações sobre essa entidade.

    ![Ver entidades no mapa](media/tutorial-investigate-cases/map-entities.png)
  
1. Expanda sua investigação pairando sobre cada entidade para revelar uma lista de perguntas que foi projetada por nossos especialistas em segurança e analistas por tipo de entidade para aprofundar sua investigação. Chamamos essas opções **de consultas de exploração.**

    ![Explorar mais detalhes](media/tutorial-investigate-cases/exploration-cases.png)

   Por exemplo, em um computador você pode solicitar alertas relacionados. Se você selecionar uma consulta de exploração, os direitos resultantes serão adicionados de volta ao gráfico. Neste exemplo, a seleção **de alertas relacionados** retornou os seguintes alertas no gráfico:

    ![Exibir alertas relacionados](media/tutorial-investigate-cases/related-alerts.png)

1. Para cada consulta de exploração, você pode selecionar a opção de abrir os resultados do evento bruto e a consulta usada no Log Analytics, selecionando **Eventos\>**.

1. Para entender o incidente, o gráfico lhe dá uma linha do tempo paralela.

    ![Ver cronograma no mapa](media/tutorial-investigate-cases/map-timeline.png)

1. Pairar sobre a linha do tempo para ver quais coisas no gráfico ocorreram em que ponto no tempo.

    ![Use a linha do tempo no mapa para investigar alertas](media/tutorial-investigate-cases/use-timeline.png)



## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como começar a investigar incidentes usando o Azure Sentinel. Continue com o tutorial [de como responder a ameaças usando cartilhas automatizadas](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Responda a ameaças](tutorial-respond-threats-playbook.md) para automatizar suas respostas a ameaças.

