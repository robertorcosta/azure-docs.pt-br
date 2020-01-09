---
title: Escrever expressões para mapeamentos de atributos no AD do Azure
description: Aprenda a usar o mapeamentos de expressão para transformar valores de atributo em um formato aceitável durante o provisionamento automatizado de objetos de aplicativo SaaS no Active Directory do Azure.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93b8387d4453a3d83bcce55c739548d914545f2f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430078"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Escrevendo expressões para mapeamentos de atributo no Active Directory do Azure
Quando você configura o provisionamento de um aplicativo SaaS, um dos tipos de mapeamentos de atributos que você pode especificar é o mapeamento de expressão. Nesses casos, você deve escrever uma expressão semelhante a script que permite transformar os dados de usuários em formatos que são mais aceitáveis para o aplicativo SaaS.

## <a name="syntax-overview"></a>Visão geral da sintaxe
A sintaxe de expressões para mapeamentos de atributos é semelhante à das funções de VBA (Visual Basic for Applications).

* A expressão inteira deve ser definida em termos de funções, que consistem em um nome seguido pelos argumentos entre parênteses: <br>
  *FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* Você pode aninhar funções dentro umas das outras. Por exemplo: <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* Você pode passar três tipos diferentes de argumentos em funções:
  
  1. Atributos, que devem ser colocados entre colchetes. Por exemplo: [attributeName]
  2. Constantes de cadeia de caracteres, que devem ser colocadas entre aspas duplas. Por exemplo: "Estados Unidos"
  3. Outras funções. Por exemplo: FunctionOne (`<<argument1>>`, FunctionTwo (`<<argument2>>`))
