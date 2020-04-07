---
title: Informações técnicas para uma oferta de Power BI App | Mercado Azure
description: Configure os campos de informações técnicas para uma oferta de aplicativo do Power BI para o Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 92bc172fd9a47d3b140e60a4083d65a2f532769a
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745565"
---
# <a name="power-bi-apps-technical-info-tab"></a>Guia de informações técnicas sobre aplicativos do Power BI

>[!Important]
>A partir de 30 de março de 2020, começaremos a mover o gerenciamento das ofertas do seu aplicativo Power BI para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções na [visão geral de criação de aplicativos do Power BI](https://aka.ms/AzureCreatePBIServiceApp) para gerenciar suas ofertas migradas.

Na página **Nova Oferta,** use a guia **Informações Técnicas** para fornecer a URL do pacote do instalador do Power BI e outras informações que você precisa para validar a nova oferta.  Para a versão inicial, todos os aplicativos Power BI são gratuitos e estão disponíveis para download no AppSource. Por causa disso, você não pode definir unidades de manutenção de estoque (SKUs) para este tipo de oferta.

![A guia Informações Técnicas](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Campos de Informações Técnicas 

Na guia **Informações Técnicas,** complete os campos descritos na tabela a seguir. Um asterisco (*) no final de uma etiqueta de campo significa que o campo é necessário.

|        Campo          |  Descrição                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **URL do instalador\***     | O Power BI gera essa URL quando você publica o aplicativo e o promove à produção.  Para obter mais informações, consulte [Publicar aplicativos com dashboards e relatórios no Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Instruções de validação**  |  Se você quiser, adicione instruções (até 3.000 caracteres) para ajudar a equipe de validação da Microsoft a configurar, conectar e testar seu aplicativo. Inclua configurações típicas, contas, parâmetros ou outras informações que possam ser usadas para testar a opção Conectar dados. Essas informações são visíveis apenas para a equipe de validação, e são usadas apenas para fins de validação.  |
| **Este aplicativo é criado como um pacote de conteúdo do Power BI?** | Atualmente, este campo é usado apenas internamente. Deixe a configuração padrão de **Não**. Se você alterar a configuração para **Sim,** você pode interromper o processo de publicação.  |  
|  |  |


## <a name="next-steps"></a>Próximas etapas

Na guia Detalhes do [Storefront,](./cpp-storefront-details-tab.md) forneça informações de marketing e legais para o seu aplicativo.

