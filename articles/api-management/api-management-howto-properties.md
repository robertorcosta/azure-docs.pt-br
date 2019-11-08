---
title: Como usar valores nomeados em políticas de gerenciamento de API do Azure
description: Saiba como usar valores nomeados em políticas de gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/05/2019
ms.author: apimpm
ms.openlocfilehash: d11239aa49a53a90a38f2b5336d36cea6c97e9df
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824168"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Como usar valores nomeados em políticas de gerenciamento de API do Azure

As políticas de gerenciamento de API são um recurso poderoso do sistema que permitem que o portal do Azure altere o comportamento da API por meio da configuração. As políticas são um conjunto de instruções executadas em sequência, na solicitação ou na resposta de uma API. É possível construir declarações de política usando valores de texto literais, expressões de política e valores nomeados.

Cada instância de serviço de gerenciamento de API tem uma coleção de propriedades de pares de chave/valor, que é chamada de valores nomeados, que são globais para a instância do serviço. Não há limite imposto sobre o número de itens na coleção. Valores nomeados podem ser usados para gerenciar valores de cadeia de caracteres constantes em todas as políticas e configuração de API. Cada valor nomeado pode ter os seguintes atributos:

| Atributo      | Tipo            | DESCRIÇÃO                                                                                                                         |
| -------------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | string          | Usado para referenciar o valor nomeado em políticas. Uma cadeia de uma a 256 caracteres. Somente letras, números, ponto e traço são permitidos. |
| `Value`        | string          | Valor real. Não deve estar vazio nem consistir apenas em espaços em branco. Máximo de 4096 caracteres de comprimento.                                     |
| `Secret`       | booleano         | Determina se o valor é um segredo e se deve ser criptografado ou não.                                                            |
| `Tags`         | matriz de cadeias de caracteres | Usado para filtrar a lista de valores nomeados. Até 32 marcas.                                                                                    |

![Valores nomeados](./media/api-management-howto-properties/named-values.png)

Os valores nomeados podem conter cadeias de caracteres literais e [expressões de política](/azure/api-management/api-management-policy-expressions). Por exemplo, o valor de `Expression` é uma expressão de política que retorna uma cadeia de caracteres que contém a data e hora atuais. O valor nomeado `Credential` é marcado como um segredo, portanto, seu valor não é exibido por padrão.

| Nome       | Valor                      | Segredo | Marcas          |
| ---------- | -------------------------- | ------ | ------------- |
| Valor      | 42                         | Falso  | números vitais |
| Credencial | ••••••••••••••••••••••     | True   | segurança      |
| Expression | @(DateTime.Now.ToString()) | Falso  |               |

## <a name="to-add-and-edit-a-named-value"></a>Para adicionar e editar um valor nomeado

![Adicionar um valor nomeado](./media/api-management-howto-properties/add-property.png)

1. Selecione **APIs** em **GERENCIAMENTO DE API**.
2. Selecione **Valores nomeados**.
3. Pressione **+Adicionar**.

    Nome e Valor são valores obrigatórios. Se o valor for um segredo, marque a caixa de seleção *este é um segredo* . Insira uma ou mais marcas opcionais para ajudar a organizar seus valores nomeados e, em seguida, clique em Salvar.

4. Clique em **Criar**.

Depois que o valor nomeado for criado, você poderá editá-lo clicando nele. Se você alterar o nome do valor nomeado, todas as políticas que fizerem referência a esse valor nomeado serão atualizadas automaticamente para usar o novo nome.

Para obter informações sobre como editar um valor nomeado usando a API REST, consulte [Editar um valor nomeado usando a API REST](/rest/api/apimanagement/2019-01-01/property?patch).

## <a name="to-delete-a-named-value"></a>Para excluir um valor nomeado

Para excluir um valor nomeado, clique em **excluir** ao lado do valor nomeado para excluir.

> [!IMPORTANT]
> Se o valor nomeado for referenciado por qualquer política, você não poderá excluí-lo com êxito até remover o valor nomeado de todas as políticas que o utilizam.

Para obter informações sobre como excluir um valor nomeado usando a API REST, consulte [excluir um valor nomeado usando a API REST](/rest/api/apimanagement/2019-01-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>Para pesquisar e filtrar valores nomeados

A guia **Valores nomeados** inclui pesquisa e filtragem de recursos para ajudá-lo a gerenciar seus valores nomeados. Para filtrar a lista de valores nomeados por nome, insira um termo de pesquisa na caixa de texto **propriedade de pesquisa** . Para exibir todos os valores nomeados, desmarque a caixa de texto **Propriedade de pesquisa** e pressione enter.

Para filtrar a lista por marca, insira uma ou mais marcas na caixa de texto **Filtrar por marcas** . Para exibir todos os valores nomeados, desmarque a caixa de texto **Filtrar por marcas** e pressione enter.

## <a name="to-use-a-named-value"></a>Para usar um valor nomeado

Para usar um valor nomeado em uma política, coloque seu nome dentro de um par duplo de chaves como `{{ContosoHeader}}`, conforme mostrado no exemplo a seguir:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Neste exemplo, `ContosoHeader` é usado como o nome de um cabeçalho em uma política `set-header` e `ContosoHeaderValue` é usado como o valor desse cabeçalho. Quando essa política é avaliada durante uma solicitação ou resposta ao gateway de gerenciamento de API, `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` são substituídas pelos respectivos valores.

Os valores nomeados podem ser usados como valores de atributo ou de elemento, como mostra o exemplo anterior, mas também podem ser inseridos ou combinados com parte de uma expressão de texto literal, como mostra o exemplo a seguir: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Os valores nomeados também podem conter expressões de política. No exemplo a seguir, o `ExpressionProperty` é usado.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Quando essa política é avaliada, `{{ExpressionProperty}}` é substituído por seu valor: `@(DateTime.Now.ToString())`. Como o valor é uma expressão de política, a expressão é avaliada e a política prossegue com a execução.

Você pode testar isso no portal do desenvolvedor chamando uma operação que tenha uma política com valores nomeados no escopo. No exemplo a seguir, uma operação é chamada com os dois exemplos de políticas `set-header` anteriores com valores nomeados. Observe que a resposta contém dois cabeçalhos personalizados configurados usando políticas com valores nomeados.

![portal do desenvolvedor][api-management-send-results]

Se você observar o [rastreamento do Inspetor de API](api-management-howto-api-inspector.md) para uma chamada que inclui as duas políticas de exemplo anteriores com valores nomeados, você poderá ver as duas políticas de `set-header` com os valores nomeados inseridos, bem como a avaliação da expressão de política para o valor nomeado que continha a expressão de política.

![Rastreamento do Inspetor de API][api-management-api-inspector-trace]

Embora os valores nomeados possam conter expressões de política, eles não podem conter outros valores nomeados. Se o texto que contém uma referência de valor nomeado for usado para um valor, como `Text: {{MyProperty}}`, essa referência não será resolvida e substituída.

## <a name="next-steps"></a>Próximas etapas

-   Saiba mais sobre como trabalhar com políticas
    -   [Políticas no Gerenciamento de API](api-management-howto-policies.md)
    -   [Referência de política](/azure/api-management/api-management-policies)
    -   [Expressões de política](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