* Para constantes de cadeia de caracteres, se você precisar de uma barra invertida (\) ou aspas (") na cadeia de caracteres, ela deve ser escapada com o símbolo de barra invertida (\). Por exemplo: "nome da empresa: \\" contoso\\""

## <a name="list-of-functions"></a>Lista de funções
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

---
### <a name="append"></a>Acrescentar
**Função:**<br> Append(source, suffix)

**Descrição:**<br> seleciona um valor da cadeia de caracteres de source e acrescenta o sufixo ao final dela.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **origem** |Obrigatório |String |Normalmente o nome do atributo do objeto de source. |
| **suffix** |Obrigatório |String |A cadeia de caracteres que você deseja acrescentar ao final do valor de source. |

---
### <a name="bitand"></a>BitAnd
**Função:**<br> BitAnd (value1, value2)

**Descrição:**<br> esta função converte ambos os parâmetros na representação binária e define um bit para:

0-se um ou ambos os bits correspondentes em value1 e value2 forem 0                                                  
1 - se ambos os bits correspondentes são 1.                                    

Em outras palavras, ele retorna 0 em todos os casos, exceto quando os bits correspondentes de ambos os parâmetros são 1.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **value1** |Obrigatório |num |Valor numérico que deve ser AND'eddo com value2|
| **value2** |Obrigatório |num |Valor numérico que deve ser AND'ed com value1|

**Exemplo:**<br>
BitAnd(&HF, &HF7)                                                                                
11110111 e 00000111 = 00000111 para que BitAnd retorne 7, o valor binário de 00000111

---
### <a name="cbool"></a>CBool
**Função:**<br> CBool (expressão)

**Descrição:**<br> CBool retorna um booliano com base na expressão avaliada. Se a expressão for avaliada como um valor diferente de zero, CBool retornará true, caso contrário retornará false..

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **expressão** |Obrigatório | expressão | Qualquer expressão válida |

**Exemplo:**<br>
CBool ([attribute1] = [attribute2])                                                                    
Retorna True se ambos os atributos têm o mesmo valor.

---
### <a name="coalesce"></a>Unir
**Função:**<br> Adesão (origem1, origem2,..., defaultValue)

**Descrição:**<br> Retorna o primeiro valor de origem que não é nulo. Se todos os argumentos forem nulos e defaultValue estiver presente, o defaultValue será retornado. Se todos os argumentos forem nulos e defaultValue não estiver presente, o adesão retornará nulo.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **source1  … sourceN** | Obrigatório | String |Obrigatório, número de vezes variável. Normalmente o nome do atributo do objeto de source. |
| **defaultValue** | Opcional | String | Valor padrão a ser usado quando todos os valores de origem forem nulos. Pode ser uma cadeia de caracteres vazia ("").

---
### <a name="converttobase64"></a>ConvertToBase64
**Função:**<br> ConvertToBase64 (origem)

**Descrição:**<br> a função ConvertToBase64 converte uma cadeia de caracteres em uma cadeia de caracteres Unicode em base64.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **origem** |Obrigatório |String |Cadeia de caracteres a ser convertida em base 64|

**Exemplo:**<br>
ConvertToBase64("Hello world!")                                                                                                        
retorna "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Função:**<br> ConvertToUTF8Hex (origem)

**Descrição:**<br> a função ConvertToUTF8Hex converte uma cadeia de caracteres em um valor codificado em UTF8 hexadecimal.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **origem** |Obrigatório |String |Cadeia de caracteres a ser convertida em UTF8 hex|

**Exemplo:**<br>
ConvertToUTF8Hex("Hello world!")                                                                                                         
retorna 48656C6C6F20776F726C6421

---
### <a name="count"></a>Contagem
**Função:**<br> Contagem (atributo)

**Descrição:**<br> a função Count retorna o número de elementos em um atributo de valores múltiplos

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **attribute** |Obrigatório |atributo |Atributo com valores múltiplos que terá elementos contados|

---
### <a name="cstr"></a>CStr
**Função:**<br> CStr (valor)

**Descrição:**<br> A função CStr converte um valor em um tipo de dados de cadeia de caracteres.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **value** |Obrigatório | Numeric, Reference ou Boolean | pode ser um valor numérico, o atributo de referência ou booliano. |

**Exemplo:**<br>
CStr([dn])                                                            
Retorna "CN = Joe, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Função:**<br> DateFromNum (valor)

**Descrição:**<br> a função DateFromNum converte um valor, no formato de data do AD, em um tipo DateTime.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **value** |Obrigatório | Data | A data do AD a ser convertida no tipo DateTime |

**Exemplo:**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum(129699324000000000)                                                            
retorna um DateTime que representa 01/01/2012 23:00:00

---
### <a name="formatdatetime"></a>FormatDateTime
**Função:**<br> FormatDateTime(source, inputFormat, outputFormat)

**Descrição:**<br> obtém uma cadeia de caracteres de data de um formato e a converte em um formato diferente.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **origem** |Obrigatório |String |Normalmente o nome do atributo do objeto de source. |
| **inputFormat** |Obrigatório |String |Formato esperado do valor de source. Para formatos com suporte, consulte [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Obrigatório |String |Formato da data de saída. |

---
### <a name="guid"></a>GUID
**Função:**<br> Guid()

**Descrição:**<br> A função GUID gera um novo GUID aleatório

---
### <a name="instr"></a>InStr
**Função:**<br> InStr (value1, value2, Start, comparetype)

**Descrição:**<br> a função InStr localiza a primeira ocorrência de uma subcadeia de caracteres em uma cadeia de caracteres

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **value1** |Obrigatório |String |Cadeia de caracteres a ser pesquisada |
| **value2** |Obrigatório |String |Cadeia de caracteres a ser encontrada |
| **start** |Opcional |Integer |Posição inicial para localizar a subcadeia de caracteres|
| **comparetype** |Opcional |Enum |Pode ser vbTextCompare ou vbBinaryCompare |

**Exemplo:**<br>
InStr("The quick brown fox","quick")                                                                             
é avaliado como 5

InStr("repEated","e",3,vbBinaryCompare)                                                                                  
é avaliado como 7

---
### <a name="isnull"></a>IsNull
**Função:**<br> IsNull (expressão)

**Descrição:**<br> se a expressão for avaliada como Null, a função IsNull retornará true. para um atributo, um valor Null é expresso pela ausência do atributo.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **expressão** |Obrigatório |expressão |Expressão a ser avaliada |

**Exemplo:**<br>
IsNull([displayName])                                                                                                
Retornará true se o atributo não estiver presente

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Função:**<br> IsNullOrEmpty (expressão)

**Descrição:**<br> se a expressão for nula ou uma cadeia de caracteres vazia, a função IsNullOrEmpty retornará true. para um atributo, isso seria avaliado como True se o atributo estivesse ausente ou presente, mas fosse uma cadeia de caracteres vazia.
O inverso dessa função é chamado de IsPresent.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **expressão** |Obrigatório |expressão |Expressão a ser avaliada |

**Exemplo:**<br>
IsNullOrEmpty ([displayName])                                               
Retornará true se o atributo não estiver presente ou for uma cadeia de caracteres vazia

---
### <a name="ispresent"></a>IsPresent
**Função:**<br> IsNullOrEmpty (expressão)

**Descrição:**<br> se a expressão for avaliada como uma cadeia de caracteres que não é Null nem vazia, a função IsPresent retornará true. o inverso dessa função é chamado de IsNullOrEmpty.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **expressão** |Obrigatório |expressão |Expressão a ser avaliada |

**Exemplo:**<br>
Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])

