---
title: Controles de página do Gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre os controles de página disponíveis para uso em modelos de portal do desenvolvedor no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: ff608cac9786ebc0d3908ce4d88fb501e621be6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244011"
---
# <a name="azure-api-management-page-controls"></a>Controles de página do Gerenciamento de API do Azure
O Gerenciamento de API do Azure fornece os controles a seguir para uso em modelos de portal do desenvolvedor.  
  
Para usar um controle, coloque-o no local desejado no modelo do portal do desenvolvedor. Alguns controles, como o [app-actions](#app-actions), têm parâmetros, como mostrado no exemplo a seguir:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
Os valores para os parâmetros são passados como parte do modelo de dados do modelo. Na maioria dos casos, você pode simplesmente colar o exemplo fornecido para cada controle para que ele funcione corretamente. Para obter mais informações sobre os valores de parâmetro, você pode ver a seção de modelo de dados de cada modelo no qual um controle pode ser usado.  

Para saber mais sobre como trabalhar com modelos, consulte [Como personalizar o portal de desenvolvedor de Gerenciamento de API usando modelos](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Controles de página do modelo do portal do desenvolvedor  
  
-   [ações de aplicativos](#app-actions)  
-   [básico-signin](#basic-signin)  
-   [paging-control](#paging-control)  
-   [providers](#providers)  
-   [controle de pesquisa](#search-control)  
-   [sign-up](#sign-up)  
-   [subscrever-botão](#subscribe-button)  
-   [assinatura-cancelar](#subscription-cancel)  
  
##  <a name="app-actions"></a><a name="app-actions"></a> app-actions  
 O controle `app-actions` fornece uma interface de usuário para interação com aplicativos na página de perfil do usuário no portal do desenvolvedor.  
  
 ![controle de ações de&#45;de aplicativos](./media/api-management-page-controls/APIM-app-actions-control.png "Controle de ações de aplicativos APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parâmetros  
  
|Parâmetro|Descrição|  
|---------------|-----------------|  
|appId|A ID do aplicativo.|  
  
### <a name="developer-portal-templates"></a>Modelos de portal do desenvolvedor  
 O controle `app-actions` pode ser usado nos modelos de portal do desenvolvedor a seguir:  
  
-   [Aplicativos](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a><a name="basic-signin"></a> basic-signin  
 O controle `basic-signin` fornece um controle para coletar informações de entrada do usuário na página de entrada do portal do desenvolvedor.  
  
 ![controle básico de&#45;signina](./media/api-management-page-controls/APIM-basic-signin-control.png "Controle de sinalização básica APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portal do desenvolvedor  
 O controle `basic-signin` pode ser usado nos modelos de portal do desenvolvedor a seguir:  
  
-   [Entrar](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a><a name="paging-control"></a>paginação-controle  
 O `paging-control` fornece funcionalidade de paginação nas páginas do portal do desenvolvedor que exibem uma lista de itens.  
  
 ![controle de paginação](./media/api-management-page-controls/APIM-paging-control.png "Controle de paginação APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portal do desenvolvedor  
 O controle `paging-control` pode ser usado nos modelos de portal do desenvolvedor a seguir:  
  
-   [Lista de API](api-management-api-templates.md#APIList)  
  
-   [Lista de problemas](api-management-issue-templates.md#IssueList)  
  
-   [Lista de produtos](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a><a name="providers"></a>Provedores  
 O controle `providers` fornece um controle para seleção de provedores de autenticação na página de entrada do portal do desenvolvedor.  
  
 ![controle de provedores](./media/api-management-page-controls/APIM-providers-control.png "Controle de provedores APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portal do desenvolvedor  
 O controle `providers` pode ser usado nos modelos de portal do desenvolvedor a seguir:  
  
-   [Entrar](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a><a name="search-control"></a> search-control  
 O `search-control` fornece funcionalidade de pesquisa nas páginas do portal do desenvolvedor que exibem uma lista de itens.  
  
 ![controle de pesquisa](./media/api-management-page-controls/APIM-search-control.png "Controle de pesquisa APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portal do desenvolvedor  
 O controle `search-control` pode ser usado nos modelos de portal do desenvolvedor a seguir:  
  
-   [Lista de API](api-management-api-templates.md#APIList)  
  
-   [Lista de produtos](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a><a name="sign-up"></a>inscrição  
 O controle `sign-up` fornece um controle para coletar informações de perfil do usuário na página de entrada do portal do desenvolvedor.  
  
 ![assinar&#45;controle](./media/api-management-page-controls/APIM-sign-up-control.png "Controle de inscrição APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portal do desenvolvedor  
 O controle `sign-up` pode ser usado nos modelos de portal do desenvolvedor a seguir:  
  
-   [Inscrição](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a><a name="subscribe-button"></a> subscribe-button  
 O `subscribe-button` fornece um controle de assinatura de um usuário para um produto.  
  
 ![assinar&#45;controle de botão](./media/api-management-page-controls/APIM-subscribe-button-control.png "Controle de botão de subscrição APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portal do desenvolvedor  
 O controle `subscribe-button` pode ser usado nos modelos de portal do desenvolvedor a seguir:  
  
-   [Product](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a><a name="subscription-cancel"></a> subscription-cancel  
 O controle `subscription-cancel` fornece um controle para cancelar uma assinatura de um produto na página de perfil do usuário no portal do desenvolvedor.  
  
 ![&#45;de assinatura cancele o controle](./media/api-management-page-controls/APIM-subscription-cancel-control.png "Controle de cancelamento de assinatura APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parâmetros  
  
|Parâmetro|Descrição|  
|---------------|-----------------|  
|subscriptionId|O ID da assinatura para cancelar.|  
|cancelUrl|A assinatura cancela a URL.|  
  
### <a name="developer-portal-templates"></a>Modelos de portal do desenvolvedor  
 O controle `subscription-cancel` pode ser usado nos modelos de portal do desenvolvedor a seguir:  
  
-   [Product](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como trabalhar com modelos, consulte [Como personalizar o portal de desenvolvedor de Gerenciamento de API usando modelos](api-management-developer-portal-templates.md).
