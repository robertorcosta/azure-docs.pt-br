---
title: Exportar APIs do Gerenciamento de API do Azure para o Power Platform | Microsoft Docs
description: Saiba como exportar APIs do Gerenciamento de API para o Power Platform.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: apimpm
ms.openlocfilehash: 9af20972a47e2d0ad20de62f1bb9d10e4d43563c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82702647"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>Exportar APIs do Gerenciamento de API do Azure para o Power Platform 

Os desenvolvedores amadores que usam a Microsoft [Power Platform](https://powerplatform.microsoft.com) muitas vezes precisam acessar funcionalidades empresariais desenvolvidas por desenvolvedores profissionais e implantadas no Azure. O [Gerenciamento de API do Azure](https://aka.ms/apimrocks) permite que desenvolvedores profissionais publiquem seu serviço de back-end como APIs e exportem facilmente essas APIs para o Power Platform (Power Apps e Power Automate) como conectores personalizados para consumo por desenvolvedores amadores. 

Este artigo percorre as etapas para exportar APIs do Gerenciamento de API para o Power Platform. 

## <a name="prerequisites"></a>Pré-requisitos

+ Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md)
+ Verifique se há uma API em sua instância de Gerenciamento de API que você gostaria de exportar para o Power Platform
+ Verifique se você tem um [ambiente](https://docs.microsoft.com/powerapps/powerapps-overview#power-apps-for-admins) do Power Apps ou do Power Automate 

## <a name="export-an-api"></a>Exportar uma API

1. Navegue até o serviço de Gerenciamento de API no portal do Azure e selecione **APIs** no menu.
2. Clique nos três pontos ao lado da API que você deseja exportar. 
3. Selecione **Exportar**.
4. Selecione **Power Apps e Power Automate**.
5. Escolha um ambiente para o qual exportar a API. 
6. Forneça um nome de exibição, que será usado como o nome do conector personalizado.  
7. Opcional. Se a API estiver protegida por um servidor OAuth 2.0, você também precisará fornecer detalhes adicionais, incluindo `Client ID`, `Client secret`, `Authorization URL`, `Token URL`e `Refresh URL`.  
8. Selecione **Exportar**. 

Quando a exportação for concluída, navegue até o Power App ou o ambiente do Power Automate. Você verá a API como um conector personalizado.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o Power Platform](https://powerplatform.microsoft.com/)
* [Aprenda tarefas comuns no Gerenciamento de API seguindo os tutoriais](https://docs.microsoft.com/azure/api-management/import-and-publish)