---
### <a name="isstring"></a>IsString
**Função:**<br> IsString (expressão)

**Descrição:**<br> se a expressão puder ser avaliada como um tipo de cadeia de caracteres, a função IsString será avaliada como True.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **expressão** |Obrigatório |expressão |Expressão a ser avaliada |

---
### <a name="item"></a>Item
**Função:**<br> Item (atributo, índice)

**Descrição:**<br> a função Item retorna um item de um atributo/cadeia de caracteres de valores múltiplos.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **attribute** |Obrigatório |Atributo |Atributo de valores múltiplos a ser pesquisado |
| **index** |Obrigatório |Integer | Índice para um item na cadeia de caracteres de valores múltiplos|

**Exemplo:**<br>
Item ([proxyAddresses], 1)

---
### <a name="join"></a>Join
**Função:**<br> Join(separator, source1, source2, …)

**Descrição:**<br> Join() é semelhante a Append(), exceto por poder combinar diversos valores de cadeia de caracteres **source** em uma única cadeia de caracteres, e cada valor será separado por uma cadeia de caracteres de **separator**.

Se um dos valores de source for um atributo com vários valores, todos os valores nesse atributo serão unidos, separados pelo valor do separador.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **separator** |Obrigatório |String |Cadeia de caracteres usada para separar os valores de source quando eles são concatenados em uma cadeia de caracteres. Pode ser "" se não for necessário nenhum separador. |
| **source1  … sourceN** |Obrigatório, número de vezes variável |String |Valores de cadeia de caracteres a serem unidos. |

---
### <a name="left"></a>Left (à esquerda)
**Função:**<br> Left (cadeia de caracteres, NumChars)

**Descrição:**<br> a função Left retorna um número especificado de caracteres a partir da esquerda de uma cadeia de caracteres. Se numChars = 0, retorne a cadeia de caracteres vazia.
Se numChars < 0, retorne a cadeia de caracteres de entrada.
Se a cadeia de caracteres for nula, retorne a cadeia de caracteres vazia.
Se a cadeia de caracteres contiver menos caracteres que o número especificado em numChars, uma cadeia de caracteres idêntica à cadeia (ou seja, que contém todos os caracteres no parâmetro 1) será retornada.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **Cadeia de caracteres** |Obrigatório |Atributo | A cadeia de caracteres da qual retornar os caracteres |
| **NumChars** |Obrigatório |Integer | Um número que identifica o número de caracteres a serem retornados do início (à esquerda) da cadeia de caracteres|

**Exemplo:**<br>
Esquerda ("João da Silva", 3)                                                            
Retorna "Joh"

---
### <a name="mid"></a>Mid
**Função:**<br> Mid(source, start, length)

