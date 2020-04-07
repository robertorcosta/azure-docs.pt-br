---
title: Oferecer configurações para uma oferta de aplicativo Power BI | Mercado Azure
description: Configure as configurações de oferta para uma oferta de Aplicativo Power BI para o mercado Microsoft AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 637217d718e6e91a3fa29bd38d8215d0a1324825
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745439"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Guia Configurações de Oferta de Aplicativos do Power BI

>[!Important]
>A partir de 30 de março de 2020, começaremos a mover o gerenciamento das ofertas do seu aplicativo Power BI para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções na [visão geral de criação de aplicativos do Power BI](https://aka.ms/AzureCreatePBIServiceApp) para gerenciar suas ofertas migradas.

Quando você abre a página **Nova oferta** para aplicativos de serviço, você verá pela primeira vez a guia **Configurações de oferta.** Você fornece os principais identificadores e o nome para sua oferta nesta guia. Um asterisco (*) indica um campo necessário.

![Guia Configurações da Oferta](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Campos de Configurações da Oferta 

Na guia **Configurações de oferta,** você precisa inserir informações nos seguintes campos necessários. Os campos obrigatórios são indicados por um asterisco (*).

|  Campo        |  Descrição                                                               |
|---------------|----------------------------------------------------------------------------|
| **ID da oferta\***  | Um identificador exclusivo (dentro de um perfil de editor) para a oferta. Esse identificador ficará visível em URLs de produtos, modelos do Azure Resource Manager e relatórios de faturamento. O tamanho máximo é de 50 caracteres. Pode conter apenas caracteres alfanuméricos minúsculos e traços (-). Não pode terminar com um traço. Este identificador não pode ser alterado depois que uma oferta entra no ar. Se contoso publicar uma oferta `sample-SvcApp`com ID de oferta, `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`a oferta será atribuída ao APPSource URL .      |
| **Publicador\*** | O identificador exclusivo de sua organização no [AppSource](https://appsource.microsoft.com). Todas as suas ofertas devem ser associadas à sua ID do publicador. Esse valor não pode ser alterado depois que a oferta é salva.                         |
| **Nome\***      | Um nome de exibição para sua oferta. Este nome aparecerá no AppSource e no Portal de Parceiros na Nuvem. O tamanho máximo é de 50 caracteres. Use uma marca que seja reconhecível para o seu produto. Não inclua o nome da sua organização aqui, a menos que o aplicativo seja comercializado com esse nome. Se você estiver fornecendo esta oferta em outros sites e publicações, use o mesmo nome em todas as publicações.    <br/>Se você liberar uma oferta durante o período de `(Preview)` visualização para aplicativos Power BI, `Sample Scv App (Preview)`adicione a string no final do nome do aplicativo, assim: . |
|     |     |


## <a name="next-steps"></a>Próximas etapas

Na próxima guia, você especificará [informações técnicas](./cpp-technical-info-tab.md) para sua oferta.
