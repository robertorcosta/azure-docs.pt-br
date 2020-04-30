---
title: Funções de modelo
description: Descreve as funções a serem usadas no modelo do Gerenciador de Recursos do Azure para recuperar valores, trabalhar com cadeias de caracteres e numéricos e recuperar informações de implantação.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: a15e7dfdf01a99cd23b216fafcfb44320a716d16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231281"
---
# <a name="arm-template-functions"></a>Funções de modelo ARM

Este artigo descreve todas as funções que você pode usar em um modelo de Azure Resource Manager (ARM). Para obter informações sobre como usar funções em seu modelo, consulte [sintaxe do modelo](template-expressions.md).

Para criar suas próprias funções, consulte [Funções definidas pelo usuário](template-syntax.md#functions).

A maioria das funções funciona da mesma quando implantada em um grupo de recursos, assinatura, grupo de gerenciamento ou locatário. Algumas funções não podem ser usadas em todos os escopos. Eles são indicados nas listas abaixo.

<a id="array" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-functions"></a>Funções de matriz

O Gerenciador de recursos fornece várias funções para trabalhar com matrizes.

* [matriz](template-functions-array.md#array)
* [Concat](template-functions-array.md#concat)
* [contains](template-functions-array.md#contains)
* [createArray](template-functions-array.md#createarray)
* [esvaziá](template-functions-array.md#empty)
* [primeiro](template-functions-array.md#first)
* [interseção](template-functions-array.md#intersection)
* [última](template-functions-array.md#last)
* [length](template-functions-array.md#length)
* [min](template-functions-array.md#min)
* [max](template-functions-array.md#max)
* [amplitude](template-functions-array.md#range)
* [saltar](template-functions-array.md#skip)
* [ter](template-functions-array.md#take)
* [unida](template-functions-array.md#union)

<a id="coalesce" aria-hidden="true" />
<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Funções de comparação

O Resource Manager fornece várias funções para fazer comparações em seus modelos.

* [COALESCE](template-functions-comparison.md#coalesce)
* [equals](template-functions-comparison.md#equals)
* [inferiores](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [grande](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="date-functions"></a>Funções de data

O Gerenciador de recursos fornece as seguintes funções para trabalhar com datas.

* [dateTimeAdd](template-functions-date.md#datetimeadd)
* [utcNow](template-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>Funções de valor de implantação

O Gerenciador de Recursos fornece as seguintes funções para obter os valores de seções do modelo e os valores relacionados à implantação:

* [planta](template-functions-deployment.md#deployment)
* [ambiente](template-functions-deployment.md#environment)
* [parâmetro](template-functions-deployment.md#parameters)
* [as](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Funções lógicas

O Gerenciador de Recursos fornece as seguintes funções para trabalhar com condições lógicas:

* [e](template-functions-logical.md#and)
* [bool](template-functions-logical.md#bool)
* [if](template-functions-logical.md#if)
* [válido](template-functions-logical.md#not)
* [ou](template-functions-logical.md#or)

<a id="add" aria-hidden="true" />
<a id="copyindex" aria-hidden="true" />
<a id="div" aria-hidden="true" />
<a id="float" aria-hidden="true" />
<a id="int" aria-hidden="true" />
<a id="minint" aria-hidden="true" />
<a id="maxint" aria-hidden="true" />
<a id="mod" aria-hidden="true" />
<a id="mul" aria-hidden="true" />
<a id="sub" aria-hidden="true" />

## <a name="numeric-functions"></a>Funções numéricas

O Gerenciador de Recursos fornece as seguintes funções para trabalhar com números inteiros:

* [add](template-functions-numeric.md#add)
* [copyIndex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [float](template-functions-numeric.md#float)
* [int](template-functions-numeric.md#int)
* [min](template-functions-numeric.md#min)
* [max](template-functions-numeric.md#max)
* [resto](template-functions-numeric.md#mod)
* [Mul](template-functions-numeric.md#mul)
* [projeto](template-functions-numeric.md#sub)

<a id="json" aria-hidden="true" />

## <a name="object-functions"></a>Funções de objeto

O Gerenciador de recursos fornece várias funções para trabalhar com objetos.

* [contains](template-functions-object.md#contains)
* [esvaziá](template-functions-object.md#empty)
* [interseção](template-functions-object.md#intersection)
* [JSON](template-functions-object.md#json)
* [length](template-functions-object.md#length)
* [unida](template-functions-object.md#union)

<a id="extensionResourceId" aria-hidden="true" />
<a id="listkeys" aria-hidden="true" />
<a id="list" aria-hidden="true" />
<a id="providers" aria-hidden="true" />
<a id="reference" aria-hidden="true" />
<a id="resourcegroup" aria-hidden="true" />
<a id="resourceid" aria-hidden="true" />
<a id="subscription" aria-hidden="true" />
<a id="subscriptionResourceId" aria-hidden="true" />
<a id="tenantResourceId" aria-hidden="true" />

## <a name="resource-functions"></a>Funções de recurso

O Gerenciador de Recursos fornece as seguintes funções para obter valores de recurso:

* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [listKeys](template-functions-resource.md#listkeys)
* [listSecrets](template-functions-resource.md#list)
* [lista](template-functions-resource.md#list)
* [fornecedor](template-functions-resource.md#providers)
* [referência](template-functions-resource.md#reference)
* [resourcegroup](template-functions-resource.md#resourcegroup) -só pode ser usado em implantações em um grupo de recursos.
* [ResourceId](template-functions-resource.md#resourceid) -pode ser usado em qualquer escopo, mas os parâmetros válidos são alterados dependendo do escopo.
* [assinatura](template-functions-resource.md#subscription) -só pode ser usada em implantações em um grupo de recursos ou assinatura.
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true" />
<a id="base64tojson" aria-hidden="true" />
<a id="base64tostring" aria-hidden="true" />
<a id="concat" aria-hidden="true" />
<a id="containsstring" aria-hidden="true" />
<a id="datauri" aria-hidden="true" />
<a id="datauritostring" aria-hidden="true" />
<a id="emptystring" aria-hidden="true" />
<a id="endswith" aria-hidden="true" />
<a id="firststring" aria-hidden="true" />
<a id="guid" aria-hidden="true" />
<a id="indexof" aria-hidden="true" />
<a id="laststring" aria-hidden="true" />
<a id="lastindexof" aria-hidden="true" />
<a id="lengthstring" aria-hidden="true" />
<a id="padleft" aria-hidden="true" />
<a id="replace" aria-hidden="true" />
<a id="skipstring" aria-hidden="true" />
<a id="split" aria-hidden="true" />
<a id="startswith" aria-hidden="true" />
<a id="string" aria-hidden="true" />
<a id="substring" aria-hidden="true" />
<a id="takestring" aria-hidden="true" />
<a id="tolower" aria-hidden="true" />
<a id="toupper" aria-hidden="true" />
<a id="trim" aria-hidden="true" />
<a id="uniquestring" aria-hidden="true" />
<a id="uri" aria-hidden="true" />
<a id="uricomponent" aria-hidden="true" />
<a id="uricomponenttostring" aria-hidden="true" />

## <a name="string-functions"></a>Funções de cadeia de caracteres

O Gerenciador de Recursos fornece as seguintes funções para trabalhar com cadeias de caracteres:

* [Base64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [Concat](template-functions-string.md#concat)
* [contains](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [esvaziá](template-functions-string.md#empty)
* [endsWith](template-functions-string.md#endswith)
* [primeiro](template-functions-string.md#first)
* [format](template-functions-string.md#format)
* [volume](template-functions-string.md#guid)
* [indexOf](template-functions-string.md#indexof)
* [última](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [length](template-functions-string.md#length)
* [newGuid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [replace](template-functions-string.md#replace)
* [saltar](template-functions-string.md#skip)
* [Divisão](template-functions-string.md#split)
* [startsWith](template-functions-string.md#startswith)
* [cadeia de caracteres](template-functions-string.md#string)
* [Subcadeia](template-functions-string.md#substring)
* [ter](template-functions-string.md#take)
* [toLower](template-functions-string.md#tolower)
* [toUpper](template-functions-string.md#toupper)
* [Trim](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [URI](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>Próximas etapas

* Para obter uma descrição das seções em um modelo do ARM, consulte [criação de modelos do ARM](template-syntax.md)
* Para mesclar diversos modelos, confira a seção [Como usar modelos vinculados com o Gerenciador de Recursos do Azure](linked-templates.md)
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte [criar várias instâncias de recursos no Azure Resource Manager](copy-resources.md).
* Para ver como implantar o modelo que você criou, consulte [implantar um aplicativo com modelos do ARM](deploy-powershell.md)
