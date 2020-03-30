---
title: Mapeamento do modo de depuração do fluxo de dados
description: Iniciar uma sessão de depuração interativa ao construir fluxos de dados
ms.author: makromer
author: kromerm
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/06/2019
ms.openlocfilehash: 18848695327a374f12fbe5a34d03366b050d8b65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74928355"
---
# <a name="mapping-data-flow-debug-mode"></a>Mapeamento do modo de depuração do fluxo de dados

## <a name="overview"></a>Visão geral

O modo de depuração do fluxo de dados do Azure Data Factory permite que você assista interativamente à forma de dados se transformar enquanto você constrói e depura seus fluxos de dados. A sessão de depuração pode ser usada tanto em sessões de design de fluxo de dados como durante a execução de depuração de fluxos de dados. Para ativar o modo de depuração, use o botão "Data Flow Debug" na parte superior da superfície do design.

![Controle deslizante de depuração](media/data-flow/debugbutton.png "Controle deslizante de depuração")

Uma vez que você ligue o controle deslizante, você será solicitado a selecionar qual configuração de tempo de execução de integração você deseja usar. Se o AutoResolveIntegrationRuntime for escolhido, um cluster com oito núcleos de computação geral com um tempo de vida de 60 minutos será girado. Para obter mais informações sobre os tempos de execução de integração do fluxo de dados, consulte [o desempenho do fluxo de dados](concepts-data-flow-performance.md#increasing-compute-size-in-azure-integration-runtime).

![Seleção de IR de depuração](media/data-flow/debugbutton2.png "Seleção de IR de depuração")

Quando o modo Debug estiver ligado, você construirá interativamente seu fluxo de dados com um cluster Spark ativo. A sessão fecha quando você desativa a depuração no Azure Data Factory. Você deve estar ciente das cobranças por hora incorridas pelo Azure Databricks durante o tempo em que a sessão de depuração está ativa.

Na maioria dos casos, é uma boa prática construir seus Fluxos de Dados no modo de depuração para que você possa validar sua lógica de negócios e visualizar suas transformações de dados antes de publicar seu trabalho na Fábrica de Dados do Azure. Use o botão "Debug" no painel do pipeline para testar seu fluxo de dados em um pipeline.

## <a name="cluster-status"></a>Status do cluster

O indicador de status do cluster na parte superior da superfície do projeto fica verde quando o cluster está pronto para depuração. Se o seu cluster já está em estado passivo, o indicador verde aparece quase que instantaneamente. Se o cluster ainda não estava funcionando quando você entrou no modo de depuração, então você terá que esperar de 5 a 7 minutos para que o cluster gire. O indicador girará até ficar pronto.

Quando terminar sua depuração, desligue o interruptor Debug para que o cluster Azure Databricks possa terminar e você não será mais cobrado pela atividade de depuração.

## <a name="debug-settings"></a>Configurações de depuração

As configurações de depuração podem ser editadas clicando em "Configurações de depuração" na barra de ferramentas da tela Fluxo de dados. Você pode selecionar o limite de linha ou a fonte de arquivo a ser usado para cada uma de suas transformações de Origem aqui. Os limites de linha nesta configuração são apenas para a sessão de depuração atual. Você também pode selecionar o serviço vinculado de preparação a ser usado para uma fonte SQL DW. 

![Configurações de depuração](media/data-flow/debug-settings.png "Configurações de depuração")

Se você tiver parâmetros em seu Fluxo de Dados ou em qualquer um de seus conjuntos de dados referenciados, você pode especificar quais valores usar durante a depuração selecionando a guia **Parâmetros.**

![Parâmetros de configuração de depuração](media/data-flow/debug-settings2.png "Parâmetros de configuração de depuração")

## <a name="data-preview"></a>Visualização dos dados

Com a depuração ativa, a guia Visualização dos Dados fica destacada no painel inferior. Sem o modo de depuração ligado, o Fluxo de Dados mostrará apenas os metadados atuais dentro e fora de cada uma de suas transformações na guia Inspecionar. A visualização de dados apenas consultará o número de linhas definidas como seu limite nas configurações de depuração. Clique **em Atualizar** para obter a visualização de dados.

![Visualização de dados](media/data-flow/datapreview.png "Visualização dos dados")

> [!NOTE]
> As fontes de arquivo limitam apenas as linhas que você vê, não as linhas que estão sendo lidas. Para conjuntos de dados muito grandes, recomenda-se que você pegue uma pequena parte desse arquivo e use-o para o seu teste. Você pode selecionar um arquivo temporário em Configurações de depuração para cada fonte que seja um tipo de conjunto de dados de arquivo.

Ao executar no Modo de Depuração no Fluxo de Dados, seus dados não são gravados na transformação de Coletor. Uma sessão de Debug destina-se a servir como um arreio de teste para suas transformações. Os coletores não são necessários durante a depuração e são ignorados no fluxo de dados. Se você deseja testar a gravação dos dados em seu Sink, execute o Fluxo de dados de um pipeline de fábrica de dados do Azure e use a execução de depuração de um pipeline.

### <a name="testing-join-conditions"></a>Testar condições de adesão

Ao testar a unidade de se juntar, existir ou procurar transformações, certifique-se de usar um pequeno conjunto de dados conhecidos para o seu teste. Você pode usar a opção Configurações de depuração acima para definir um arquivo temporário para usar para seus testes. Isso é necessário porque, ao limitar ou amostrar linhas de um grande conjunto de dados, você não pode prever quais linhas e quais teclas serão lidas no fluxo para testes. O resultado não é determinista, o que significa que suas condições de adesão podem falhar.

### <a name="quick-actions"></a>Ações rápidas

Uma vez que você vê a visualização de dados, você pode gerar uma transformação rápida para digitar, remover ou fazer uma modificação em uma coluna. Clique no cabeçalho da coluna e selecione uma das opções na barra de ferramentas de visualização de dados.

![Ações rápidas](media/data-flow/quick-actions1.png "Ações rápidas")

Depois de selecionar uma modificação, a visualização de dados será atualizada imediatamente. Clique em **Confirmar** no canto superior direito para gerar uma nova transformação.

![Ações rápidas](media/data-flow/quick-actions2.png "Ações rápidas")

**Typecast** e **Modify** gerarão uma transformação de Coluna Derivada e **Remover** gerará uma transformação Select.

![Ações rápidas](media/data-flow/quick-actions3.png "Ações rápidas")

> [!NOTE]
> Se você editar seu Fluxo de Dados, você precisará rebuscar a visualização de dados antes de adicionar uma transformação rápida.

### <a name="data-profiling"></a>Criação de perfil de dados

Selecionar uma coluna na sua guia de visualização de dados e clicar em **Estatísticas** na barra de ferramentas de visualização de dados aparecerá um gráfico na extrema-direita da sua grade de dados com estatísticas detalhadas sobre cada campo. O Azure Data Factory faz uma determinação com base na amostragem de dados do tipo de gráfico a ser exibido. Os campos de alta cardinalidade serão padrão para gráficos NULA/NÃO NULL, enquanto dados categóricos e numéricos que têm baixa cardinalidade exibirão gráficos de barras mostrando frequência de valor de dados. Você também verá o comprimento máximo/len dos campos de cordas, valores min/max em campos numéricos, dev padrão, percentil, contagem e média.

![Estatísticas da coluna](media/data-flow/stats.png "Estatísticas da coluna")

## <a name="next-steps"></a>Próximas etapas

* Assim que terminar de construir e depurar seu fluxo de dados, [execute-o a partir de um pipeline.](control-flow-execute-data-flow-activity.md)
* Ao testar seu pipeline com um fluxo de dados, use a [opção de execução de execução de depuração](iterative-development-debugging.md) do pipeline Debug.