**Descrição:**<br> retorna uma subcadeia de caracteres do valor de source. Uma subcadeia de caracteres é uma cadeia de caracteres que contém apenas alguns dos caracteres da cadeia de caracteres de source.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **origem** |Obrigatório |String |Geralmente o nome do atributo. |
| **start** |Obrigatório |inteiro |Índice na cadeia de caracteres de **source** em que a subcadeia de caracteres deve começar. O primeiro caractere na cadeia de caracteres terá o índice de 1, o segundo caractere terá o índice 2 e assim por diante. |
| **length** |Obrigatório |inteiro |Comprimento da subcadeia de caracteres. Se o comprimento terminar fora da cadeia de caracteres **source**, a função retornará uma subcadeia de caracteres do índice **start** até o final da cadeia de caracteres **source**. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Função:**<br> NormalizeDiacritics(source)

**Descrição:**<br> Requer um argumento de cadeia de caracteres. Retorna a cadeia de caracteres, mas com nenhum dos caracteres diacríticos substituídos por caracteres não diacríticas equivalentes. Normalmente usado para converter os nomes e sobrenomes que contêm caracteres diacríticos (acentos) em valores legais que podem ser usados em vários identificadores de usuário, como nomes de entidade de segurança do usuário, nomes de conta SAM e endereços de email.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **origem** |Obrigatório |String | Geralmente um atributo de nome ou sobrenome. |

---
### <a name="not"></a>Not
**Função:**<br> Not(source)

**Descrição:**<br> Inverte o valor booliano de **source**. Se o valor de **source** for "*True*", retorna "*False*". Caso contrário, retorna "*True*".

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **origem** |Obrigatório |Cadeia de caracteres booliana |Os valores de **source** esperados são "True" ou "False". |

---
### <a name="removeduplicates"></a>RemoveDuplicates
**Função:**<br> RemoveDuplicates (atributo)

**Descrição:**<br> a função RemoveDuplicates obtém uma cadeia de caracteres de valores múltiplos e verifica se cada valor é exclusivo.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **attribute** |Obrigatório |Atributo de valores múltiplos |Atributo com valores múltiplos que terá duplicatas removidas|

**Exemplo:**<br>
RemoveDuplicates([proxyAddresses])                                                                                                       
Retorna um atributo proxyAddress limpo em que todos os valores duplicados foram removidos

---
### <a name="replace"></a>Substitua
**Função:**<br> Substitua(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**Descrição:**<br>
substitui valores dentro de uma cadeia de caracteres. Ela funciona de maneira diferente dependendo dos parâmetros fornecidos:

* Quando **oldValue** e **replacementValue** são fornecidos:
  
  * Substitui todas as ocorrências de **oldValue** em **source** por **replacementValue**
* Quando **oldValue** e **template** são fornecidos:
  
  * Substitui todas as ocorrências de **oldValue** em **template** com o valor de **source**
* Quando **regexPattern** e **replacementValue** são fornecidos:

  * A função aplica o **regexPattern** à cadeia de caracteres **source** e você pode usar os nomes de grupo Regex para construir a cadeia de caracteres para **replacementValue**
* Quando **regexPattern**, **regexGroupName** e **replacementValue** são fornecidos:
  
  * A função aplica o **regexPattern** à cadeia de caracteres **source** e substitui todos os valores correspondentes a **regexGroupName** com **replacementValue**
* Quando **regexPattern**, **regexGroupName** e **replacementAttributeName** são fornecidos:
  
  * Se **source** não tiver um valor, **source** será retornado
  * Se **source** tiver um valor, a função aplicará o **regexPattern** à cadeia de caracteres **source** e substituirá todos os valores correspondentes a **regexGroupName** pelo valor associado a **replacementAttributeName**

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **origem** |Obrigatório |String |Normalmente, o nome do atributo do objeto de **source**. |
| **oldValue** |Opcional |String |Valor a ser substituído em **source** ou **template**. |
| **regexPattern** |Opcional |String |Padrão de Regex para o valor a ser substituído em **source**. Ou, quando **replacementPropertyName** for usado, o padrão para extrair o valor de **replacementPropertyName**. |
| **regexGroupName** |Opcional |String |Nome do grupo dentro de **regexPattern**. Somente quando **replacementPropertyName** for usado, extrairemos o valor desse grupo como **replacementValue** de **replacementPropertyName**. |
| **replacementValue** |Opcional |String |Novo valor com o qual substituir um antigo. |
| **replacementAttributeName** |Opcional |String |Nome do atributo a ser usado para o valor de substituição |
| **template** |Opcional |String |Quando o valor de **template** for fornecido, procuraremos **oldValue** dentro de template e o substituiremos pelo valor de **source**. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Função:**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3,...)

