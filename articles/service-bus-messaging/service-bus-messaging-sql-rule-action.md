---
title: Referência de sintaxe SQLRuleAction no Ônibus de Serviço Azure
description: Este artigo fornece uma referência para sintaxe SQLRuleAction. As ações são escritas em sintaxe baseada em linguagem SQL que é realizada contra uma mensagem intermediada.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 37615e39577ef60cccc9df91b61a6aa24ca794d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759621"
---
# <a name="sqlruleaction-syntax-reference-for-azure-service-bus"></a>Referência de sintaxe SQLRuleAction para ônibus de serviço Azure

Um *SqlRuleAction* é uma instância da classe [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) e representa um conjunto de ações gravadas na sintaxe baseada na linguagem SQL executada em uma [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
Este artigo lista os detalhes sobre a gramática de ação de regras do SQL.  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
```

```
<expression> ::=
    <constant>
    | <function>
    | <property>
    | <expression> { + | - | * | / | % } <expression>
    | { + | - } <expression>
    | ( <expression> )
``` 

```
<property> := 
    [<scope> .] <property_name>
``` 
  
## <a name="arguments"></a>Argumentos  
  
-   `<scope>` é uma cadeia de caracteres opcional que indica o escopo do `<property_name>`. Os valores válidos são `sys` ou `user`. O valor `sys` indica o escopo do sistema, em que `<property_name>` é um nome de propriedade pública da [Classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` indica o escopo do usuário, em que `<property_name>` é uma chave de dicionário da [Classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). O escopo `user` será o escopo padrão se `<scope>` não for especificado.  
  
### <a name="remarks"></a>Comentários  

Uma tentativa de acessar uma propriedade inexistente do sistema é um erro, ao passo que uma tentativa de acessar uma propriedade de usuário inexistente não é um erro. Em vez disso, uma propriedade de usuário inexistente é internamente avaliada como um valor desconhecido. Um valor desconhecido é tratado especialmente durante a avaliação de operador.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumentos  
 `<regular_identifier>` é uma cadeia de caracteres representada pela seguinte expressão regular:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 Isso significa qualquer cadeia de caracteres que começa com uma letra e é seguida por um ou mais sublinhados/letras/dígitos.  
  
 `[:IsLetter:]` significa qualquer caractere Unicode categorizado como uma letra do Unicode. `System.Char.IsLetter(c)` retorna `true` se `c` é uma letra do Unicode.  
  
 `[:IsDigit:]` significa qualquer caractere Unicode categorizado como um dígito decimal. `System.Char.IsDigit(c)` retorna `true` se `c` é um dígito do Unicode.  
  
 Um `<regular_identifier>` não pode ser uma palavra-chave reservada.  
  
 `<delimited_identifier>` é qualquer cadeia de caracteres incluída em colchetes esquerdo/direito ([]). Um colchete direito é representado como dois colchetes direitos. Estes são exemplos de `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>` é qualquer cadeia de caracteres entre aspas duplas. Aspas duplas no identificador são representadas como duas aspas duplas. Não é recomendável usar identificadores entre aspas, porque pode ser confundido facilmente por uma constante de cadeia de caracteres. Use um identificador delimitado, se possível. Este é um exemplo de `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>pattern  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Comentários
  
 `<pattern>` deve ser uma expressão avaliada como uma cadeia de caracteres. Usado como um padrão para o operador LIKE.      Pode conter os seguintes caracteres curinga:  
  
-   `%`: qualquer cadeia de caracteres com zero ou mais caracteres.  
  
-   `_`: qualquer caractere único.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Comentários
  
 `<escape_char>` deve ser uma expressão avaliada como uma cadeia de caracteres de comprimento 1. Usado como um caractere de escape para o operador LIKE.  
  
 Por exemplo, `property LIKE 'ABC\%' ESCAPE '\'` corresponde a `ABC%`, em vez de a uma cadeia de caracteres que começa com `ABC`.  
  
## <a name="constant"></a>constante  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumentos  
  
-   `<integer_constant>` é uma cadeia de números que não são incluídos em aspas e que não contêm pontos decimais. Os valores são armazenados como `System.Int64` internamente e seguem o mesmo intervalo.  
  
     Estes são exemplos de constantes longas:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` é uma cadeia de números que não são incluídos em aspas e que contêm um ponto decimal. Os valores são armazenados como `System.Double` internamente e seguem o mesmo intervalo e a mesma precisão.  
  
     Em uma versão futura, esse número poderá ser armazenado em outro tipo de dados para dar suporte à semântica de número exato; portanto, você não deve se basear no fato de que o tipo de dados subjacente é `System.Double` para `<decimal_constant>`.  
  
     Estes são exemplos de constantes decimais:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` é um número escrito em notação científica. Os valores são armazenados como `System.Double` internamente e seguem o mesmo intervalo e a mesma precisão. Estes são exemplos de constantes de número aproximado:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Comentários
  
Constantes boolianas são representadas pelas palavras-chave `TRUE` ou `FALSE`. Os valores são armazenados como `System.Boolean`.  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Comentários
  
Constantes de cadeia de caracteres são incluídas em aspas simples e incluem caracteres Unicode válidos. Uma aspa simples inserida em uma constante de cadeia de caracteres é representada como duas aspas simples.  
  
## <a name="function"></a>função  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Comentários  

A função `newid()` retorna um **System.Guid** gerado pelo método `System.Guid.NewGuid()`.  
  
A função `property(name)` retorna o valor da propriedade referenciada por `name`. O valor `name` pode ser qualquer expressão válida que retorna um valor de cadeia de caracteres.  
  
## <a name="considerations"></a>Considerações

- SET é usado para criar uma nova propriedade ou atualizar o valor de uma propriedade existente.
- REMOVE é usado para remover uma propriedade.
- SET executa uma conversão implícita, se possível, quando o tipo de expressão e o tipo de propriedade existente são diferentes.
- A ação falhará se propriedades do sistema inexistentes forem referenciadas.
- A ação não falhará se propriedades de usuário inexistentes forem referenciadas.
- Uma propriedade de usuário inexistente é avaliada como “Desconhecida” internamente, seguindo a mesma semântica de [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) ao avaliar operadores.

## <a name="next-steps"></a>Próximas etapas

- [Classe SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [Classe SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
