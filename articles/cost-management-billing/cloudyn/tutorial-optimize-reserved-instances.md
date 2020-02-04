---
title: Tutorial – Otimizar o custo da instância de reserva com o Cloudyn – Azure
description: Neste tutorial, você aprenderá a otimizar os custos de instância reservada para os serviços Azure e AWS (Amazon Web Services).
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: tutorial
ms.service: cost-management-billing
ms.custom: seodec18
ms.reviewer: benshy
ms.openlocfilehash: 9b0dbc31f3789cb0033a531ad6c5395d3cfb8bcc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76769935"
---
<!-- Intent: As a cloud-consuming administrator, I need to ensure that my reserved instances are optimized for cost and usage
-->

# <a name="tutorial-optimize-reserved-instances"></a>Tutorial: Otimizar instâncias reservadas

Neste tutorial, você aprenderá como o Cloudyn pode ajudá-lo a otimizar os custos e o uso da instância reservada para os serviços Azure e AWS (Amazon Web Services). Uma instância reservada com um desses provedores de serviços de nuvem é um compromisso com um contrato de longo prazo em que você se compromete de antemão a usar a VM no futuro. E isso talvez possa trazer uma economia significativa em relação ao modelo de preços de VM padrão com pagamento por uso. A possível economia só ocorre quando você usa totalmente a capacidade das instâncias reservadas.

Este tutorial explica como o Cloudyn dá suporte ao Azure e a RIs (Instâncias Reservadas) do AWS. O tutorial descreve também como otimizar os custos da instância reservada. Primeiramente, fazendo com que as reservas sejam totalmente utilizadas. Neste tutorial, você irá:

> [!div class="checklist"]
> * Compreender os custos de RI do Azure
> * Saber mais sobre os benefícios das RIs
> * Otimizar os custos de RI do Azure
> * Exibir os custos de RI
> * Avaliar o custo-benefício da RI do Azure
> * Otimizar os custos de RI do AWS
> * Comprar RIs recomendadas
> * Modificar reservas não utilizadas

Se você não tem uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Prerequisites

- Você deve ter uma conta do Azure.
- Você precisa ter um registro de avaliação ou uma assinatura paga do Cloudyn.
- Você deve ter comprado RIs no Azure ou no AWS.

## <a name="understand-azure-ri-costs"></a>Compreender os custos de RI do Azure

Quando você compra Instâncias de VM Reservadas do Azure, já paga adiantadamente pelo seu uso. O pagamento adiantado abrange o custo do uso futuro das VMs:

- de um tipo específico
- em uma região específica
- por um prazo de um ou três anos
- até uma quantidade de VMs adquirida.