**Descrição:**<br> Requer um mínimo de dois argumentos, que são definidas usando expressões de regras de geração de valor exclusivo. A função avalia cada regra e, em seguida, verifica o valor gerado para exclusividade no aplicativo/diretório de destino. O primeiro valor exclusivo encontrado será retornado o um. Se todos os valores já existem no destino, a entrada será obter mantida em garantia e o motivo pelo qual obtém registrado nos logs de auditoria. Não há nenhum limite superior para o número de argumentos que podem ser fornecidos.

> [!NOTE]
> - Essa é uma função de nível superior, ele não pode ser aninhado.
> - Esta função não pode ser aplicada a atributos que têm uma precedência correspondente.  
> - Essa função destina-se somente a ser usado para criações de entrada. Ao usá-lo com um atributo, defina a **Aplicar mapeamento** propriedade **somente durante a criação do objeto**.
> - Atualmente, essa função só é compatível com "Provisionamento de usuário do Workday para o Active Directory". Ele não pode ser usado com outros aplicativos de provisionamento. 


**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |Pelo menos 2 são necessários, sem limite superior |String | Lista de regras de geração de valor exclusivo para avaliar. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Função:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Descrição:**<br> Retorna um único appRoleAssignment da lista de todos os appRoleAssignments atribuída a um usuário para um determinado aplicativo. Essa função é necessária para converter o objeto appRoleAssignments em uma cadeia de caracteres de nome de função única. Observe que a prática recomendada é garantir que apenas um appRoleAssignment seja atribuído por usuário por vez, e se várias funções forem atribuídas a cadeia de caracteres de função retornada pode não ser previsível. 

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Obrigatório |String |Objeto **[appRoleAssignments]** . |

---
### <a name="split"></a>Divisão
**Função:**<br> Split(source, delimiter)

