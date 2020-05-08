---
title: Crie contêineres e bancos de dados do Azure Cosmos na taxa de transferência provisionada de dimensionamento automático.
description: Saiba mais sobre os benefícios, casos de uso e como provisionar bancos de dados e contêineres do Azure Cosmos na taxa de transferência provisionada de dimensionamento automático.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 81a13dcb7955a7d46f485416bf9b7e4e7be4d9ac
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791707"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-provisioned-throughput"></a>Criar contêineres e bancos de dados do Azure cosmos com taxa de transferência provisionada de dimensionamento automático

Azure Cosmos DB permite que você configure seus contêineres com taxa de transferência (manual) provisionada padrão ou taxa de transferência provisionada de dimensionamento automático. Este artigo descreve os benefícios e casos de uso do dimensionamento automático.

> [!NOTE]
> Você pode [habilitar o dimensionamento automático somente para novos bancos de dados e contêineres](#create-db-container-autoscale) . Ele não está disponível para contêineres e bancos de dados existentes.

Além do provisionamento padrão de taxa de transferência, agora você pode configurar contêineres Cosmos do Azure com taxa de transferência provisionada de dimensionamento automático. Os contêineres e os bancos de dados configurados na taxa de transferência provisionada prodimensional irão **dimensionar automaticamente e de forma instantânea a taxa de transferência provisionada com base nas necessidades do aplicativo sem afetar a disponibilidade, a latência, a taxa de transferência ou o desempenho da carga de trabalho globalmente.**

Ao configurar contêineres e bancos de dados em dimensionamento automático, você precisa especificar a `Tmax` taxa de transferência máxima que não deve ser excedida. Os contêineres podem dimensionar sua taxa de `0.1*Tmax < T < Tmax`transferência para que. Em outras palavras, os contêineres e os bancos de dados são dimensionados instantaneamente com base nas necessidades de carga de trabalho, de até 10% do valor máximo de taxa de transferência que você configurou até o valor máximo de taxa de transferência configurado. Depois de configurar o dimensionamento automático, você pode alterar`Tmax`a configuração de taxa de transferência máxima () em um banco de dados ou contêiner em qualquer ponto no tempo. Com a opção de dimensionamento automático, a taxa de transferência mínima de 400 RU/s por contêiner ou banco de dados não é mais aplicável.

Para a taxa de transferência máxima especificada no contêiner ou no banco de dados, o sistema permite operar dentro do limite de armazenamento calculado. Se o limite de armazenamento for excedido, a taxa de transferência máxima será ajustada automaticamente para um valor mais alto. Ao usar a taxa de transferência no nível do banco de dados com dimensionamento automático, o número de `0.001*TMax`contêineres permitidos em um banco de dados é calculado como: Por exemplo, se você provisionar 20.000 dimensionamento automático RU/s, o banco de dados poderá ter 20 contêineres.

## <a name="benefits-of-autoscale-provisioned-throughput"></a><a id="autoscale-benefits"></a>Benefícios da taxa de transferência provisionada de dimensionamento automático

Os contêineres Cosmos do Azure configurados com dimensionamento automático têm os seguintes benefícios:

* **Simples:** Os contêineres com dimensionamento automático removem a complexidade para gerenciar a taxa de transferência provisionada (RUs) e a capacidade manualmente para vários contêineres.

* **Escalonável:** Os contêineres com dimensionamento automático dimensionam diretamente a capacidade de taxa de transferência provisionada conforme necessário. Não há interrupção nas conexões de cliente, aplicativos e elas não afetam nenhum SLA existente.

* Econômico **:** Quando você usa contêineres configurados com dimensionamento automático, você paga apenas pelos recursos de que suas cargas de trabalho precisam por hora.

* **Altamente disponível:** Os contêineres com dimensionamento automático usam o mesmo back-end distribuído globalmente, tolerante a falhas e altamente disponível para garantir a durabilidade dos dados e a alta disponibilidade.

## <a name="use-cases-of-autoscale-provisioned-throughput"></a><a id="autoscale-usecases"></a>Casos de uso de taxa de transferência provisionada prodimensionamento

Os casos de uso para contêineres Cosmos do Azure configurados com dimensionamento automático incluem:

* **Cargas de trabalho variáveis:** Quando você estiver executando um aplicativo levemente usado com pico de uso de 1 hora a várias horas algumas vezes por dia ou várias vezes por ano. Os exemplos incluem aplicativos para recursos humanos, orçamento e relatórios operacionais. Para esses cenários, os contêineres configurados com dimensionamento automático podem ser usados e você não precisa mais provisionar manualmente para a capacidade de pico ou média.

* **Cargas de trabalho imprevisíveis:** Quando você estiver executando cargas de trabalho em que há uso de banco de dados ao longo do dia, mas também aos picos de atividade que são difíceis de prever. Um exemplo inclui um site de tráfego que vê um surto de atividade quando a previsão do tempo muda. Os contêineres configurados com dimensionamento automático ajustam a capacidade para atender às necessidades da carga de pico do aplicativo e de reduzir verticalmente quando o surto da atividade terminar.

* **Novos aplicativos:** Se você estiver implantando um novo aplicativo e não tiver certeza quanto à quantidade de produtividade provisionada (ou seja, quantos RUs) você precisa. Com os contêineres configurados com dimensionamento automático, você pode dimensionar automaticamente para as necessidades de capacidade e os requisitos de seu aplicativo.

* **Aplicativos usados com pouca frequência:** Se você tiver um aplicativo que só é usado por algumas horas várias vezes por dia ou semana ou mês, como um aplicativo de baixo volume/Web/site de blog.

* **Bancos de dados de desenvolvimento e teste:** Se você tiver desenvolvedores que usam contêineres durante horas de trabalho, mas não precisarem deles em noites ou fins de semana. Com os contêineres configurados com dimensionamento automático, eles são reduzidos para um mínimo quando não estão em uso.

* **Cargas de trabalho/consultas de produção agendadas:** Quando você tem uma série de solicitações/operações/consultas agendadas em um único contêiner e, se houver períodos ociosos em que você deseja executar com uma taxa de transferência baixa absoluta, agora você pode fazer isso facilmente. Quando uma consulta/solicitação agendada for enviada para um contêiner configurado com dimensionamento automático, ela será dimensionada verticalmente de forma automática tanto quanto necessário e executar a operação.

As soluções para os problemas anteriores não só exigem uma enorme quantidade de tempo na implementação, mas também introduzem complexidade na configuração ou no código, e muitas vezes exigem intervenção manual para solucioná-los. O dimensionamento automático permite os cenários acima prontos para uso, para que você não precise mais se preocupar com esses problemas.

## <a name="comparison--standard-manual-vs-autoscale-provisioned-throughput"></a>Comparação – padrão (manual) versus taxa de transferência provisionada de dimensionamento automático

|  | Contêineres configurados com taxa de transferência padrão provisionada  | Contêineres configurados com taxa de transferência provisionada prodimensional |
|---------|---------|---------|
| **Taxa de transferência provisionada** | Provisionado manualmente. | Escalado de imediato e instantâneo com base nos padrões de uso da carga de trabalho. |
| **Taxa-limitação de solicitações/operações (429)**  | Pode acontecer se o consumo exceder a capacidade provisionada. | Não ocorrerá se a taxa de transferência consumida estiver dentro da taxa de transferência máxima que você escolher com dimensionamento automático.   |
| **planejamento de capacidade** |  Você precisa fazer um planejamento de capacidade inicial e provisionar a taxa de transferência de que precisa. |    Você não precisa se preocupar com o planejamento de capacidade. O sistema cuida automaticamente do planejamento de capacidade e do gerenciamento de capacidade. |
| **Preços** | O provisionamento manual de RU/s por hora. | Para contas de região de gravação única, você paga pela taxa de transferência usada por hora, usando a tarifa de autoescala RU/s por hora. <br/><br/>Para contas com várias regiões de gravação, não há custo adicional para o dimensionamento automático. Você paga pela taxa de transferência usada por hora usando a mesma tarifa de RU/s de vários mestres por hora. |
| **Mais adequado para tipos de carga de trabalho** |  Cargas de trabalho previsíveis e estáveis|   Cargas de trabalho imprevisíveis e variáveis  |

## <a name="create-a-database-or-a-container-with-autoscale"></a><a id="create-db-container-autoscale"></a>Criar um banco de dados ou um contêiner com dimensionamento automático

Você pode configurar o dimensionamento automático para novos bancos de dados ou contêineres ao criá-los por meio do portal do Azure. Use as etapas a seguir para criar um novo banco de dados ou contêiner, habilitar o dimensionamento automático e especificar a taxa de transferência máxima (RU/s).

1. Entre no [portal do Azure](https://portal.azure.com) ou no [Azure Cosmos DB Explorer.](https://cosmos.azure.com/)

1. Navegue até sua conta do Azure Cosmos DB e abra a guia **Data Explorer** .

1. Selecione **novo contêiner.** Insira um nome para seu banco de dados, contêiner e uma chave de partição. Em **taxa de transferência**, selecione a opção **autoescala** e escolha a taxa de transferência máxima (ru/s) que o banco de dados ou o contêiner não pode exceder ao usar a opção autoescala.

   ![Criando um contêiner e configurando a taxa de transferência de autoescala](./media/provision-throughput-autoscale/create-container-autoscale-mode.png)

1. Selecione **OK**.

Você pode criar um banco de dados de produtividade compartilhado com dimensionamento automático selecionando a opção de **taxa de transferência de banco de dados**

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a>Taxa de transferência e limites de armazenamento para dimensionamento automático

A tabela a seguir mostra o máximo em todo e os limites de armazenamento para diferentes opções em dimensionamento automático:

|Limite máximo de taxa de transferência  |Limite máximo de armazenamento  |
|---------|---------|
|4000 RU/s  |   50 GB    |
|20.000 RU/s  |  200 GB  |
|100.000 RU/s    |  1 TB   |
|500.000 RU/s    |  5 TB  |

## <a name="next-steps"></a>Próximas etapas

* Examine as [perguntas frequentes do dimensionamento automático](autoscale-faq.md).
* Saiba mais sobre [partições lógicas](partition-data.md).
* Saiba como [provisionar taxa de transferência em um contêiner do Azure Cosmos DB](how-to-provision-container-throughput.md).
* Saiba como [provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).
