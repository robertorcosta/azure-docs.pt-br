---
title: Otimizar custos para armazenamento Blob com capacidade reservada - Armazenamento Azure
description: Saiba mais sobre a compra de recursos reservados do Azure Storage para economizar custos com blob de bloco e recursos do Azure Data Lake Storage Gen2.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e73686629de8481f6a37e5bfafc9b723206b4853
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351023"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>Otimizar custos para o armazenamento de blobs com capacidade reservada

Você pode economizar dinheiro em custos de armazenamento para dados blob com capacidade reservada do Azure Storage. A capacidade reservada do Azure Storage oferece um desconto na capacidade para blobs de bloco e para dados Do Azure Data Lake Storage Gen2 em contas de armazenamento padrão quando você se compromete com uma reserva por um ano ou três anos. Uma reserva fornece uma quantidade fixa de capacidade de armazenamento para o prazo da reserva.

A capacidade reservada do Azure Storage pode reduzir significativamente os custos de capacidade para blobs de blocos e dados do Azure Data Lake Storage Gen2. A redução de custos alcançada depende da duração da sua reserva, da capacidade total que você escolhe reservar e do nível de acesso e do tipo de redundância que você escolheu para sua conta de armazenamento. A capacidade reservada fornece um desconto de faturamento e não afeta o estado dos recursos de armazenamento do Azure.

