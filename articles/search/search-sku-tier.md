---
title: Escolha um nível de preço ou SKU
titleSuffix: Azure Cognitive Search
description: 'A Pesquisa Cognitiva do Azure pode ser provisionada nessas SKUs: Free, Basic e Standard, e o Standard está disponível em várias configurações de recursos e níveis de capacidade.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: f103db1d0de7a9d538f56b8ade331dc856b26bce
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547006"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Escolha um nível de preço para a Pesquisa Cognitiva do Azure

Quando você cria um serviço de pesquisa cognitiva do Azure, um [recurso é criado](search-create-service-portal.md) em um nível de preço (ou SKU) que é fixo durante a vida útil do serviço. Os níveis incluem otimização gratuita, básica, padrão e otimizada de armazenamento. Padrão e Armazenamento Otimizado estão disponíveis com várias configurações e capacidades.

A maioria dos clientes começa com o nível Free para que eles possam avaliar o serviço. Após a avaliação, é comum criar um segundo serviço em um dos níveis mais altos para implantações de desenvolvimento e produção.

## <a name="feature-availability-by-tier"></a>Disponibilidade de recursos por nível

Quase todos os recursos estão disponíveis em todos os níveis, incluindo o Free, mas um recurso ou fluxo de trabalho que é intensivo em recursos pode não funcionar bem a menos que você lhe dê capacidade suficiente. Por exemplo, [o enriquecimento de IA](cognitive-search-concept-intro.md) tem habilidades de longa duração que tempo fora em um serviço gratuito, a menos que o conjunto de dados seja pequeno.

A tabela a seguir descreve as restrições de recurso relacionadas ao nível.

| Recurso | Limitações |
|---------|-------------|
| [Indexadores](search-indexer-overview.md) | Os indexadores não estão disponíveis no S3 HD. |
| [Chaves de criptografia gerenciadas pelo cliente](search-security-manage-encryption-keys.md) | Não está disponível no nível Free. |

## <a name="tiers-skus"></a>Níveis (SKUs)

Os níveis são diferenciados por:

+ Quantidade de índices e indexadores que você pode criar
+ Tamanho e velocidade de partições (armazenamento físico)

