---
title: Azure Event Grid - Guia de solução de problemas
description: Este artigo fornece uma lista de códigos de erro, mensagens de erro, descrições e ações recomendadas.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1ab9aeac0bde21e229fdb57b7ad02d5d48471551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645065"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Solução de problemas de erros da Grade de Eventos do Azure
Este guia de solução de problemas fornece uma lista de códigos de erro do Azure Event Grid, mensagens de erro, suas descrições e ações recomendadas que você deve tomar quando receber esses erros. 

## <a name="error-code-400"></a>Código de erro: 400
| Código do erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ----------- | -------------- | 
| httpstatuscode.badRequest<br/>400 | O nome do tópico deve ter entre 3 e 50 caracteres de comprimento. | O comprimento do nome do tópico personalizado deve ter entre 3 e 50 caracteres de comprimento. Somente letras alfanuméricas, dígitos e o caractere '-' são permitidos no nome do tópico. Além disso, o nome não deve começar com as seguintes palavras reservadas: <ul><li>Microsoft</li><li>EventGrid</li><li>Sistema</li></ul> | Escolha um nome de tópico diferente que adere aos requisitos de nome do tópico. |
| httpstatuscode.badRequest<br/>400 | O nome de domínio deve ter entre 3 e 50 caracteres de comprimento. | O comprimento do nome de domínio deve ter entre 3 e 50 caracteres de comprimento. Somente letras alfanuméricas, dígitos e o caractere '-' são permitidos no nome do tópico. Além disso, o nome não deve começar com as seguintes palavras reservadas:<ul><li>Microsoft</li><li>EventGrid</li><li>Sistema</li> | Escolha um nome de domínio diferente que adere aos requisitos de nome de domínio. |
| httpstatuscode.badRequest<br/>400 | Tempo de validade inválido. | O prazo de validade da assinatura do evento determina quando a assinatura do evento se aposentará. Esse valor deve ser um valor de Hora de Data válida no futuro.| Certifique-se de que o tempo de expiração da assinatura do evento em um formato datetime válido e ele está definido para ser no futuro. |

## <a name="error-code-409"></a>Código de erro: 409
| Código do erro | Mensagem de erro | Descrição | Ação recomendada |
| ---------- | ------------- | ----------- | -------------- | 
| httpstatuscode.conflict <br/>409 | Tópico com o nome especificado já existe. Escolha um nome de tópico diferente.   | O nome do tópico personalizado deve ser único em uma única região do Azure, a fim de garantir uma operação de publicação correta. O mesmo nome pode ser usado em diferentes regiões do Azure. | Escolha um nome diferente para o tópico. |
| httpstatuscode.conflict <br/> 409 | Domínio com o especificado já existe. Escolha um nome de domínio diferente. | O nome de domínio deve ser único em uma única região do Azure, a fim de garantir uma operação de publicação correta. O mesmo nome pode ser usado em diferentes regiões do Azure. | Escolha um nome diferente para o domínio. |
| httpstatuscode.conflict<br/>409 | Limite de cota atingido. Para obter mais informações sobre esses limites, consulte [os limites da Grade de Eventos do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Cada assinatura do Azure tem um limite no número de recursos do Azure Event Grid que ele pode usar. Parte ou todo esse contingente foi excedido e não mais recursos poderiam ser criados. |    Verifique o uso dos recursos atuais e exclua os que não são necessários. Se você ainda precisar aumentar sua cota, envie um e-mail para [aeg@microsoft.com](mailto:aeg@microsoft.com) com o número exato de recursos necessários. |


## <a name="next-steps"></a>Próximas etapas
Se você precisar de mais ajuda, poste seu problema no [fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um bilhete de [suporte](https://azure.microsoft.com/support/options/). 
