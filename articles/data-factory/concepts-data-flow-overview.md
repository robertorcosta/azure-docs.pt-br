---
title: Fluxos de dados de mapeamento
description: Uma visão geral dos fluxos de dados de mapeamento na Fábrica de Dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/28/2020
ms.openlocfilehash: 39d1f15b771168b618bfbc4951f2036a8b95b027
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418449"
---
# <a name="what-are-mapping-data-flows"></a>O que são os fluxos de dados de mapeamento?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

O mapeamento dos fluxos de dados são transformações de dados projetadas visualmente na Fábrica de Dados Do Azure. Os fluxos de dados permitem que os engenheiros de dados desenvolvam lógica de transformação de dados gráficos sem escrever código. Os fluxos de dados resultantes são executados como atividades dentro dos pipelines da Fábrica de Dados Azure que usam clusters Apache Spark dimensionados. As atividades de fluxo de dados podem ser engajadas através dos recursos de agendamento, controle, fluxo e monitoramento existentes da Fábrica de Dados.

O mapeamento de fluxos de dados fornece uma experiência totalmente visual sem necessidade de codificação. Seus fluxos de dados são executados em seu cluster de execução para processamento de dados dimensionados. A Fábrica de Dados do Azure lida com toda a tradução de código, otimização de caminho e execução de seus trabalhos de fluxo de dados.

## <a name="getting-started"></a>Introdução

Para criar um fluxo de dados, selecione o sinal de acrescido em **Recursos de Fábrica**e, em seguida, selecione Fluxo de **dados**. 

![Novo fluxo de dados](media/data-flow/newdataflow2.png "novo fluxo de dados")

Essa ação leva você à tela de fluxo de dados, onde você pode criar sua lógica de transformação. Selecione **Adicionar origem** para começar a configurar sua transformação de origem. Para obter mais informações, consulte [Fonte de transformação](data-flow-source.md).

## <a name="data-flow-canvas"></a>Tela de fluxo de dados

A tela de fluxo de dados é separada em três partes: a barra superior, o gráfico e o painel de configuração. 

![Tela](media/data-flow/canvas1.png "Tela")

### <a name="graph"></a>Grafo

O gráfico exibe o fluxo de transformação. Ele mostra a linhagem de dados de origem à medida que flui em um ou mais pias. Para adicionar uma nova fonte, selecione **Adicionar origem**. Para adicionar uma nova transformação, selecione o sinal de adição no direito inferior de uma transformação existente.

![Tela](media/data-flow/canvas2.png "Tela")

### <a name="azure-integration-runtime-data-flow-properties"></a>Propriedades de fluxo de dados em tempo de execução de integração do Azure

![Botão depurar](media/data-flow/debugbutton.png "Botão depurar")

Quando você começa a trabalhar com fluxos de dados no ADF, você deseja ativar o switch "Debug" para fluxos de dados na parte superior da interface do usuário do navegador. Isso gira um cluster Spark para usar para depuração interativa, visualizações de dados e execuções de depuração de pipeline. Você pode definir o tamanho do cluster que está sendo utilizado escolhendo um Runtime de integração personalizado [do Azure](concepts-integration-runtime.md). A sessão de depuração permanece viva por até 60 minutos após sua última visualização de dados ou última execução do pipeline de depuração.

Quando você operacionaliza seus pipelines com atividades de fluxo de dados, o ADF usa o Tempo de Execução de Integração do Azure associado à [atividade](control-flow-execute-data-flow-activity.md) na propriedade "Run On".

O Azure Integration Runtime padrão é um pequeno cluster de nó de trabalho único de 4 núcleos que permite visualizar dados e executar rapidamente pipelines de depuração a custos mínimos. Defina uma configuração maior do Azure IR se você estiver executando operações contra grandes conjuntos de dados.

Você pode instruir o ADF a manter um pool de recursos de cluster (VMs) definindo um TTL nas propriedades de fluxo de dados do Azure IR. Essa ação resulta em uma execução mais rápida do trabalho nas atividades subsequentes.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Estratégias de tempo de execução de integração do Azure e fluxo de dados