O nível selecionado determina a taxa de faturamento. A captura de tela a seguir do portal Azure mostra os níveis disponíveis, menos preços (que você pode encontrar no portal e na [página de preços](https://azure.microsoft.com/pricing/details/search/). **Free**, **Basic**e **Standard** são os níveis mais comuns.

**O Free** cria um serviço de pesquisa limitado para projetos menores, incluindo quickstarts e tutoriais. Internamente, réplicas e partições compartilhadas entre vários assinantes. Você não pode escalar um serviço gratuito ou executar cargas de trabalho significativas.

**Basic** e **Standard** são os níveis faturados mais usados, com o **Standard** sendo o padrão. Com recursos dedicados sob seu controle, você pode implantar projetos maiores, otimizar o desempenho e definir a capacidade.

![Níveis de preços da Pesquisa Cognitiva do Azure](media/search-sku-tier/tiers.png "Níveis de preços da Pesquisa Cognitiva do Azure")

Alguns níveis são otimizados para certos tipos de trabalho. Por exemplo, **Standard 3 High Density (S3 HD)** é um *modo de hospedagem* para S3, onde o hardware subjacente é otimizado para um grande número de índices menores e é destinado a cenários de multitenancy. O S3 HD tem a mesma carga por unidade que o S3, mas o hardware é otimizado para leituras rápidas de arquivos em um grande número de índices menores.

Os níveis **otimizados** de armazenamento oferecem maior capacidade de armazenamento a um preço menor por TB do que os níveis Standard. A negociação principal é a latência de consulta mais alta, que você deve validar para os requisitos específicos do aplicativo.  Para saber mais sobre as considerações de desempenho deste nível, consulte considerações de [desempenho e otimização](search-performance-optimization.md).

Você pode saber mais sobre os vários níveis na [página de preços,](https://azure.microsoft.com/pricing/details/search/)nos limites de serviço no artigo [do Azure Cognitive Search](search-limits-quotas-capacity.md) e na página do portal quando você está provisionando um serviço.

## <a name="billable-events"></a>Eventos faturados

Uma solução construída no Azure Cognitive Search pode incorrer em custos das seguintes maneiras:

+ Custo fixo do serviço em si, rodando 24 horas por dia, 7 dias por semana, na configuração mínima (uma partição e réplica)
+ Custo incremental ao dimensionar (adicionar réplicas ou partições)
+ Taxas de largura de banda (transferência de dados de saída) 
+ Pesquisa cognitiva (anexando serviços cognitivos para enriquecimento de IA, ou usando o armazenamento do Azure para armazenamento de conhecimento)

### <a name="service-costs"></a>Custos de serviço

Ao contrário de máquinas virtuais ou outros recursos que podem ser "pausados" para evitar cobranças, um serviço de Pesquisa Cognitiva do Azure está sempre disponível em hardware dedicado para seu uso exclusivo. Como tal, criar um serviço é um evento faturado que começa quando você cria o serviço, e termina quando você exclui o serviço. 

A carga mínima é a primeira unidade de pesquisa (uma réplica x uma partição) à taxa de faturamento. Este mínimo é fixo para a vida útil do serviço porque o serviço não pode ser executado em nada menos do que esta configuração. Além do mínimo, você pode adicionar réplicas e partições independentemente umas das outras. Aumentos incrementais na capacidade através de réplicas e partições aumentarão sua conta com base na seguinte fórmula: [(réplicas x partições x taxa)](#search-units), onde a taxa que você é cobrado depende do nível de preços selecionado.

Quando você está estimando o custo de uma solução de pesquisa, tenha em mente que preços e capacidade não são lineares. (Duplicar a capacidade mais do que dobra o custo.) Para um exemplo de como a fórmula funciona, consulte [Como alocar réplicas e partições](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Cargas de largura de banda

O uso [de indexadores de pesquisa cognitiva do Azure](search-indexer-overview.md) pode afetar o faturamento, dependendo da localização de seus serviços. Você pode eliminar as taxas de saída de dados inteiramente se você criar o serviço de pesquisa cognitiva do Azure na mesma região que seus dados. Aqui estão algumas informações da página de [preços de largura de banda:](https://azure.microsoft.com/pricing/details/bandwidth/)

+ A Microsoft não cobra por nenhum dado de entrada em qualquer serviço no Azure ou por quaisquer dados de saída da Pesquisa Cognitiva do Azure.
+ Em soluções multiserviço, não há cobrança de dados cruzando o fio quando todos os serviços estão na mesma região.

As taxas se aplicam aos dados de saída se os serviços estiverem em diferentes regiões. Essas acusações não fazem parte da sua conta de pesquisa cognitiva do Azure. Eles são mencionados aqui porque se você estiver usando dados ou indexadores enriquecidos com IA para extrair dados de diferentes regiões, você verá os custos refletidos em sua conta geral.

### <a name="ai-enrichment-with-cognitive-services"></a>Enriquecimento de IA com Serviços Cognitivos

Para [o enriquecimento de IA,](cognitive-search-concept-intro.md)você deve planejar [anexar um recurso de Serviços Cognitivos Azure faturado,](cognitive-search-attach-cognitive-services.md)na mesma região que o Azure Cognitive Search, na camada de preços S0 para processamento pago à medida que você vai. Não há custo fixo associado à anexação dos Serviços Cognitivos. Você paga apenas pelo processamento que precisa.

| Operação | Impacto de cobrança |
|-----------|----------------|
| Quebra de documento, extração de texto | Grátis |
| Quebra de documento, extração de imagem | Cobrado de acordo com o número de imagens extraídas de seus documentos. Em uma [configuração indexador,](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters) **imageAction** é o parâmetro que aciona a extração de imagem. Se **a imageAction** estiver definida como "nenhum" (o padrão), você não será cobrado pela extração de imagem. A taxa de extração de imagens está documentada na página [de detalhes de preços](https://azure.microsoft.com/pricing/details/search/) para a Pesquisa Cognitiva do Azure.|
| [Habilidades cognitivas internas](cognitive-search-predefined-skills.md) | Cobrado na mesma taxa como se você tivesse realizado a tarefa usando serviços cognitivos diretamente. |
| Habilidades personalizadas | Uma habilidade personalizada é a funcionalidade que você fornece. O custo de usar uma habilidade personalizada depende inteiramente se o código personalizado está chamando outros serviços medidos. |

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Fórmula de faturamento (R x P = SU)

O conceito de faturamento mais importante a ser compreendido para as operações de Busca Cognitiva do Azure é a *unidade de busca* (SU). Como o Azure Cognitive Search depende de réplicas e partições para indexação e consultas, não faz sentido faturar apenas uma ou outra. Em vez disso, a cobrança baseia-se em uma combinação de ambos.

SU é o produto das *réplicas* e *partições* usadas por um serviço: **(R x P = SU)**.

Cada serviço começa com uma UA (uma réplica multiplicada por uma partição) como o mínimo. O máximo para qualquer serviço é 36 SUs. Esse máximo pode ser alcançado de várias maneiras: 6 partições x 6 réplicas, ou 3 partições x 12 réplicas, por exemplo. É comum usar menos do que a capacidade total (por exemplo, um serviço de 3 réplicas e 3 partições faturado como 9 SUs). Consulte o gráfico [de combinações de partição e réplica](search-capacity-planning.md#chart) para combinações válidas.

A taxa de faturamento é por hora por SU. Cada camada tem uma taxa progressivamente maior. Níveis mais altos vêm com partições maiores e mais rápidas, e isso contribui para uma taxa geral mais alta por hora para esse nível. Você pode ver as taxas de cada nível na página [de detalhes de preços.](https://azure.microsoft.com/pricing/details/search/)

A maioria dos clientes coloca apenas uma parte da capacidade total online, mantendo o restante em reserva. Para o faturamento, o número de partições e réplicas que você traz on-line, calculado pela fórmula SU, determina o que você paga por hora.

## <a name="how-to-manage-costs"></a>Como gerenciar custos

As seguintes sugestões podem ajudá-lo a manter os custos no mínimo:

+ Crie todos os recursos na mesma região, ou em o menor número possível de regiões, para minimizar ou eliminar as taxas de largura de banda.

+ Consolide todos os serviços em um único grupo de recursos, como a Pesquisa Cognitiva do Azure, serviços cognitivos e quaisquer outros serviços do Azure usados em sua solução. No portal Azure, encontre o grupo de recursos e use os comandos **de Gerenciamento de Custos** para obter informações sobre os gastos reais e projetados.

+ Considere o Azure Web App para o seu aplicativo front-end para que as solicitações e respostas permaneçam dentro do limite do data center.

+ Dimensione para operações com uso intensivo de recursos, como indexação, e depois reajuste para baixo para cargas de trabalho regulares de consulta. Comece com a configuração mínima para a Pesquisa Cognitiva do Azure (uma SU composta por uma partição e uma réplica) e, em seguida, monitore a atividade do usuário para identificar padrões de uso que indicariam a necessidade de mais capacidade. Se houver um padrão previsível, você pode ser capaz de sincronizar escala com atividade (você precisaria escrever código para automatizar isso).

Além disso, visite [Billing e gerenciamento](https://docs.microsoft.com/azure/billing/billing-getting-started) de custos para ferramentas e recursos incorporados relacionados a gastos.

Fechar um serviço de busca temporariamente não é possível. Os recursos dedicados estão sempre operacionais, alocados para seu uso exclusivo durante toda a vida útil do seu serviço. A exclusão de um serviço é permanente e também exclui seus dados associados.

Em termos de serviço em si, a única maneira de reduzir sua conta é reduzir réplicas e partições a um nível que ainda fornece desempenho aceitável e [conformidade com SLA,](https://azure.microsoft.com/support/legal/sla/search/v1_0/)ou criar um serviço em um nível mais baixo (as taxas horárias S1 são menores do que as taxas S2 ou S3). Supondo que você prover seu serviço na extremidade inferior de suas projeções de carga, se você superar o serviço, você pode criar um segundo serviço de nível maior, reconstruir seus índices no segundo serviço e, em seguida, excluir o primeiro.

## <a name="how-to-evaluate-capacity-requirements"></a>Como avaliar os requisitos de capacidade

Na Pesquisa Cognitiva do Azure, a capacidade é estruturada como *réplicas* e *partições.*

+ Réplicas são instâncias do serviço de pesquisa. Cada réplica hospeda uma cópia balanceada de carga de um índice. Por exemplo, um serviço com seis réplicas tem seis cópias de cada índice carregado no serviço.

+ As partições armazenam índices e dividem automaticamente os dados pesquisáveis. Duas partições dividem seu índice ao meio, três partições dividem-no em terços, e assim por diante. Em termos de capacidade, o tamanho da *partição* é o principal diferencial entre os níveis.

> [!NOTE]
> Todos os níveis padrão e otimizado de armazenamento suportam [combinações flexíveis de réplicas e partições](search-capacity-planning.md#chart) para que você possa [otimizar seu sistema para velocidade ou armazenamento](search-performance-optimization.md) alterando o equilíbrio. O nível Basic oferece até três réplicas para alta disponibilidade, mas tem apenas uma partição. Os níveis gratuitos não fornecem recursos dedicados: os recursos de computação são compartilhados por vários assinantes.

### <a name="evaluating-capacity"></a>Avaliação da capacidade

A capacidade e os custos de execução do serviço andam lado a lado. Os níveis impõem limites a dois níveis: armazenamento e recursos. Você deve pensar em ambos, porque qualquer limite que você alcançar primeiro é o limite efetivo.

Os requisitos de negócios normalmente ditam o número de índices que você precisará. Por exemplo, você pode precisar de um índice global para um grande repositório de documentos. Ou você pode precisar de vários índices com base na região, aplicativo ou nicho de negócios.

Para determinar o tamanho de um índice, você precisa [compilar um](search-create-index-portal.md). Seu tamanho será baseado em dados importados e configuração de índice, como se você habilitar sugestionantes, filtragem e classificação. Para obter mais informações sobre o impacto da configuração no tamanho, consulte [Criar um índice básico ](search-what-is-an-index.md).

Para pesquisa completa de texto, a estrutura de dados primários é uma estrutura [de índice invertido,](https://en.wikipedia.org/wiki/Inverted_index) que tem características diferentes dos dados de origem. Para um índice invertido, tamanho e complexidade são determinados pelo conteúdo, não necessariamente pela quantidade de dados que você alimenta nele. Uma grande fonte de dados com alta redundância poderia resultar em um índice menor do que um conjunto de dados menor que o que contém conteúdo altamente variável. Portanto, raramente é possível inferir o tamanho do índice com base no tamanho do conjunto de dados original.

> [!NOTE] 
> Mesmo que estimar as necessidades futuras de índices e armazenamento possa parecer adivinhação, vale a pena fazer. Se a capacidade de um nível for muito baixa, você precisará prover um novo serviço em um nível mais alto e, em seguida, [recarregar seus índices](search-howto-reindex.md). Não há atualização no local de um serviço de um SKU para outro.
>

### <a name="estimate-with-the-free-tier"></a>Estimativa com o nível Livre

Uma abordagem para calcular a capacidade é iniciar com a camada Livre. Lembre-se que o serviço Gratuito oferece até três índices, 50 MB de armazenamento e 2 minutos de tempo de indexação. Pode ser desafiador estimar um tamanho de índice projetado com essas restrições, mas estes são os passos:

+ [Crie um serviço gratuito.](search-create-service-portal.md)
+ Prepare um pequeno conjunto de dados representativo.
+ [Construa um índice inicial no portal](search-create-index-portal.md) e observe seu tamanho. Recursos e atributos têm um impacto no armazenamento. Por exemplo, adicionar sugestionários (consultas de pesquisa como você digita) aumentará os requisitos de armazenamento. Usando o mesmo conjunto de dados, você pode tentar criar várias versões de um índice, com atributos diferentes em cada campo, para ver como os requisitos de armazenamento variam. Para obter mais informações, consulte ["Implicações de armazenamento" em Criar um índice básico](search-what-is-an-index.md#index-size).

Com uma estimativa aproximada em mãos, você pode dobrar esse valor para o orçamento de dois índices (desenvolvimento e produção) e, em seguida, escolher o seu nível de acordo.

### <a name="estimate-with-a-billable-tier"></a>Estimativa com um nível de faturamento

Os recursos dedicados podem acomodar tempos maiores de amostragem e processamento para estimativas mais realistas de quantidade, tamanho e volumes de consulta de índice durante o desenvolvimento. Alguns clientes entram com um nível de faturamento e depois reavaliam à medida que o projeto de desenvolvimento amadurece.

1. [Revise os limites de serviço em cada nível](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) para determinar se os níveis inferiores podem suportar o número de índices necessários. Nos níveis Básico, S1 e S2, os limites de índice são 15, 50 e 200, respectivamente. O nível otimizado de armazenamento tem um limite de 10 índices porque foi projetado para suportar um número baixo de índices muito grandes.

1. [Criar um serviço em uma camada faturável](search-create-service-portal.md):

    + Comece baixo, no Basic ou S1, se você não tiver certeza sobre a carga projetada.
    + Comece alto, em S2 ou mesmo S3, se você sabe que vai ter cargas de indexação e consulta em larga escala.
    + Comece com o Storage Optimized, em L1 ou L2, se você estiver indexando uma grande quantidade de dados e a carga de consulta é relativamente baixa, como em um aplicativo de negócios interno.

1. [Criar um índice inicial](search-create-index-portal.md) para determinar como a fonte de dados traduz para um índice. Essa é a única maneira de estimar o tamanho do índice.

1. [Monitorar armazenamento, limites de serviço, volume de consulta e latência](search-monitor-usage.md) no portal. O portal mostra consultas por segundo, consultas estranguladas e latência de pesquisa. Todos esses valores podem ajudá-lo a decidir se você selecionou o nível certo. 

O número e o tamanho do índice são igualmente importantes para sua análise. Isso ocorre porque os limites máximos são alcançados através da utilização total do armazenamento (partições) ou pelos limites máximos de recursos (índices, indexadores e assim por diante), o que vier primeiro. O portal ajuda a manter o controle de ambos, mostrando o uso atual e os limites máximos lado a lado na página Visão geral.

> [!NOTE]
> Os requisitos de armazenamento podem ser inflados se os documentos contiverem dados estranhos. Idealmente, os documentos contêm apenas os dados necessários para a experiência de pesquisa. Os dados binários não são pesquisáveis e devem ser armazenados separadamente (talvez em uma tabela Azure ou armazenamento blob). Um campo deve então ser adicionado no índice para manter uma referência de URL aos dados externos. O tamanho máximo de um documento individual é de 16 MB (ou menos se você estiver carregando vários documentos em uma única solicitação). Para obter mais informações, consulte [limites de serviço na Pesquisa Cognitiva do Azure](search-limits-quotas-capacity.md).
>

**Considerações sobre volume de consultas**

As consultas por segundo (QPS) são uma métrica importante durante a sintonia de desempenho, mas geralmente é apenas uma consideração de nível se você espera um alto volume de consulta no início.

Os níveis Padrão podem fornecer um equilíbrio de réplicas e partições. Você pode aumentar a reviravolta na consulta adicionando réplicas para balanceamento de carga ou adicionar partições para processamento paralelo. Em seguida, você pode sintonizar o desempenho depois que o serviço for provisionado.

Se você espera altos volumes de consulta sustentados desde o início, você deve considerar níveis Padrão mais altos, apoiados por hardware mais poderoso. Em seguida, você pode tirar partições e réplicas off-line, ou até mesmo mudar para um serviço de nível inferior, se esses volumes de consulta não ocorrerem. Para obter mais informações sobre como calcular o throughput da consulta, consulte [o desempenho e otimização da Pesquisa Cognitiva do Azure](search-performance-optimization.md).

Os níveis otimizados de armazenamento são úteis para grandes cargas de trabalho de dados, suportando mais armazenamento de índice disponível em geral para quando os requisitos de latência de consulta são menos importantes. Você ainda deve usar réplicas adicionais para balanceamento de carga e partições adicionais para processamento paralelo. Em seguida, você pode sintonizar o desempenho depois que o serviço for provisionado.

**Contratos em nível de serviço**

Os recursos de nível livre e visualização não fornecem [SLAs (Service-level Agreements, contratos de nível de serviço).](https://azure.microsoft.com/support/legal/sla/search/v1_0/) Para todas as camadas faturáveis, os SLAs entram em vigor quando você provisiona redundância suficiente para o serviço. Você precisa ter duas ou mais réplicas para consultar (ler) SLAs. Você precisa ter três ou mais réplicas para consultas e SLAs de indexação (leitura-gravação). O número de partições não afeta slas.

## <a name="tips-for-tier-evaluation"></a>Dicas para avaliação de camada

+ Permitir que as métricas sejam construídas em torno de consultas e coletar dados em torno de padrões de uso (consultas durante o horário comercial, indexação durante horários de pico). Use esses dados para informar as decisões de provisionamento de serviços. Embora não seja prático em uma cadência de hora em hora ou diária, você pode ajustar dinamicamente partições e recursos para acomodar mudanças planejadas nos volumes de consulta. Você também pode acomodar mudanças não planejadas, mas sustentadas se os níveis aguentarem tempo suficiente para justificar a tomada de ação.

+ Lembre-se que a única desvantagem do provisionamento é que você pode ter que derrubar um serviço se os requisitos reais forem maiores do que suas previsões. Para evitar interrupções no serviço, você criaria um novo serviço em um nível mais alto e o executaria lado a lado até que todos os aplicativos e solicitações direcionassem o novo ponto final.

## <a name="next-steps"></a>Próximas etapas

Comece com um nível Livre e construa um índice inicial usando um subconjunto de seus dados para entender suas características. A estrutura de dados no Azure Cognitive Search é uma estrutura de índice invertido. O tamanho e a complexidade de um índice invertido são determinados pelo conteúdo. Lembre-se de que o conteúdo altamente redundante tende a resultar em um índice menor que o conteúdo altamente irregular. Assim, as características de conteúdo em vez do tamanho do conjunto de dados determinam os requisitos de armazenamento do índice.

Depois de ter uma estimativa inicial do tamanho do seu índice, [preditor de um serviço faturado](search-create-service-portal.md) em um dos níveis discutidos neste artigo: Básico, Padrão ou Otimizado de Armazenamento. Relaxe quaisquer restrições artificiais no dimensionamento de dados e [reconstrua seu índice](search-howto-reindex.md) para incluir todos os dados que você deseja ser pesquisados.

[Alocar partições e réplicas](search-capacity-planning.md) conforme necessário para obter o desempenho e escalabilidade que você precisa.

Se o desempenho e a capacidade estão bem, você está feito. Caso contrário, crie novamente um serviço de pesquisa em uma outra camada mais estreitamente alinhada a suas necessidades.

> [!NOTE]
> Em caso de dúvidas, poste no [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [entre em contato com o suporte do Azure](https://azure.microsoft.com/support/options/).
