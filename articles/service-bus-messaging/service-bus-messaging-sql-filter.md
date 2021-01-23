---
title: Sintaxe de filtro SQL da regra de assinatura do barramento de serviço do Azure | Microsoft Docs
description: Este artigo fornece detalhes sobre a gramática do filtro SQL. Um filtro SQL dá suporte a um subconjunto do padrão SQL-92.
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: 93739b0d64fb029f4d2af1d8dbbf91947085337d
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737652"
---
# <a name="subscription-rule-sql-filter-syntax"></a>Sintaxe de filtro SQL de regra de assinatura

Um *filtro SQL* é um dos tipos de filtro disponíveis para assinaturas de tópico do barramento de serviço. É uma expressão de texto que se baseia em um subconjunto do padrão SQL-92. Expressões de filtro são usadas com o `sqlExpression` elemento da propriedade ' sqlfilter ' de um barramento de serviço `Rule` em um [modelo de Azure Resource Manager](service-bus-resource-manager-namespace-topic-with-rule.md), ou o argumento de CLI do Azure `az servicebus topic subscription rule create` comando [`--filter-sql-expression`](/cli/azure/servicebus/topic/subscription/rule#az_servicebus_topic_subscription_rule_create) e várias funções de SDK que permitem o gerenciamento de regras de assinatura.

O barramento de serviço Premium também dá suporte à [sintaxe de seletor de mensagem SQL JMS](https://docs.oracle.com/javaee/7/api/javax/jms/Message.html) por meio da API JMS 2,0.

  
``` 
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
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
  
-   `<scope>` é uma cadeia de caracteres opcional que indica o escopo do `<property_name>`. Os valores válidos são `sys` ou `user`. O `sys` valor indica o escopo do sistema em que `<property_name>` é um nome de propriedade pública da [classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` indica o escopo do usuário, em que `<property_name>` é uma chave do dicionário da [classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) . `user` escopo será o escopo padrão se `<scope>` não for especificado.  
  
## <a name="remarks"></a>Comentários

Uma tentativa de acessar uma propriedade de sistema inexistente é um erro, enquanto uma tentativa de acessar uma propriedade de usuário não existente não é um erro. Em vez disso, uma propriedade de usuário inexistente é internamente avaliada como um valor desconhecido. Um valor desconhecido é tratado especialmente durante a avaliação de operador.  
  
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
  
Essa gramática significa qualquer cadeia de caracteres que começa com uma letra e é seguida por um ou mais sublinhados/letras/dígitos.  
  
`[:IsLetter:]` significa qualquer caractere Unicode categorizado como uma letra do Unicode. `System.Char.IsLetter(c)` retorna `true` se `c` é uma letra do Unicode.  
  
`[:IsDigit:]` significa qualquer caractere Unicode categorizado como um dígito decimal. `System.Char.IsDigit(c)` retorna `true` se `c` é um dígito do Unicode.  
  
Um `<regular_identifier>` não pode ser uma palavra-chave reservada.  
  
`<delimited_identifier>` é qualquer cadeia de caracteres incluída em colchetes esquerdo/direito ([]). Um colchete direito é representado como dois colchetes direitos. Estes são exemplos de `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` é qualquer cadeia de caracteres entre aspas duplas. Aspas duplas no identificador são representadas como duas aspas duplas. Não é recomendável usar identificadores entre aspas porque ele pode ser facilmente confundido com uma constante de cadeia de caracteres. Use um identificador delimitado, se possível. Aqui está um exemplo de `<quoted_identifier>` :  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>pattern  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Comentários
  
`<pattern>` deve ser uma expressão avaliada como uma cadeia de caracteres. Ele é usado como um padrão para o operador LIKE.      Pode conter os seguintes caracteres curinga:  
  
-   `%`: qualquer cadeia de caracteres com zero ou mais caracteres.  
  
-   `_`: qualquer caractere único.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Comentários  

`<escape_char>` deve ser uma expressão avaliada como uma cadeia de caracteres de comprimento 1. Ele é usado como um caractere de escape para o operador LIKE.  
  
 Por exemplo, `property LIKE 'ABC\%' ESCAPE '\'` corresponde a `ABC%`, em vez de a uma cadeia de caracteres que começa com `ABC`.  
  
## <a name="constant"></a>constante  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumentos  
  
-   `<integer_constant>` é uma cadeia de números que não está entre aspas e não contém pontos decimais. Os valores são armazenados como `System.Int64` internamente e seguem o mesmo intervalo.  
  
     Aqui estão exemplos de constantes longas:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` é uma cadeia de números que não está entre aspas e que contém um ponto decimal. Os valores são armazenados como `System.Double` internamente e seguem o mesmo intervalo e a mesma precisão.  
  
     Em uma versão futura, esse número pode ser armazenado em um tipo de dados diferente para dar suporte à semântica numérica exata, portanto, você não deve contar com o fato de que o tipo de dados subjacente é `System.Double` para `<decimal_constant>` .  
  
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

Constantes boolianas são representadas pelas palavras-chave **TRUE** ou **FALSE**. Os valores são armazenados como `System.Boolean`.  
  
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
  
A `newid()` função retorna um `System.Guid` gerado pelo `System.Guid.NewGuid()` método.  
  
A função `property(name)` retorna o valor da propriedade referenciada por `name`. O valor `name` pode ser qualquer expressão válida que retorna um valor de cadeia de caracteres.  
  
## <a name="considerations"></a>Considerações
  
Considere a seguinte semântica de [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter):  
  
-   Os nomes de propriedade não diferenciam maiúsculas de minúsculas.  
  
-   Os operadores seguem a semântica de conversão implícita do C# sempre que possível.  
  
-   As propriedades do sistema são propriedades públicas expostas em instâncias de [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).  
  
    Considere a seguinte semântica de `IS [NOT] NULL`:  
  
    -   `property IS NULL` é avaliado como `true` se a propriedade não existe ou se o valor da propriedade é `null`.  
  
### <a name="property-evaluation-semantics"></a>Semântica de avaliação da propriedade  
  
- Uma tentativa de avaliar uma propriedade do sistema inexistente gerará uma exceção [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception).  
  
- Uma propriedade que não existe é avaliada internamente como **desconhecida**.  
  
  Avaliação desconhecida em operadores aritméticos:  
  
- Para operadores binários, se o lado esquerdo ou direito dos operandos for avaliado como **desconhecido**, o resultado será **desconhecido**.  
  
- Em operadores unários, se um operando for avaliado como **desconhecido**, o resultado será **desconhecido**.  
  
  Avaliação desconhecida em operadores de comparação binária:  
  
- Se o lado esquerdo ou direito dos operandos for avaliado como **desconhecido**, o resultado será **desconhecido**.  
  
  Avaliação desconhecida em `[NOT] LIKE`:  
  
- Se qualquer operando for avaliado como **desconhecido**, o resultado será **desconhecido**.  
  
  Avaliação desconhecida em `[NOT] IN`:  
  
- Se o operando esquerdo for avaliado como **desconhecido**, o resultado será **desconhecido**.  
  
  Avaliação desconhecida no operador **AND**:  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 Avaliação desconhecida no operador **OR**:  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>Semântica de associação do operador
  
-   Operadores de comparação, como `>`, `>=`, `<`, `<=`, `!=` e `=` seguem a mesma semântica que a associação do operador do C# em promoções de tipo de dados e conversões implícitas.  
  
-   Operadores aritméticos, como `+`, `-`, `*`, `/` e `%` seguem a mesma semântica que a associação do operador do C# em promoções de tipo de dados e conversões implícitas.


## <a name="examples"></a>Exemplos

### <a name="filter-on-system-properties"></a>Filtrar nas propriedades do sistema
Para fazer referência a uma propriedade do sistema em um filtro, use o seguinte formato: `sys.<system-property-name>` . 

```csharp
sys.Label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

## <a name="filter-on-message-properties"></a>Filtrar nas propriedades da mensagem
Aqui estão os exemplos de como usar as propriedades de mensagem em um filtro. Você pode acessar as propriedades da mensagem usando `user.property-name` ou apenas `property-name` .

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

### <a name="filter-on-message-properties-with-special-characters"></a>Filtrar as propriedades da mensagem com caracteres especiais
Se o nome da propriedade da mensagem tiver caracteres especiais, use aspas duplas ( `"` ) para incluir o nome da propriedade. Por exemplo, se o nome da propriedade for `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` , use a sintaxe a seguir no filtro. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

### <a name="filter-on-message-properties-with-numeric-values"></a>Filtrar as propriedades da mensagem com valores numéricos
Os exemplos a seguir mostram como você pode usar propriedades com valores numéricos em filtros. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

### <a name="parameter-based-filters"></a>Filtros baseados em parâmetros
Aqui estão alguns exemplos de como usar filtros baseados em parâmetros. Nesses exemplos, `DataTimeMp` é uma propriedade Message do tipo `DateTime` e `@dtParam` é um parâmetro passado para o filtro como um `DateTime` objeto.

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

### <a name="using-in-and-not-in"></a>Usando IN e NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Para obter um exemplo em C#, consulte o [tópico filtros de exemplo no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


### <a name="set-rule-action-for-a-sql-filter"></a>Definir ação de regra para um filtro SQL

```csharp
// instantiate the ManagementClient
this.mgmtClient = new ManagementClient(connectionString);

// create the SQL filter
var sqlFilter = new SqlFilter("source = @stringParam");

// assign value for the parameter
sqlFilter.Parameters.Add("@stringParam", "orders");

// instantiate the Rule = Filter + Action
var filterActionRule = new RuleDescription
{
    Name = "filterActionRule",
    Filter = sqlFilter,
    Action = new SqlRuleAction("SET source='routedOrders'")
};

// create the rule on Service Bus
await this.mgmtClient.CreateRuleAsync(topicName, subscriptionName, filterActionRule);
```


## <a name="next-steps"></a>Próximas etapas

- [Classe SQLFilter (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [Classe SQLFilter (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [Classe sqlfilter (Java)](/java/api/com.microsoft.azure.servicebus.rules.SqlFilter)
- [SqlRuleFilter (JavaScript)](/javascript/api/@azure/service-bus/sqlrulefilter)
- [`az servicebus topic subscription rule`](/cli/azure/servicebus/topic/subscription/rule)
- [New-AzServiceBusRule](/powershell/module/az.servicebus/new-azservicebusrule)