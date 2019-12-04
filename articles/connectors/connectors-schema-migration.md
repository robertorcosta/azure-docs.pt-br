---
title: Migrar aplicativos para o esquema mais recente
description: Como migrar os aplicativos lógicos para a última versão do esquema
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: dc967c90f28b29a63a1f26d0aae86b6d769c1ccf
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786921"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Migrar os aplicativos lógicos para a última versão do esquema

Para mover os aplicativos lógicos existentes para o esquema mais recente, siga estas etapas: 

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

2. No menu do aplicativo lógico, escolha **Visão geral**. Na barra de ferramentas, escolha **Atualizar esquema**.

   > [!NOTE]
   > Quando você seleciona **Atualizar esquema**, os Aplicativos Lógicos do Azure executam automaticamente as etapas de migração e fornecem a você a saída do código. Você pode usar essa saída para atualizar sua definição de aplicativo lógico. No entanto, certifique-se de seguir as práticas recomendadas, conforme descrito na seção **Práticas recomendadas** a seguir.

   ![Atualizar esquema](./media/connectors-schema-migration/update-schema.png)

   A página Atualizar esquema aparece e mostra um link para um documento que descreve os aperfeiçoamentos no novo esquema.

## <a name="best-practices"></a>Práticas recomendadas

Aqui estão algumas práticas recomendadas para migrar os aplicativos lógicos para a última versão do esquema:

* Copie o script migrado para um novo aplicativo lógico. Não substitua a versão antiga até você concluir o teste e confirmar que seu aplicativo migrado funciona conforme o esperado.

* Teste o aplicativo lógico **antes** de colocá-lo em produção.

* Após concluir a migração, comece a atualizar os aplicativos lógicos para usar as [APIs gerenciadas](../connectors/apis-list.md) sempre que possível. Por exemplo, comece a usar o Dropbox v2 em todos os lugares em que você usa o DropBox v1.

## <a name="next-steps"></a>Próximos passos

* Saiba como [migrar manualmente os aplicativos lógicos](../logic-apps/logic-apps-schema-2015-08-01.md)
