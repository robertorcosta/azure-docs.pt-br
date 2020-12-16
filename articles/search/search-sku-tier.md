---
title: Escolher um tipo de preço
titleSuffix: Azure Cognitive Search
description: 'Os Pesquisa Cognitiva do Azure podem ser provisionados nessas camadas: gratuita, básica e Standard, e Standard está disponível em várias configurações de recursos e níveis de capacidade.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 062bd41b0803cbb08f74fbcbcebb89bbddeb0d45
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97559796"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Escolha um tipo de preço para o Azure Pesquisa Cognitiva

Ao [criar um serviço de pesquisa](search-create-service-portal.md), você escolhe um tipo de preço que é fixo para o tempo de vida do serviço. A camada selecionada determina:

+ Quantidade de índices e outros objetos que você pode criar (limites máximos)
+ Tamanho e velocidade de partições (armazenamento físico)
+ Taxa Faturável, um custo mensal fixo, mas também um custo incremental se você adicionar partições ou réplicas

Além disso, alguns [recursos premium](#premium-features) são fornecidos com requisitos de camada.

## <a name="tier-descriptions"></a>Descrições da camada

As camadas incluem **gratuito**, **básico**, **Standard** e **otimizado para armazenamento**. O padrão e o armazenamento otimizado estão disponíveis com várias configurações e capacidades.

A captura de tela a seguir de portal do Azure mostra as camadas disponíveis, menos preços (que você pode encontrar no portal e na [página de preços](https://azure.microsoft.com/pricing/details/search/). 

![Tipos de preço do Azure Pesquisa Cognitiva](media/search-sku-tier/tiers.png "Tipos de preço do Azure Pesquisa Cognitiva")

**Gratuito** cria um serviço de pesquisa limitado para projetos menores, como a execução de tutoriais e exemplos de código. Internamente, as réplicas e as partições são compartilhadas entre vários assinantes. Você não pode dimensionar um serviço gratuito ou executar cargas de trabalho significativas.

**Básico** e **Standard** são as camadas faturáveis usadas com mais frequência, com **padrão** sendo o padrão. Com recursos dedicados sob seu controle, você pode implantar projetos maiores, otimizar o desempenho e aumentar a capacidade.

Algumas camadas são otimizadas para determinados tipos de trabalho. Por exemplo, a **alta densidade padrão (S3 HD)** é um *modo de hospedagem* para S3, em que o hardware subjacente é otimizado para um grande número de índices menores e destina-se a cenários de multilocação. S3 HD tem o mesmo encargo por unidade que S3, mas o hardware é otimizado para leituras rápidas de arquivos em um grande número de índices menores.

As camadas de **armazenamento otimizado** oferecem maior capacidade de armazenamento a um preço menor por TB do que as camadas padrão. A compensação primária é maior latência de consulta, que você deve validar para os requisitos de aplicativo específicos. Para saber mais sobre as considerações de desempenho dessa camada, consulte [Considerações sobre desempenho e otimização](search-performance-optimization.md).

Você pode saber mais sobre as várias camadas na [página de preços](https://azure.microsoft.com/pricing/details/search/), no artigo limites de [serviço no Azure pesquisa cognitiva](search-limits-quotas-capacity.md) e na página do portal quando estiver provisionando um serviço.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Disponibilidade de recursos por camada

A maioria dos recursos está disponível em todas as camadas, incluindo a camada gratuita. Em alguns casos, a camada escolhida afetará sua capacidade de implementar um recurso. A tabela a seguir descreve as restrições de recurso relacionadas à camada de serviço.

| Recurso | Limitações |
|---------|-------------|
| [indexadores](search-indexer-overview.md) | Os indexadores não estão disponíveis no S3 HD.  |
| [Enriquecimento de IA](search-security-manage-encryption-keys.md) | É executado na camada gratuita, mas não é recomendado. |
| [Chaves de criptografia gerenciadas pelo cliente](search-security-manage-encryption-keys.md) | Não disponível na camada gratuita. |
| [Acesso de firewall IP](service-configure-firewall.md) | Não disponível na camada gratuita. |
| [Ponto de extremidade privado (integração com o link privado do Azure)](service-create-private-endpoint.md) | Para conexões de entrada para um serviço de pesquisa, não disponível na camada gratuita. Para conexões de saída por indexadores para outros recursos do Azure, não disponível em alta ou S3 HD. Para indexadores que usam habilidades, não disponível em gratuito, básico, S1 ou S3 HD.|

Recursos com uso intensivo de recursos podem não funcionar bem, a menos que você forneça capacidade suficiente. Por exemplo, o [enriquecimento de ia](cognitive-search-concept-intro.md) tem habilidades de longa execução que atingirão o tempo limite em um serviço gratuito, a menos que o conjunto de informações seja pequeno.

## <a name="billable-events"></a>Eventos faturáveis

Uma solução criada no Azure Pesquisa Cognitiva pode gerar custos das seguintes maneiras:

+ [Custo do próprio serviço](#service-costs) , executando 24x7, na configuração mínima (uma partição e réplica), na taxa base. Você pode considerar isso como o custo fixo da execução do serviço.

+ Adição de capacidade (réplicas ou partições), em que os custos aumentam em incrementos da taxa Faturável

+ Encargos de largura de banda (transferência de dados de saída)

+ Serviços de complemento necessários para recursos ou recursos específicos:

  + Enriquecimento de ia (requer [Serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/))
  + loja de conhecimento (requer o [armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/))
  + enriquecimento incremental (requer o [armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/), aplica-se ao enriquecimento de ia)
  + chaves gerenciadas pelo cliente e criptografia dupla (requer [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/))
  + pontos de extremidade privados para um modelo de acesso sem Internet (requer o [link privado do Azure](https://azure.microsoft.com/pricing/details/private-link/))

### <a name="service-costs"></a>Custos de serviço

Ao contrário das máquinas virtuais ou outros recursos que podem ser "pausados" para evitar cobranças, um serviço de Pesquisa Cognitiva do Azure está sempre disponível em hardware dedicado para seu uso exclusivo. Dessa forma, a criação de um serviço é um evento cobrável que começa quando você cria o serviço e termina quando você exclui o serviço. 

O encargo mínimo é a primeira unidade de pesquisa (uma réplica x uma partição) na taxa faturável. Esse mínimo é fixo durante o tempo de vida do serviço, pois o serviço não pode ser executado em nada menor que essa configuração. Além do mínimo, você pode adicionar réplicas e partições independentemente umas das outras. Aumentos incrementais na capacidade por meio de réplicas e partições aumentarão sua fatura com base na fórmula a seguir: [(réplicas x partições x taxa)](#search-units), em que a taxa cobrada depende do tipo de preço selecionado.

Quando estiver estimando o custo de uma solução de pesquisa, tenha em mente que os preços e a capacidade não são lineares. (Dobrar a capacidade mais do que o dobro do custo.) Para obter um exemplo de como a fórmula funciona, consulte [como alocar réplicas e partições](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Cobranças de largura de banda

O uso de [indexadores](search-indexer-overview.md) pode afetar a cobrança se a fonte de dados do Azure estiver em uma região diferente da pesquisa cognitiva do Azure. Nesse cenário, esse é um custo para mover dados de saída da fonte de dados do Azure para o Azure Pesquisa Cognitiva. 

Você pode eliminar os encargos de saída de dados inteiramente se criar o serviço de Pesquisa Cognitiva do Azure na mesma região que seus dados. Aqui estão algumas informações da [página de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/):

+ A Microsoft não cobra por nenhum dado de entrada para nenhum serviço no Azure.
+ Não há nenhuma cobrança de dados de saída do Azure Pesquisa Cognitiva. Por exemplo, se o serviço de pesquisa estiver no oeste dos EUA e um aplicativo Web do Azure estiver no leste dos EUA, a Microsoft não cobrará pelas cargas de resposta de consulta originadas no oeste dos EUA.
+ Em soluções de vários serviços, não há nenhum encargo para dados que cruzam a conexão quando todos os serviços estão na mesma região.

Os encargos se aplicam a dados de saída se os serviços estiverem em regiões diferentes. Esses encargos não são, na verdade, parte da sua conta de Pesquisa Cognitiva do Azure. Eles são mencionados aqui porque, se você estiver usando os dados ou indexadores aprimorados de ia para efetuar pull de dados de regiões diferentes, verá os custos refletidos na sua fatura geral.

### <a name="ai-enrichment-with-cognitive-services"></a>Enriquecimento de ia com serviços cognitivas

Para [aprimorar o ia](cognitive-search-concept-intro.md), você deve planejar [anexar um recurso de serviços cognitivas do Azure cobrável](cognitive-search-attach-cognitive-services.md), na mesma região que o Azure pesquisa cognitiva, no tipo de preço S0 para processamento pago conforme o uso. Não há nenhum custo fixo associado à anexação de serviços cognitivas. Você paga apenas pelo processamento de que precisa.

| Operação | Impacto de cobrança |
|-----------|----------------|
| Quebra de documentos, extração de texto | Gratuita |
| Quebra de documento, extração de imagem | Cobrado de acordo com o número de imagens extraídas dos seus documentos. Em uma [configuração de indexador](/rest/api/searchservice/create-indexer#indexer-parameters), **imageaction** é o parâmetro que dispara a extração de imagem. Se **imageaction** for definido como "None" (o padrão), você não será cobrado pela extração de imagem. A taxa de extração de imagem está documentada na página de [detalhes de preços](https://azure.microsoft.com/pricing/details/search/) do Azure pesquisa cognitiva.|
| [Habilidades cognitivas internas](cognitive-search-predefined-skills.md) | Cobrado na mesma taxa que se você executou a tarefa usando serviços cognitivas diretamente. |
| Habilidades personalizadas | Uma habilidade personalizada é A funcionalidade que você fornece. O custo de usar uma habilidade personalizada depende totalmente de se o código personalizado está chamando outros serviços medidos. |

O recurso de [enriquecimento incremental (versão prévia)](cognitive-search-incremental-indexing-conceptual.md) permite que você forneça um cache que permite que o indexador seja mais eficiente na execução apenas das habilidades cognitivas necessárias se você modificar o seu Skill no futuro, economizando tempo e dinheiro.

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Fórmula de cobrança (R x P = SU)

O conceito de cobrança mais importante a ser compreendido para as operações de Pesquisa Cognitiva do Azure é a Su ( *unidade de pesquisa* ). Como o Azure Pesquisa Cognitiva depende de réplicas e partições para indexação e consultas, não faz sentido cobrar por apenas um ou outro. Em vez disso, a cobrança baseia-se em uma combinação de ambos.

SU é o produto das *réplicas* e *partições* usadas por um serviço: **(R x P = su)**.

Cada serviço começa com uma UA (uma réplica multiplicada por uma partição) como o mínimo. O máximo de qualquer serviço é o SUs 36. Esse máximo pode ser alcançado de várias maneiras: 6 partições x 6 réplicas ou 3 partições x 12 réplicas, por exemplo. É comum usar a capacidade menor que o total (por exemplo, um serviço de 3 réplicas, de três partições cobrado como 9 SUs). Consulte o gráfico de [combinações de partição e réplica](search-capacity-planning.md#chart) para obter combinações válidas.

A taxa de cobrança é por hora por SU. Cada camada tem uma taxa progressivamente maior. As camadas mais altas vêm com partições maiores e speediers, e isso contribui para uma taxa geral maior por hora para essa camada. Você pode exibir as taxas de cada camada na página de [detalhes de preços](https://azure.microsoft.com/pricing/details/search/) .

A maioria dos clientes coloca apenas uma parte da capacidade total online, mantendo o restante em reserva. Para cobrança, o número de partições e réplicas que você coloca online, calculadas pela fórmula SU, determina o que você paga por hora.

## <a name="next-steps"></a>Próximas etapas

O gerenciamento de custos é uma parte integrante do planejamento de capacidade. Como uma próxima etapa, continue com o seguinte artigo para obter orientação sobre como estimar a capacidade e gerenciar os custos.

> [!div class="nextstepaction"]
> [Como gerenciar custos e estimar a capacidade no Azure Pesquisa Cognitiva](search-sku-manage-costs.md)