##### <a name="execute-data-flows-in-parallel"></a>Executar fluxos de dados em paralelo

Se você executar fluxos de dados em um pipeline em paralelo, o ADF girará clusters Spark separados para cada execução de atividade com base nas configurações do tempo de execução de integração do Azure anexados a cada atividade. Para projetar execuções paralelas em pipelines ADF, adicione suas atividades de fluxo de dados sem restrições de precedência na ui.

Dessas três opções, essa opção provavelmente é executada no menor tempo. No entanto, cada fluxo de dados paralelos é executado ao mesmo tempo em clusters separados, de modo que a ordenação de eventos não é determinista.

Se você estiver executando suas atividades de fluxo de dados em paralelo dentro de seus pipelines, é recomendável não usar TTL. Essa ação é porque execuções paralelas do seu fluxo de dados simultaneamente usando o mesmo Azure Integration Runtime resulta em várias instâncias quentes de pool para sua fábrica de dados.

##### <a name="overload-single-data-flow"></a>Sobrecarga de fluxo único de dados

Se você colocar toda a sua lógica dentro de um único fluxo de dados, o ADF executará esse mesmo contexto de execução de trabalho em uma única instância de cluster Spark.

Essa opção pode ser mais desafiadora para seguir e solucionar problemas, pois suas regras de negócios e lógica de negócios podem ser misturadas. Essa opção também não oferece muita reutilização.

##### <a name="execute-data-flows-serially"></a>Executar fluxos de dados em série

Se você executar suas atividades de fluxo de dados em série no pipeline e tiver definido um TTL na configuração de IR do Azure, o ADF reutiliza os recursos de computação (VMs), resultando em tempos de execução mais rápidos subseqüentes. Você ainda recebe um novo contexto de Faísca para cada execução.

Dessas três opções, essa ação provavelmente leva mais tempo para executar de ponta a ponta. Mas fornece uma separação limpa das operações lógicas em cada etapa de fluxo de dados.

### <a name="configuration-panel"></a>Painel de configuração

O painel de configuração mostra as configurações específicas da transformação selecionada no momento. Se nenhuma transformação for selecionada, ela mostrará o fluxo de dados. Na configuração geral do fluxo de dados, você pode editar o nome e a descrição na guia **Geral** ou adicionar parâmetros através da guia **Parâmetros.** Para obter mais informações, consulte [Mapeamento de parâmetros de fluxo de dados](parameters-data-flow.md).

Cada transformação contém pelo menos quatro guias de configuração.

#### <a name="transformation-settings"></a>Configurações de transformação

A primeira guia no painel de configuração de cada transformação contém as configurações específicas para essa transformação. Para obter mais informações, consulte a página de documentação dessa transformação.

![Guia configurações de origem](media/data-flow/source1.png "Guia configurações de origem")

#### <a name="optimize"></a>Otimizar

A guia **Otimizar** contém configurações para configurar esquemas de particionamento.

![Otimizar](media/data-flow/optimize1.png "Otimizar")

A configuração padrão é **Usar particionamento atual,** que instrui a Fábrica de Dados do Azure a usar o esquema de particionamento nativo aos fluxos de dados em execução no Spark. Na maioria dos cenários, recomendamos essa configuração.

Há casos em que você pode querer ajustar o particionamento. Por exemplo, se você quiser fazer suas transformações em um único arquivo no lago, selecione **Partição única** em uma transformação de pia.

Outro caso em que você pode querer controlar os esquemas de particionamento é otimizar o desempenho. O ajuste do particionamento fornece controle sobre a distribuição de seus dados entre nós computados e otimizações de localização de dados que podem ter efeitos positivos e negativos sobre o desempenho geral do fluxo de dados. Para obter mais informações, consulte o [guia de desempenho do fluxo de dados](concepts-data-flow-performance.md).