Você pode exibir suas Instâncias de VM Reservadas do Azure adquiridas no portal do Azure em [Reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

O termo _Instância de VM Reservada do Azure_ só se aplica a um modelo de preços. Ele não altera suas VMs em execução. O termo é específico para o Azure e costuma ser chamado de _instância reservada_ ou _reserva_. As instâncias reservadas que você comprou não se aplicam a VMs específicas; elas se aplicam a qualquer VM correspondente. Por exemplo, uma reserva para um tipo de VM que é executado em uma região que você escolheu para a reserva adquirida.

As instâncias reservadas adquiridas se aplicam apenas ao hardware básico. Elas não abrangem licenças de software de uma VM. Por exemplo, você pode reservar uma instância e ter uma VM correspondente que executa o Windows. A instância reservada só cobre o custo base da VM. Neste exemplo, você paga o preço total das licenças Windows necessárias. Para obter um desconto no sistema operacional ou em outro software executado nas VMs, pense em usar os [Benefícios Híbridos do Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Os Benefícios Híbridos oferecem um desconto para suas licenças de software de maneira semelhante às instâncias reservadas em relação às VMs de base.

A utilização de instância reservada não afeta diretamente o custo. Em outras palavras, a execução de uma VM a 100% de utilização da CPU ou a 0% de utilização da CPU tem o mesmo efeito: você paga pela alocação da VM, não pela utilização real.

Vamos ver como fica o custo do uso de VM sob demanda padrão em relação a instâncias reservadas na imagem abaixo:

![Custos sob demanda em comparação com os custos de instância reservada](./media/tutorial-optimize-reserved-instances/azure01.png)



As barras em vermelho mostram o custo acumulado da compra de instância reservada. Você paga apenas o valor de uso único. Uso da VM é gratuito. As barras azuis mostram o custo acumulado da mesma VM executada com o modelo de preços pago conforme o uso ou sob demanda. Entre o sétimo e o oitavo mês de uso da VM, há um *ponto de neutralização de custo*. A partir do oitavo mês é que você começa a reduzir os custos neste exemplo.

## <a name="benefits-of-ris"></a>Benefícios das RIs

Todas as compras de instância reservada aplicam-se a uma máquina virtual de um tamanho e um local específicos. Por exemplo, D2s\_v3 em execução no local Oeste dos EUA, conforme mostrado na imagem abaixo:

![Detalhes de instância reservada do Azure](./media/tutorial-optimize-reserved-instances/azure02.png)

A compra de instância reservada se torna útil quando uma máquina virtual é executada por tempo suficiente para alcançar o ponto de neutralização de custo da reserva. A VM deve corresponder ao tamanho e ao local da instância reservada. Por exemplo, o ponto de neutralização de custo está entre o sétimo e o oitavo mês no gráfico anterior. Portanto, a compra é benéfica quando a VM correspondente à reserva é executada pelo menos por 7,5 meses \* 30 dias \* 24 horas = 5.400 horas. Se a VM correspondente for executada por menos de 5.400 horas, a reserva é mais cara do que no pagamento conforme o uso.

O ponto de neutralização de custo pode ser diferente para cada tamanho de VM e para cada local. Isso também depende do preço da VM pago conforme o uso negociado. Antes de fazer uma compra, verifique o ponto de neutralização de custo aplicável ao seu caso.

Outro ponto a considerar quando você adquire a reserva é o escopo da instância reservada. O escopo determina se o benefício da reserva é compartilhado ou se aplica-se a uma assinatura específica. As instâncias reservadas compartilhadas são aplicadas aleatoriamente a todas as suas assinaturas com as primeiras VMs de correspondência encontradas.

O escopo de compra compartilhado é o mais flexível e recomendado sempre que possível. As chances de usar todas as instâncias reservadas são significativamente maiores com o escopo compartilhado. No entanto, quando o proprietário de uma assinatura paga a instância reservada, ele pode não ter outra opção além de comprá-la com o escopo de Assinatura Única.

## <a name="optimize-azure-ri-costs"></a>Otimizar os custos de RI do Azure

O Cloudyn dá suporte a instâncias reservadas e Benefícios híbridos da seguinte maneira:

- Mostrando os custos associados aos modelos de preços
- Controlando o uso da RI
- Avaliando o impacto da RI
- Alocando custos da RI de acordo com as suas políticas

A primeira ação que você deve executar antes de adquirir uma instância reservada é avaliar o impacto da compra da RI:

- Quanto isso custará a você?
- Quanto você economizará?
- Qual é o ponto de neutralização de custo?

O relatório Impacto de Compra de Instância Reservada pode ajudar a responder a essas perguntas.

## <a name="assess-azure-ri-cost-effectiveness"></a>Avaliar o custo-benefício da RI do Azure

No portal do Cloudyn, navegue até **Otimizador** > **Comparação de RI** e selecione **Impacto de Compra de Instância Reservada**.

No relatório Impacto de Compra de Instância Reservada, selecione um tamanho de VM (tipo de instância), local (região), prazo de reserva, quantidade e runtime esperado. Em seguida, avalie se sua compra resultará em economias.

Por exemplo, se você comprar uma reserva para uma VM do tipo DS1\_v2 no Leste dos EUA que é executada 24 horas por dia, 7 dias por semana durante um ano inteiro, poderá economizar US$ 369,48 por ano. O ponto de neutralização de custo ocorre em cinco meses. Veja a seguinte imagem:

![Ponto de neutralização de custo da instância reservada do Azure](./media/tutorial-optimize-reserved-instances/azure03.png)

No entanto, se ela é executada apenas 50% do tempo, o ponto de neutralização de custo ocorrerá no décimo mês e a economia será de apenas US$ 49,74 por ano. Você pode não se beneficiar com a compra de reserva para esse tipo de instância neste exemplo. Veja a seguinte imagem:

![Exemplo do ponto de equilíbrio para VMs do Azure](./media/tutorial-optimize-reserved-instances/azure04.png)

## <a name="view-ri-costs"></a>Exibir os custos de RI

Ao adquirir uma reserva, você faz um pagamento único. Há duas maneiras de exibir o pagamento no Cloudyn:

- Custo real
- Custo amortizado

### <a name="actual-reserved-instance-cost"></a>Custo real da instância reservada

Os relatórios Análise de Custo Real e Análise ao Longo do Tempo mostram a quantidade total paga pela reserva, iniciando no mês da compra. Eles ajudam você a ver seus gastos reais durante um período.

Navegue até **Custos** > **Análise de Custo** > no portal do Cloudyn e selecione **Análise de Custo Real** ou **Custo Real ao Longo do Tempo**. Em seguida, defina os filtros. Por exemplo, filtre apenas o serviço da VM/do Azure e agrupe por tipo de recurso e modelo de preço. Veja a seguinte imagem:

![Exemplo do custo real de instâncias reservadas](./media/tutorial-optimize-reserved-instances/azure05.png)

Você pode filtrar por um serviço, **Azure/VM** neste exemplo e agrupar por **modelo de preço** e **tipo de recurso**, conforme mostrado na imagem abaixo:

![Exemplo de grupos de relatórios de custo real e filtros agrupados por tipo de recurso e modelo de preço](./media/tutorial-optimize-reserved-instances/azure06.png)

Você também pode analisar os tipos de pagamentos feitos, como tarifas únicas, de uso e de licença.

### <a name="amortized-reserved-instance-cost"></a>Custo amortizado de instância reservada

Ao comprar uma RI, você paga um valor inicial que pode ser visto no mês da compra. Ela não fica visível nas suas faturas subsequentes. Dessa forma, a análise do seu uso mensal pode não ser precisa. O custo mensal real consiste do uso mensal mais a parte proporcional (amortizada) de tarifas de uso único pagas anteriormente. O relatório de Custo Amortizado pode ajudá-lo a obter uma visão real.

O custo da instância reservada amortizado é calculado considerando o valor de uso único de reserva e sua amortização durante o prazo de reserva. Em relatórios de custo real, os valores de uso únicos só são vistos no mês da compra da reserva. Os gastos diários e mensais não aparecem no custo real da implantação. Os relatórios de custos amortizados mostram o custo real da implantação ao longo do tempo.  O relatório de custo amortizado é a única maneira de ver suas tendências de custo reais. Também é a única maneira de projetar seus gastos futuros.

No relatório de Custo Real, você observou um pico de compra de RI em 16 de novembro de US$ 747. No relatório Custo Amortizado (consulte a imagem a seguir), há um custo diário parcial em 16 de novembro. A partir de 17 de novembro, você verá o custo de RI amortizado de US $747/365 = US$ 2,05. Você perceberá também que a reserva adquirida não foi usada e, portanto, você pode otimizá-la alternando-a para um tamanho de VM diferente.

Para exibi-la, navegue até **Custos** > **Análise de Custo** > e selecione **Análise de Custo Amortizado** ou **Custo Amortizado ao Longo do Tempo**.

![Relatório de exemplo mostrando o custo de instância reservada amortizado](./media/tutorial-optimize-reserved-instances/azure07.png)

## <a name="optimize-aws-ri-costs"></a>Otimizar os custos de RI do AWS

Instâncias reservadas são um compromisso aberto. Elas são úteis quando você tem um uso prolongado de VMs porque as instâncias reservadas são mais baratas do que as instâncias sob demanda. No entanto, elas precisam ser usadas suficientemente. O compromisso é usar os recursos, normalmente VMs, por um período definido: um ou três anos. Quando você assume o compromisso de comprar, você paga antecipadamente pelos recursos com reserva. No entanto, você não poderá sempre usar totalmente o que confirmou na reserva.

Por exemplo, você pode avaliar seu ambiente e determinar que teve 20 instâncias de D2 standard em execução constantemente no último ano. Você pode comprar uma reserva para elas e possivelmente economizar algum dinheiro. Em um exemplo diferente, talvez tenha confirmado o uso de dez instâncias MA4 no ano. Mas você pode ter usado somente cinco até hoje. Ambos os exemplos ilustram um uso de RI ineficiente. Há duas maneiras de otimizar os custos para instâncias reservadas com relatórios de otimização do Cloudyn:

- Analise as recomendações do que você poderia adquirir com base no seu histórico de uso
- Modificar reservas não utilizadas

Você usa os relatórios _Recomendações de compra de RI EC2_ e _Reservas de EC2 sem uso atualmente_ para melhorar o uso das instâncias reservadas e os custos.

## <a name="buy-recommended-ris"></a>Comprar RIs recomendadas

O Cloudyn compara o uso da instância sob demanda e a compara com possíveis instâncias reservadas. Onde ele encontrar economias possíveis, suas recomendações serão mostradas no relatório Recomendações de compra de EC2.

No menu de relatórios na parte superior do portal, clique em **Otimizador** > **Otimização de Preços** > **Recomendações de compra de RI EC2**.

A imagem a seguir mostra as recomendações de compra do relatório.

![Exemplo mostrando as recomendações de compra no relatório Recomendações de compra de EC2](./media/tutorial-optimize-reserved-instances/aws01.png)

Neste exemplo, o Cloudyn mostra \_uma conta com 32 recomendações de compra de instância reservada. Se você seguir todas as recomendações de compra, poderia poupar até US$ 137.770 por ano. Tenha em mente que as recomendações de compra fornecidas pelo Cloudyn presumem que o uso de suas cargas de trabalho em execução permanecerão consistentes.

Para exibir detalhes sobre por que cada compra é recomendada, clique no símbolo de adição ( **+** ) em **Justificativas** . Aqui está um exemplo para a primeira recomendação na lista.

![Exemplo mostrando detalhes de justificativa de compra](./media/tutorial-optimize-reserved-instances/aws02.png)

O exemplo anterior mostra que a execução da carga de trabalho sob demanda custaria US$ 90.456 por ano. No entanto, se você comprar a reserva com antecedência, a mesma carga de trabalho teria um custo de US$ 56.592 e economizaria US$ 33.864 por ano.

Clique no símbolo de adição ao lado de **Impacto de compra de RI EC2** para exibir o ponto de neutralização de custo durante um ano para ver aproximadamente quando seu investimento de compra se paga. Cerca de oito meses depois da compra o custo acumulado sob demanda começa a exceder o custo acumulado da RI no exemplo abaixo:

![Exemplo mostrando detalhes de impacto de compra](./media/tutorial-optimize-reserved-instances/aws03.png)

Você começa a poupar neste ponto.

Examine as **Instâncias ao Longo do Tempo** para verificar a precisão da recomendação da compra sugerida. Neste exemplo, você pode ver que seis instâncias foram usadas em média para a carga de trabalho no último período de 30 dias.

![Exemplo mostrando o uso histórico de instâncias ao longo do tempo](./media/tutorial-optimize-reserved-instances/aws04.png)

## <a name="modify-unused-reservations"></a>Modificar reservas não utilizadas

As reservas não utilizadas são comuns em ambientes de computação de muitos consumidores de recursos de nuvem. Garantir que reservas não utilizadas sejam totalmente usadas pode gerar economia quando você modifica as reservas para atender às suas necessidades atuais. Por exemplo, você pode ter uma assinatura que contém instâncias D3 padrão em execução no Linux. Se você não utilizar a reserva totalmente, poderá alterar o tipo de instância. Ou poderá mover os recursos não utilizados para uma reserva diferente ou para uma conta diferente.

O AWS vende instâncias reservadas para regiões e zonas de disponibilidade específicas. Se você tiver adquirido instâncias reservadas para uma zona de disponibilidade específica, não poderá mover as reservas entre regiões. No entanto, você pode mover facilmente instâncias reservadas regionais entre zonas usando o relatório **Reservas de EC2 não usadas atualmente**. Ou, como alternativa, você pode modificá-las para ter um escopo regional e ele aplicará instâncias correspondentes em todas as regiões de disponibilidade.

No menu de relatórios, na parte superior do portal, clique em **Otimizador** > **Ineficiências** > **Instâncias de EC2 sem uso atualmente**.

As imagens a seguir mostram o relatório com instâncias reservadas não utilizadas.

![Exemplo mostrando informações resumidas sobre reservas não utilizadas](./media/tutorial-optimize-reserved-instances/unused-ri01.png)

Clique no sinal de adição em **Detalhes** para exibir os detalhes de uma reserva específica.

![Exemplo mostrando detalhes de reservas não utilizadas](./media/tutorial-optimize-reserved-instances/unused-ri02.png)

No exemplo anterior, há um total de 77 reservas não utilizadas em várias zonas de disponibilidade. A primeira reserva tem 51 instâncias não utilizadas. Procurando mais abaixo na lista, há possíveis modificações de instância de reserva que você pode fazer usando o tipo de instância **m3.2xlarge** na zona de disponibilidade **us-east-1c**.

Clique em **Modificar** na primeira reserva da lista para abrir a página **Modificar RI** que mostra dados sobre a reserva.

![Exemplo mostrando reservas que você pode modificar](./media/tutorial-optimize-reserved-instances/unused-ri03.png)

As instâncias de reserva que você pode modificar são listadas. Na imagem de exemplo a seguir, há 51 reservas não utilizadas que você pode modificar, mas há uma necessidade de 54 dentre as duas reservas. Se você modificar suas reservas não utilizadas para usar todas, quatro instâncias continuarão a ser executadas sob demanda. Neste exemplo, divida suas reservas não utilizadas, com a primeira usando 30 e a segunda usando 21.

Clique no símbolo de adição da primeira entrada de reserva e defina a **Quantidade de reserva** como **30**. Para a segunda entrada, defina a quantidade de reserva como **21** e clique em **Aplicar**.

![Exemplo mostrando alterações à quantidade de reserva](./media/tutorial-optimize-reserved-instances/unused-ri04.png)

Todas as instâncias não utilizadas da reserva estão totalmente usadas e 51 instâncias deixaram de ser executadas sob demanda. Neste exemplo, você pode economizar dinheiro reduzindo significativamente o uso sob demanda e usando reservas já pagas.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você realizou as seguintes tarefas com sucesso:

> [!div class="checklist"]
> * Entender os custos da RI do Azure
> * Aprender sobre os benefícios das RIs
> * Otimizar os custos de RI do Azure
> * Exibir custos de RI
> * Avaliar o custo-benefício da RI do Azure
> * Otimizar custos de RI do AWS
> * Comprar RIs recomendadas
> * Reservas não utilizadas modificadas


Avance para o próximo tutorial para saber mais sobre como controlar o acesso a dados.

> [!div class="nextstepaction"]
> [Controlar o acesso a dados](tutorial-user-access.md)
