---
title: Estimar custos usando o planejador de capacidade azure Cosmos DB
description: O planejador de capacidade azure Cosmos DB permite estimar o throughput (RU/s) necessário e o custo da sua carga de trabalho. Este artigo descreve como usar a nova versão do planejador de capacidade para estimar o throughput e o custo necessários.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: f10ace47f774e31b586f7736f5fb8e5dfea0c948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68707624"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Estimar RU/s usando o planejador de capacidade Azure Cosmos DB

Configurar seus bancos de dados e contêineres do Azure Cosmos com a quantidade certa de throughput provisionado, ou [Unidades de Solicitação (RU/s)](request-units.md), para sua carga de trabalho é essencial para otimizar custos e desempenho. Este artigo descreve como usar o planejador [de capacidade](https://cosmos.azure.com/capacitycalculator/) azure Cosmos DB para obter uma estimativa dos RU/s necessários e do custo de sua carga de trabalho. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Como estimar throughput e custo com o planejador de capacidade azure Cosmos DB

O planejador de capacidade pode ser usado em dois modos.

|**Modo**  |**Descrição**  |
|---------|---------|
|Basic|Fornece uma estimativa rápida e de alto nível de custos e RU/s. Este modo assume as configurações padrão do Azure Cosmos DB para política de indexação, consistência e outros parâmetros. <br/><br/>Use o modo básico para uma estimativa rápida e de alto nível quando você estiver avaliando uma carga de trabalho potencial para executar no Azure Cosmos DB.|
|Avançado|Fornece uma estimativa de custos e RU/s mais detalhada, com a capacidade de ajustar configurações adicionais — política de indexação, nível de consistência e outros parâmetros que afetam o custo e o throughput. <br/><br/>Use o modo avançado quando estiver estimando RU/s para um novo projeto ou deseja uma estimativa mais detalhada. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>Estimativa de throughput provisionado e custo usando o modo básico
Para obter uma estimativa rápida da sua carga de trabalho usando o modo básico, navegue até o [planejador de capacidade](https://cosmos.azure.com/capacitycalculator/). Insira os seguintes parâmetros com base na sua carga de trabalho: 

|**Entrada**  |**Descrição**  |
|---------|---------|
|Número de regiões|O Microsoft Azure Cosmos DB está disponível em todas as regiões do Azure por padrão. Selecione o número de regiões necessárias para sua carga de trabalho. Você pode associar qualquer número de regiões à sua conta cosmos. Consulte [a distribuição global](distribute-data-globally.md) no Azure Cosmos DB para obter mais detalhes.|
|Gravações de várias regiões|Se você habilitar [gravações em várias regiões,](distribute-data-globally.md#key-benefits-of-global-distribution)seu aplicativo poderá ler e gravar em qualquer região do Azure. Se você desativar gravações em várias regiões, seu aplicativo poderá gravar dados em uma única região. <br/><br/> Habilite gravações em várias regiões se você espera ter uma carga de trabalho ativa ativa que exija gravações de baixa latência em diferentes regiões. Por exemplo, uma carga de trabalho iOT que grava dados para o banco de dados em grandes volumes em diferentes regiões. <br/><br/> A gravação multi-região garante 99,999% de disponibilidade de leitura e gravação. As gravações de várias regiões requerem mais throughput quando comparadas às regiões de gravação única. Para saber mais, veja [como as RUs são diferentes para um artigo de regiões de gravação única e múltipla.](optimize-cost-regions.md)|
|Total de dados armazenados (por região)|Total de dados estimados armazenados em GB em uma única região.|
|Tamanho do item|O tamanho estimado do item de dados (por exemplo, documento), variando de 1 KB a 2 MB. |
|Leituras/seg por região|Número de leituras esperadas por segundo. |
|Gravações/seg por região|Número de gravações esperadas por segundo. |

Depois de preencher os detalhes necessários, selecione **Calcular**. A guia **Estimativa de Custo** mostra o custo total para armazenamento e throughput provisionado. Você pode expandir o link **Mostrar detalhes** nesta guia para obter a divisão do throughput necessário para solicitações de leitura e gravação. Cada vez que você alterar o valor de qualquer campo, **selecione Calcular** para recalcular o custo estimado. 

![Modo básico de planejador de capacidade](./media/estimate-ru-with-capacity-planner/basic-mode.png)

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>Estimativa de throughput provisionado e custo usando o modo avançado

O modo avançado permite fornecer mais configurações que impactam a estimativa ru/s. Para usar essa opção, navegue até o [planejador de capacidade](https://cosmos.azure.com/capacitycalculator/) e faça login na ferramenta com uma conta que você usa para o Azure. A opção de login está disponível no canto direito. 

Depois de fazer login, você pode ver campos adicionais em comparação com os campos no modo básico. Digite os parâmetros adicionais com base na sua carga de trabalho. 

|**Entrada**  |**Descrição**  |
|---------|---------|
|API|O Azure Cosmos DB é um serviço multi-modelo e multi-API. Para novas cargas de trabalho, selecione API SQL (Core). |
|Número de regiões|O Microsoft Azure Cosmos DB está disponível em todas as regiões do Azure por padrão. Selecione o número de regiões necessárias para sua carga de trabalho. Você pode associar qualquer número de regiões à sua conta cosmos. Consulte [a distribuição global](distribute-data-globally.md) no Azure Cosmos DB para obter mais detalhes.|
|Gravações de várias regiões|Se você habilitar [gravações em várias regiões,](distribute-data-globally.md#key-benefits-of-global-distribution)seu aplicativo poderá ler e gravar em qualquer região do Azure. Se você desativar gravações em várias regiões, seu aplicativo poderá gravar dados em uma única região. <br/><br/> Habilite gravações em várias regiões se você espera ter uma carga de trabalho ativa ativa que exija gravações de baixa latência em diferentes regiões. Por exemplo, uma carga de trabalho iOT que grava dados para o banco de dados em grandes volumes em diferentes regiões. <br/><br/> A gravação multi-região garante 99,999% de disponibilidade de leitura e gravação. As gravações de várias regiões requerem mais throughput quando comparadas às regiões de gravação única. Para saber mais, veja [como as RUs são diferentes para um artigo de regiões de gravação única e múltipla.](optimize-cost-regions.md)|
|Consistência padrão|O Azure Cosmos DB suporta 5 níveis de consistência, para permitir que os desenvolvedores equilibrem a troca entre consistência, disponibilidade e compensações de latência. Para saber mais, consulte o artigo [dos níveis de consistência.](consistency-levels.md) <br/><br/> Por padrão, o Azure Cosmos DB usa a consistência da sessão, o que garante a capacidade de ler suas próprias gravações em uma sessão. <br/><br/> Escolher o obsoleto forte ou limitado exigirá o dobro dos RU/s necessários para leituras, quando comparado com a sessão, prefixo consistente e consistência eventual. A forte consistência com gravações de várias regiões não é suportada e será automaticamente padrão para gravações de uma região com forte consistência. |
|Política de indexação|Por padrão, o Azure Cosmos DB [indexa todas as propriedades](index-policy.md) em todos os itens para consultas flexíveis e eficientes (mapas para a política de indexação **automática).** <br/><br/> Se você **escolher,** nenhuma das propriedades é indexada. Isso resulta na menor taxa de RU para gravações. Selecione **a** política se você espera fazer apenas [leituras de pontos](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) (análises de valor-chave) e/ou gravações, e sem consultas. <br/><br/> A política de indexação personalizada permite incluir ou excluir propriedades específicas do índice para menor throughput e armazenamento de gravação. Para saber mais, consulte [artigos de política de indexação](index-overview.md) e [indexação de amostras.](how-to-manage-indexing-policy.md#indexing-policy-examples)|
|Total de dados armazenados (por região)|Total de dados estimados armazenados em GB em uma única região.|
|Modo de trabalho de trabalho|Selecione **Steady** se o volume de carga de trabalho for constante. <br/><br/> Selecione **Variável** se o volume de carga de trabalho for alterado ao longo do tempo.  Por exemplo, durante um dia específico ou um mês. <br/><br/> As seguintes configurações estão disponíveis se você escolher a opção de carga de trabalho variável:<ul><li>Porcentagem de tempo no pico: Porcentagem de tempo em um mês em que sua carga de trabalho requer pico (mais alto) de throughput. <br/><br/> Por exemplo, se você tem uma carga horária que tem alta atividade durante as 9h às 18h no horário comercial da semana, então a porcentagem de tempo no pico é: 45 horas no pico / 730 horas / mês = ~6%.<br/><br/></li><li>Leituras/seg por região no pico - Número de leituras esperadas por segundo no pico.</li><li>Gravações/seg por região no pico – Número de gravações esperadas por segundo no pico.</li><li>Leituras/seg por região fora do pico – Número de leituras esperadas por segundo durante o pico.</li><li>Gravações/seg por região fora do pico – Número de gravações esperadas por segundo durante o pico.</li></ul>Com intervalos de pico e fora do pico, você pode otimizar seu [custo, dimensionando programáticamente seu throughput provisionado](set-throughput.md#update-throughput-on-a-database-or-a-container) para cima e para baixo de acordo.|
|Tamanho do item|O tamanho do item de dados (por exemplo, documento), variando de 1 KB a 2 MB. <br/><br/>Você também pode enviar um documento **de amostra (JSON)** para uma estimativa mais precisa.<br/><br/>Se sua carga de trabalho tiver vários tipos de itens (com diferentes conteúdos JSON) no mesmo contêiner, você pode carregar vários documentos JSON e obter a estimativa. Use o botão **Adicionar novo item** para adicionar vários documentos JSON de amostra.|

Você também pode usar o botão **Save Estimate** para baixar um arquivo CSV contendo a estimativa atual. 

![Modo avançado de planejador de capacidade](./media/estimate-ru-with-capacity-planner/advanced-mode.png)

Os preços mostrados no planejador de capacidade azure Cosmos DB são estimativas baseadas nas taxas de preços públicas para throughput e armazenamento. Todos os preços são mostrados em dólares americanos. Consulte a [página de preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) para ver todas as tarifas por região.  

## <a name="estimating-throughput-for-queries"></a>Estimando o throughput para consultas

A calculadora de capacidade do Azure Cosmos assume leituras de ponto (uma leitura de um único item, por exemplo, documento, por ID e valor-chave de partição) e grava para a carga de trabalho. Para estimar o throughput necessário para consultas, execute sua consulta em um conjunto de dados representativos em um contêiner Cosmos e [obtenha a taxa RU](find-request-unit-charge.md). Multiplique a taxa RU pelo número de consultas que você prevê executar por segundo para obter o total de RU/s necessário. 

Por exemplo, se a sua ``SELECT * FROM c WHERE c.id = 'Alice'`` carga de trabalho exigir uma consulta, que é executada 100 vezes por segundo, e a carga RU da consulta é de 10 RUs, você precisará de 100 consultas / se * 10 RU / consulta = 1000 RU/s no total para atender a essas solicitações. Adicione esses RU/s aos RU/s necessários para quaisquer leituras ou gravações acontecendo em sua carga de trabalho.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [o modelo de preços do Azure Cosmos DB](how-pricing-works.md).
* Crie uma nova [conta cosmos, banco de dados e contêiner](create-cosmosdb-resources-portal.md).
* Saiba como otimizar o [custo de throughput provisionado](optimize-cost-throughput.md).
* Aprenda a [otimizar custos com capacidade reservada.](cosmos-db-reserved-capacity.md)