Para saber mais sobre os preços de reserva do Armazenamento do Azure, confira [Preços do blob de blocos](https://azure.microsoft.com/pricing/details/storage/blobs/) e [Preços do Azure Data Lake Storage Gen 2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="reservation-terms-for-azure-storage"></a>Termos de reserva para armazenamento Azure

As seções a seguir descrevem os termos de uma reserva de armazenamento azure.

### <a name="reservation-capacity"></a>Capacidade de reserva

Você pode comprar a capacidade reservada do Azure Storage em unidades de 100 TB e 1 PB por mês por um período de um ano ou três anos.

### <a name="reservation-scope"></a>Escopo de reserva

A capacidade reservada do Azure Storage está disponível para uma única assinatura ou para várias assinaturas (escopo compartilhado). Quando escopo de uma única assinatura, o desconto de reserva é aplicado apenas à assinatura selecionada. Quando escopo de várias assinaturas, o desconto de reserva é compartilhado entre essas assinaturas dentro do contexto de faturamento do cliente.

Ao comprar a capacidade reservada do Azure Storage, você pode usar sua reserva para dados block blob e Azure Data Lake Storage Gen2. Uma reserva é aplicada ao seu uso dentro do escopo adquirido e não pode ser limitada a uma conta de armazenamento específica, contêiner ou objeto dentro da assinatura. Uma reserva não pode ser dividida em várias assinaturas.

Uma reserva de armazenamento do Azure cobre apenas a quantidade de dados armazenados em uma assinatura ou grupo de recursos compartilhados. Exclusão antecipada, operações, largura de banda e taxas de transferência de dados não estão incluídas na reserva. Assim que você compra uma reserva, as taxas de capacidade que correspondem aos atributos de reserva são cobradas nas taxas de desconto em vez de nas taxas de pagamento à medida que você vai. Para obter mais informações sobre as reservas do Azure, [consulte O que são reservas do Azure?](/azure/billing/billing-save-compute-costs-reservations).

### <a name="supported-account-types-tiers-and-redundancy-options"></a>Tipos de conta suportados, níveis e opções de redundância

A capacidade reservada do Azure Storage está disponível para recursos em contas de armazenamento padrão, incluindo contas de armazenamento V2 (GPv2) e Blob.

Todas as camadas de acesso (quente, legal e arquivo) são suportadas para reservas. Para obter mais informações sobre os níveis de acesso, consulte [o armazenamento Azure Blob: níveis de acesso quentes, legais e de arquivamento](storage-blob-storage-tiers.md).

Todos os tipos de redundância são suportados para reservas. Para obter mais informações sobre opções de redundância, consulte [redundância do Azure Storage](../common/storage-redundancy.md).

> [!NOTE]
> A capacidade reservada do Azure Storage não está disponível para contas de armazenamento premium, contas de armazenamento v1 (GPv1) de uso geral, Azure Data Lake Storage Gen1, blobs de página, armazenamento azure Queue, armazenamento azure Table ou Arquivos Azure.  

### <a name="security-requirements-for-purchase"></a>Requisitos de segurança para compra

Para comprar capacidade reservada:

- Você deve estar na função **Proprietário** para pelo menos uma assinatura Enterprise ou individual com taxas de pagamento.
- Para assinaturas Corporativas, **adicionar instâncias reservadas** deve ser habilitado no portal Da EA. Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA na assinatura.
- Para o programa CSP (Cloud Solution Provider, provedor de soluções em nuvem), apenas agentes de administradores ou agentes de vendas podem comprar a capacidade reservada do Azure Blob Storage.

## <a name="determine-required-capacity-before-purchase"></a>Determine a capacidade necessária antes da compra

Ao comprar uma reserva de armazenamento do Azure, você deve escolher a região, a camada de acesso e a opção de redundância para a reserva. Sua reserva é válida apenas para dados armazenados nessa região, nível de acesso e nível de redundância. Por exemplo, suponha que você compre uma reserva de dados no Us West para o nível quente usando o armazenamento redundante de zona (ZRS). Não é possível usar a mesma reserva para dados no Leste dos EUA, dados no nível de arquivamento ou dados em grs (geo-redundante). No entanto, você pode comprar outra reserva para suas necessidades adicionais.  

As reservas estão disponíveis hoje para blocos de 100 TB ou 1 PB, com descontos maiores para blocos de 1 PB. Ao comprar uma reserva no portal Azure, a Microsoft pode fornecer recomendações baseadas no seu uso anterior para ajudar a determinar qual reserva você deve comprar.

## <a name="purchase-azure-storage-reserved-capacity"></a>Comprar capacidade reservada do Azure Storage

Você pode comprar a capacidade reservada do Azure Storage através do [portal Azure.](https://portal.azure.com) Pague pela reserva antecipadamente ou com pagamentos mensais. Para obter mais informações sobre compras com pagamentos mensais, consulte [Reservas do Buy Azure com pagamentos antecipados ou mensais.](/azure/billing/billing-monthly-payments-reservations)

Para obter ajuda para identificar os termos de reserva certos para o seu cenário, consulte [Entenda o desconto de capacidade reservada do Azure Storage](../../cost-management-billing/reservations/understand-storage-charges.md).

Siga estas etapas para adquirir capacidade reservada:

1. Navegue até o painel [de reservas de compras](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) no portal Azure.  
1. Selecione **Azure Blob Storage** para comprar uma nova reserva.  
1. Preencha os campos obrigatórios conforme descrito na tabela a seguir:

    ![Captura de tela mostrando como comprar capacidade reservada](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |Campo  |Descrição  |
   |---------|---------|
   |**Escopo**   |  Indica quantas assinaturas podem usar o benefício de faturamento associado à reserva. Também controla como a reserva será aplicada a assinaturas específicas. <br/><br/> Se você selecionar **Compartilhado,** o desconto de reserva será aplicado à capacidade de armazenamento do Azure em qualquer assinatura dentro do seu contexto de faturamento. O contexto de cobrança é baseado em como você se inscreveu no Azure. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes que pagam, o escopo compartilhado inclui todas as assinaturas individuais com taxas de pagamento criadas pelo administrador da conta.  <br/><br/>  Se você selecionar **Assinatura única,** o desconto de reserva será aplicado à capacidade de armazenamento do Azure na assinatura selecionada. <br/><br/> Se você selecionar **Grupo de recursos individuais,** o desconto de reserva será aplicado à capacidade de armazenamento do Azure na assinatura selecionada e ao grupo de recursos selecionado dentro dessa assinatura. <br/><br/> Você pode alterar o escopo da reserva depois de comprar a reserva.  |
   |**Assinatura**  | A assinatura que é usada para pagar a reserva de armazenamento do Azure. O método de pagamento na assinatura selecionada é usado na cobrança dos custos. A assinatura deve ser um dos seguintes tipos: <br/><br/>  Contrato Empresarial (números de oferta: MS-AZR-0017P ou MS-AZR-0148P): Para uma assinatura Enterprise, os encargos são deduzidos do saldo de compromisso monetário da inscrição ou cobrados como excesso de poder. <br/><br/> Assinatura individual com taxas de pagamento (números de oferta: MS-AZR-0003P ou MS-AZR-0023P): Para uma assinatura individual com taxas de pagamento, os encargos são cobrados no cartão de crédito ou no método de pagamento da fatura na assinatura.    |
   | **Região** | A região onde a reserva está em vigor. |
   | **Nível de acesso** | A camada de acesso para onde a reserva está em vigor. As opções incluem *Hot,* *Cool*ou *Archive*. Para obter mais informações sobre os níveis de acesso, consulte [o armazenamento Azure Blob: níveis de acesso quentes, legais e de arquivamento](storage-blob-storage-tiers.md). |
   | **Redundância** | A opção de redundância para a reserva. As opções incluem *LRS,* *ZRS,* *GRS*e *RA-GZRS*. Para obter mais informações sobre opções de redundância, consulte [redundância do Azure Storage](../common/storage-redundancy.md). |
   | **Frequência de faturamento** | Indica com que frequência a conta é cobrada pela reserva. As opções incluem *Mensal ou* *Inicial*. |
   | **Tamanho** | A região onde a reserva está em vigor. |
   |**Termo**  | Um ano ou três anos.   |

1. Depois de selecionar os parâmetros para sua reserva, o portal Azure exibe o custo. O portal também mostra a porcentagem de desconto sobre o faturamento do pay-as-you-go.

1. No painel **de reservas de compras,** revise o custo total da reserva. Você também pode fornecer um nome para a reserva.

    ![Captura de tela mostrando como comprar uma reserva](media/storage-blob-reserved-capacity/purchase-reservations.png)

Depois de comprar uma reserva, ela é aplicada automaticamente a qualquer blob de bloco de armazenamento Azure existente ou aos recursos Azure Data Lake Storage Gen2 que correspondam aos termos da reserva. Se você ainda não criou nenhum recurso de armazenamento do Azure, a reserva será aplicada sempre que você criar um recurso que corresponda aos termos da reserva. Em ambos os casos, o prazo da reserva começa imediatamente após uma compra bem sucedida.

## <a name="exchange-or-refund-a-reservation"></a>Troque ou reembolse uma reserva

Você pode trocar ou reembolsar uma reserva, com certas limitações. Essas limitações são descritas nas seções a seguir.

Para trocar ou reembolsar uma reserva, navegue até os detalhes da reserva no portal azure. Selecione **Troca** ou **Reembolso**e siga as instruções para enviar uma solicitação de suporte. Quando a solicitação for processada, a Microsoft enviará um e-mail para confirmar a conclusão da solicitação.

Para obter mais informações sobre as políticas de reservas do Azure, consulte [trocas de autoatendimento e reembolsos para reservas do Azure](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Troque uma reserva

A troca de uma reserva permite que você receba um reembolso rateado com base na parte não utilizada da reserva. Em seguida, você pode aplicar o reembolso ao preço de compra de uma nova reserva de armazenamento Azure.

Não há limite no número de trocas que você pode fazer. Além disso, não há taxa associada a uma troca. A nova reserva que você comprar deve ser de igual ou maior valor do que o crédito rateado da reserva original. Uma reserva de armazenamento Azure pode ser trocada apenas por outra reserva de armazenamento do Azure, e não por uma reserva para qualquer outro serviço do Azure.

### <a name="refund-a-reservation"></a>Reembolso de uma reserva

Você pode cancelar uma reserva de armazenamento Azure a qualquer momento. Quando você cancelar, você receberá um reembolso rateado com base no prazo restante da reserva, menos uma taxa de rescisão antecipada de 12%. O reembolso máximo por ano é de $50.000.

Cancelar uma reserva imediatamente encerra a reserva e retorna os meses restantes para a Microsoft. O saldo rateado restante, menos a taxa, será reembolsado à sua forma original de compra.

## <a name="expiration-of-a-reservation"></a>Expiração de uma reserva

Quando uma reserva expira, qualquer capacidade de armazenamento do Azure que você está usando essa reserva é cobrada na taxa de pagamento à medida que você vai. As reservas não são renovadas automaticamente.

Você receberá uma notificação por e-mail 30 dias antes do vencimento da reserva, e novamente na data de validade. Para continuar aproveitando a redução de custos que uma reserva proporciona, renove-a até a data de vencimento.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [O que são Reservas do Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Entenda como o desconto de reserva é aplicado ao Armazenamento do Azure](../../cost-management-billing/reservations/understand-storage-charges.md)
