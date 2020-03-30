---
title: Livros de trabalho do Azure Monitor criam parâmetros
description: Simplifique a emissão de relatórios complexos com pastas de trabalho parametrizadas predefinidas e personalizadas
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 3e7dda85f1f890d5ae0eb4722c3e028b373fdcab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658210"
---
# <a name="workbook-parameters"></a>Parâmetros da carteira de trabalho

Os parâmetros permitem que os autores da carteira de trabalho coletem entradas dos consumidores e as referenciam em outras partes da carteira de trabalho – geralmente para escopo do conjunto de resultados ou definição do visual certo. É uma capacidade fundamental que permite aos autores construir relatórios e experiências interativas. 

As câmaras de trabalho permitem controlar como os controles de parâmetros são apresentados aos consumidores – caixa de texto vs. saqueada, single- vs. multi-select, valores de texto, JSON, KQL ou Gráfico de Recursos do Azure, etc.  

Os tipos de parâmetros suportados incluem:
* [Tempo](workbooks-time.md) - permite que um usuário selecione entre intervalos de tempo pré-preenchidos ou selecione um intervalo personalizado
* [Drop down](workbooks-dropdowns.md) - permite que um usuário selecione a partir de um valor ou conjunto de valores
* [Texto](workbooks-text.md) - permite que um usuário insira texto arbitrário
* [Recurso](workbooks-resources.md) - permite que um usuário selecione um ou mais recursos do Azure
* [Assinatura](workbooks-resources.md) - permite que um usuário selecione um ou mais recursos de assinatura do Azure
* Tipo de recurso - permite que um usuário selecione um ou mais valores do tipo de recurso do Azure
* Localização - permite que um usuário selecione um ou mais valores de localização do Azure

Esses valores de parâmetro podem ser referenciados em outras partes da cartm, seja através de vinculações ou expansões de valor.

## <a name="creating-a-parameter"></a>Criando um parâmetro
1. Comece com uma carteira de trabalho vazia no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro da caderneta de trabalho.
3. Clique no botão adicionar _parâmetro_ azul.
4. No novo painel de parâmetros que aparece digite:
    1. Nome do `TimeRange` parâmetro: *(note que nomes de __parâmetros__ **não podem** incluir espaços ou caracteres especiais)*
    2. Nome da `Time Range`exibição: *(no entanto, __os nomes de exibição__ podem incluir espaços, caracteres especiais, emojis, etc.)*  
    2. Tipo de parâmetro:`Time range picker`
    3. Necessário:`checked`
    4. Faixas de tempo disponíveis: Última hora, Últimas 12 horas, Últimas 24 horas, Últimas 48 horas, Últimos 3 dias, Últimos 7 dias e Permitir seleção personalizada do intervalo de tempo
5. Escolha 'Salvar' na barra de ferramentas para criar o parâmetro.

   ![Imagem mostrando a criação de um parâmetro de intervalo de tempo](./media/workbooks-parameters/time-settings.png)

É assim que a caderneta de trabalho ficará no modo de leitura, no estilo "Pílulas".

   ![Imagem mostrando um parâmetro de intervalo de tempo no modo de leitura](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>Fazendo referência a um parâmetro
### <a name="via-bindings"></a>Via Vinculações
1. Adicione um controle de consulta à caderneta de trabalho e selecione um recurso Application Insights.
2. Abra a queda do `Time Range` intervalo de _tempo_ e selecione a opção na seção Parâmetros na parte inferior.
3. Isso liga o parâmetro de intervalo de tempo ao intervalo de tempo do gráfico. O escopo de tempo da consulta de amostra agora é de 24 horas.
4. Executar consulta para ver os resultados

    ![Imagem mostrando um parâmetro de intervalo de tempo referenciado através de vinculações](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>Em KQL
1. Adicione um controle de consulta à caderneta de trabalho e selecione um recurso Application Insights.
2. No KQL, digite um filtro de escopo de tempo usando o parâmetro:`| where timestamp {TimeRange}`
3. Isso se expande no `| where timestamp > ago(1d)`tempo de avaliação de consulta para , que é o valor do intervalo de tempo do parâmetro.
4. Executar consulta para ver os resultados

    ![Imagem mostrando um intervalo de tempo referenciado em KQL](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>Em Texto 
1. Adicione um controle de texto à caderneta de trabalho.
2. Na marcação, digite`The chosen time range is {TimeRange:label}`
3. Escolha _a edição feita_
4. O controle de texto mostrará texto: _O intervalo de tempo escolhido é de 24 horas_

## <a name="parameter-options"></a>Opções de parâmetro
A seção Em `label` _Texto_ usou o parâmetro em vez de seu valor. Os parâmetros expõem várias dessas opções dependendo do seu tipo - por exemplo, os catadores de intervalo de tempo permitem valor, rótulo, consulta, início, fim e grão.

Use `Previews` a seção do painel _Editar parâmetro_ para ver as opções de expansão do seu parâmetro:

![Imagem mostrando opções de parâmetro de intervalo de tempo](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>Próximas etapas

* [Comece a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções ricas de visualizações.
* [Controle](workbooks-access-control.md) e compartilhe o acesso aos recursos da sua carteira de trabalho.
