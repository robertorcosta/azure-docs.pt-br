---
title: Crie regras de análise personalizadas para detectar ameaças com o Azure Sentinel | Microsoft Docs
description: Use este tutorial para aprender a criar regras de análise personalizadas para detectar ameaças à segurança com o Azure Sentinel. Tire proveito do agrupamento de eventos e do agrupamento de alertas e entenda o AUTO DESABILITAdo.
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
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: 55853cc6a3dc27df4c63e0a28ab079813040e45d
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617172"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Tutorial: criar regras de análise personalizadas para detectar ameaças

Depois de [conectar suas fontes de dados](quickstart-onboard.md)   ao Azure Sentinel, você pode criar regras personalizadas que podem Pesquisar critérios específicos em seu ambiente e gerar incidentes quando os critérios forem correspondidos para que você possa investigar-los. Este tutorial ajuda você a criar regras personalizadas para detectar ameaças com o Azure Sentinel.

Este tutorial ajuda você a detectar ameaças com o Azure Sentinel.
> [!div class="checklist"]
> * Criar regras de análise
> * Automatizar as respostas a ameaças

## <a name="create-custom-analytics-rules"></a>Criar regras de análise personalizadas

Você pode criar regras de análise personalizadas para ajudá-lo a Pesquisar os tipos de ameaças e anomalias suspeitas em seu ambiente. A regra garante que você seja notificado imediatamente, para que você possa fazer a triagem, investigar e corrigir as ameaças.

1. No portal do Azure, no Azure Sentinel, selecione **Análise**.

1. Na barra de menus superior, selecione **+ criar** e selecione **regra de consulta agendada**. Isso abre o **Assistente de regra de análise**.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query.png" alt-text="Criar consulta agendada":::

1. Na guia **geral** , forneça um **nome** exclusivo e uma **Descrição**. No campo **táticas** , você pode escolher entre as categorias de ataques pelas quais classificar a regra. Defina a **severidade** do alerta conforme necessário. Quando você cria a regra, seu **status** é **habilitado** por padrão, o que significa que ela será executada imediatamente depois que você terminar de criá-la. Se você não quiser que ele seja executado imediatamente, selecione **desabilitado**e a regra será adicionada à guia **regras ativas** e você poderá habilitá-la a partir daí, quando precisar.

    ![Começar a criar uma regra de análise personalizada](media/tutorial-detect-threats-custom/general-tab.png)

