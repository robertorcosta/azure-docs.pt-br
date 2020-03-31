---
title: Restrições e detalhes do suporte a formatos de API
titleSuffix: Azure API Management
description: Detalhes de problemas e restrições conhecidos nos formatos Open API, WSDL e WADL são suportados no Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: 61d43addfdf9008cb7aa8a073dcf3bb702cb55f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513364"
---
# <a name="api-import-restrictions-and-known-issues"></a>Restrições de importação de API e problemas conhecidos

## <a name="about-this-list"></a>Sobre esta lista

Ao importar uma API, você pode encontrar algumas restrições ou identificar problemas que precisam ser corrigidos antes de realizar a importação com sucesso. Este artigo documenta essas limitações, organizadas pelo formato de importação da API. Também descreve como funciona a exportação openapi.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Limitações de importação OpenAPI/Swagger

Caso esteja recebendo erros ao importar seu documento da OpenAPI, verifique se você o validou com antecedência. Você pode fazer isso usando o designer no portal do Azure (Design – Front-End – Editor de especificação de OpenAPI) ou com uma ferramenta de terceiros, como o <a href="https://editor.swagger.io">Editor do Swagger</a>.

### <a name="general"></a><a name="open-api-general"> </a>Geral

-   Os parâmetros obrigatórios para o caminho e a consulta precisam ter nomes exclusivos. (Na OpenAPI, um nome de parâmetro só precisa ser exclusivo em uma localização, por exemplo, caminho, consulta, cabeçalho. No entanto, no Gerenciamento de API, permitimos que as operações sejam discriminadas por parâmetros de caminho e de consulta [o que não é compatível com a OpenAPI]. Por isso é necessário que os nomes de parâmetro sejam exclusivos dentro de todo o modelo de URL.)
-   `\$ref`ponteiros não podem referenciar arquivos externos.
-   `x-ms-paths`e `x-servers` são as únicas extensões suportadas.
-   As extensões personalizadas são ignoradas na importação e não são salvas ou preservadas para exportação.
-   `Recursion`- A Gestão de API não suporta definições definidas recursivamente (por exemplo, esquemas referentes a si mesmos).
-   A URL do arquivo de origem (se disponível) é aplicada a URLs de servidor relativo.
-   As definições de segurança são ignoradas.
-   Definições de esquema inline para operações de API não são suportadas. As definições de esquema são definidas no escopo da API e podem ser referenciadas em escopos de solicitação ou resposta de operações de API.
-   Um parâmetro de URL definido precisa fazer parte do modelo de URL.
-   `Produces`palavra-chave, que descreve tipos mime retornados por uma API, não é suportada. 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI versão 2

-   Somente o formato JSON tem suporte.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI versão 3

-   Se `servers` muitos forem especificados, o Gerenciamento de API tentará selecionar a primeira URL de HTTPs. Se não houver URLs HTTPs, a primeira URL HTTP. Se não houver URLs HTTP, a URL do servidor ficará vazia.
-   `Examples`não é suportado, `example` mas é.

## <a name="openapi-import-update-and-export-mechanisms"></a>Mecanismos de importação, atualização e exportação do OpenAPI

### <a name="add-new-api-via-openapi-import"></a>Adicionar nova API via importação OpenAPI

Para cada operação encontrada no documento OpenAPI, uma nova operação será criada com `operationId` o `summary` nome do recurso do Azure e o nome de exibição definidos para e respectivamente. `operationId`o valor é normalizado seguindo as regras descritas abaixo. `summary`o valor é importado como está e seu comprimento é limitado a 300 caracteres.

Se `operationId` não for especificado (isto `null`é, não presente, ou vazio), o valor do nome do recurso `get-foo`do Azure será gerado combinando o método HTTP e o modelo de caminho, por exemplo, .

Se `summary` não for especificado (isto `null`é, não `display name` presente, `operationId`ou vazio), o valor será definido como . Se `operationId` não for especificado, o valor do nome de exibição será `Get - /foo`gerado combinando o método HTTP e o modelo de caminho, por exemplo, .

### <a name="update-an-existing-api-via-openapi-import"></a>Atualize uma API existente via importação OpenAPI

Durante a importação, a API existente é alterada para corresponder à API descrita no documento OpenAPI. Cada operação no documento OpenAPI é compatível com a `operationId` operação existente, comparando seu valor com o nome de recurso do Azure da operação existente.

Se uma correspondência for encontrada, as propriedades da operação existentes serão atualizadas "no local".

Se uma correspondência não for encontrada, uma nova operação será criada usando as regras descritas na seção acima. Para cada nova operação, a importação tentará copiar políticas de uma operação existente com o mesmo método HTTP e modelo de caminho.

Todas as operações incomparáveis existentes serão excluídas.

Para tornar a importação mais previsível, siga estas diretrizes:

- Certifique-se `operationId` de especificar propriedade para cada operação.
- Abstenha-se de mudar `operationId` após a importação inicial.
- Nunca `operationId` mude e o método HTTP ou modelo de caminho ao mesmo tempo.

### <a name="export-api-as-openapi"></a>API de exportação como OpenAPI

Para cada operação, seu nome de recurso Azure será exportado como um `operationId`, e o nome de exibição será exportado como um `summary`.
Regras de normalização para operaçãoId

- Converter para letras minúsculas.
- Substitua cada seqüência de caracteres não alfanuméricos por um único traço, por exemplo, `GET-/foo/{bar}?buzz={quix}` será transformado em `get-foo-bar-buzz-quix-`.
- Traços de corte em ambos `get-foo-bar-buzz-quix-` os lados, por exemplo, se tornarão`get-foo-bar-buzz-quix`
- Truncar para caber 76 caracteres, quatro caracteres menos do que o limite máximo para um nome de recurso.
- Use os quatro caracteres restantes para um sufixo `-1, -2, ..., -999`de duplicação, se necessário, na forma de .


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

Os arquivos WSDL são usados para criar APIs de passagem SOAP e SOAP-to-REST.

-   **Associações SOAP** - Apenas as associações SOAP de estilo "documento" e codificação "literal" têm suporte. Não há suporte para estilo "rpc" ou SOAP-Encoding.
-   **WSDL:Import** – não há suporte para esse atributo. Os clientes devem mesclar as importações em um documento.
-   **Mensagens com várias partes** – não há suporte para esses tipos de mensagens.
-   **WCF wsHttpBinding** – serviços SOAP criados com Windows Communication Foundation devem usar basicHttpBinding – não há suporte para wsHttpBinding.
-   **MTOM** - Serviços usando MTOM <em>podem</em> funcionar. No momento, não oferecemos suporte oficial.
-   **Recursão** - Tipos que são definidos recursivamente (por exemplo, referem-se a uma matriz deles) não têm suporte pelo APIM.
-   **Vários Namespaces** – vários namespaces podem ser usados em um esquema, mas apenas o namespace de destino pode ser usado para definir partes da mensagem. Os namespaces que não sejam o destino, que são usados para definir outros elementos de entrada ou saída, não são preservados. Embora um documento WSDL desse tipo possa ser importado, na exportação, todas as partes da mensagem terão o namespace de destino do WSDL.
-   **Arrays** - A transformação SOAP-to-REST suporta apenas matrizes embrulhadas mostradas no exemplo abaixo:

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>WADL

Atualmente, não há problemas de importação de WADL conhecidos.
