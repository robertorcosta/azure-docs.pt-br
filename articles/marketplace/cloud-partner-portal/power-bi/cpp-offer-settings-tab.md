---
title: Configurações da oferta para uma oferta de aplicativo Power BI | Azure Marketplace
description: Defina as configurações de oferta para uma oferta de aplicativo Power BI para o Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: ddedc15b41b2b163f85f5443275daf9fbb56bac8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143041"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Guia Configurações de Oferta de Aplicativos do Power BI

>[!Important]
>A partir de 13 de abril de 2020, começaremos a mover o gerenciamento de suas ofertas de aplicativo Power BI para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções em [Power bi visão geral da criação de aplicativo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) para gerenciar suas ofertas migradas.

Ao abrir a nova página de **oferta** para aplicativos de serviço, você verá primeiro a guia **configurações da oferta** . Você fornece os identificadores primários e o nome da sua oferta nessa guia. Um asterisco (*) indica um campo obrigatório.

![Guia Configurações da Oferta](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Campos de Configurações da Oferta 

Na guia **configurações da oferta** , você precisa inserir informações nos campos obrigatórios a seguir. Os campos obrigatórios são indicados por um asterisco (*).

|  Campo        |  Descrição                                                               |
|---------------|----------------------------------------------------------------------------|
| **ID da oferta\***  | Um identificador exclusivo (dentro de um perfil de editor) para a oferta. Esse identificador ficará visível em URLs de produtos, modelos do Azure Resource Manager e relatórios de faturamento. O tamanho máximo é de 50 caracteres. Ele pode conter apenas caracteres alfanuméricos minúsculos e traços (-). Ele não pode terminar com um traço. Esse identificador não pode ser alterado depois que uma oferta é ativada. Se a contoso publicar uma oferta com uma ID `sample-SvcApp`de oferta, a oferta será atribuída à `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`URL AppSource.      |
| **Editor\*** | O identificador exclusivo de sua organização no [AppSource](https://appsource.microsoft.com). Todas as suas ofertas devem ser associadas à sua ID do publicador. Esse valor não pode ser alterado depois que a oferta é salva.                         |
| **Name\***      | Um nome de exibição para sua oferta. Esse nome aparecerá em AppSource e na Portal do Cloud Partner. O tamanho máximo é de 50 caracteres. Use um nome de marca que seja reconhecível para seu produto. Não inclua o nome da sua organização aqui, a menos que o aplicativo esteja comercializado com esse nome. Se você estiver fornecendo essa oferta em outros sites e publicações, use o mesmo nome em todas as publicações.    <br/>Se você liberar uma oferta durante o período de visualização para aplicativos Power BI, adicione a `(Preview)` cadeia de caracteres no final do nome do aplicativo, desta forma `Sample Scv App (Preview)`:. |
|     |     |


## <a name="next-steps"></a>Próximas etapas

Na próxima guia, você especificará [informações técnicas](./cpp-technical-info-tab.md) para sua oferta.
