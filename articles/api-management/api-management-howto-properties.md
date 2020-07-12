---
title: Como usar valores nomeados nas políticas de Gerenciamento de API do Azure
description: Saiba como usar valores nomeados nas políticas de Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 28a9f37f58a6c056bf23a85fcf2641f407988891
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86243470"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Como usar valores nomeados nas políticas de Gerenciamento de API do Azure

As políticas de gerenciamento de API são um recurso poderoso do sistema que permitem que o portal do Azure altere o comportamento da API por meio da configuração. As políticas são um conjunto de instruções executadas em sequência, na solicitação ou na resposta de uma API. É possível construir declarações de política usando valores de texto literais, expressões de política e valores nomeados.

Cada instância de serviço do Gerenciamento de API tem uma coleção de propriedades de pares de chave/valor, chamada valores nomeados, que são globais à instância do serviço. Não há limite imposto sobre o número de itens na coleção. Os valores nomeados podem ser usados para gerenciar valores de cadeia de caracteres constantes em todas as configurações e as políticas de API. Cada valor nomeado pode ter os seguintes atributos:

| Atributo      | Type            | Descrição                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | string          | Usado para referenciar o valor nomeado em políticas. Uma cadeia de um a 256 caracteres. Somente letras, números, pontos e traços são permitidos. |
| `Value`        | string          | Valor real. Ele não pode ficar vazio ou conter apenas espaços em branco. Máximo de 4096 caracteres.                                        |
| `Secret`       | booleano         | Determina se o valor é um segredo e se deve ser criptografado ou não.                                                               |
| `Tags`         | matriz de cadeias de caracteres | Usado para filtrar a lista de valores nomeados. Até 32 tags.                                                                                    |

![Valores nomeados](./media/api-management-howto-properties/named-values.png)

Os valores nomeados podem conter cadeias de caracteres literais e [expressões de política](./api-management-policy-expressions.md). Por exemplo, o valor de `Expression` é uma expressão de política que retorna uma cadeia de caracteres que contém a data e hora atuais. O valor nomeado `Credential` é marcado como um segredo e, portanto, seu valor não é exibido por padrão.

| Nome       | Valor                      | Segredo | Marcas          |
| ---------- | -------------------------- | ------ | ------------- |
| Valor      | 42                         | Falso  | vital-numbers |
| Credencial | ••••••••••••••••••••••     | True   | segurança      |
| Expression | @(DateTime.Now.ToString()) | Falso  |               |

> [!NOTE]
> Em vez de valores nomeados armazenados em um serviço de gerenciamento de API, você pode usar valores armazenados no serviço de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), conforme demonstrado nesse [exemplo](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml).

## <a name="to-add-and-edit-a-named-value"></a>Para adicionar e editar um valor nomeado

![Adicionar um valor nomeado](./media/api-management-howto-properties/add-property.png)

1. Selecione **APIs** em **GERENCIAMENTO DE API**.
2. Selecione **Valores nomeados**.
3. Pressione **+Adicionar**.

    Nome e Valor são valores obrigatórios. Se o valor for confidencial, marque a caixa de seleção _Isto é um segredo_. Insira uma ou mais marcas opcionais para ajudar a organizar seus valores nomeados e, em seguida, clique em Salvar.

4. Clique em **Criar**.

Quando o valor nomeado é criado, você pode editá-lo clicando na propriedade. Se você alterar o nome do valor nomeado, todas as políticas que fizerem referência a esse valor nomeado serão automaticamente atualizados para usar o novo nome.

## <a name="to-delete-a-named-value"></a>Excluir um valor nomeado

Para excluir um valor nomeado, clique em **Excluir** ao lado do valor nomeado a ser excluído.

> [!IMPORTANT]
> Se o valor nomeado for consultado por quaisquer políticas, não será possível excluí-lo até que você remova o valor nomeado de todas as políticas que a utilizam.

## <a name="to-search-and-filter-named-values"></a>Para pesquisar e filtrar valores nomeados

A guia **Valores nomeados** inclui pesquisa e filtragem de recursos para ajudá-lo a gerenciar seus valores nomeados. Para filtrar a lista de valores nomeados por nome, insira um termo de pesquisa na caixa de texto **Propriedade de pesquisa**. Para exibir todos os valores nomeados, desmarque a caixa de texto **Propriedade de pesquisa** e pressione enter.

Para filtrar a lista por tag, insira uma ou mais tags na caixa de texto **Filtrar por tags**. Para exibir todos os valores nomeados, desmarque a caixa de texto **Filtrar por marcas** e pressione enter.

## <a name="to-use-a-named-value"></a>Para usar um valor nomeado

Para usar um valor nomeado em uma política, coloque o nome entre chaves como `{{ContosoHeader}}`, conforme mostrado no exemplo a seguir:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Neste exemplo, `ContosoHeader` é usado como o nome de um cabeçalho em uma política `set-header` e `ContosoHeaderValue` é usado como o valor desse cabeçalho. Quando essa política é avaliada durante uma solicitação ou uma resposta para o gateway de Gerenciamento de API, `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` são substituídos por seus respectivos valores.

Os valores nomeados podem ser usados como valores de atributo ou de elemento, como mostra o exemplo anterior, mas também podem ser inseridos ou combinados com parte de uma expressão de texto literal, como mostra o exemplo a seguir: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Os valores nomeados também podem conter expressões de política. No exemplo a seguir, o `ExpressionProperty` é usado.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Quando essa política é avaliada, `{{ExpressionProperty}}` é substituído por seu valor: `@(DateTime.Now.ToString())`. Como o valor é uma expressão de política, a expressão é avaliada e a política prossegue com a execução.

Você pode testar isso no portal do desenvolvedor chamando uma operação que tenha uma política com valores nomeados no escopo. No exemplo a seguir, uma operação é chamada com os dois exemplos de políticas `set-header` anteriores com valores nomeados. Observe que a resposta contém dois cabeçalhos personalizados configurados usando políticas com valores nomeados.

![Portal do desenvolvedor][api-management-send-results]

Se você analisar o [rastreamento do Inspetor de API](api-management-howto-api-inspector.md) de uma chamada que inclui os dois exemplos de política anteriores com valores nomeados, será possível ver as duas políticas `set-header` com os valores nomeados inseridos, bem como a avaliação da expressão de política para o valor nomeado que continha a expressão de política.

![Rastreamento do Inspetor de API][api-management-api-inspector-trace]

Embora os valores nomeados possam conter expressões de política, eles não podem conter outros valores nomeados. Se o texto que contém uma referência de valor nomeado for usado para um valor, como `Text: {{MyProperty}}`, essa referência não será resolvida e substituída.

## <a name="next-steps"></a>Próximas etapas

-   Saiba mais sobre como trabalhar com políticas
    -   [Políticas no Gerenciamento de API](api-management-howto-policies.md)
    -   [Referência de política](./api-management-policies.md)
    -   [Expressões de política](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
