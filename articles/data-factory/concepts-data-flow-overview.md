---
title: Fluxos de dados de mapeamento
description: Uma visão geral do mapeamento de fluxos de dados no Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/09/2020
ms.openlocfilehash: 850879675d4554329f24c86f2ac28660b303084c
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475559"
---
# <a name="what-are-mapping-data-flows"></a>O que são os fluxos de dados de mapeamento?

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O mapeamento de fluxos de dados são transformações de dados visualmente projetadas em Azure Data Factory. Os fluxos de dados permitem que os engenheiros de dados desenvolvam a lógica de transformação de dados gráficos sem escrever código. Os fluxos de dados resultantes são executados como atividades dentro de Azure Data Factory pipelines que usam clusters de Apache Spark expandidos. As atividades de fluxo de dados podem ser envolvidas por meio de recursos existentes de agendamento, controle, fluxo e monitoramento de Data Factory.

O mapeamento de fluxos de dados fornece uma experiência totalmente visual sem a necessidade de codificação. Seus fluxos de dados são executados em seu cluster de execução para processamento de dados expandido. Azure Data Factory manipula toda a conversão de código, a otimização de caminho e a execução de seus trabalhos de fluxo de dados.

![Arquitetura](media/data-flow/adf-data-flows.png "Arquitetura")

## <a name="getting-started"></a>Introdução

Para criar um fluxo de dados, selecione o sinal de adição em **recursos de fábrica**e, em seguida, selecione **fluxo de dados**. 

![Novo fluxo de dados](media/data-flow/newdataflow2.png "novo fluxo de dados")

Essa ação leva você para a tela de fluxo de dados, na qual você pode criar a lógica de transformação. Selecione **Adicionar origem** para começar a configurar sua transformação de origem. Para obter mais informações, consulte [Source Transformation](data-flow-source.md).

## <a name="data-flow-canvas"></a>Tela de fluxo de dados

A tela de fluxo de dados é separada em três partes: a barra superior, o grafo e o painel de configuração. 

![Canvas](media/data-flow/canvas1.png "Canvas")

### <a name="graph"></a>Grafo

O grafo exibe o fluxo de transformação. Ele mostra a linhagem dos dados de origem conforme eles fluem em um ou mais coletores. Para adicionar uma nova origem, selecione **Adicionar origem**. Para adicionar uma nova transformação, selecione o sinal de adição no canto inferior direito de uma transformação existente.

![Canvas](media/data-flow/canvas2.png "Canvas")

### <a name="azure-integration-runtime-data-flow-properties"></a>Propriedades de fluxo de dados do Azure Integration Runtime

![Botão depurar](media/data-flow/debugbutton.png "Botão depurar")

Ao começar a trabalhar com fluxos de dados no ADF, você deseja ativar a opção de "depuração" para fluxos de dados na parte superior da interface do usuário do navegador. Isso gira um cluster Spark para usar para depuração interativa, visualizações de dados e execuções de depuração de pipeline. Você pode definir o tamanho do cluster que está sendo utilizado escolhendo um [Azure Integration Runtime](concepts-integration-runtime.md)personalizado. A sessão de depuração permanece ativa por até 60 minutos após a última visualização de dados ou a última execução do pipeline de depuração.

Quando você operacionalizou seus pipelines com atividades de fluxo de dados, o ADF usa o Azure Integration Runtime associado à [atividade](control-flow-execute-data-flow-activity.md) na propriedade "executar em".

O Azure Integration Runtime padrão é um cluster de nó de trabalho único de 4 núcleos que permite visualizar dados e executar rapidamente os pipelines de depuração com custos mínimos. Defina uma configuração de Azure IR maior se você estiver executando operações em grandes conjuntos de altos.

Você pode instruir o ADF a manter um pool de recursos de cluster (VMs) definindo um TTL na Azure IR Propriedades de fluxo de dados. Essa ação resulta em uma execução de trabalho mais rápida nas atividades subsequentes.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Tempo de execução de integração do Azure e estratégias de fluxo de dados

##### <a name="execute-data-flows-in-parallel"></a>Executar fluxos de dados em paralelo

Se você executar fluxos de dados em um pipeline em paralelo, o ADF girará clusters Spark separados para cada execução de atividade com base nas configurações de sua Azure Integration Runtime anexadas a cada atividade. Para criar execuções paralelas em pipelines do ADF, adicione suas atividades de fluxo de dados sem restrições de precedência na interface do usuário.