Para alterar o particionamento em qualquer transformação, selecione a guia **Otimizar** e selecione o botão **'Definir particionamento'** de rádio. Você é apresentado com uma série de opções para particionamento. O melhor método de particionamento difere com base em seus volumes de dados, chaves de candidatos, valores nulos e cardinalidade. 

Uma prática recomendada é começar com particionamento padrão e, em seguida, tentar diferentes opções de particionamento. Você pode testar usando executos de depuração de pipeline e visualizar o tempo de execução e o uso da partição em cada agrupamento de transformação a partir da exibição de monitoramento. Para obter mais informações, consulte [Monitorando fluxos de dados](concepts-data-flow-monitoring.md).

As seguintes opções de particionamento estão disponíveis.

##### <a name="round-robin"></a>Rodízio 

Round robin é uma partição simples que distribui automaticamente dados igualmente através de partições. Use round-robin quando você não tem bons candidatos-chave para implementar uma estratégia de particionamento sólida e inteligente. É possível definir o número de partições físicas.

##### <a name="hash"></a>Hash

A Azure Data Factory produz um hash de colunas para produzir partições uniformes de tal forma que linhas com valores semelhantes caem na mesma partição. Quando você usar a opção Hash, teste para possível distorção de partição. É possível definir o número de partições físicas.

##### <a name="dynamic-range"></a>Alcance dinâmico

O alcance dinâmico usa faixas dinâmicas spark com base nas colunas ou expressões que você fornece. É possível definir o número de partições físicas. 

##### <a name="fixed-range"></a>Faixa fixa

Construa uma expressão que forneça um intervalo fixo para valores dentro de suas colunas de dados particionadas. Para evitar distorção de partição, você deve ter uma boa compreensão de seus dados antes de usar esta opção. Os valores digitados para a expressão são usados como parte de uma função de partição. É possível definir o número de partições físicas.

##### <a name="key"></a>Chave

Se você tem uma boa compreensão da cardinalidade de seus dados, particionamento de chaves pode ser uma boa estratégia. Partição de chaves cria partições para cada valor único em sua coluna. Você não pode definir o número de partições porque o número é baseado em valores únicos nos dados.

#### <a name="inspect"></a>Inspecionar

A guia **Inspecionar** fornece uma visualização nos metadados do fluxo de dados que você está transformando. Você pode ver contagem de colunas, as colunas alteradas, as colunas adicionadas, os tipos de dados, a ordem das colunas e as referências das colunas. **Inspecione** é uma exibição somente leitura de seus metadados. Você não precisa ter o modo de depuração ativado para ver metadados no painel **Inspecionar.**

![Inspecionar](media/data-flow/inspect1.png "Inspecionar")

À medida que você altera a forma de seus dados através de transformações, você verá o fluxo de alterações de metadados no painel **Inspecionar.** Se não houver um esquema definido na transformação da sua fonte, os metadados não serão visíveis no painel **Inspecionar.** A falta de metadados é comum em cenários de deriva de esquemas.

#### <a name="data-preview"></a>Visualização dos dados

Se o modo de depuração estiver ligado, a guia **Visualização de dados** lhe dá um instantâneo interativo dos dados em cada transformação. Para obter mais informações, consulte [Visualização de dados no modo de depuração](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Barra superior

A barra superior contém ações que afetam todo o fluxo de dados, como economia e validação. Você também pode alternar entre os modos gráfico e de configuração usando os botões **Mostrar gráfico** e **ocultar gráficos.**

![Ocultar grafo](media/data-flow/hideg.png "Ocultar grafo")

Se você ocultar seu gráfico, você pode navegar através de seus nós de transformação lateralmente através dos botões **Previous** e **Next.**

![Botões anteriores e próximos](media/data-flow/showhide.png "botões anteriores e próximos")

## <a name="next-steps"></a>Próximas etapas

* Aprenda a criar uma [transformação de origem.](data-flow-source.md)
* Saiba como construir seus fluxos de dados no [modo de depuração](concepts-data-flow-debug-mode.md).
