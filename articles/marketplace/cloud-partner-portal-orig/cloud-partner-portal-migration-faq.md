---
title: Perguntas frequentes sobre a migração para o Partner Center | Mercado Azure
description: Este artigo aborda perguntas comumente feitas sobre a migração de ofertas do Portal de Parceiros na Nuvem para o Partner Center.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mingshen
ms.openlocfilehash: 672153eba4aa2b739b67694f939c4796b39ac4c6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274374"
---
# <a name="frequently-asked-questions-for-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Perguntas frequentes para migrar do Portal de Parceiros em Nuvem para o Partner Center

Este artigo aborda perguntas comumente feitas sobre a migração de ofertas do Portal de Parceiros em Nuvem para o Partner Center.

## <a name="what-does-offer-migration-mean"></a>O que significa a migração?

Estamos movendo seus dados de oferta do Portal de Parceiros em Nuvem para o Partner Center com mudanças na experiência de publicação e gerenciamento de ofertas.

| Área  | Alterações  |
|-------|----------|
| **Publicando e oferecendo experiência de gestão** | Você terá uma melhor experiência de usuário com uma interface intuitiva no Partner Center. Para mais detalhes, veja [Quais são as diferenças entre o Partner Center e o Portal de Parceiros na Nuvem?](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **Disponibilidade de suas ofertas no marketplace** | Nenhuma alteração. Se sua oferta estiver ao vivo no mercado, ela continuará viva durante e após a migração ser concluída. |
| **Novas compras e implantações** | Nenhuma alteração. Seus clientes continuarão a ser capazes de comprar e implantar suas ofertas sem interrupções. |
| **Pagamentos** | Quaisquer compras e implantações que ocorram durante ou após a migração continuarão a ser pagas a você normalmente. |
|**Integrações de API com APIs existentes [do Portal de Parceiros de Nuvem](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)** | As APIs existentes do Portal de Parceiros em Nuvem continuarão a ser suportadas após a migração e suas integrações existentes continuarão a funcionar. Para obter mais detalhes, [consulte As APIs do Cloud Partner Portal REST serão suportadas após a migração?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>Ainda posso acessar o Portal de Parceiros na Nuvem e gerenciar minhas ofertas durante a migração?

As ofertas serão migradas para o Partner Center a partir de 13 de abril durante todo o mês de maio. Durante este período de tempo, você poderá acessar o Portal de Parceiros na Nuvem como de costume, exceto pelo tempo que você oferecer está programado para migração.
Enquanto suas ofertas estão sendo migradas, elas terão o status "Bloqueado – movendo-se para o Partner Center", como visto na captura de tela a seguir. Esse período de migração deve ser inferior a 24 horas. Durante este tempo, você não poderá fazer nenhuma atualização de suas ofertas. Você receberá uma notificação por e-mail depois de concluirmos a migração de suas ofertas.

:::image type="content" source="media/migration-faq/all-offers-1.png" alt-text="Ilustra o status das ofertas migradas.":::

Depois que suas ofertas forem migradas, elas estarão no modo somente leitura **por um período limitado de tempo** no Portal do Parceiro na Nuvem. Seu status mostrará "Movido para o Partner Center" e incluirá um link para sua oferta no Partner Center, como mostrado nas capturas de tela a seguir. A partir deste ponto, você gerenciará atualizações para todas as suas ofertas ou criará novas ofertas exclusivamente através do Partner Center,

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="Ilustra a mensagem dada para ofertas que foram migradas para o Partner Center":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="Ilustra a página do Portal do Parceiro na Nuvem para uma oferta migrada.":::

## <a name="how-will-i-create-new-offers"></a>Como criarei novas ofertas?

A partir do Portal de Parceiros em Nuvem, você será direcionado para criar novas ofertas no Partner Center

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Ilustra o menu para criar uma nova oferta no Cloud Partner Portal":::

Depois de selecionar uma nova oferta, você verá uma mensagem, como a seguinte.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="Ilustra a mensagem recebida ao criar uma nova oferta no CPP":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>Preciso criar uma nova conta para gerenciar ofertas no Partner Center?

Não. Sua conta subjacente será preservada, e você já deve gerenciá-la no Partner Center. Isso significa que, se você é um parceiro existente, você pode usar as credenciais existentes do Portal do Parceiro na Nuvem para fazer login no Partner Center após a migração. Apenas as ofertas e a experiência de gestão associada estão mudando do Portal de Parceiros em Nuvem para o Partner Center. Pedimos que você não crie novas contas, pois suas ofertas não serão associadas à nova conta.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>Vejo uma mensagem no Portal parceiros da nuvem para ativar minha conta, o que isso significa?

Precisamos de mais alguns detalhes de você para migrar adequadamente sua conta para o Partner Center e permitir que você gerencie suas ofertas no Partner Center após a migração da oferta ser concluída. Para obter mais detalhes sobre a ativação da conta, consulte [a migração da conta do Portal de Parceiros da Nuvem para o Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc).

As etapas necessárias para concluir a ativação da conta variam de acordo com a função da sua conta.

| Função de conta | Etapas de ativação |
|--------------|----------------|
|Proprietário | Acesse a página perfil do [editor](https://cloudpartner.azure.com/#profile) no Portal do Parceiro de Nuvem e clique no link no banner para ativar. Você será redirecionado para o Partner Center para concluir a ativação da conta. |
| Colaborador | Apenas um usuário na conta com uma função de proprietário pode ativar a conta. Entre em contato com os proprietários da sua conta para concluir a ativação da conta. Os proprietários de suas contas devem ser listados na mensagem do banner. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>Estou tendo problemas para entrar na minha conta e abrir um bilhete de suporte

Se você não puder fazer login na sua conta, você pode abrir um [ticket de suporte](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>Onde posso encontrar documentação sobre a nova experiência de publicação do Partner Center?

Vá para a documentação do [mercado comercial.](https://docs.microsoft.com/azure/marketplace/) Em seguida, expanda **o Portal do Mercado Comercial no Partner Center**  > **Crie uma nova oferta** para ver os tópicos de ajuda para criar cada tipo de oferta.

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="Ilustra os tópicos de ajuda para o Partner Center":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>Quais são as diferenças entre o Partner Center e o Portal de Parceiros na Nuvem?

Você pode notar as seguintes diferenças entre o Portal de Parceiros na Nuvem e o Partner Center.

### <a name="modular-publishing-capabilities"></a>Recursos de publicação modular

O Partner Center fornece uma opção de publicação modular que permite selecionar as alterações que deseja publicar em vez de sempre publicar todas as atualizações de uma só vez. Por exemplo, a captura de tela a seguir mostra que as únicas alterações selecionadas a serem publicadas são as alterações em **Propriedades** e **Listagem de Ofertas**.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="Ilustra a página Pré-visualização e publicação":::

As atualizações que você não publica são salvas como rascunhos. Continue a usar sua pré-visualização de oferta para verificar sua oferta antes de torná-la ao vivo para o público.

### <a name="rich-text-format"></a>Formato de texto rico

Melhore sua oferta e a descrição do plano usando o Rich Text Editor na página De Listagem de Ofertas e Lista de Planos.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="Ilustra o rico editor de texto":::

### <a name="enhanced-preview-options"></a>Opções de visualização aprimoradas

O Partner Center inclui um [recurso de comparação](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) com opções de filtragem melhoradas. Isso lhe dá a capacidade de comparar com as versões de pré-visualização e ao vivo da oferta.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="Ilustra o recurso de comparação":::

### <a name="branding-and-navigation-changes"></a>Mudanças de marca e navegação

Você vai notar algumas mudanças de marca. Por exemplo, "SKUs" são marcados como "Planos" no Partner Center.

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="Ilustra a visão geral do plano.":::

Além disso, as informações que você costumava fornecer nas páginas **Marketplace** ou S**torefront Details** (Consulting Service, Power BI app) no Portal do Parceiro em Nuvem são coletadas na página **de listagem de ofertas** no Partner Center.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="Ilustra a página de listagem de ofertas.":::

As informações que você usou para fornecer para SKUs em uma única página no Portal de Parceiros na Nuvem podem agora ser coletadas em várias páginas no Partner Center:

* Página de configuração do plano
* Página de listagem de planos
* Página de disponibilidade do plano
* Planeje a página de configuração técnica, como mostrado nesta captura de tela.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="Ilustra a página de configuração técnica do Plano.":::

Sua id de oferta agora é mostrada na barra de navegação à esquerda da oferta.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="Ilustra o menu de navegação à esquerda com o ID de oferta.":::

### <a name="stop-selling-an-offer"></a>Pare de vender uma oferta.

Você pode solicitar parar [de vender uma oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) no marketplace diretamente do portal do Partner Center. A opção está disponível na página **visão geral da Oferta** para sua oferta.

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="Ilustra a página Visão Geral da Oferta com a opção stop selling.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Quais páginas no Partner Center correspondem às páginas que usei no Portal de Parceiros na Nuvem?

A tabela a seguir mostra links correspondentes entre os dois portais.

| Página | Link do Portal do Parceiro na Nuvem | Link do Partner Center |
|------|---------------------------|---------------------|
| **Página de todas as ofertas** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **Página de todos os publicadores** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **Perfil do editor** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **Página Usuários** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **Página de história** | https://cloudpartner.azure.com/#history | O recurso História ainda não é suportado no Partner Center. |
| **Painel de insights** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **Relatório de pagamento** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>As APIs do Cloud Partner Portal REST serão suportadas após a migração?

As APIs do Portal de Parceiros em Nuvem são integradas ao Partner Center e continuarão a funcionar depois que suas ofertas forem migradas para o Partner Center. A integração introduz pequenas mudanças. Revise as alterações na tabela abaixo para garantir que seu código continue funcionando após a migração para o Partner Center.

| **API** | **Descrição da alteração** | **Impacto** |
| ------- | ---------------------- | ---------- |
| POST Publique, GoLive, Cancele | Para ofertas migradas, o cabeçalho de resposta terá um formato diferente, mas continuará a funcionar da mesma forma, denotando um caminho relativo para recuperar o status da operação. | Ao enviar qualquer uma das solicitações POST correspondentes para uma oferta, o cabeçalho de localização terá um de dois formatos, dependendo do status de migração da oferta:<ul><li>Ofertas não migradas<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Ofertas migradas<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Operação GET | Para ofertas que anteriormente suportavam o campo 'notificação-e-mail' na resposta, este campo será preterido e não retornará mais para ofertas migradas. | Para ofertas migradas, não enviaremos mais notificações para a lista de e-mails especificados nas solicitações. Em vez disso, o serviço de API se alinhará com o processo de e-mail de notificação no Partner Center para enviar e-mails. Especificamente, as notificações serão enviadas para o endereço de e-mail definido na seção informações de contato do Vendedor das configurações da sua conta no Partner Center, para notificá-lo sobre o progresso da operação.<br><br>Revise o endereço de e-mail definido na seção informações de contato do Vendedor nas [configurações da Conta](https://partner.microsoft.com/dashboard/account/management) na Central de Parceiros para garantir que o e-mail correto seja fornecido para notificações.  |
| | | |
