---
title: Condições de correspondência no motor de regras padrão para Azure CDN | Microsoft Docs
description: Documentação de referência para condições de correspondência no mecanismo de regras padrão para Azure Content Delivery Network (Azure CDN).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 425266e2a7ca42bb17ca598ddfc2f2b86591f32e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900187"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Condições de correspondência no mecanismo de regras padrão para CDN Azure

No [mecanismo de regras padrão](cdn-standard-rules-engine.md) para a Azure Content Delivery Network (Azure CDN), uma regra consiste em uma ou mais condições de correspondência e uma ação. Este artigo fornece descrições detalhadas das condições de correspondência que você pode usar no mecanismo de regras Padrão para CDN Azure.

A primeira parte de uma regra é uma condição de jogo ou um conjunto de condições de jogo. No mecanismo de regras padrão para CDN Azure, cada regra pode ter até quatro condições de correspondência. Uma condição de correspondência identifica tipos específicos de solicitações para as quais as ações definidas são realizadas. Se você usar várias condições de correspondência, as condições de correspondência são agrupadas usando E lógica.

Por exemplo, você pode usar uma condição de correspondência para:

- Filtrar solicitações com base em um endereço IP específico, país ou região.
- Filtrar solicitações por informações de cabeçalho.
- Filtrar solicitações de dispositivos móveis ou dispositivos de desktop.

## <a name="match-conditions"></a>Condições de correspondência

As seguintes condições de correspondência estão disponíveis para uso no mecanismo de regras padrão para CDN Azure. 

### <a name="device-type"></a>Tipo de dispositivo 

Identifica solicitações feitas a partir de um dispositivo móvel ou dispositivo de desktop.  

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores com suporte
---------|----------------
Iguais, Não é igual | Mobile, Desktop

### <a name="http-version"></a>Versão HTTP

Identifica solicitações com base na versão HTTP da solicitação.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores com suporte
---------|----------------
Iguais, Não é igual | 2.0, 1.1, 1.0, 0.9, Todos

### <a name="request-cookies"></a>Solicitar cookies

Identifica solicitações com base em informações de cookies na solicitação recebida.

#### <a name="required-fields"></a>Campos obrigatórios

