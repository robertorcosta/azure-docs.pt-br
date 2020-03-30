---
title: Ações no motor de regras Padrão para Azure CDN | Microsoft Docs
description: Documentação de referência para ações no mecanismo de regras padrão para a Azure Content Delivery Network (Azure CDN).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 53280bc90f629d93ff8a045c80f34a73970b43f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74171627"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Ações no motor de regras padrão para CDN Azure

No [mecanismo de regras padrão](cdn-standard-rules-engine.md) para a Azure Content Delivery Network (Azure CDN), uma regra consiste em uma ou mais condições de correspondência e uma ação. Este artigo fornece descrições detalhadas das ações que você pode usar no mecanismo de regras Padrão para CDN Azure.

A segunda parte de uma regra é uma ação. Uma ação define o comportamento aplicado ao tipo de solicitação que uma condição de correspondência ou um conjunto de condições de correspondência identifica.

## <a name="actions"></a>Ações

As seguintes ações estão disponíveis para uso no mecanismo de regras Padrão para CDN Azure. 

### <a name="cache-expiration"></a>Expiração do cache

Use esta ação para substituir o valor de tempo de vida (TTL) do ponto final para solicitações que as regras correspondem às condições especificadas.

#### <a name="required-fields"></a>Campos obrigatórios

Comportamento de cache |  Descrição              
---------------|----------------
Cache de bypass | Quando esta opção é selecionada e a regra corresponde, o conteúdo não é armazenado em cache.
Substituições | Quando essa opção é selecionada e a regra corresponde, o valor TTL retornado de sua origem é substituído com o valor especificado na ação.
Definir se faltar | Quando essa opção é selecionada e a regra corresponde, se nenhum valor TTL foi devolvido de sua origem, a regra define o TTL para o valor especificado na ação.

#### <a name="additional-fields"></a>Campos adicionais

Days (dias) | Horas | minutos | Segundos
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>Cadeia de consulta de tecla cache

Use essa ação para modificar a chave de cache com base em strings de consulta.

#### <a name="required-fields"></a>Campos obrigatórios

Comportamento | Descrição
---------|------------
Incluir | Quando essa opção é selecionada e a regra corresponde, as strings de consulta especificadas nos parâmetros são incluídas quando a chave de cache é gerada. 
Armazenar em cache todas as URLs exclusivas | Quando essa opção é selecionada e a regra corresponde, cada URL exclusiva tem sua própria chave de cache. 
Excluir | Quando essa opção é selecionada e a regra é correspondida, as strings de consulta especificadas nos parâmetros são excluídas quando a chave de cache é gerada.
Ignorar as cadeias de caracteres de consulta | Quando essa opção é selecionada e a regra é correspondida, as strings de consulta não são consideradas quando a chave de cache é gerada. 

### <a name="modify-request-header"></a>Modificar cabeçalho de solicitação

Use esta ação para modificar cabeçalhos que estão presentes nas solicitações enviadas à sua origem.

#### <a name="required-fields"></a>Campos obrigatórios

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome cabeçalho** é adicionado à solicitação com o valor especificado. Se o cabeçalho já estiver presente, o valor será anexado ao valor existente. | String
Overwrite | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome cabeçalho** é adicionado à solicitação com o valor especificado. Se o cabeçalho já estiver presente, o valor especificado sobregrava o valor existente. | String
Excluir | Quando esta opção é selecionada, a regra corresponde e o cabeçalho especificado na regra está presente, o cabeçalho é excluído da solicitação. | String

### <a name="modify-response-header"></a>Modificar cabeçalho de resposta

Use esta ação para modificar cabeçalhos que estão presentes nas respostas devolvidas aos seus clientes.

#### <a name="required-fields"></a>Campos obrigatórios

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome cabeçalho** é adicionado à resposta usando o **Valor**especificado . Se o cabeçalho já estiver presente, **o Valor** será anexado ao valor existente. | String
Overwrite | Quando esta opção é selecionada e a regra corresponde, o cabeçalho especificado no **nome cabeçalho** é adicionado à resposta usando o **Valor**especificado . Se o cabeçalho já estiver presente, **o Valor** substitui o valor existente. | String
Excluir | Quando esta opção é selecionada, a regra corresponde e o cabeçalho especificado na regra está presente, o cabeçalho é excluído da resposta. | String

### <a name="url-redirect"></a>Redirecionamento de URL

Use essa ação para redirecionar os clientes para uma nova URL. 

#### <a name="required-fields"></a>Campos obrigatórios

Campo | Descrição 
------|------------
Type | Selecione o tipo de resposta para retornar ao solicitante: Encontrado (302), Movido (301), Redirecionamento temporário (307) e Redirecionamento Permanente (308).
Protocolo | Pedido de correspondência, HTTP, HTTPS.
Nome do host | Selecione o nome do host para o nome de hospedagem para o que deseja que a solicitação seja redirecionada. Deixe em branco para preservar o hospedeiro de entrada.
Caminho | Defina o caminho a ser usado no redirecionamento. Deixe em branco para preservar o caminho de entrada.  
Cadeia de consulta | Defina a seqüência de consulta usada no redirecionamento. Deixe em branco para preservar a seqüência de consultas recebida. 
Fragmento | Defina o fragmento a ser usado no redirecionamento. Deixe em branco para preservar o fragmento de entrada. 

Recomendamos que você use uma URL absoluta. O uso de uma URL relativa pode redirecionar urls cdn do Azure para um caminho inválido. 

### <a name="url-rewrite"></a>Reconfiguração de URL

Use esta ação para reescrever o caminho de uma solicitação que está a caminho de sua origem.

#### <a name="required-fields"></a>Campos obrigatórios

Campo | Descrição 
------|------------
Padrão de origem | Defina o padrão de origem no caminho da URL para substituir. Atualmente, o padrão de origem usa uma correspondência baseada em prefixo. Para corresponder a todos os caminhos de URL, use uma barra para a frente ()**/** como o valor do padrão de origem.
Destino | Defina o caminho de destino a ser usado na reescrita. O caminho de destino substitui o padrão de origem.
Preservar caminho inigualável | Se definido como **Sim,** o caminho restante após o padrão de origem é anexado ao novo caminho de destino. 

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da CDN do Azure](cdn-overview.md)
- [Referência do mecanismo de regras Standard](cdn-standard-rules-engine-reference.md)
- [Condições de correspondência no motor de regras Padrão](cdn-standard-rules-engine-match-conditions.md)
- [Impor HTTPS usando o mecanismo de regras Standard](cdn-standard-rules-engine.md)