**Descrição:**<br> Divide uma cadeia de caracteres em uma matriz com vários valores, usando o caractere delimitador especificado.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **origem** |Obrigatório |String |**fonte** a atualizar. |
| **delimiter** |Obrigatório |String |Especifica o caractere que será usado para dividir a cadeia de caracteres (exemplo: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**Função:**<br> StripSpaces(source)

**Descrição:**<br> remove todos os caracteres de espaço (" ") da caracteres da cadeia de source.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **origem** |Obrigatório |String |**fonte** a atualizar. |

---
### <a name="switch"></a>Opção
**Função:**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**Descrição:**<br> Quando o valor de **source** corresponde a um parâmetro **key**, retorna **value** para esse parâmetro **key**. Se o valor de **source** não corresponder a nenhum parâmetro key, **defaultValue** será retornado.  Os parâmetros **key** e **value** devem sempre ocorrer em pares. A função sempre espera um número par de parâmetros. A função não deve ser usada para atributos referenciais como Manager. 

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **origem** |Obrigatório |String |**Source** a atualizar. |
| **defaultValue** |Opcional |String |Valor padrão a ser usado quando source não corresponde a nenhum parâmetro. Pode ser uma cadeia de caracteres vazia (""). |
| **chave** |Obrigatório |String |Parâmetro **key** com o qual comparar o valor de **source**. |
| **value** |Obrigatório |String |Valor de substituição para o **source** que corresponde ao parâmetro key. |

---
### <a name="tolower"></a>ToLower
**Função:**<br> ToLower (origem, cultura)

**Descrição:**<br> Usa um valor de cadeia de caracteres de *origem* e o converte em letras minúsculas usando as regras de cultura especificadas. Se não houver nenhuma informação de *cultura* especificada, ela usará a Cultura invariável.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **origem** |Obrigatório |String |Normalmente o nome do atributo do objeto de source |
| **cultura** |Opcional |String |O formato para o nome da cultura com base em RFC 4646 é *languagecode2-country/regioncode2*, em que *regioncode2* é o código de idioma de duas letras e *country/regioncode2* é o código de subcultura de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Quando não há um código de idioma de duas letras disponível, um código de três letras derivado da ISO 639-2 é usado.|

---
### <a name="toupper"></a>ToUpper
**Função:**<br> ToUpper (origem, cultura)

**Descrição:**<br> Usa um valor de cadeia de caracteres de *origem* e o converte em letras maiúsculas usando as regras de cultura especificadas. Se não houver nenhuma informação de *cultura* especificada, ela usará a Cultura invariável.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **origem** |Obrigatório |String |Normalmente o nome do atributo do objeto de source. |
| **cultura** |Opcional |String |O formato para o nome da cultura com base em RFC 4646 é *languagecode2-country/regioncode2*, em que *regioncode2* é o código de idioma de duas letras e *country/regioncode2* é o código de subcultura de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Quando não há um código de idioma de duas letras disponível, um código de três letras derivado da ISO 639-2 é usado.|

---
### <a name="word"></a>Word
**Função:**<br> Word (cadeia de caracteres, WordNumber, delimitadores)

**Descrição:**<br> a função Word retorna uma palavra contida em uma cadeia de caracteres com base nos parâmetros que descrevem os delimitadores a serem usados e o número de palavras a serem retornadas. cada cadeia de caracteres separada por um dos caracteres delimitadores na cadeia de caracteres é identificada como palavra:

Se number < 1, retorna uma cadeia de caracteres vazia.
Se a cadeia de caracteres for nula, retorna a cadeia de caracteres vazia.
Se a cadeia de caracteres for menor que o número de palavras ou a cadeia não contiver nenhuma palavra identificada por delimitadores, uma cadeia de caracteres vazia será retornada.

**Parâmetros:**<br> 

| Nome | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **Cadeia de caracteres** |Obrigatório |Atributo de valores múltiplos |Cadeia de caracteres da qual retornar uma palavra.|
| **WordNumber** |Obrigatório | Integer | Número que identifica qual número de palavras deve retornar|
| **delimitadores** |Obrigatório |String| Uma cadeia de caracteres que representa os delimitadores que devem ser usados para identificar palavras|

**Exemplo:**<br>
Word ("The Quick Brown raposa", 3, "")                                                                                       
retorna "brown"

Word ("this, String! tem & muitos separadores", 3, ",! & #")                                                                       
Retorna "tem"

---

## <a name="examples"></a>Exemplos
### <a name="strip-known-domain-name"></a>Retirar o nome de domínio conhecido
Você precisa retirar um nome de domínio conhecido do email de um usuário para obter um nome de usuário. <br>
Por exemplo, se o domínio for "contoso.com", você pode usar a seguinte expressão:

**Expressão:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Entrada/saída de exemplo:** <br>

* **INPUT** (mail): "john.doe@contoso.com"
* **SAÍDA**: "davi.barros"

### <a name="append-constant-suffix-to-user-name"></a>Acrescentar sufixo constante ao nome de usuário
Se você estiver usando um Salesforce Sandbox, talvez seja necessário acrescentar um sufixo adicional a todos os nomes de usuário antes de sincronizá-los.

**Expressão:** <br>
`Append([userPrincipalName], ".test")`

**Entrada/saída de exemplo:** <br>

* **INPUT**: (userPrincipalName): "John.Doe@contoso.com"
* **OUTPUT**:  "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Gerar o alias de usuário concatenando partes do nome e do sobrenome
Você precisa gerar um alias de usuário selecionando as três primeiras letras do nome do usuário e as cinco primeiras letras do sobrenome do usuário.

**Expressão:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Entrada/saída de exemplo:** <br>

* **ENTRADA** (givenName): "Davi"
* **ENTRADA** (sobrenome): "Barros"
* **SAÍDA**: "DaviBarros"

### <a name="remove-diacritics-from-a-string"></a>Remover diacríticos de uma cadeia de caracteres
Você precisa substituir caracteres que contenham os acentos por caracteres equivalentes que não contenham acentos.

**Expressão:** <br>
NormalizeDiacritics([givenName])

**Entrada/saída de exemplo:** <br>

* **INPUT** (givenName): "Zoë"
* **SAÍDA**:  "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dividir uma cadeia de caracteres em uma matriz de vários valores
É necessário usar uma lista de cadeias de caracteres delimitada por vírgula e dividi-las em uma matriz que possa ser conectada a um atributo com vários valores, como o atributo PermissionSets do Salesforce. Neste exemplo, uma lista de conjuntos de permissões foi preenchida em extensionAttribute5 no Azure AD.

**Expressão:** <br>
Split([extensionAttribute5], ",")

**Entrada/saída de exemplo:** <br>

* **Entrada** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **SAÍDA**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Gerar data como uma cadeia de caracteres em um determinado formato
Você deseja enviar datas para um aplicativo SaaS em um determinado formato. <br>
Por exemplo, você deseja formatar datas para o ServiceNow.

**Expressão:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Entrada/saída de exemplo:**

* **ENTRADA** (extensionAttribute1): "20150123105347.1Z"
* **SAÍDA**:  "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Substituir um valor com base em um conjunto predefinido de opções

Você precisa definir o fuso horário do usuário com base no código de estado armazenado no AD do Azure. <br>
Se o código de estado não corresponder a nenhuma das opções predefinidas, use o valor padrão de "Australia/Sydney".

**Expressão:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Entrada/saída de exemplo:**

* **ENTRADA** (estado): "QLD"
* **SAÍDA**: "Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Substituir caracteres usando uma expressão regular
É necessário localizar caracteres que correspondam a um valor de expressão regular e removê-los.

**Expressão:** <br>

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**Entrada/saída de exemplo:**

* **ENTRADA** (mailNickname: "john_doe72"
* **Saída**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Converter o valor userPrincipalName (UPN) gerado em letras minúsculas
No exemplo a seguir, o valor do UPN é gerado pela concatenação dos campos de origem PreferredFirstName e PreferredLastName, e a função ToLower opera na cadeia de caracteres gerada para converter todos os caracteres em letras minúsculas. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Entrada/saída de exemplo:**

* **INPUT** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **OUTPUT**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Gerar um valor exclusivo para o atributo userPrincipalName (UPN)
Com base no usuário primeiro nome, sobrenome e sobrenome, você precisa gerar um valor para o atributo UPN e procure sua exclusividade no diretório de destino AD antes de atribuir o valor para o atributo UPN.

**Expressão:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Entrada/saída de exemplo:**

* **INPUT** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **SAÍDA**: "John.Smith@contoso.com" se o valor de UPN do John.Smith@contoso.com ainda não existir no diretório
* **SAÍDA**: "J.Smith@contoso.com" se o valor UPN de John.Smith@contoso.com já existir no diretório
* **SAÍDA**: "Jo.Smith@contoso.com" se os dois valores UPN acima já existirem no diretório

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>O valor do fluxo de email se não for nulo; caso contrário, flua userPrincipalName
Você deseja fluir o atributo mail se ele estiver presente. Se não for, você deseja fluir o valor de userPrincipalName em vez disso.

**Expressão:** <br>
`Coalesce([mail],[userPrincipalName])`

**Entrada/saída de exemplo:** <br>

* **Entrada** (email): nulo
* **Entrada** (userPrincipalName): "John.Doe@contoso.com"
* **OUTPUT**:  "John.Doe@contoso.com"

## <a name="related-articles"></a>Artigos relacionados
* [Automatizar o provisionamento/desprovisionamento de usuários para aplicativos SaaS](user-provisioning.md)
* [Personalizando os mapeamentos de atributos para provisionamento de usuários](customize-application-attributes.md)
* [Filtros de escopo para provisionamento de usuários](define-conditional-rules-for-provisioning-user-accounts.md)
* [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](use-scim-to-provision-users-and-groups.md)
* [Notificações de provisionamento de conta](user-provisioning.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md)