Nome do cookie | Operador | Valor do cookie | Transformação de caso
------------|----------|--------------|---------------
String | [Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, em maiúsculas, em minúsculas

#### <a name="key-information"></a>Principais informações

- Você não pode usar valores curinga\*(incluindo asteriscos ()) quando você especifica um nome de cookie; você deve usar um nome de cookie exato.
- Você pode especificar apenas um único nome de cookie por instância desta condição de correspondência.
- Comparações de nome de cookie diferenciam maiusculas de minúsculas.
- Para especificar vários valores de cookie, use um único espaço entre cada valor de cookie. 
- Os valores dos cookies podem tirar proveito dos valores curingas.
- Se um valor curinga não foi especificado, apenas uma correspondência exata satisfaz esta condição de correspondência. Por exemplo, "Valor" corresponderá a "Valor" mas não a "Valor1". 

### <a name="post-argument"></a>Post argumente

Identifica solicitações com base em argumentos definidos para o método de solicitação POST que é usado na solicitação. 

#### <a name="required-fields"></a>Campos obrigatórios

Nome do argumento | Operador | Valor do argumento | Transformação de caso
--------------|----------|----------------|---------------
String | [Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, em maiúsculas, em minúsculas

### <a name="query-string"></a>Cadeia de consulta

Identifica solicitações que contêm um parâmetro de seqüência de consulta específico. Este parâmetro é definido como um valor que corresponde a um padrão específico. Os parâmetros da seqüência de consulta (por exemplo, **parâmetro=valor)** na URL de solicitação determinam se essa condição é atendida. Essa condição de correspondência identifica um parâmetro de cadeia de caracteres de consulta por seu nome e aceita um ou mais valores para o valor do parâmetro.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Cadeia de consulta | Transformação de caso
---------|--------------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, em maiúsculas, em minúsculas

### <a name="remote-address"></a>Endereço remoto

Identifica solicitações com base na localização do solicitante ou endereço IP.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores com suporte
---------|-----------------
Qualquer | N/D
Geo Match | Código do país
Correspondência IP | Endereço IP (separado do espaço)
Não qualquer | N/D
Não Geo Match | Código do país
Não é compatível com IP | Endereço IP (separado do espaço)

#### <a name="key-information"></a>Principais informações

- Use a notação CIDR.
- Para especificar vários endereços IP e blocos de endereços IP, use um único espaço entre os valores:
  - **Exemplo IPv4:** *1.2.3.4 10.20.30.40* corresponde a quaisquer solicitações que cheguem do endereço 1.2.3.4 ou 10.20.30.40.
  - **Exemplo iPv6**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* corresponde a quaisquer solicitações que cheguem de qualquer endereço 1:2:3:4:5:6:7:8 ou 10:20:30:40:50:60:70:80.
- A sintaxe para um bloco de endereços IP é o endereço IP básico seguido por uma barra invertida e o tamanho do prefixo. Por exemplo: 
  - **Exemplo IPv4**: *5.5.5.64/26* corresponde a quaisquer solicitações que cheguem dos endereços 5.5.5.64 a 5.5.5.127.
  - **Exemplo iPv6**: *1:2:3:/48* corresponde a quaisquer solicitações que chegam dos endereços 1:2:3:0:0:0:0:0 a 1:2:3:ffff:ffff:ffff:ffff.

### <a name="request-body"></a>Corpo da solicitação

Identifica solicitações com base em texto específico que aparece no corpo da solicitação.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Corpo da solicitação | Transformação de caso
---------|--------------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, em maiúsculas, em minúsculas

### <a name="request-header"></a>Cabeçalho da solicitação

Identifica solicitações que usam um cabeçalho específico na solicitação.

#### <a name="required-fields"></a>Campos obrigatórios

Nome do cabeçalho | Operador | Valor do cabeçalho | Transformação de caso
------------|----------|--------------|---------------
String | [Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, em maiúsculas, em minúsculas

### <a name="request-method"></a>Método de solicitação

Identifica solicitações que usam o método de solicitação especificado.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores com suporte
---------|----------------
Iguais, Não é igual | OBTER, POSTAR, COLOCAR, EXCLUIR, CABEÇA, OPÇÕES, RASTREAR

#### <a name="key-information"></a>Principais informações

- Apenas o método de solicitação GET pode gerar conteúdo armazenado em cache no CDN do Azure. Todos os outros métodos de solicitação são solicitados por proxy através da rede. 

### <a name="request-protocol"></a>Protocolo de solicitação

Identifica solicitações que usam o protocolo especificado utilizado.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores com suporte
---------|----------------
Iguais, Não é igual | HTTP, HTTPS

### <a name="request-url"></a>URL de Solicitação

Identifica solicitações que correspondem à URL especificada.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | URL de Solicitação | Transformação de caso
---------|-------------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, em maiúsculas, em minúsculas

#### <a name="key-information"></a>Principais informações

- Quando você usar esta condição de regra, certifique-se de incluir informações de protocolo. Por exemplo: *https://www.\<yourdomain\>.com*.

### <a name="url-file-extension"></a>Extensão de arquivo URL

Identifica solicitações que incluem a extensão de arquivo especificada no nome do arquivo na URL solicitante.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Extensão | Transformação de caso
---------|-----------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, em maiúsculas, em minúsculas

#### <a name="key-information"></a>Principais informações

- Para prorrogação, não inclua um período de liderança; por exemplo, use *html* em vez de *.html*.

### <a name="url-file-name"></a>Nome do arquivo DE URL

Identifica solicitações que incluem o nome do arquivo especificado na URL solicitante.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Nome do arquivo | Transformação de caso
---------|-----------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, em maiúsculas, em minúsculas

#### <a name="key-information"></a>Principais informações

- Para especificar vários nomes de arquivo, separe cada nome do arquivo com um único espaço. 

### <a name="url-path"></a>Caminho da URL

Identifica solicitações que incluem o caminho especificado na URL solicitante.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valor | Transformação de caso
---------|-------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, em maiúsculas, em minúsculas

#### <a name="key-information"></a>Principais informações

- Um valor de nome de arquivo pode aproveitar os valores curinga. Por exemplo, cada padrão de nome de arquivo pode consistir em um ou mais asteriscos (*), onde cada asterisco corresponde a uma sequência de um ou mais caracteres.

## <a name="reference-for-rules-engine-match-conditions"></a>Referência para condições de correspondência do mecanismo de regras

### <a name="standard-operator-list"></a>Lista de operadores padrão

Para regras que aceitam valores da lista de operadores padrão, os seguintes operadores são válidos:

- Qualquer
- É igual a 
- Contém 
- Começa com 
- Termina com 
- Menor que
- Menos do que ou iguais
- Maior que
- Maior do que ou igual
- Não qualquer
- Não contém
- Não começa com 
- Não termina com 
- Não é menor que
- Não menos do que ou é igual
- Não é maior que
- Não maior essegundo acho ou igual

Para operadores numéricos como *Menos e* *Maiores do que ou iguais,* a comparação utilizada é baseada no comprimento. Neste caso, o valor na condição de correspondência deve ser um inteiro que é igual ao comprimento que você deseja comparar. 

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da CDN do Azure](cdn-overview.md)
- [Referência do mecanismo de regras Standard](cdn-standard-rules-engine-reference.md)
- [Ações no mecanismo de regras Padrão](cdn-standard-rules-engine-actions.md)
- [Impor HTTPS usando o mecanismo de regras Standard](cdn-standard-rules-engine.md)
