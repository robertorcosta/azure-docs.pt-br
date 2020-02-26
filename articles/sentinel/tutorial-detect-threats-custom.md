---
title: Crie regras analíticas personalizadas para detectar ameaças suspeitas com o Azure Sentinel | Microsoft Docs
description: Use este tutorial para aprender a criar regras analíticas personalizadas para detectar ameaças suspeitas com o Azure Sentinel.
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
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: cea7429ecea105355b0afe306bfa334e55d5d9c4
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585100"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Tutorial: criar regras analíticas personalizadas para detectar ameaças suspeitas

Depois de [conectar suas fontes de dados](quickstart-onboard.md) ao Azure Sentinel, você pode criar regras personalizadas que podem Pesquisar critérios específicos em seu ambiente e gerar incidentes quando os critérios forem correspondidos para que você possa investigar-los. Este tutorial ajuda você a criar regras personalizadas para detectar ameaças com o Azure Sentinel.

Este tutorial ajuda você a detectar ameaças com o Azure Sentinel.
> [!div class="checklist"]
> * Criar regras analíticas
> * Automatizar as respostas a ameaças

## <a name="create-custom-analytic-rules"></a>Criar regras analíticas personalizadas

Você pode criar regras analíticas personalizadas para ajudá-lo a Pesquisar os tipos de ameaças e anomalias suspeitas em seu ambiente. A regra garante que você seja notificado imediatamente, para que você possa fazer a triagem, investigar e corrigir as ameaças.

1. No portal do Azure, no Azure Sentinel, selecione **Análise**.

