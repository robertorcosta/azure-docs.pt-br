---
title: Entender sua fatura do Azure
description: Saiba como ler e entender o uso e a fatura de sua assinatura do Azure.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 9486d56a723bb311c05ab7aa776060dfa9561aae
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223019"
---
# <a name="understand-your-microsoft-azure-bill"></a>Noções básicas sobre sua fatura do Microsoft Azure
Para entender sua fatura do Azure, compare-a com o arquivo de uso diário detalhado e com os relatórios de gerenciamento de custos no portal do Azure.

Este artigo não se aplica aos seguintes clientes:
- Clientes do Azure com um Contrato Enterprise (clientes EA). Se você for cliente do EA, confira [Noções básicas sobre sua fatura dos clientes do Azure com um Contrato Enterprise](billing-understand-your-bill-ea.md).
- Clientes do Azure com um [Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement). Se você tiver um Contrato de Cliente da Microsoft, confira [Noções básicas sobre os encargos do Azure em sua fatura do Contrato de Cliente da Microsoft](billing-mca-understand-your-bill.md).

Para obter uma explicação sobre como o faturamento funciona no programa Provedor de Soluções de Nuvem do Azure (Azure CSP), incluindo o ciclo de cobrança, o preço e o uso, confira [Visão geral da cobrança do Azure CSP](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/).

## <a name="charges"></a>Examine os encargos

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Se houver uma cobrança em sua fatura sobre a qual você deseja obter mais informações, compare o uso e os custos com o arquivo de uso ou com o portal do Azure.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Opção 1: Comparar o uso e os custos com o arquivo de uso

O arquivo CSV de uso detalhado mostra os encargos por período de cobrança e de uso diário. Para baixar ou exibir o arquivo, confira [Obtenha sua fatura de cobrança do Azure e os dados de uso diário](billing-download-azure-invoice-daily-usage-date.md).

Os encargos de uso são exibidos no nível do medidor. Os termos na lista a seguir têm o mesmo significado na fatura e no arquivo de uso detalhado. Por exemplo, o ciclo de faturamento na fatura é o mesmo que o período de faturamento mostrado no arquivo de uso detalhado.

 | Fatura (PDF) | Uso detalhado (CSV)|
 | --- | --- |
|Ciclo de cobrança | Período de Cobrança |
 |NOME |Categoria de medidor |
 |Type |Subcategoria de medidor |
 |Recurso |Nome do medidor |
 |Região |Região do medidor |
 |Consumido |Quantidade consumida |
 |Incluso |Quantidade incluída |
 |Faturável |Quantidade de excesso |

A seção **Encargos de Uso** da sua fatura mostra o valor total de cada medidor que foi consumido durante o período de cobrança. Por exemplo, a imagem a seguir mostra um preço de uso para o serviço Agendador do Azure.

![Encargos de uso da fatura](./media/billing-understand-your-bill/1.png)

O mesmo encargo é mostrado na seção **Instrução** de seu CSV de uso detalhado. A quantidade em *Consumido* e o *Valor* correspondem à fatura.

![Encargos de uso de CSV](./media/billing-understand-your-bill/2.png)

Para ver um detalhamento diária desse preço, acesse a seção **Uso Diário** do arquivo CSV. Filtre por *Agendador* em *Categoria do Medidor*. Você pode ver em quais dias o medidor foi usado e quanto foi consumido. As informações de *Recurso* e *Grupo de recursos* também são mostradas para comparação. A soma dos valores de *Consumido* devem equivaler e corresponder ao que é mostrado na fatura.

![Seção Uso Diário no CSV](./media/billing-understand-your-bill/3.png)

Para obter o custo por dia, multiplique os valores de *Consumido* com o valor de *Taxa* da seção **Instrução**.

Para obter mais informações, consulte:

