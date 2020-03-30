---
title: Digestão de recomendação para O Azure Advisor
description: Obtenha resumo periódico de suas recomendações ativas
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 98f1bfd42a486e005cd1e777a83f5b615a7c8304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502458"
---
# <a name="configure-periodic-summary-for-recommendations"></a>Configure resumo periódico para recomendações

Os **resumos das recomendações do orientador** fornecem uma maneira fácil e proativa de ficar por dentro de suas recomendações ativas, em diferentes categorias. O recurso oferece a capacidade de configurar notificações periódicas para o resumo de todas as suas recomendações ativas, em diferentes categorias. Você pode escolher o canal desejado para notificações como e-mail, sms ou outros, usando grupos de ação. Este artigo mostra como configurar uma **digestia de recomendação** para suas recomendações do Advisor.


## <a name="setting-up-your-recommendation-digest"></a>Configuração do seu digestor de recomendação 

A experiência **de criação de digestão** de recomendação ajuda a configurar o resumo. Você pode selecionar abaixo parâmetros para configurações:
1. Categoria: Temos categorias de recomendação como custo, alta disponibilidade, desempenho e excelência operacional. O recurso ainda não está disponível para recomendações de segurança.
2. Frequência de digestão: A frequência para as notificações sumárias pode ser semanal, quinzenal e mensal.
3. Grupo de ação: Você pode selecionar um grupo de ação existente ou criar um novo grupo de ação. Para saber mais sobre grupos de ação, consulte [criar e gerenciar grupos de ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).
4. Linguagem para digerir
5. Nome de digestão de recomendação: Você pode usar uma corda fácil de usar para rastrear melhor e monitorar os digestos.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Etapas para criar digestde recomendação no portal Azure

Aqui estão as etapas para criar **a digestão da recomendação:**
* **Passo 1:** No portal Azure, vá para **o Advisor** e na seção **Monitoramento,** selecione **Recomendação de digestão** 

   ![Ponto de entrada de digestão de recomendação](./media/digest-0.png)

* **Passo 2:** Selecione **Nova recomendação digerir** da barra superior abaixo:

   ![Criar digestor de recomendação](./media/digest-5.png)

* **Passo 3:** Na seção **escopo,** selecione a **assinatura** para o seu digest

   ![Fornecer entradas de digestão de recomendação](./media/digest-1.png)

* **Passo 4:** Na seção **de condições,** selecione as configurações como **categoria,** **frequência** e **idioma**

   ![Fornecer condições de entrada de digestão de recomendação](./media/digest-2.png)

* **Passo 5:** Na seção **grupo de ação,** selecione o **grupo de ação** para a digestão. Você pode aprender mais aqui - [Criar e gerenciar grupos de ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

   ![Fornecer grupo de ação de entrada digestora de recomendação](./media/digest-3.png)

* **Passo 6:** Nesta seção final para **obter detalhes de digestão,** você pode atribuir nome e estado à sua recomendação de digestão. Pressione **criar digest de recomendação** para completar a configuração.
   ![Criação completa de digestor de recomendação](./media/digest-4.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as recomendações do Assistente, consulte:
* [Introdução ao Azure Advisor](advisor-overview.md)
* [Introdução ao Advisor](advisor-get-started.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de Excelência Operacional do Orientador](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)
