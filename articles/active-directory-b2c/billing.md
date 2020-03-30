---
title: Modelo de faturamento para Azure Active Directory B2C
description: Saiba mais sobre o modelo de faturamento mensal de usuários ativos (MAU) do Azure AD B2C e como ativar o faturamento de uma assinatura específica do Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30ecb1e6faa29482a8d69dd1d08e4f127f515596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190000"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Modelo de faturamento para Azure Active Directory B2C

O uso do Azure Active Directory B2C (Azure AD B2C) é cobrado por uma assinatura vinculada do Azure e usa um modelo de faturamento de usuários ativos (MAU) mensalmente. Saiba como vincular um recurso Azure AD B2C a uma assinatura e como funciona o modelo de faturamento MAU nas seguintes seções.

> [!IMPORTANT]
> Este artigo não contém informações sobre preços. Para obter as informações mais recentes sobre faturamento e preços de uso, consulte [os preços do Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="monthly-active-users-mau-billing"></a>Faturamento mensal dos usuários ativos (MAU)

O faturamento do Azure AD B2C é medido na contagem de usuários únicos com atividade de autenticação dentro de um mês civil, conhecido como faturamento mensal dos usuários ativos (MAU).

A partir de **01 de novembro de 2019**, todos os recém-criados inquilinos Azure AD B2C são cobrados por usuários ativos mensais (MAU). Os inquilinos existentes vinculados [a uma assinatura](#link-an-azure-ad-b2c-tenant-to-a-subscription) em ou após 01 de novembro de 2019 serão cobrados por usuários ativos mensais (MAU).

Se você tem um inquilino Azure AD B2C existente que foi vinculado a uma assinatura antes de 01 de novembro de 2019, você pode optar por fazer um dos seguintes:

* Atualize para o modelo de faturamento dos usuários ativos mensais (MAU) ou
* Mantenha-se no modelo de faturamento por autenticação

### <a name="upgrade-to-monthly-active-users-billing-model"></a>Atualize para o modelo de faturamento de usuários ativos mensais

Os proprietários de assinaturas do Azure que têm acesso administrativo ao recurso Azure AD B2C podem mudar para o modelo de faturamento MAU. As opções de cobrança estão configuradas no recurso Azure AD B2C.

A mudança para o faturamento mensal dos usuários ativos (MAU) é **irreversível.** Depois de converter um recurso Azure AD B2C para o modelo de faturamento baseado em MAU, você não poderá reverter esse recurso para o modelo de faturamento por autenticação.

Veja como fazer a mudança para o faturamento mau para um recurso Azure AD B2C existente:

1. Faça login no [portal Azure](https://portal.azure.com) como proprietário da assinatura.
1. Selecione o filtro **de assinatura Diretório +** no menu superior e selecione o diretório Ad B2C do Azure que deseja atualizar para o faturamento MAU.<br/>
    ![Diretório e filtro de assinatura no portal Azure](./media/billing/portal-mau-01-select-b2c-directory.png)
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Na página **Visão geral** do inquilino Azure AD B2C, selecione o link em nome **do recurso**. Você é direcionado para o recurso Azure AD B2C em seu inquilino Azure AD.<br/>
    ![Link de recursos Azure AD B2C é destaque no portal Azure](./media/billing/portal-mau-02-b2c-resource-link.png)
1. Na página **Visão geral** do recurso Azure AD B2C, em **Unidades faturadas,** selecione o link **Por Autenticação (Alterar para MAU).**<br/>
    ![Mudança para link MAU destacada no portal Azure](./media/billing/portal-mau-03-change-to-mau-link.png)
1. Selecione **Confirmar** para concluir a atualização para o faturamento MAU.<br/>
    ![Diálogo de confirmação de faturamento baseado em MAU no portal Azure](./media/billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>O que esperar quando você faz a transição para o faturamento mau do faturamento por autenticação

A medição baseada em MAU é habilitada assim que você, o proprietário da assinatura/recurso, confirmar a alteração. Sua conta mensal refletirá as unidades de autenticação faturadas até a mudança, e novas unidades da MAU começando com a mudança.

Os usuários não são contados duas vezes durante o mês de transição. Usuários ativos exclusivos que autenticam antes da alteração são cobrados uma taxa por autenticação em um mês calendário. Esses mesmos usuários não estão incluídos no cálculo MAU para o restante do ciclo de faturamento da assinatura. Por exemplo: 

* O inquilino Contoso B2C tem 1.000 usuários. 250 usuários estão ativos em um determinado mês. O administrador de assinatura muda de autenticação por autenticação para usuários ativos mensais (MAU) no dia 10 do mês.
* A cobrança do 1º ao 10º é cobrada usando o modelo de autenticação por autenticação.
  * Se 100 usuários fizerem login durante esse período (1º a 10º), esses usuários serão marcados como *pagos para o mês*.
* O faturamento a partir do dia 10 (o tempo efetivo de transição) é cobrado na taxa de MAU.
  * Se mais 150 usuários fizerem login durante este período (10º-30º), apenas os 150 adicionais são cobrados.
  * A atividade contínua dos primeiros 100 usuários não afeta o faturamento do restante do mês civil.

Durante o período de faturamento da transição, o proprietário da assinatura provavelmente verá entradas para ambos os métodos (por autenticação e por-MAU) aparecerem em sua declaração de faturamento de assinatura do Azure:

* Uma entrada para o uso até a data/hora da alteração que reflete por autenticação.
* Uma entrada para o uso após a mudança que reflete os usuários ativos mensais (MAU).

Para obter as informações mais recentes sobre faturamento de uso e preços para OZure AD B2C, consulte [os preços do Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Vincule um inquilino Azure AD B2C a uma assinatura

As taxas de uso do Azure Active Directory B2C (Azure AD B2C) são cobradas em uma assinatura do Azure. Quando um locatário do Azure AD B2C é criado, o administrador de inquilinos precisa vincular explicitamente o locatário do Azure AD B2C a uma assinatura do Azure.

O link de assinatura é alcançado criando um *recurso* Azure AD B2C dentro da assinatura azure alvo. Vários recursos do Azure AD B2C podem ser criados em uma única assinatura do Azure, juntamente com outros recursos do Azure, como máquinas virtuais, contas de armazenamento e aplicativos lógicos. Você pode ver todos os recursos dentro de uma assinatura indo para o inquilino do Azure Active Directory (Azure AD) com o qual a assinatura está associada.

Uma assinatura vinculada a um inquilino Azure AD B2C pode ser usada para o faturamento do uso do Azure AD B2C ou outros recursos do Azure, incluindo recursos adicionais do Azure AD B2C. A assinatura não pode ser usada para adicionar outros serviços baseados em licença do Azure ou licenças do Office 365 no locatário do Azure AD B2C.

### <a name="prerequisites"></a>Pré-requisitos

* [Assinatura do Azure](https://azure.microsoft.com/free/)
* [Inquilino Azure AD B2C](tutorial-create-tenant.md) que você deseja vincular a uma assinatura
  * Você deve ser um administrador de inquilinos.
  * O inquilino ainda não deve ser vinculado a uma assinatura

### <a name="create-the-link"></a>Criar o link

1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione o filtro **de assinatura Diretório +** no menu superior e selecione o diretório que contém a assinatura do Azure que você gostaria de usar *(não* o diretório que contém o inquilino Azure AD B2C).
1. Selecione Criar um `Active Directory B2C` **recurso,** entrar no campo Pesquisar o **Mercado** e, em seguida, selecionar O Diretório Ativo **Do Azure B2C**.
1. Selecione **Criar**
1. Selecione **Vincular um inquilino AD B2C existente à minha assinatura do Azure**.
1. Selecione **um inquilino Azure AD B2C** a partir da queda. Apenas os inquilinos para os quais você é um administrador global e que ainda não estão vinculados a uma assinatura são mostrados. O **campo de nome de recurso Azure AD B2C** é preenchido com o nome de domínio do inquilino Azure AD B2C selecionado.
1. Selecione uma **assinatura** ativa do Azure da qual você é um administrador.
1. Em **grupo Resource,** selecione **Criar novo**e, em seguida, especificar o local do grupo **Recurso**. As configurações do grupo de recursos aqui não têm impacto na localização, desempenho ou status de faturamento do seu inquilino Azure AD B2C.
1. Selecione **Criar**.
    ![A página de criação de recursos Azure AD B2C no portal Azure](./media/billing/portal-01-create-b2c-resource-page.png)

Depois de concluir essas etapas para um inquilino Azure AD B2C, sua assinatura do Azure é cobrada de acordo com os detalhes do Azure Direct ou Enterprise Agreement, se aplicável.

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Gerenciar seus recursos de locatário do Azure AD B2C

Depois de criar o recurso Azure AD B2C em uma assinatura do Azure, você deve ver um novo recurso do tipo "inquilino B2C" aparecer com seus outros recursos do Azure.

Você pode usar esse recurso para:

* Navegue até a assinatura para revisar as informações de faturamento
* Obtenha o ID de inquilino do inquilino Azure AD B2C em formato GUID
* Ir até seu locatário do Azure AD B2C
* Enviar uma solicitação de suporte
* Mova seu recurso de inquilino Azure AD B2C para outro grupo de recursos ou assinatura do Azure

![Página de configurações de recursos B2C no portal Azure](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>Restrições regionais

Se você estabeleceu restrições regionais para a criação de recursos do Azure em sua assinatura, essa restrição pode impedi-lo de criar o recurso Azure AD B2C.

Para mitigar esse problema, relaxe suas restrições regionais.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Assinaturas do Azure Cloud Solution Providers (CSP)

Há suporte para as assinaturas do CSP (Provedores de Soluções de Nuvem) do Azure no Azure AD B2C. A funcionalidade está disponível por meio de APIs ou pelo portal do Azure para o Azure AD B2C e todos os recursos do Azure. Os administradores de assinatura do CSP podem vincular, mover e excluir relacionamentos com o Azure AD B2C, como feito com outros recursos do Azure.

O gerenciamento do Azure AD B2C usando o controle de acesso baseado em função não é afetado pela associação entre o locatário do Azure AD B2C e uma assinatura do Azure CSP. O controle de acesso baseado em funções é obtido usando funções baseadas em inquilinos, e não por assinatura.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Alterar a assinatura de faturamento do inquilino Azure AD B2C

Os inquilinos Do Azure AD B2C podem ser movidos para outra assinatura se as assinaturas de origem e destino existirem dentro do mesmo inquilino do Azure Active Directory.

Para saber como mover recursos do Azure, como o inquilino Azure AD B2C para outra assinatura, consulte [Mover recursos para novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Antes de iniciar a mudança, certifique-se de ler todo o artigo para entender completamente as limitações e requisitos para tal movimento. Além das instruções para movimentar recursos, ele inclui informações críticas, como uma lista de verificação pré-movimento e como validar a operação de movimento.

## <a name="next-steps"></a>Próximas etapas

Para obter as informações de preços mais recentes, consulte [os preços do Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
