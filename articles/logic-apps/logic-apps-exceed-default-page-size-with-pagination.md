---
title: Obtenha mais itens ou registros com paginação
description: Configurar paginação para exceder o limite de tamanho da página padrão para ações de conector escoamento em Aplicativos azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 75d9660eb35b5d7ddc644d177c11ae489e2853dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792116"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Obtenha mais dados, itens ou registros usando paginação no Azure Logic Apps

Quando você recupera dados, itens ou registros usando uma ação de conector no [Azure Logic Apps,](../logic-apps/logic-apps-overview.md)você pode obter conjuntos de resultados tão grandes que a ação não retorne todos os resultados ao mesmo tempo. Com algumas ações, o número de resultados pode exceder o tamanho da página padrão do conector. Nesse caso, a ação retorna apenas a primeira página de resultados. Por exemplo, o tamanho da página padrão para a ação **Get rows** do conector sql server é de 2048, mas pode variar de acordo com outras configurações.

Algumas ações permitem que você ligue uma configuração de *paginação* para que seu aplicativo lógico possa recuperar mais resultados até o limite de paginação, mas retornar esses resultados como uma única mensagem quando a ação terminar. Quando você usa paginação, você deve especificar um valor *limite,* que é o número de resultados que você deseja que a ação retorne. A ação recupera os resultados até atingir o limite especificado. Quando o número total de itens é menor do que o limite especificado, a ação recupera todos os resultados.

Ligar a configuração de paginação recupera páginas de resultados com base no tamanho da página de um conector. Esse comportamento significa que, às vezes, você pode obter mais resultados do que o seu limite especificado. Por exemplo, ao usar a ação SQL Server **Get linhas,** que suporta a configuração de paginação:

* O tamanho da página padrão da ação é de 2048 registros por página.
* Suponha que você tenha 10.000 registros e especifique 5.000 registros como o mínimo.
* Paginação obtém páginas de registros, então para obter pelo menos o mínimo especificado, a ação retorna 6144 registros (3 páginas x 2048 registros), e não 5000 registros.

Aqui está uma lista com apenas alguns dos conectores onde você pode exceder o tamanho da página padrão para ações específicas:

* [Armazenamento azure Blob](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Equipes da Microsoft](https://docs.microsoft.com/connectors/teams/)
* [Banco de dados Oracle](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico e a ação onde você quer ativar a paginação. Se você não tem um aplicativo lógico, consulte [Quickstart: Crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Ativar paginação

Para determinar se uma ação suporta paginação no Logic App Designer, verifique as configurações da ação para a configuração **de Paginação.** Este exemplo mostra como ativar a paginação na ação **Get rows** do SQL Server.

1. No canto superior direito da ação, escolha o botão elipses (**...**) e selecione **Configurações**.

   ![Abra as configurações da ação](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Se a ação suportar paginação, a ação mostrará a configuração **de Paginação.**

1. Alterar a configuração de **paginação** de **off** para **on**. Na propriedade **Limiar,** especifique um valor inteiro para o número de resultados que deseja que a ação retorne.

   ![Especifique o número mínimo de resultados a retornar](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Quando estiver pronto, escolha **Concluído**.

## <a name="workflow-definition---pagination"></a>Definição de fluxo de trabalho - paginação

Quando você liga a paginação para uma ação que suporta esse recurso, `"paginationPolicy"` a definição `"minimumItemCount"` do fluxo de `"runtimeConfiguration"` trabalho do seu aplicativo lógico inclui a propriedade junto com a propriedade na propriedade dessa ação, por exemplo:

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>Obter suporte

Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
