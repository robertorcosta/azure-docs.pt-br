---
title: Azure Marketplace
description: Descreve como os clientes do EA podem usar o Azure Marketplace
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/01/2020
ms.author: banders
ms.openlocfilehash: 4721d8dbe5973baf2ac82124ff516a89398227cb
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298186"
---
# <a name="azure-marketplace"></a>Azure Marketplace

Este artigo explica como os clientes e parceiros do EA podem exibir os encargos do Marketplace e habilitar as compras do Azure Marketplace.

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace para clientes EA

Para clientes diretos, as cobranças do Azure Marketplace ficam visíveis no Azure Enterprise Portal. As compras e consumo no Azure Marketplace são faturadas fora do compromisso monetário e são cobradas trimestral ou mensalmente e em atraso.

Clientes indiretos podem encontrar suas assinaturas do Azure Marketplace na página **Gerenciar Assinaturas** do Azure Enterprise Portal, mas os preços estarão ocultos. Os clientes devem entrar em contato com o LSP (Provedor de Soluções de Licenciamento) para saber mais sobre cobranças do Azure Marketplace.

Novas compras mensais ou anuais recorrentes do Azure Marketplace são cobradas por completo durante o período em que os itens do Azure Marketplace são comprados. Esses itens serão renovados automaticamente após o período seguinte no mesmo dia da compra original.

Os encargos mensais recorrentes existentes continuarão a ser renovados no primeiro dia de cada mês do calendário. Os encargos anuais serão renovados no aniversário da data de compra.

