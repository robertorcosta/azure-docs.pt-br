---
title: Crie contêineres e bancos de dados do Azure Cosmos no modo piloto automático.
description: Saiba mais sobre os benefícios, casos de uso e como provisionar bancos de dados e contêineres do Azure Cosmos no modo piloto automático.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 89af30788fe5129cddc6a3607b8c722549b610d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246650"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Criar contêineres e bancos de dados do Azure Cosmos no modo de piloto automático (versão prévia)

O Azure Cosmos DB permite provisionar a taxa de transferência em seus contêineres no modo manual ou piloto automático. Este artigo descreve os benefícios e casos de uso do modo de piloto automático.

> [!NOTE]
> O modo piloto automático está disponível na visualização pública. Você pode [habilitar o piloto automático apenas para novos bancos de dados e contêineres.](#create-a-database-or-a-container-with-autopilot-mode) Não está disponível para contêineres e bancos de dados existentes.

Além do provisionamento manual de throughput, agora você pode configurar contêineres Azure Cosmos no modo piloto automático. Os contêineres e bancos de dados configurados no modo piloto automático **dimensionarão automaticamente e instantaneamente o throughput provisionado com base nas necessidades do aplicativo sem afetar a disponibilidade, latência, throughput ou desempenho da carga de trabalho globalmente.**

Ao configurar contêineres e bancos de dados no modo piloto `Tmax` automático, você precisa especificar o throughput máximo a não ser excedido. Os contêineres podem, então, `0.1*Tmax < T < Tmax`dimensionar sua passagem para que . Em outras palavras, os contêineres e os bancos de dados são dimensionados instantaneamente com base nas necessidades de carga de trabalho, a partir de até 10% do valor máximo de throughput que você configurou até o valor máximo de throughput configurado. Você pode alterar a`Tmax`configuração de throughput máximo () em um banco de dados de piloto automático ou contêiner a qualquer momento. Com a opção de piloto automático, o rendimento mínimo de 400 RU/s por contêiner ou banco de dados não é mais aplicável.

Durante a visualização do piloto automático, para o throughput máximo especificado no contêiner ou no banco de dados, o sistema permite operar dentro do limite de armazenamento calculado. Se o limite de armazenamento for excedido, o throughput máximo será automaticamente ajustado para um valor mais alto. Ao usar o throughput de nível de banco de dados com o `0.001*TMax`modo piloto automático, o número de contêineres permitidos dentro de um banco de dados é calculado como: . Por exemplo, se você prover 20.000 RU/s de piloto automático, então o banco de dados pode ter 20 contêineres.

## <a name="benefits-of-autopilot-mode"></a>Benefícios do modo piloto automático

Os contêineres Azure Cosmos configurados no modo piloto automático têm os seguintes benefícios:

* **Simples:** Os contêineres no modo piloto automático removem a complexidade para gerenciar o throughput provisionado (RUs) e a capacidade manualmente para vários contêineres.

* **Escalável:** Os contêineres no modo piloto automático dimensionam perfeitamente a capacidade de transferência provisionada conforme necessário. Não há interrupção nas conexões, aplicativos dos clientes e eles não afetam nenhum SLAs existente.

* **Custo-benefício:** Quando você usa contêineres configurados no modo piloto automático, você só paga pelos recursos que suas cargas de trabalho precisam por hora.

* **Altamente disponível:** Os contêineres no modo piloto automático usam o mesmo backend distribuído globalmente, tolerante a falhas e altamente disponível para garantir a durabilidade dos dados e a alta disponibilidade.

## <a name="use-cases-of-autopilot-mode"></a>Use casos de modo piloto automático

Os casos de uso dos contêineres Azure Cosmos configurados no modo piloto automático incluem:

* **Cargas de trabalho variáveis:** Quando você está executando um aplicativo levemente usado com pico de uso de 1 hora a várias horas algumas vezes por dia ou várias vezes por ano. Exemplos incluem aplicações para recursos humanos, orçamento e relatórios operacionais. Para tais cenários, os contêineres configurados no modo piloto automático podem ser usados, e você não precisa mais prover manualmente para o pico ou capacidade média.

* **Cargas de trabalho imprevisíveis:** Quando você está executando cargas de trabalho onde há uso de banco de dados ao longo do dia, mas também picos de atividade que são difíceis de prever. Um exemplo inclui um site de tráfego que vê uma onda de atividade quando a previsão do tempo muda. Os contêineres configurados no modo piloto automático ajustam a capacidade para atender às necessidades da carga máxima do aplicativo e reduzem a escala quando a onda de atividade acabar.

* **Novos aplicativos:** Se você estiver implantando um novo aplicativo e não tiver certeza sobre quanto throughput provisionado (ou seja, quantas RUs) você precisa. Com os contêineres configurados no modo piloto automático, você pode dimensionar automaticamente para as necessidades e requisitos de capacidade da sua aplicação.

* **Aplicativos pouco utilizados:** Se você tiver um aplicativo que só é usado por algumas horas várias vezes por dia ou semana ou mês, como um aplicativo/web/blog de baixo volume.

* **Bancos de dados de desenvolvimento e teste:** Se você tem desenvolvedores usando contêineres durante o horário de trabalho, mas não precisa deles em noites ou fins de semana. Com os contêineres configurados no modo piloto automático, eles reduzem para um mínimo quando não estão em uso.

* **Cargas/consultas de produção programadas:** Quando você tem uma série de solicitações/operações/consultas programadas em um único contêiner, e se houver períodos ociosos em que você deseja executar com um throughput absolutamente baixo, agora você pode fazer isso facilmente. Quando uma consulta/solicitação programada é submetida a um contêiner configurado no modo piloto automático, ele aumentará automaticamente o quanto necessário e executará a operação.

As soluções para os problemas anteriores não só requerem uma enorme quantidade de tempo na implementação, mas também introduzem complexidade na configuração ou no seu código, e frequentemente requerem intervenção manual para enfrentá-los. O modo piloto automático permite que os cenários acima fora da caixa, de modo que você não precisa mais se preocupar com esses problemas.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Comparação – Contêineres configurados no modo manual vs. modo piloto automático

|  | Contêineres configurados no modo manual  | Contêineres configurados no modo piloto automático |
|---------|---------|---------|
| **Throughput provisionado** | Provisionado manualmente. | Dimensionado automaticamente e instantaneamente com base nos padrões de uso da carga de trabalho. |
| **Limitação de taxas de solicitações/operações (429)**  | Pode acontecer, se o consumo exceder a capacidade provisão. | Não acontecerá se o throughput consumido estiver dentro do throughput máximo que você escolher com o modo piloto automático.   |
| **Planejamento da capacidade** |  Você tem que fazer um planejamento inicial de capacidade e provisão do throughput que você precisa. |    Não precisa se preocupar com planejamento de capacidade. O sistema cuida automaticamente do planejamento de capacidade e gerenciamento de capacidade. |
| **Preços** | Ru/s provisionado manualmente por hora. | Para contas de região de gravação única, você paga pelo throughput usado por hora, usando a taxa de ru/s do piloto automático por hora. <br/><br/>Para contas com várias regiões de gravação, não há cobrança extra para piloto automático. Você paga pelo throughput usado por hora usando a mesma taxa multi-master RU/s por hora. |
| **Mais adequado para tipos de carga de trabalho** |  Cargas de trabalho previsíveis e estáveis|   Cargas de trabalho imprevisíveis e variáveis  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Crie um banco de dados ou um contêiner com modo piloto automático

Você pode configurar o piloto automático para novos bancos de dados ou contêineres ao criá-los através do portal Azure. Use as seguintes etapas para criar um novo banco de dados ou contêiner, habilite o piloto automático e especifique o throughput máximo (RU/s).

1. Faça login no [portal Azure](https://portal.azure.com) ou no [explorador Azure Cosmos DB.](https://cosmos.azure.com/)

1. Navegue até sua conta do Azure Cosmos DB e abra a guia **Data Explorer.**

1. Selecione **Novo contêiner.** Digite um nome para seu banco de dados, contêiner e uma chave de partição. Em **Throughput,** selecione a opção **Piloto Automático** e escolha o throughput máximo (RU/s) que o banco de dados ou o contêiner não podem exceder ao usar a opção piloto automático.

   ![Criando um contêiner e configurando o throughput do Piloto Automático](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Selecione **OK**.

Você pode criar um banco de dados de throughput compartilhado com o modo piloto automático selecionando a opção **de throughput do banco de dados Provisão.**

## <a name="throughput-and-storage-limits-for-autopilot"></a><a id="autopilot-limits"></a>Limites de throughput e armazenamento para piloto automático

A tabela a seguir mostra os limites máximos e de armazenamento para diferentes opções no modo piloto automático:

|Limite máximo de throughput  |Limite máximo de armazenamento  |
|---------|---------|
|4000 RU/s  |   50 GB    |
|20.000 RU/s  |  200 GB  |
|100.000 RU/s    |  1 TB   |
|500.000 RU/s    |  5 TB  |

## <a name="next-steps"></a>Próximas etapas

* Revise o [FAQ do piloto automático](autopilot-faq.md).
* Saiba mais sobre [partições lógicas](partition-data.md).
* Saiba como [provisionar taxa de transferência em um contêiner do Azure Cosmos DB](how-to-provision-container-throughput.md).
* Saiba como [provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).
