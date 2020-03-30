---
title: Como usar valores nomeados nas políticas de gerenciamento de API do Azure
description: Saiba como usar valores nomeados nas políticas de gerenciamento de API do Azure.
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
ms.openlocfilehash: 7c25455e28e57ff40664a69718a2e406b52b7632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834306"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Como usar valores nomeados nas políticas de gerenciamento de API do Azure

As políticas de gerenciamento de API são um recurso poderoso do sistema que permitem que o portal do Azure altere o comportamento da API por meio da configuração. As políticas são um conjunto de instruções executadas em sequência, na solicitação ou na resposta de uma API. É possível construir declarações de política usando valores de texto literais, expressões de política e valores nomeados.

Cada instância de serviço de gerenciamento de API tem uma coleção de pares de chaves/valores, que são chamados de valores, que são globais para a instância de serviço. Não há limite imposto para o número de itens da coleção. Os valores nomeados podem ser usados para gerenciar valores de seqüência constantes em todas as configurações e políticas de API. Cada valor nomeado pode ter os seguintes atributos:

| Atributo      | Type            | Descrição                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | string          | Usado para referenciar o valor nomeado em políticas. Uma seqüência de um a 256 caracteres. Apenas letras, números, dot e traço são permitidos. |
| `Value`        | string          | Valor real. Não deve estar vazio ou consistir apenas de espaço em branco. Máximo de 4096 caracteres.                                        |
| `Secret`       | booleano         | Determina se o valor é um segredo e se deve ser criptografado ou não.                                                               |
| `Tags`         | matriz de cadeias de caracteres | Usado para filtrar a lista de valores nomeada. Até 32 tags.                                                                                    |

![Valores nomeados](./media/api-management-howto-properties/named-values.png)

Valores nomeados podem conter strings literais e [expressões políticas](/azure/api-management/api-management-policy-expressions). Por exemplo, o valor de `Expression` é uma expressão de política que retorna uma cadeia de caracteres que contém a data e hora atuais. O valor `Credential` nomeado é marcado como um segredo, de modo que seu valor não é exibido por padrão.

| Nome       | Valor                      | Segredo | Marcas          |
| ---------- | -------------------------- | ------ | ------------- |
| Valor      | 42                         | Falso  | números vitais |
| Credencial | ••••••••••••••••••••••     | True   | segurança      |
| Expression | @(DateTime.Now.ToString()) | Falso  |               |

> [!NOTE]
> Em vez de valores nomeados armazenados dentro de um serviço de gerenciamento de API, você pode usar valores armazenados no serviço [Azure Key Vault,](https://azure.microsoft.com/services/key-vault/) conforme demonstrado por este [exemplo](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml).

## <a name="to-add-and-edit-a-named-value"></a>Para adicionar e editar um valor nomeado

![Adicionar um valor nomeado](./media/api-management-howto-properties/add-property.png)

1. Selecione **APIs** em **GERENCIAMENTO DE API**.
2. Selecione **Valores nomeados**.
3. Pressione **+Adicionar**.

    Nome e Valor são valores obrigatórios. Se o valor é um segredo, verifique se esta é uma caixa de seleção _secreta._ Insira uma ou mais marcas opcionais para ajudar a organizar seus valores nomeados e, em seguida, clique em Salvar.

4. Clique em **Criar**.

Uma vez que o valor nomeado é criado, você pode editá-lo clicando nele. Se você alterar o nome de valor nomeado, quaisquer políticas que fazem referência ao valor nomeado serão automaticamente atualizadas para usar o novo nome.

Para obter informações sobre a edição de um valor nomeado usando a API REST, consulte [Editar um valor nomeado usando a API REST](/rest/api/apimanagement/2019-01-01/property?patch).

## <a name="to-delete-a-named-value"></a>Para excluir um valor nomeado

Para excluir um valor nomeado, clique **em Excluir** ao lado do valor nomeado para excluir.

> [!IMPORTANT]
> Se o valor nomeado for referenciado por quaisquer políticas, você não poderá excluí-lo com sucesso até remover o valor nomeado de todas as políticas que o utilizam.

Para obter informações sobre a exclusão de um valor nomeado usando a API REST, consulte [Excluir um valor nomeado usando a API REST](/rest/api/apimanagement/2019-01-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>Para pesquisar e filtrar valores nomeados

A guia **Valores nomeados** inclui pesquisa e filtragem de recursos para ajudá-lo a gerenciar seus valores nomeados. Para filtrar a lista de valores nomeados por nome, digite um termo de pesquisa na caixa de texto **da propriedade Pesquisar.** Para exibir todos os valores nomeados, desmarque a caixa de texto **Propriedade de pesquisa** e pressione enter.

Para filtrar a lista por tag, digite uma ou mais tags no **Filtro por tags** textbox. Para exibir todos os valores nomeados, desmarque a caixa de texto **Filtrar por marcas** e pressione enter.

## <a name="to-use-a-named-value"></a>Para usar um valor nomeado

Para usar um valor nomeado em uma diretiva, coloque seu `{{ContosoHeader}}`nome dentro de um par duplo de chaves como , como mostrado no exemplo a seguir:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Neste exemplo, `ContosoHeader` é usado como o nome de um cabeçalho em uma política `set-header` e `ContosoHeaderValue` é usado como o valor desse cabeçalho. Quando essa política é avaliada durante uma solicitação ou `{{ContosoHeader}}` resposta `{{ContosoHeaderValue}}` ao gateway de gerenciamento de API, e são substituídas por seus respectivos valores.

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

Se você olhar para o rastreamento do [Inspetor de API](api-management-howto-api-inspector.md) para uma chamada que `set-header` inclui as duas políticas de amostra anteriores com valores nomeados, você poderá ver as duas políticas com os valores nomeados inseridos, bem como a avaliação de expressão de política para o valor nomeado que continha a expressão da diretiva.

![Rastreamento do Inspetor de API][api-management-api-inspector-trace]

Embora os valores nomeados possam conter expressões de política, eles não podem conter outros valores nomeados. Se o texto contendo uma referência de valor `Text: {{MyProperty}}`nomeada for usado para um valor, como , essa referência não será resolvida e substituída.

## <a name="next-steps"></a>Próximas etapas

-   Saiba mais sobre como trabalhar com políticas
    -   [Políticas no Gerenciamento de API](api-management-howto-policies.md)
    -   [Referência de política](/azure/api-management/api-management-policies)
    -   [Expressões de política](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