Dessas três opções, essa opção provavelmente é executada na quantidade mais curta de tempo. No entanto, cada fluxo de dados paralelo é executado ao mesmo tempo em clusters separados, portanto, a ordem dos eventos é não determinística.

Se você estiver executando suas atividades de fluxo de dados em paralelo dentro de seus pipelines, é recomendável não usar TTL. Essa ação ocorre porque as execuções paralelas de seu fluxo de dados simultaneamente usando o mesmo Azure Integration Runtime resultam em várias instâncias de pools quentes para seu data factory.

##### <a name="overload-single-data-flow"></a>Sobrecarga de fluxo de dados único

Se você colocar toda a lógica dentro de um único fluxo de dados, o ADF executará esse mesmo contexto de execução de trabalho em uma única instância de cluster do Spark.

Essa opção pode ser mais desafiadora para seguir e solucionar problemas, pois suas regras de negócios e a lógica de negócios podem ser jumbled juntas. Essa opção também não fornece muita reutilização.

##### <a name="execute-data-flows-sequentially"></a>Executar fluxos de dados sequencialmente

Se você executar suas atividades de fluxo de dados em sequência no pipeline e tiver definido um TTL na configuração de Azure IR, o ADF reutilizará os recursos de computação (VMs) resultando em tempos de execução subsequentes mais rápidos. Você ainda receberá um novo contexto do Spark para cada execução.

Dessas três opções, essa ação provavelmente levará o tempo mais longo para ser executado de ponta a ponta. Mas ele fornece uma separação limpa das operações lógicas em cada etapa do fluxo de dados.

### <a name="configuration-panel"></a>Painel de configuração

O painel de configuração mostra as configurações específicas para a transformação selecionada no momento. Se nenhuma transformação for selecionada, ela mostrará o fluxo de dados. Na configuração geral do fluxo de dados, você pode editar o nome e a descrição na guia **geral** ou adicionar parâmetros por meio da guia **parâmetros** . Para obter mais informações, consulte [mapeando parâmetros de fluxo de dados](parameters-data-flow.md).

Cada transformação contém pelo menos quatro guias de configuração.

#### <a name="transformation-settings"></a>Configurações de transformação

A primeira guia em cada painel de configuração de transformação contém as configurações específicas para essa transformação. Para obter mais informações, consulte a página de documentação da transformação.

![Guia Configurações de origem](media/data-flow/source1.png "Guia Configurações de origem")

#### <a name="optimize"></a>Otimizar

A guia **otimizar** contém configurações para configurar esquemas de particionamento. Para saber mais sobre como otimizar seus fluxos de dados, consulte o [Guia de desempenho do fluxo de dados de mapeamento](concepts-data-flow-performance.md).

![Otimizar](media/data-flow/optimize.png "Otimizar")

#### <a name="inspect"></a>Inspecionar

A guia **inspecionar** fornece uma exibição dos metadados do fluxo de dados que você está transformando. Você pode ver as contagens de colunas, as colunas alteradas, as colunas adicionadas, os tipos de dados, a ordem das colunas e as referências de coluna. **Inspecionar** é uma exibição somente leitura de seus metadados. Você não precisa ter o modo de depuração habilitado para ver os metadados no painel **inspecionar** .

![Inspecionar](media/data-flow/inspect1.png "Inspecionar")

Ao alterar a forma de seus dados por meio de transformações, você verá o fluxo de alterações de metadados no painel **inspecionar** . Se não houver um esquema definido em sua transformação de origem, os metadados não estarão visíveis no painel **inspecionar** . A falta de metadados é comum em cenários de descompasso de esquema.

#### <a name="data-preview"></a>Visualização dos dados

Se o modo de depuração estiver ativado, a guia **visualização de dados** fornecerá um instantâneo interativo dos dados em cada transformação. Para obter mais informações, consulte [visualização de dados em modo de depuração](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Barra superior

A barra superior contém ações que afetam todo o fluxo de dados, como salvar e validar. Você também pode alternar entre modos de gráfico e de configuração usando os botões **Mostrar grafo** e **ocultar grafo** .

![Ocultar grafo](media/data-flow/hideg.png "Ocultar grafo")

Se você ocultar o grafo, poderá navegar pelos nós de transformação posteriormente por meio dos botões **anterior** e **próximo** .

![Botões anterior e próximo](media/data-flow/showhide.png "botões anterior e próximo")

## <a name="next-steps"></a>Próximas etapas

* Saiba como criar uma [transformação de origem](data-flow-source.md).
* Saiba como criar fluxos de dados no [modo de depuração](concepts-data-flow-debug-mode.md).