- [Entenda sua fatura do Azure](billing-understand-your-invoice.md)
- [Entenda o uso detalhado do Azure](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>Opção 2: Comparar o uso e os custos no portal do Azure

O portal do Azure também pode lhe ajudar a verificar seus encargos. Para obter uma visão geral rápida do uso e das cobranças faturadas, consulte os gráficos de gerenciamento de custos.

1. No portal do Azure, vá para [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione sua assinatura> **Análise de custo**.
1. Filtrar por **Timespan**.
1. Para continuar o exemplo anterior, você vê uma taxa de uso para o serviço Agendador do Azure.

   ![Exibição de análise de custo no portal do Azure](./media/billing-understand-your-bill/4.png)

1. Selecione a linha que mostra o preço para ver o detalhamento do custo diário.

   ![Exibição de histórico de custo no Portal do Azure](./media/billing-understand-your-bill/5.png)

Para saber mais, veja [Evitar custos inesperados com o gerenciamento de custos e cobrança do Azure](billing-getting-started.md#costs).

## <a name="external"></a>Serviços externos cobrados separadamente

Serviços externos ou encargos de Marketplace são para recursos que foram criados por fornecedores de software de terceiros. Esses recursos estão disponíveis para uso no Azure Marketplace. Por exemplo, um Barracuda Firewall é um recurso do Azure Marketplace oferecido por terceiros. Todos os encargos para o firewall e seus medidores correspondentes aparecem como cobranças de serviços externos.

As taxas de serviço externas são cobradas separadamente. Os encargos não aparecem na fatura do Azure. Para saber mais, confira [Entender os encargos dos serviços externos do Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Recursos cobrados por medidores de uso

O Azure não fatura diretamente com base no custo do recurso. Os encargos de um recurso são calculados usando um ou mais medidores. Medidores são usados para rastrear o uso de um recurso durante todo o seu tempo de vida. Esses medidores são usados para calcular a conta.

Por exemplo, quando você cria um único recurso do Azure, como uma máquina virtual, ele tem uma ou mais instâncias de medidor criadas. Medidores são usados para rastrear o uso do recurso ao longo do tempo. Cada medidor emite registros de uso usados pelo Azure para calcular a fatura.

Por exemplo, uma única máquina virtual (VM) criada no Azure pode ter os seguintes medidores criados para rastrear seu uso:

- Calcular horas
- Horas de endereço IP
- Transferência de dados em
- Transferência de dados
- Disco gerenciado padrão
- Operações de disco gerenciado padrão
- Disco IO padrão
- Leitura de blob padrão do bloco IO
- Escrita de Blob Padrão de Bloqueio de E/S
- Bloqueio de blobs padrão IO-Block

Quando a VM é criada, cada medidor começa a emitir registros de uso. Esse uso e o preço do medidor são rastreados no sistema de medição do Azure.

## <a name="payment"></a>Pagar sua fatura

Se você tiver configurado um cartão de crédito como forma de pagamento, o pagamento será cobrado automaticamente em até 10 dias após o término do período de cobrança. Na sua declaração de cartão de crédito, o item de linha diria **MSFT Azure**.

Para alterar o cartão de crédito cobrado, confira [Adicionar, atualizar ou remover um cartão de crédito do Azure](billing-how-to-change-credit-card.md).

Se você [pagar pela fatura](billing-how-to-pay-by-invoice.md), envie seu pagamento para o local listado na parte inferior da fatura.

Para verificar o status do seu pagamento, [crie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Dicas para gerenciamento de custos

- Estimar os custos usando o:
  - [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)
  - [Custo total de Calculadora de propriedade](https://aka.ms/azure-tco-calculator)
  - [Obter informações para cada serviço de preço](https://azure.microsoft.com/pricing/)
- [Examine o uso e os custos regularmente no portal do Azure](billing-getting-started.md#costs)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="learn-more"></a>Saiba mais

- [Obter sua fatura de cobrança do Azure e os dados de uso diário](billing-download-azure-invoice-daily-usage-date.md)
- [Compreender os termos na sua fatura do Microsoft Azure](billing-understand-your-invoice.md)
- [Compreender os termos em uso detalhado do Microsoft Azure](billing-understand-your-usage.md)
- [Gerenciamento de custos do portal do Azure](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Evitar custos inesperados com o gerenciamento de custos e cobrança do Azure](billing-getting-started.md#costs)
