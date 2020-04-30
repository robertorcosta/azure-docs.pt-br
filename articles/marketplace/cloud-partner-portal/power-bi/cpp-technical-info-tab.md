---
title: Informações técnicas para uma oferta de aplicativo Power BI | Azure Marketplace
description: Configure os campos de informações técnicas para uma oferta de aplicativo do Power BI para o Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: b012ac012c7de3c6428ecbe2d2b922a2a9aa0523
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82141771"
---
# <a name="power-bi-apps-technical-info-tab"></a>Guia de informações técnicas sobre aplicativos do Power BI

>[!Important]
>A partir de 13 de abril de 2020, começaremos a mover o gerenciamento de suas ofertas de aplicativo Power BI para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções em [Power bi visão geral da criação de aplicativo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) para gerenciar suas ofertas migradas.

Na página **nova oferta** , use a guia **informações técnicas** para fornecer o Power bi URL do pacote do instalador e outras informações necessárias para validar a nova oferta.  Para a versão inicial, todos os aplicativos Power BI são gratuitos e estão disponíveis para download em AppSource. Por isso, você não pode definir as unidades de manutenção de estoque (SKUs) para esse tipo de oferta.

![A guia informações técnicas](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Campos de Informações Técnicas 

Na guia **informações técnicas** , preencha os campos descritos na tabela a seguir. Um asterisco (*) no final de um rótulo de campo significa que o campo é obrigatório.

|        Campo          |  Descrição                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **URL do instalador\***     | Power BI gera essa URL quando você publica o aplicativo e o promove para produção.  Para obter mais informações, consulte [publicar aplicativos com dashboards e relatórios no Power bi](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Instruções de validação**  |  Se desejar, adicione instruções (até 3.000 caracteres) para ajudar a equipe de validação da Microsoft a configurar, conectar e testar seu aplicativo. Inclua definições de configuração típicas, contas, parâmetros ou outras informações que podem ser usadas para testar a opção conectar dados. Essas informações são visíveis somente para a equipe de validação e são usadas somente para fins de validação.  |
| **Este aplicativo é criado como um pacote de conteúdo do Power BI?** | Atualmente, esse campo é usado somente internamente. Deixe a configuração padrão **não**. Se você alterar a configuração para **Sim**, poderá parar o processo de publicação.  |  
|  |  |


## <a name="next-steps"></a>Próximas etapas

Na guia [detalhes da vitrine](./cpp-storefront-details-tab.md) , forneça informações legais e de marketing para seu aplicativo.