1. Na guia **definir lógica de regra** , você pode escrever uma consulta diretamente no campo de **consulta de regra** ou criar a consulta em log Analytics e, em seguida, copiá-la e colá-la lá.
 
   ![Criar consulta no Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   - Consulte a área **visualização de resultados** à direita, em que o Azure Sentinel mostra o número de resultados (eventos de log) que a consulta gerará, alterando ao mesmo tempo à medida que você escreve e configura sua consulta. O grafo mostra o número de resultados no período de tempo definido, que é determinado pelas configurações na seção **agendamento de consulta** .
    - Se você vir que sua consulta dispararia muitos alertas ou muito frequentes, você poderá definir uma linha de base na seção **limite de alerta** .

      Aqui está um exemplo de consulta que alertaria quando um número anormal de recursos for criado na atividade do Azure.

      ```kusto
      AzureActivity
      | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
      | where ActivityStatus == "Succeeded"
      | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
      ```

        > [!NOTE]
        > O comprimento da consulta deve ter entre 1 e 10.000 caracteres e não pode conter "Search \* " ou "Union \* ".

    1. Use a seção **mapear entidades** para vincular os parâmetros dos resultados da consulta às entidades reconhecidas do Sentinela do Azure. Essas entidades formam a base para análise adicional, incluindo o agrupamento de alertas em incidentes na guia **configurações de incidentes** .
  
    1. Na seção **agendamento de consulta** , defina os seguintes parâmetros:

       1. Defina **Executar consulta a cada** para controlar a frequência com que a consulta é executada-com frequência a cada 5 minutos ou com pouca frequência como uma vez por dia.

       1. Defina **dados de pesquisa do último** para determinar o período de tempo dos dados cobertos pela consulta-por exemplo, ele pode consultar os últimos 10 minutos de dados ou as últimas 6 horas de dados.

          > [!NOTE]
          > **Intervalos de consulta e período de lookback**
          > - Essas duas configurações são independentes umas das outras, até um ponto. Você pode executar uma consulta em um intervalo curto, cobrindo um período de tempo maior do que o intervalo (em vigor com consultas sobrepostas), mas não é possível executar uma consulta em um intervalo que exceda o período de cobertura, caso contrário, você terá lacunas na cobertura de consulta geral.
          >
          > **Atraso de ingestão**
          > - Para considerar a **latência** que pode ocorrer entre a geração de um evento na origem e sua ingestão na sentinela do Azure, e para garantir a cobertura completa sem a duplicação de dados, o Azure Sentinel executa regras de análise agendadas em um **atraso de cinco minutos** em relação ao horário agendado.

    1. Use a seção **limite de alerta** para definir uma linha de base. Por exemplo, defina **gerar alerta quando o número de resultados da consulta** **for maior que** e insira o número 1000 se você quiser que a regra gere um alerta somente se a consulta retornar mais de 1000 resultados cada vez que for executada. Este é um campo obrigatório, portanto, se você não quiser definir uma linha de base – ou seja, se quiser que seu alerta Registre todos os eventos – Insira 0 no campo número.
    
    1. Em **agrupamento de eventos**, escolha uma das duas maneiras de lidar com o agrupamento de **eventos** em **alertas**: 

       - **Agrupar todos os eventos em um único alerta** (a configuração padrão). A regra gera um único alerta toda vez que ele é executado, desde que a consulta retorne mais resultados do que o **limite de alerta** especificado acima. O alerta inclui um resumo de todos os eventos retornados nos resultados. 

       - **Disparar um alerta para cada evento**. A regra gera um alerta exclusivo para cada evento retornado pela consulta. Isso será útil se você quiser que os eventos sejam exibidos individualmente ou se quiser agrupá-los por determinados parâmetros-por usuário, nome do host ou outra coisa. Você pode definir esses parâmetros na consulta.
    
       Atualmente, o número de alertas que uma regra pode gerar está limitado a 20. Se estiver em uma regra específica, o **agrupamento de eventos** será definido para **disparar um alerta para cada evento**e a consulta da regra retornará mais de 20 eventos, cada um dos primeiros 19 eventos gerará um alerta exclusivo e o alerta de vigésimo resumirá todo o conjunto de eventos retornados. Em outras palavras, o alerta de vigésimo é o que teria sido gerado na opção **agrupar todos os eventos em um único alerta** .

       > [!NOTE]
       > Qual é a diferença entre **eventos** e **alertas**?
       >
       > - Um **evento** é uma descrição de uma única ocorrência. Por exemplo, uma única entrada em um arquivo de log pode ser contada como um evento. Nesse contexto, um evento se refere a um único resultado retornado por uma consulta em uma regra de análise.
       >
       > - Um **alerta** é uma coleção de eventos que, juntos, são significativos do ponto de vista da segurança. Um alerta pode conter um único evento se o evento tivesse implicações de segurança significativas – um logon administrativo de um país estrangeiro fora do horário comercial, por exemplo.
       >
       > - A propósito, o que são **incidentes**? A lógica interna do Azure Sentinel cria **incidentes** de **alertas** ou grupos de alertas. A fila de incidentes é o ponto focal da triagem de trabalho de analistas, investigação e correção.
       > 
       > O Azure Sentinel ingere eventos brutos de algumas fontes de dados e alertas já processados de outras pessoas. É importante observar com qual deles você está lidando a qualquer momento.

       > [!IMPORTANT]
       > O agrupamento de eventos está atualmente em visualização pública. Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
    
    1. Na seção **supressão** , você pode desativar a configuração **parar a execução da consulta após o alerta ser gerado** **em** se, depois de receber um alerta, você deseja suspender a operação dessa regra por um período de tempo excedendo o intervalo de consulta. Se você ativar essa opção, deverá definir **parar a execução da consulta para** o tempo durante o qual a consulta deve parar de ser executada, até 24 horas.

1. Na guia **configurações de incidente** , você pode escolher se e como o Azure Sentinel transforma os alertas em incidentes acionáveis. Se essa guia for deixada sozinha, o Azure Sentinel criará um único incidente separado de cada alerta. Você pode optar por não ter nenhum incidente criado ou agrupar vários alertas em um único incidente, alterando as configurações nessa guia.

   > [!IMPORTANT]
   > A guia Configurações de incidente está atualmente em visualização pública. Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
    
    1. Na seção **configurações de incidente** , **criar incidentes de alertas disparados por essa regra de análise** é definido por padrão como **habilitado**, o que significa que o Azure Sentinel criará um único incidente separado de cada um e de todos os alertas disparados pela regra.
       - Se você não quiser que essa regra resulte na criação de quaisquer incidentes (por exemplo, se essa regra for apenas para coletar informações para análise subsequente), defina isso como **desabilitado**.

    1. Na seção **agrupamento de alertas** , se você quiser que um único incidente seja gerado a partir de um grupo de até 150 alertas semelhantes ou recorrentes (consulte a observação), defina **alertas relacionados ao grupo, disparados por essa regra de análise, em incidentes** a serem **habilitados**e defina os parâmetros a seguir.

    - **Limitar o grupo aos alertas criados dentro do período de tempo selecionado**: Determine o intervalo de tempo no qual os alertas semelhantes ou recorrentes serão agrupados juntos. Todos os alertas correspondentes nesse período de tempo gerarão coletivamente um incidente ou um conjunto de incidentes (dependendo das configurações de agrupamento abaixo). Os alertas fora desse período de tempo gerarão um incidente separado ou um conjunto de incidentes.

    - Os **alertas de grupo disparados por essa regra de análise em um único incidente**: escolha a base em que os alertas serão agrupados juntos:

        - **Agrupar alertas em um único incidente se todas as entidades corresponderem: os**alertas serão agrupados se compartilharem valores idênticos para cada uma das entidades mapeadas (definidas na guia definir lógica de regra acima). Esta é a configuração recomendável.

        - **Agrupar todos os alertas disparados por essa regra em um único incidente**: todos os alertas gerados por essa regra são agrupados em conjunto mesmo que não compartilhem valores idênticos.

        - **Agrupar alertas em um único incidente se as entidades selecionadas forem correspondentes: os**alertas serão agrupados se compartilharem valores idênticos para algumas das entidades mapeadas (que você pode selecionar na lista suspensa). Você talvez queira usar essa configuração se, por exemplo, desejar criar incidentes separados com base nos endereços IP de origem ou de destino.

    - **Reabrir incidentes de correspondência fechados**: se um incidente tiver sido resolvido e fechado, e posteriormente em outro alerta for gerado que deve pertencer a esse incidente, defina essa configuração como **habilitada** se você quiser que o incidente fechado seja reaberto e deixe como **desabilitado** se desejar que o alerta crie um novo incidente.
    
        > [!NOTE]
        > Até 150 alertas podem ser agrupados em um único incidente. Se mais de 150 alertas forem gerados por uma regra que os agrupa em um único incidente, um novo incidente será gerado com os mesmos detalhes de incidente que o original e os alertas em excesso serão agrupados no novo incidente.

1. Na guia **respostas automatizadas** , selecione os guias estratégicos que você deseja executar automaticamente quando um alerta for gerado pela regra personalizada. Para obter mais informações sobre como criar e automatizar os guias estratégicos, consulte [responder a ameaças](tutorial-respond-threats-playbook.md).

1. Selecione **revisar e criar** para examinar todas as configurações da nova regra de alerta e, em seguida, selecione **criar para inicializar a regra de alerta**.
  
1. Depois que o alerta é criado, uma regra personalizada é adicionada à tabela em **regras ativas**. Nessa lista, você pode habilitar, desabilitar ou excluir cada regra.

1. Para exibir os resultados das regras de alerta que você cria, vá para a página **incidentes** , na qual você pode fazer a triagem, [investigar incidentes](tutorial-investigate-cases.md)e corrigir as ameaças.


> [!NOTE]
> Os alertas gerados no Azure Sentinel estão disponíveis por meio do [Microsoft Graph Security](https://aka.ms/securitygraphdocs). Para obter mais informações, consulte a [documentação de alertas de segurança do Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="troubleshooting"></a>Solução de problemas

### <a name="a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Uma regra agendada não pôde ser executada ou aparece com DESABILITAdo automaticamente adicionado ao nome

É uma rara ocorrência de falha na execução de uma regra de consulta agendada, mas isso pode acontecer. O Azure Sentinel classifica as falhas antecipadamente como transitórias ou permanentes, com base no tipo específico da falha e nas circunstâncias que levaram a ela.

#### <a name="transient-failure"></a>Falha transitória

Uma falha transitória ocorre devido a uma circunstância que é temporária e em breve retornará ao normal, ponto em que a execução da regra terá êxito. Alguns exemplos de falhas que o Azure Sentinel classifica como transitória são:

- Uma consulta de regra leva muito tempo para ser executada e atinge o tempo limite.
- Problemas de conectividade entre fontes de dados e Log Analytics, ou entre Log Analytics e o Azure Sentinel.
- Qualquer outra falha nova e desconhecida é considerada transitória.

No caso de uma falha transitória, o Azure Sentinel continua tentando executar a regra novamente após os intervalos predeterminados e cada vez maiores, até um ponto. Depois disso, a regra será executada novamente apenas no próximo horário agendado. Uma regra nunca será desabilitada automaticamente devido a uma falha transitória.

#### <a name="permanent-failure---rule-auto-disabled"></a>Falha permanente-regra desabilitada automaticamente

Uma falha permanente ocorre devido a uma alteração nas condições que permitem a execução da regra, o que sem intervenção humana não retornará ao status anterior. Veja a seguir alguns exemplos de falhas que são classificadas como permanentes:

- O espaço de trabalho de destino (no qual a consulta de regra operada) foi excluído.
- A tabela de destino (na qual a consulta de regra operada) foi excluída.
- O Azure Sentinel foi removido do espaço de trabalho de destino.
- Uma função usada pela consulta de regra não é mais válida; Ele foi modificado ou removido.
- As permissões para uma das fontes de dados da consulta de regra foram alteradas.
- Uma das fontes de dados da consulta de regra foi excluída ou desconectada.

**No caso de um número predeterminado de falhas permanentes consecutivas, do mesmo tipo e da mesma regra,** O Azure Sentinel interrompe a tentativa de executar a regra e também executa as seguintes etapas:

- Desabilita a regra.
- Adiciona as palavras **"desabilitadas automaticamente"** ao início do nome da regra.
- Adiciona o motivo da falha (e a desabilitação) à descrição da regra.

Você pode determinar facilmente a presença de qualquer regra desabilitada automaticamente, classificando a lista de regras por nome. As regras desabilitadas automaticamente estarão no início da lista ou próximo dela.

Os gerentes de SOC devem ter certeza de verificar a lista de regras regularmente para a presença de regras desabilitadas automaticamente.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a começar a detectar ameaças usando o Azure Sentinel.

Para saber como automatizar suas respostas a ameaças, [Configure as respostas de ameaças automatizadas no Azure Sentinel](tutorial-respond-threats-playbook.md).