1. Na barra de menus superior, selecione **+ criar** e selecione **regra de consulta agendada**. Isso abre o **Assistente de regra de análise**.

    ![Criar consulta agendada](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Na guia **geral** , forneça um **nome**exclusivo e uma **Descrição**. No campo **táticas** , você pode escolher entre as categorias de ataques pelas quais classificar a regra. Defina a **severidade** do alerta conforme necessário. Quando você cria a regra, seu **status** é **habilitado** por padrão, o que significa que ela será executada imediatamente depois que você terminar de criá-la. Se você não quiser que ele seja executado imediatamente, selecione **desabilitado**e a regra será adicionada à guia **regras ativas** e você poderá habilitá-la a partir daí, quando precisar.

    ![Começar a criar uma regra analítica personalizada](media/tutorial-detect-threats-custom/general-tab.png)

1. Na guia **definir lógica de regra** , você pode escrever uma consulta diretamente no campo de **consulta de regra** ou criar a consulta em log Analytics e, em seguida, copiá-la e colá-la lá.
 
   ![Criar consulta no Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   - Consulte a área **visualização de resultados** à direita, em que o Azure Sentinel mostra o número de resultados (eventos de log) que a consulta gerará, alterando ao mesmo tempo à medida que você escreve e configura sua consulta. O grafo mostra o número de resultados no período de tempo definido, que é determinado pelas configurações na seção **agendamento de consulta** .
    - Se você vir que sua consulta dispararia muitos alertas ou muito frequentes, você poderá definir uma linha de base na seção **limite de alerta** .

      Aqui está um exemplo de consulta que alertaria quando um número anormal de recursos for criado na atividade do Azure.

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > O comprimento da consulta deve ter entre 1 e 10.000 caracteres e não pode conter "Search \*" ou "Union \*".

    1. Use a seção **mapear entidades** para vincular os parâmetros dos resultados da consulta às entidades reconhecidas do Sentinela do Azure. Essas entidades formam a base para análise adicional, incluindo o agrupamento de alertas em incidentes na guia **configurações de incidentes** .
    1. Na seção **agendamento de consulta** , defina os seguintes parâmetros:

       1. Defina **Executar consulta a cada** para controlar a frequência com que a consulta é executada-com frequência a cada 5 minutos ou com pouca frequência como uma vez por dia.

       1. Defina **dados de pesquisa do último** para determinar o período de tempo dos dados cobertos pela consulta-por exemplo, ele pode consultar os últimos 10 minutos de dados ou as últimas 6 horas de dados.

       > [!NOTE]
       > Essas duas configurações são independentes umas das outras, até um ponto. Você pode executar uma consulta em um intervalo curto, cobrindo um período de tempo maior do que o intervalo (em vigor com consultas sobrepostas), mas não é possível executar uma consulta em um intervalo que exceda o período de cobertura, caso contrário, você terá lacunas na cobertura de consulta geral.

    1. Use a seção **limite de alerta** para definir uma linha de base. Por exemplo, defina **gerar alerta quando o número de resultados da consulta** **for maior que** e insira o número 1000 se você quiser que a regra gere um alerta somente se a consulta gerar mais de 1000 resultados cada vez que for executada. Como este é um campo obrigatório, se você não quiser definir uma linha de base – ou seja, se desejar que o alerta Registre todos os eventos – Insira 0 no campo número.

    1. Na seção **supressão** , você pode desativar a configuração **parar a execução da consulta após o alerta ser gerado** **em** se, depois de receber um alerta, você deseja suspender a operação dessa regra por um período de tempo excedendo o intervalo de consulta. Se você ativar essa opção, deverá definir **parar a execução da consulta para** o tempo durante o qual a consulta deve parar de ser executada, até 24 horas.

1. Na guia **configurações de incidente** , você pode escolher se e como o Azure Sentinel transforma os alertas em incidentes acionáveis. Se essa guia for deixada sozinha, o Azure Sentinel criará um único incidente separado de cada alerta. Você pode optar por não ter nenhum incidente criado ou agrupar vários alertas em um único incidente, alterando as configurações nessa guia.

    1. Na seção **configurações de incidente** , **criar incidentes de alertas disparados por essa regra de análise** é definido por padrão como **habilitado**, o que significa que o Azure Sentinel criará um único incidente separado de cada um e de todos os alertas disparados pela regra.<br></br>Se você não quiser que essa regra resulte na criação de quaisquer incidentes (por exemplo, se essa regra for apenas para coletar informações para análise subsequente), defina isso como **desabilitado**.

    1. Na seção **agrupamento de alertas** , se você quiser que um único incidente seja gerado a partir de um grupo de alertas semelhantes ou recorrentes, defina **alertas relacionados a grupos, disparados por essa regra de análise, em incidentes** a serem **habilitados**e defina os parâmetros a seguir.

    1. **Limitar o grupo aos alertas criados dentro do período de tempo selecionado**:<br></br> Determine o intervalo de tempo no qual os alertas semelhantes ou recorrentes serão agrupados juntos. Todos os alertas correspondentes nesse período de tempo gerarão coletivamente um incidente ou um conjunto de incidentes (dependendo das configurações de agrupamento abaixo). Os alertas fora desse período de tempo gerarão um incidente separado ou um conjunto de incidentes.

    2. Os **alertas de grupo disparados por essa regra de análise em um único incidente**: escolha a base em que os alertas serão agrupados juntos:

        - **Agrupar alertas em um único incidente se todas as entidades corresponderem**: <br></br>Os alertas serão agrupados se compartilharem valores idênticos para cada uma das entidades mapeadas (definidas na guia definir lógica de regra acima). Essa é a configuração recomendada.

        - **Agrupar todos os alertas disparados por essa regra em um único incidente**: <br></br>Todos os alertas gerados por essa regra são agrupados mesmo que não compartilhem valores idênticos.

        - **Agrupar alertas em um único incidente se as entidades selecionadas corresponderem**: <br></br>Os alertas serão agrupados se compartilharem valores idênticos para algumas das entidades mapeadas (que você pode selecionar na lista suspensa). Você talvez queira usar essa configuração se, por exemplo, desejar criar incidentes separados com base nos endereços IP de origem ou de destino.

    3. **Reabrir incidentes de correspondência fechados**: se um incidente tiver sido fechado (o que significa que o problema subjacente foi resolvido) e, subsequentemente, outro alerta for gerado que teria sido agrupado nesse incidente, defina essa configuração como **habilitada** se você quiser que o incidente fechado seja reaberto e deixe como **desabilitado** se desejar que o alerta crie um novo incidente.

1. Na guia **respostas automatizadas** , selecione os guias estratégicos que você deseja executar automaticamente quando um alerta for gerado pela regra personalizada. Para obter mais informações sobre como criar e automatizar os guias estratégicos, consulte [responder a ameaças](tutorial-respond-threats-playbook.md).

1. Selecione **revisar e criar** para examinar todas as configurações da nova regra de alerta e, em seguida, selecione **criar para inicializar a regra de alerta**.
  
1. Depois que o alerta é criado, uma regra personalizada é adicionada à tabela em **regras ativas**. Nessa lista, você pode habilitar, desabilitar ou excluir cada regra.

1. Para exibir os resultados das regras de alerta que você cria, vá para a página **incidentes** , na qual você pode fazer a triagem, [investigar incidentes](tutorial-investigate-cases.md)e corrigir as ameaças.


> [!NOTE]
> Os alertas gerados no Azure Sentinel estão disponíveis por meio do [Microsoft Graph Security](https://aka.ms/securitygraphdocs). Para obter mais informações, consulte a [documentação de alertas de segurança do Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a começar a detectar ameaças usando o Azure Sentinel.

Para saber como automatizar suas respostas a ameaças, [Configure as respostas de ameaças automatizadas no Azure Sentinel](tutorial-respond-threats-playbook.md).

