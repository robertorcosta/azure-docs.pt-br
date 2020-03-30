---
title: Crie regras analíticas personalizadas para detectar ameaças suspeitas com o Azure Sentinel| Microsoft Docs
description: Use este tutorial para aprender como criar regras analíticas personalizadas para detectar ameaças suspeitas com o Azure Sentinel.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585100"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Tutorial: Crie regras analíticas personalizadas para detectar ameaças suspeitas

Depois [de conectar suas fontes](quickstart-onboard.md) de dados ao Azure Sentinel, você pode criar regras personalizadas que podem procurar critérios específicos em todo o seu ambiente e gerar incidentes quando os critérios são combinados para que você possa investigá-los. Este tutorial ajuda você a criar regras personalizadas para detectar ameaças com o Azure Sentinel.

Este tutorial ajuda você a detectar ameaças com o Azure Sentinel.
> [!div class="checklist"]
> * Criar regras analíticas
> * Automatizar as respostas a ameaças

## <a name="create-custom-analytic-rules"></a>Crie regras analíticas personalizadas

Você pode criar regras analíticas personalizadas para ajudá-lo a procurar os tipos de ameaças e anomalias que são suspeitas em seu ambiente. A regra garante que você seja notificado imediatamente, para que você possa triagem, investigação e remediação das ameaças.

1. No portal do Azure, no Azure Sentinel, selecione **Análise**.

