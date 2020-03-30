---
title: Introdução ao Azure Advisor
description: Use o Azure Advisor para otimizar as implantações do Azure.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 600bda282d46f86979d0366719826c3a6c1323e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443088"
---
# <a name="introduction-to-azure-advisor"></a>Introdução ao Azure Advisor

Saiba mais sobre as principais funcionalidades do Assistente do Azure e obtenha respostas para perguntas frequentes.

## <a name="what-is-advisor"></a>O que é o Assistente?
O Assistente é um consultor de nuvem personalizado que ajuda você a seguir as melhores práticas para otimizar suas implantações do Azure. Ele analisa a telemetria de uso e configuração do recurso e, depois, recomenda soluções que podem ajudar você a melhorar a economia, o desempenho, a alta disponibilidade e a segurança de seus recursos do Azure.

Com o Assistente, é possível:
* Obter recomendações de melhores práticas proativas, personalizadas e prontas para uso. 
* Melhorar o desempenho, a segurança e a alta disponibilidade de seus recursos, enquanto você identifica oportunidades para reduzir o gasto geral do Azure.
* Obter recomendações com ações propostas embutidas.

Você pode acessar o Advisor através do [portal Azure.](https://aka.ms/azureadvisordashboard) Entre no [Portal](https://portal.azure.com), localize o **Assistente** no menu de navegação ou pesquise por ele no menu **Todos os serviços**.

O painel do Assistente exibe recomendações personalizadas para todas as assinaturas.  Você pode aplicar filtros para exibir as recomendações para assinaturas e tipos de recursos específicos.  As recomendações são divididas em quatro categorias: 

* **Alta Disponibilidade**: para garantir e melhorar a continuidade dos aplicativos críticos para os negócios. Para saber mais, veja [Advisor High Availability recommendations](advisor-high-availability-recommendations.md) (Recomendações de alta disponibilidade do Advisor).
* **Segurança**: para detectar ameaças e vulnerabilidades que podem levar a possíveis violações de segurança. Para saber mais, veja [Advisor Security recommendations](advisor-security-recommendations.md) (Recomendações de segurança do Advisor).
* **Desempenho**: para melhorar a velocidade dos aplicativos. Para saber mais, veja [Advisor Performance recommendations](advisor-performance-recommendations.md) (Recomendações de desempenho do Advisor).
* **Custo**: para otimizar e reduzir o gasto geral do Azure. Para saber mais, veja [Advisor Cost recommendations](advisor-cost-recommendations.md) (Recomendações de custo do Advisor).
* **Excelência Operacional**: Para ajudá-lo a alcançar a eficiência de processos e fluxos de trabalho, a capacidade de gerenciamento de recursos e as práticas recomendadas de implantação. . Para obter mais informações, consulte [as recomendações de Excelência Operacional do Advisor .](advisor-operational-excellence-recommendations.md)

  ![Tipos de recomendação do Advisor](./media/advisor-overview/advisor-dashboard.png)

Você pode clicar em uma categoria para exibir a lista de recomendações nessa categoria e selecionar uma recomendação para saber mais sobre ela.  Saiba mais sobre as ações que você pode realizar para aproveitar uma oportunidade ou resolver um problema.

![Categoria de recomendação do Assistente](./media/advisor-overview/advisor-ha-category-example.png) 

Clique na ação recomendada para implementá-la.  Será aberta uma interface simples que permite que você implemente a recomendação ou fornece documentação que ajuda você com a implementação.  Quando você implementa uma recomendação, pode demorar até um dia para que o Assistente reconheça isso.

Se você não pretende tomar uma ação imediata em uma recomendação, você pode adiá-la por um período de tempo especificado ou ignorá-la.  Se você não deseja receber as recomendações para uma assinatura ou grupo de recursos específico, você poderá configurar o Assistente para gerar apenas recomendações para grupos de recursos e assinaturas especificados.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-do-i-access-advisor"></a>Como acesso o Advisor?
Você pode acessar o Advisor através do [portal Azure.](https://aka.ms/azureadvisordashboard) Entre no [Portal](https://portal.azure.com), localize o **Assistente** no menu de navegação ou pesquise por ele no menu **Todos os serviços**.

Você também pode exibir as recomendações do Assistente por meio interface de recursos da máquina virtual. Escolha uma máquina virtual e role até as recomendações do Advisor no menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>De quais permissões preciso para acessar o Advisor?
 
Você pode acessar as recomendações do Assistente como *Proprietário*, *Colaborador* ou *Leitor* de uma assinatura.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Para quais recursos o Advisor fornece recomendações?

O Advisor fornece recomendações para gateway de aplicativos, serviços de aplicativos, conjuntos de disponibilidade, Cache Azure, Fábrica de Dados Azure, Banco de Dados Azure para MySQL, Banco de Dados Azure para PostgreSQL, Banco de Dados Azure para MariaDB, Azure ExpressRoute, Azure Cosmos DB, Público do Azure Endereços IP, SQL Data Warehouse, servidores SQL, contas de armazenamento, perfis de gerenciador de tráfego e máquinas virtuais.

O Azure Advisor também inclui suas recomendações do [Azure Security Center,](https://docs.microsoft.com/azure/security-center/security-center-recommendations) que podem incluir recomendações para tipos adicionais de recursos.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Posso adiar ou ignorar uma recomendação?

Para adiar ou ignorar uma recomendação, clique no link **Adiar**. Você pode especificar um período de adiamento ou selecione **Nunca** para ignorar a recomendação.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as recomendações do Assistente, consulte:

* [Introdução ao Advisor](advisor-get-started.md)
* [Recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
