---
title: Funções de modelo
description: Descreve as funções a serem usadas no modelo do Gerenciador de Recursos do Azure para recuperar valores, trabalhar com cadeias de caracteres e numéricos e recuperar informações de implantação.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 1d2789e59c091b4e6c39be48b83fe610a592abe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156252"
---
# <a name="arm-template-functions"></a>Funções do modelo ARM

Este artigo descreve todas as funções que você pode usar em um modelo ARM (Azure Resource Manager). Para obter informações sobre o uso de funções em seu modelo, consulte [sintaxe de modelo](template-expressions.md).

Para criar suas próprias funções, consulte [Funções definidas pelo usuário](template-syntax.md#functions).

A maioria das funções funciona da mesma forma quando implantada em um grupo de recursos, assinatura, grupo de gerenciamento ou inquilino. Algumas funções não podem ser usadas em todos os escopos. Eles estão anotados nas listas abaixo.

<a id="array" aria-hidden="true" />
<a id="coalesce" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="json" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-and-object-functions"></a>Funções de objeto e matriz

O Resource Manager fornece diversas funções para trabalhar com matrizes e objetos.

* [Matriz](template-functions-array.md#array)
* [coalesce](template-functions-array.md#coalesce)
* [concat](template-functions-array.md#concat)
* [Contém](template-functions-array.md#contains)
* [createArray](template-functions-array.md#createarray)
* [empty](template-functions-array.md#empty)
* [Primeiro](template-functions-array.md#first)
* [Interseção](template-functions-array.md#intersection)
* [Json](template-functions-array.md#json)
* [Última](template-functions-array.md#last)
* [comprimento](template-functions-array.md#length)
* [Min](template-functions-array.md#min)
* [max](template-functions-array.md#max)
* [Gama](template-functions-array.md#range)
* [skip](template-functions-array.md#skip)
* [Levar](template-functions-array.md#take)
* [União](template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Funções de comparação

O Resource Manager fornece várias funções para fazer comparações em seus modelos.

* [equals](template-functions-comparison.md#equals)
* [Menos](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [Maior](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="deployment-value-functions"></a>Funções de valor de implantação

O Gerenciador de Recursos fornece as seguintes funções para obter os valores de seções do modelo e os valores relacionados à implantação:

* [Implantação](template-functions-deployment.md#deployment)
* [Ambiente](template-functions-deployment.md#environment)
* [Parâmetros](template-functions-deployment.md#parameters)
* [Variáveis](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Funções lógicas

O Gerenciador de Recursos fornece as seguintes funções para trabalhar com condições lógicas:

* [E](template-functions-logical.md#and)
* [bool](template-functions-logical.md#bool)
* [if](template-functions-logical.md#if)
* [Não](template-functions-logical.md#not)
* [Ou](template-functions-logical.md#or)

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

* [adicionar](template-functions-numeric.md#add)
* [copyIndex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [Flutuar](template-functions-numeric.md#float)
* [Int](template-functions-numeric.md#int)
* [Min](template-functions-numeric.md#min)
* [max](template-functions-numeric.md#max)
* [Mod](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [Sub](template-functions-numeric.md#sub)

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

* [extensãoResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [listaschaves](template-functions-resource.md#listkeys)
* [ListSecrets](template-functions-resource.md#list)
* [lista*](template-functions-resource.md#list)
* [providers](template-functions-resource.md#providers)
* [Referência](template-functions-resource.md#reference)
* [resourceGroup](template-functions-resource.md#resourcegroup) - só pode ser usado em implantações para um grupo de recursos.
* [resourceId](template-functions-resource.md#resourceid) - pode ser usado em qualquer escopo, mas os parâmetros válidos mudam dependendo do escopo.
* [assinatura](template-functions-resource.md#subscription) - só pode ser usado em implantações para um grupo de recursos ou assinatura.
* [assinaturaResourceId](template-functions-resource.md#subscriptionresourceid)
* [inquilinoResourceId](template-functions-resource.md#tenantresourceid)

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

* [base64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [concat](template-functions-string.md#concat)
* [Contém](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [empty](template-functions-string.md#empty)
* [Endswith](template-functions-string.md#endswith)
* [Primeiro](template-functions-string.md#first)
* [Formato](template-functions-string.md#format)
* [Guid](template-functions-string.md#guid)
* [Indexof](template-functions-string.md#indexof)
* [Última](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [comprimento](template-functions-string.md#length)
* [Newguid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [Substituir](template-functions-string.md#replace)
* [skip](template-functions-string.md#skip)
* [split](template-functions-string.md#split)
* [Startswith](template-functions-string.md#startswith)
* [String](template-functions-string.md#string)
* [substring](template-functions-string.md#substring)
* [Levar](template-functions-string.md#take)
* [Tolower](template-functions-string.md#tolower)
* [Toupper](template-functions-string.md#toupper)
* [Guarnição](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [Uri](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)
* [utcNow](template-functions-string.md#utcnow)

## <a name="next-steps"></a>Próximas etapas

* Para obter uma descrição das seções em um modelo ARM, consulte [Modelos de arm de autoria](template-syntax.md)
* Para mesclar diversos modelos, confira a seção [Como usar modelos vinculados com o Gerenciador de Recursos do Azure](linked-templates.md)
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte [Criar várias instâncias de recursos no Azure Resource Manager](copy-resources.md).
* Para ver como implantar o modelo criado, consulte [Implantar um aplicativo com modelos ARM](deploy-powershell.md)