1. Na barra de menu superior, selecione **+Criar** e selecionar **Regra de consulta programada**. Isso abre o **assistente de regras do Analytics**.

    ![Criar consulta programada](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Na guia **Geral,** forneça um **nome**único e uma **descrição**. No campo **Táticas,** você pode escolher entre as categorias de ataques pelas quais classificar a regra. Defina o alerta **Severidade** conforme necessário. Quando você cria a regra, seu **Status** é **Ativado** por padrão, o que significa que ele será executado imediatamente após você terminar de criá-la. Se você não quiser que ele seja executado imediatamente, selecione **Disabled**, e a regra será adicionada à sua guia **de regras ativas** e você poderá habilitá-la a partir daí quando precisar.

    ![Comece a criar uma regra analítica personalizada](media/tutorial-detect-threats-custom/general-tab.png)

1. Na guia **Definir regra lógica,** você pode escrever uma consulta diretamente no campo **de consulta de Regras** ou criar a consulta no Log Analytics e, em seguida, copiá-la e colá-la lá.
 
   ![Criar consulta no Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   - Consulte a área **de visualização de resultados** à direita, onde o Azure Sentinel mostra o número de resultados (eventos de log) que a consulta irá gerar, alterando em tempo real à medida que você escreve e configura sua consulta. O gráfico mostra o número de resultados durante o período de tempo definido, que é determinado pelas configurações na seção **de agendamento de consulta.**
    - Se você ver que sua consulta desencadearia alertas muito ou muito frequentes, você pode definir uma linha de base na seção **limite alerta.**

      Aqui está uma consulta de exemplo que o alertaria quando um número anômalo de recursos for criado no Azure Activity.

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > O comprimento da consulta deve ser entre 1 e 10.000 caracteres e não pode conter "pesquisa" \*ou "união". \*

    1. Use a seção **'Mapear entidades'** para vincular parâmetros dos resultados de consulta a entidades reconhecidas pelo Azure Sentinel. Essas entidades formam a base para análises posteriores, incluindo o agrupamento de alertas em incidentes na guia **Configurações de Incidentes.**
    1. Na seção **de agendamento de consulta,** defina os seguintes parâmetros:

       1. Definir **executar consulta a cada** um para controlar a frequência com que a consulta é executada - tão freqüentemente quanto a cada 5 minutos ou tão pouco freqüentemente como uma vez por dia.

       1. Configure **os dados da Pesquisa do último** para determinar o período de tempo dos dados cobertos pela consulta - por exemplo, ele pode consultar os últimos 10 minutos de dados, ou as últimas 6 horas de dados.

       > [!NOTE]
       > Essas duas configurações são independentes uma da outra, até certo ponto. Você pode executar uma consulta em um curto intervalo cobrindo um período de tempo maior do que o intervalo (com efeito tendo consultas sobrepostas), mas você não pode executar uma consulta em um intervalo que exceda o período de cobertura, caso contrário você terá lacunas na cobertura geral da consulta.

    1. Use a seção **Limite alerta** para definir uma linha de base. Por exemplo, defina Gerar alerta quando o **número de resultados de consulta** for maior do **que** e digite o número 1000 se você quiser que a regra gere um alerta somente se a consulta gerar mais de 1000 resultados cada vez que for executada. Como este é um campo obrigatório, se você não quiser definir uma linha de base – ou seja, se você quiser que seu alerta registre cada evento – digite 0 no campo numério.

    1. Na seção **Supressão,** você pode ativar a **consulta 'Parar em execução após a** **configuração** de alerta'. Se você ativar isso, você deve definir **Parar de executar consulta para** a quantidade de tempo que a consulta deve parar de funcionar, até 24 horas.

1. Na guia **Configurações de Incidente,** você pode escolher se e como o Azure Sentinel transforma alertas em incidentes acionáveis. Se essa guia for deixada em paz, o Azure Sentinel criará um único incidente separado de cada alerta. Você pode optar por não ter nenhum incidente criado, ou agrupar vários alertas em um único incidente, alterando as configurações nesta guia.

    1. Na seção **Configurações de Incidente,** **Criar incidentes a partir de alertas acionados por esta regra de análise** é definido por padrão para **Ativado**, o que significa que o Azure Sentinel criará um único incidente separado de cada alerta acionado pela regra.<br></br>Se você não quiser que essa regra resulte na criação de quaisquer incidentes (por exemplo, se esta regra for apenas coletar informações para análise posterior), defina isso como **Desativado**.

    1. Na seção **de agrupamento Alerta,** se você quiser que um único incidente seja gerado a partir de um grupo de alertas semelhantes ou recorrentes, defina **alertas relacionados ao Grupo, acionados por esta regra de análise, em incidentes** para **Habilitado**e defina os seguintes parâmetros.

    1. **Limite o grupo a alertas criados dentro do período de tempo selecionado:**<br></br> Determine o período de tempo no qual os alertas semelhantes ou recorrentes serão agrupados. Todos os alertas correspondentes dentro desse prazo gerarão coletivamente um incidente ou um conjunto de incidentes (dependendo das configurações de agrupamento abaixo). Alertas fora desse período de tempo gerarão um incidente separado ou um conjunto de incidentes.

    2. **Alertas de grupo acionados por esta regra de análise em um único incidente por**: Escolha a base na qual os alertas serão agrupados:

        - **Alertas de grupo em um único incidente se todas as entidades corresponderem:** <br></br>Os alertas são agrupados se compartilharem valores idênticos para cada uma das entidades mapeadas (definidas na guia Definir lógica de regra acima). Esta é a configuração recomendável.

        - **Agrupar todos os alertas acionados por esta regra em um único incidente**: <br></br>Todos os alertas gerados por essa regra são agrupados mesmo que não compartilhem valores idênticos.

        - **Agrupar alertas em um único incidente se as entidades selecionadas corresponderem:** <br></br>Os alertas são agrupados se eles compartilham valores idênticos para algumas das entidades mapeadas (que você pode selecionar na lista suspensa). Você pode querer usar essa configuração se, por exemplo, quiser criar incidentes separados com base na origem ou endereços IP de destino.

    3. **Reabra incidentes de correspondência fechados**: Se um incidente foi encerrado (o que significa que o problema subjacente foi resolvido), e, posteriormente, outro alerta é gerado que teria sido agrupado nesse incidente, defina esta configuração para **Habilitado** se você quiser que o incidente fechado seja reaberto e saia como **Desativado** se quiser que o alerta crie um novo incidente.

1. Na guia **Respostas automatizadas,** selecione todas as cartilhas que deseja executar automaticamente quando um alerta for gerado pela regra personalizada. Para obter mais informações sobre como criar e automatizar livros de jogos, consulte [Responder a ameaças](tutorial-respond-threats-playbook.md).

1. Selecione **'Revisar' e criar** para rever todas as configurações da nova regra de alerta e, em seguida, selecione **Criar para inicializar sua regra de alerta**.
  
1. Depois que o alerta é criado, uma regra personalizada é adicionada à tabela **regras ativas**. A partir desta lista você pode ativar, desativar ou excluir cada regra.

1. Para visualizar os resultados das regras de alerta **criadas,** acesse a página Incidentes, onde você pode triagem, [investigar incidentes](tutorial-investigate-cases.md)e remediar as ameaças.


> [!NOTE]
> Os alertas gerados no Azure Sentinel estão disponíveis através do [Microsoft Graph Security](https://aka.ms/securitygraphdocs). Para obter mais informações, consulte a [documentação de alertas](https://aka.ms/graphsecurityreferencebetadocs)de segurança do Gráfico do Microsoft .

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como começar a detectar ameaças usando o Azure Sentinel.

Para saber como automatizar suas respostas a ameaças, [configure respostas automatizadas de ameaças no Azure Sentinel](tutorial-respond-threats-playbook.md).

