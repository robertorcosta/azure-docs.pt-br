---
title: arquivo de inclusão
description: arquivo de inclusão
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/11/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a9a837d8a486e10ea2a62be69b889e657b1dbc05
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72600831"
---
Você aplica marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia. Cada marca consiste em um par de nome e valor. Por exemplo, você pode aplicar o nome "ambiente" e o valor "produção" a todos os recursos em produção.

Depois de aplicar marcas, você pode recuperar todos os recursos em sua assinatura com esse nome de marca e valor. As marcas permitem que você recupere recursos relacionados de diferentes grupos de recursos. Essa abordagem é útil quando você precisa organizar recursos de cobrança ou gerenciamento.

Sua taxonomia deve considerar uma estratégia de marcação de metadados de autoatendimento, além de uma estratégia de marcação automática para reduzir a carga sobre os usuários e aumentar a precisão.

As seguintes limitações se aplicam a marcas:

* Nem todos os tipos de recurso dão suporte a marcas. Para determinar se você pode aplicar uma marca a um tipo de recurso, consulte [suporte a marcas para recursos do Azure](../articles/azure-resource-manager/tag-support.md).
* Cada recurso ou grupo de recursos pode ter um máximo de 50 pares de nome/valor de marca. Atualmente, as contas de armazenamento dão suporte apenas a 15 marcas, mas esse limite será gerado para 50 em uma versão futura. Se você precisar aplicar mais marcas do que o número máximo permitido, use uma cadeia de caracteres JSON para o valor da marca. A cadeia de caracteres JSON pode conter muitos valores que são aplicados a um único nome de marca. Um grupo de recursos pode conter muitos recursos que têm um número de 50 pares de nome/valor de marca.
* O nome da marca é limitado a 512 caracteres e o valor da marca é limitado a 256 caracteres. Para contas de armazenamento, o nome da marca é limitado a 128 caracteres e o valor da marca é limitado a 256 caracteres.
* As VMs generalizadas não dão suporte a marcas.
* As marcas aplicadas ao grupo de recursos não são herdadas pelos recursos nesse grupo de recursos.
* As marcas não podem ser aplicadas a recursos clássicos, como serviços de nuvem.
* Os nomes de marca não podem conter estes caracteres: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > Atualmente, as zonas DNS do Azure e os serviços do Gerenciador de tráfego também não permitem o uso de espaços na marca. 