Alguns serviços de revendedores de terceiros disponíveis no Azure Marketplace agora consumirão o saldo do compromisso monetário do seu EA (Contrato Enterprise). Anteriormente, esses serviços eram cobrados fora do compromisso monetário do EA, sendo faturados separadamente. O compromisso monetário do EA para esses serviços no Azure Marketplace ajudam a simplificar o gerenciamento de compras e pagamentos do cliente. Para obter uma lista completa dos serviços que agora consomem o compromisso monetário, confira a [atualização de 6 de março de 2018 no site do Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

### <a name="partners"></a>Parceiros

Os LSPs podem baixar uma lista de preços do Azure Marketplace na página da tabela de preços no Azure Enterprise Portal. Selecione o link **lista de preços do Marketplace** no canto superior direito. A lista de preços do Azure Marketplace mostra todos os serviços disponíveis e seus preços.

Para baixar a lista de preços:

1. No Azure Enterprise Portal, acesse **Relatórios** > **Tabela de Preços**.
1. No canto superior direito, localize o link para a lista de preços do Azure Marketplace sob seu nome de usuário.
1. Clique com o botão direito do mouse no link e selecione **Salvar Destino Como**.
1. Na janela **Salvar**, altere o título do documento para `AzureMarketplacePricelist.zip`, o que alterará o arquivo de .xlsx para um arquivo .zip.
1. Quando o download for concluído, você terá um arquivo zip com listas de preços específicas de cada país.
1. Os LSPs devem consultar o arquivo de seu país para obter os preços específicos. Os LSPs podem usar a guia **Notificações** para estarem atentos às SKUs que são novas ou foram desativadas.
1. As alterações de preço ocorrem com pouca frequência. Os LSPs recebem notificações por email de aumentos de preço e alterações de FX (câmbio internacional) com 30 dias de antecedência.
1. Os LSPs recebem uma fatura por registro, por ISV, por trimestre.

### <a name="enabling-azure-marketplace-purchases"></a>Habilitar compras no Azure Marketplace

Os administradores corporativos podem desabilitar ou habilitar compras no Azure Marketplace para todas as assinaturas do Azure sob o registro deles. Se o administrador corporativo desabilitar as compras e houver assinaturas do Azure que já têm assinaturas do Azure Marketplace, essas assinaturas do Azure Marketplace não serão canceladas nem afetadas.

Embora os clientes possam converter suas assinaturas diretas do Azure para o EA do Azure associando-as ao registro no Azure Enterprise Portal, essa ação não converte automaticamente as assinaturas filhas.

Para habilitar compras no Azure Marketplace:

1. Entre no Azure Enterprise Portal como um administrador corporativo.
1. Acesse **Gerenciar**.
1. Em **Detalhe do registro**, selecione o ícone de lápis ao lado do item de linha **Azure Marketplace**.
1. Selecione **Habilitado/Desabilitado** ou **Somente SKUs Gratuitas e BYOL**, conforme apropriado.
1. Clique em **Salvar**.

> [!NOTE]
> A opção Somente SKUs Gratuitas e BYOL (traga sua própria licença) limita a compra e a aquisição de SKUs do Azure Marketplace a apenas SKUs Gratuitas e BYOL.

### <a name="services-billed-hourly-for-azure-ea"></a>Serviços cobrados por hora para o EA do Azure

Os seguintes serviços são cobrados por hora em um Contrato Enterprise em vez da taxa mensal em MOSP:

- Rede de Distribuição de Aplicativos
- Firewall do Aplicativo Web

### <a name="azure-remoteapp"></a>RemoteApp do Azure

Se você tiver um Contrato Enterprise, pagará pelo Azure RemoteApp com base em sua categoria de preços de Contrato Enterprise. Não há cobranças adicionais. O preço padrão inclui um inicial de 40 horas. O preço ilimitado cobre um inicial de 80 horas. O RemoteApp para de emitir o uso além das 80 horas.

## <a name="azure-marketplace-faq"></a>Perguntas frequentes sobre o Azure Marketplace

Esta seção explica como seu compromisso monetário do Azure pode se aplicar a alguns serviços de revendedores de terceiros no Azure Marketplace.

### <a name="what-changed-with-azure-marketplace-services-and-ea-monetary-commitment"></a>O que mudou com os serviços do Azure Marketplace e o compromisso monetário de EA?

A partir de 1º de março de 2018, alguns serviços de revendedores de terceiros consomem o MC (compromisso monetário) de EA. Exceto para as RI (instâncias de VM Reservadas) do Azure, os serviços foram cobrados anteriormente fora do compromisso monetário de EA e foram faturados separadamente.

Expandimos o uso do MC para incluir alguns dos serviços do Azure Marketplace publicados por terceiros que são adquiridos com mais frequência. O compromisso monetário do EA para esses serviços no Azure Marketplace ajuda a simplificar o gerenciamento de compras e pagamentos.

### <a name="why-did-we-make-this-change"></a>Por que fizemos essa alteração?

Os clientes estão continuamente procurando maneiras adicionais de aproveitar o pagamento adiantado do MC. Essa alteração foi solicitada com frequência pelos clientes e afetou uma grande parte dos clientes do Azure Marketplace.

### <a name="how-do-you-benefit"></a>Como você se beneficia?

Você tem uma experiência de cobrança mais simples e é mais capaz de gastar seu compromisso monetário de EA. Como esses serviços estão incluídos no MC pré-pago, seu compromisso monetário de EA torna-se mais valioso.

### <a name="what-azure-marketplace-services-use-ea-monetary-commitment-and-how-do-i-know"></a>Quais serviços do Azure Marketplace usam o compromisso monetário de EA e como posso saber?

Quando você compra um serviço que usa o MC, o Azure Marketplace apresenta um aviso de isenção de responsabilidade. Há suporte para alguns serviços publicados por Red Hat, SUSE, Autodesk e Oracle. Atualmente, com nomes semelhantes, os serviços publicados por outras partes não deduzem do MC. Uma lista completa está disponível no final das perguntas frequentes.

### <a name="what-if-my-ea-monetary-commitment-runs-out"></a>E se o meu compromisso monetário de EA acabar?

Se você consumir todo os seu MC e ficar em excedente, as cobranças relacionadas a esses serviços aparecerão na próxima fatura excedente, com outros serviços de consumo. Antes da alteração de 1º de março de 2018, essas cobranças eram faturadas com outros serviços do Azure Marketplace.

### <a name="why-dont-all-azure-marketplaces-consume-ea-monetary-commitment"></a>Por que nem todos os Azure Marketplaces consomem o compromisso monetário de EA?

Trabalhamos com frequência para fornecer a melhor experiência do cliente relacionada ao compromisso monetário do EA. Essa alteração afetou uma grande quantidade de clientes e uma parte significativa do gasto total no Azure Marketplace. Outros serviços podem ser adicionados no futuro.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Como isso afeta o registro indireto e os parceiros?

Nossos clientes ou parceiros de registro indireto não serão afetados. Esses serviços estão sujeitos aos mesmos recursos de marcação de parceiros que outros serviços de consumo. A única alteração é que as cobranças aparecem em uma fatura diferente e o pagamento delas sai do compromisso monetário do EA do cliente.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-ea-monetary-commitment"></a>Há uma lista de serviços do Azure Marketplace que consomem o compromisso monetário de EA?

As ofertas específicas do Azure Marketplace podem usar fundos do compromisso monetário. Confira [serviços de terceiros que usam o compromisso monetário](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment) para obter uma lista completa de produtos participantes desse programa.


## <a name="next-steps"></a>Próximas etapas

- Obtenha mais informações sobre [Preços](ea-pricing-overview.md).