---
title: Perguntas comuns sobre a migração para o Partner Center – marketplace comercial da Microsoft
description: Respostas para perguntas frequentes sobre a migração de ofertas de Portal do Cloud Partner para o Partner Center.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mingshen
ms.openlocfilehash: c4fdde94ea703d194e2de27a8df429f62ea374f4
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727474"
---
# <a name="common-questions-about-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Perguntas comuns sobre a migração do Portal do Cloud Partner para o Partner Center

Este artigo aborda as perguntas mais frequentes sobre a migração de ofertas do Portal do Cloud Partner para o Partner Center.

## <a name="what-does-offer-migration-mean"></a>O que significa a oferta de migração?

Estamos movendo seus dados de oferta do Portal do Cloud Partner para o Partner Center com alterações na experiência de publicação e gerenciamento da oferta.

| Área  | Alterações  |
|-------|----------|
| **Publicação e oferta de experiência de gerenciamento** | Você terá uma experiência de usuário aprimorada com uma interface intuitiva no Partner Center. Para obter mais detalhes, consulte [Quais são as diferenças entre o Partner Center e o Portal do Cloud Partner?](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **Disponibilidade de suas ofertas no Marketplace** | Sem alterações. Se sua oferta estiver ativa no Marketplace, ela continuará a residir durante e após a conclusão da migração. |
| **Novas compras e implantações** | Sem alterações. Seus clientes continuarão a ser capazes de comprar e implantar suas ofertas sem interrupções. |
| **Pagamentos** | Todas as compras e implantações que ocorrem durante ou após a migração continuarão sendo pagas para você normalmente. |
|**Integrações de API com APIs existentes de [Portal do Cloud Partner ](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)** | As APIs existentes do Portal do Cloud Partner continuarão a ter suporte após a migração e suas integrações existentes continuarão funcionando. Para obter mais detalhes, consulte [As APIs REST Portal do Cloud Partner terão suporte após a migração?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>Ainda posso acessar o Portal do Cloud Partner e gerenciar minhas ofertas durante a migração?

Todas as ofertas foram migrar para o Partner Center. O período de migração para cada oferta deve ter sido inferior a 24 horas. Depois que sua oferta foi migrada, você deve ter recebido uma notificação por e-mail.

Depois que suas ofertas forem migradas, elas estarão no modo somente leitura **por um período de tempo limitado** no Portal do Cloud Partner. Seu status mostrará "Movido para o Partner Center" e incluirá um link para sua oferta no Partner Center, conforme mostrado nas capturas de tela a seguir. A partir deste ponto, você gerenciará as atualizações para todas as suas ofertas ou criará novas ofertas exclusivamente por meio do Partner Center,

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="Ilustra a mensagem fornecida para ofertas que foram migradas para o Partner Center":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="Ilustra a página de Portal do Cloud Partner para uma oferta migrada.":::

## <a name="how-will-i-create-new-offers"></a>Como criar novas ofertas?

No Portal do Cloud Partner, você será direcionado para criar novas ofertas no Partner Center

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Ilustra o menu para criar uma nova oferta no Portal do Cloud Partner":::

Depois de selecionar uma nova oferta, você verá uma mensagem, como a seguinte.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="Ilustra a mensagem recebida ao criar uma nova oferta em CPP":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>É necessário criar uma nova conta para gerenciar ofertas no Partner Center?

Não. Sua conta subjacente será preservada e você já deverá estar gerenciando-a no Partner Center. Isso significa que, se você for um parceiro existente, poderá usar suas credenciais de conta de Portal do Cloud Partner existentes para fazer logon no Partner Center após a migração. Apenas ofertas e a experiência de gerenciamento associada estão mudando do Portal do Cloud Partner para o Partner Center. Pedimos que você não crie novas contas, pois suas ofertas não serão associadas à nova conta.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>Vejo uma mensagem no Portal do Cloud Partner para ativar minha conta, o que isso significa?

Precisamos de mais alguns detalhes para que você possa migrar corretamente sua conta para o Partner Center e permitir que você gerencie suas ofertas no Partner Center após a conclusão da migração da oferta. Para obter mais detalhes sobre a ativação da conta, consulte [migração de conta do Portal do Cloud Partner para o Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc).

As etapas necessárias para concluir a ativação da conta variam com base em sua função de conta.

| Função de conta | Etapas de ativação |
|--------------|----------------|
|Proprietário | Acesse a página [Perfil do Publisher](https://cloudpartner.azure.com/#profile) no Portal do Cloud Partner e, em seguida, clique no link na faixa para ativar. Você será redirecionado para o Partner Center para concluir a ativação da conta. |
| Colaborador | Somente um usuário na conta com uma função de proprietário pode ativar a conta. Contate os proprietários da conta para concluir a ativação da conta. Os proprietários da sua conta devem ser listados na mensagem do banner. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>Estou tendo problemas para fazer logon na minha conta e abrir um tíquete de suporte

Se não for possível fazer logon em sua conta, você poderá abrir um [tíquete de suporte](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>Onde posso encontrar a documentação sobre a nova experiência de publicação do Partner Center?

Vá para [Documentação do marketplace comercial](https://docs.microsoft.com/azure/marketplace/). Em seguida, expanda **Portal do Marketplace comercial no Partner Center**  > **Crie uma nova oferta** para ver os tópicos da ajuda para a criação de cada tipo de oferta.

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="Ilustra os tópicos de ajuda para o Partner Center":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>Quais são as diferenças entre o Partner Center e o Portal do Cloud Partner?

Você pode observar as seguintes diferenças entre o Portal do Cloud Partner e o Partner Center.

### <a name="modular-publishing-capabilities"></a>Recursos de publicação modulares

O Partner Center fornece uma opção de publicação modular que permite selecionar as alterações que você deseja publicar em vez de sempre publicar todas as atualizações ao mesmo tempo. Por exemplo, a captura de tela a seguir mostra que as únicas alterações selecionadas a serem publicadas são as alterações **Propriedades** e **listagem de oferta**.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="Ilustra a página de visualização e publicação":::

As atualizações que você não publica são salvas como rascunhos. Continue a usar sua visualização de oferta para verificar sua oferta antes de torná-la dinâmica para o público.

### <a name="rich-text-format"></a>Formato Rich Text

Aprimore sua oferta e a descrição do plano usando o editor de Rich Text na página de listagem da oferta e do plano.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="Ilustra o editor de rich text":::

### <a name="enhanced-preview-options"></a>Opções de visualização avançada

O Partner Center inclui um recurso [comparar](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) com opções de filtragem aprimoradas. Isso lhe dá a capacidade de comparar com as versões de visualização e dinâmica da oferta.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="Ilustra o recurso comparar":::

### <a name="branding-and-navigation-changes"></a>Alterações de identidade visual e navegação

Você observará algumas alterações de identidade visual. Por exemplo, "SKUs" são marcadas como "planos" no Partner Center.

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="Ilustra a visão geral do plano.":::

Além disso, as informações que você usou para fornecer nas páginas **Marketplace** ou **Detalhes da vitrine** (serviço de consultoria, aplicativo Power BI) no Portal do Cloud Partner são coletadas na página **Lista de ofertas** no Partner Center.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="Ilustra a página de listagem da oferta.":::

As informações que você usou para fornecer SKUs em uma única página no Portal do Cloud Partner podem agora ser coletadas em várias páginas no Partner Center:

* Página Configurar plano
* Página Listagem de planos
* Página de disponibilidade do plano
* Planejar a página de configuração técnica, conforme mostrado nesta captura de tela.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="Ilustra a página de configuração técnica do plano.":::

Sua ID de oferta agora é mostrada na barra de navegação à esquerda da oferta.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="Ilustra o menu de navegação à esquerda com a ID da oferta.":::

### <a name="stop-selling-an-offer"></a>Parar de vender a oferta

Você pode solicitar para [parar de vender uma oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) no Marketplace diretamente do portal do Partner Center. A opção está disponível na página **Visão geral da oferta** para sua oferta.

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="Ilustra a página Visão geral da oferta com a opção parar venda.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Quais páginas no Partner Center correspondem às páginas que usei na Portal do Cloud Partner?

A tabela a seguir mostra os links correspondentes entre os dois portais.

| Página | Link do Portal do Cloud Partner | Link do Partner Center |
|------|---------------------------|---------------------|
| **Página de todas as ofertas** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **Página de todos os editores** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **Perfil do editor** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **Página dos usuários** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **Página de histórico** | https://cloudpartner.azure.com/#history | O recurso de histórico ainda não tem suporte no Partner Center. |
| **Painel de informações** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **Relatórios de pagamento** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>As APIs REST Portal do Cloud Partner terão suporte após a migração?

As APIs de Portal do Cloud Partner são integradas ao Partner Center e continuarão funcionando depois que suas ofertas forem migradas para o Partner Center. A integração apresenta pequenas alterações. Examine as alterações na tabela abaixo para garantir que seu código continue a funcionar após a migração para o Partner Center.

| **API** | **Descrição da alteração** | **Impacto** |
| ------- | ---------------------- | ---------- |
| POSTAR publicação, GoLive, cancelar | Para ofertas migradas, o cabeçalho de resposta terá um formato diferente, mas continuará a funcionar da mesma forma, denotando um caminho relativo para recuperar o status da operação. | Ao enviar qualquer uma das solicitações POST correspondentes para uma oferta, o cabeçalho Location terá um dos dois formatos, dependendo do status de migração da oferta:<ul><li>Ofertas não migradas<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Ofertas migradas<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Operação GET | Para ofertas que anteriormente tinham suporte no campo 'notification-e-mail' na resposta, esse campo será preterido e não retornará mais para as ofertas migradas. | Para ofertas migradas, não enviaremos mais notificações para a lista de e-mails especificados nas solicitações. Em vez disso, o serviço de API será alinhado com o processo de e-mail de notificação no Partner Center para enviar e-mails. Especificamente, as notificações serão enviadas para o endereço de e-mail definido na seção informações de contato do vendedor de suas configurações de conta no Partner Center, para notificá-lo sobre o andamento da operação.<br><br>Examine o endereço de e-mail definido na seção informações de contato do vendedor na [configurações da conta](https://partner.microsoft.com/dashboard/account/management) no Partner Center para garantir que o e-mail correto seja fornecido para notificações.  |
| | | |
