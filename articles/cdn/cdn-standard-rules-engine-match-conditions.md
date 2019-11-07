---
title: CDN do Azure do mecanismo de regras padrão da Microsoft critérios de correspondência | Microsoft Docs
description: Documentação de referência para a rede de distribuição de conteúdo do Azure das condições de correspondência do mecanismo de regras padrão da Microsoft.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: a72452d37b152a9463a5aee0e199fd42ea852236
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615956"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-match-conditions"></a>CDN do Azure do mecanismo de regras padrão da Microsoft condições de correspondência

Este artigo lista descrições detalhadas das condições de correspondência disponíveis para a CDN (rede de distribuição de conteúdo) do Azure do [mecanismo de regras padrão](cdn-standard-rules-engine.md)da Microsoft.

A primeira parte de uma regra é um conjunto de condições de correspondência. Cada regra pode ter até 4 condições de correspondência. Uma condição de correspondência identifica tipos específicos de solicitações para as quais as ações definidas na regra serão executadas. Se você usar várias condições de correspondência, elas serão agrupadas usando a lógica e.

Por exemplo, você pode usar uma condição de correspondência para:

- Filtrar solicitações geradas de um determinado endereço IP ou país/região.
- Filtrar solicitações por informações de cabeçalho.
- Filtrar solicitações de dispositivos móveis ou de desktop.

## <a name="match-conditions"></a>Condições de correspondência

As seguintes condições de correspondência estão disponíveis para uso. 

### <a name="device-type"></a>Tipo de dispositivo 

A condição de correspondência de tipo de dispositivo identifica solicitações feitas de um dispositivo móvel ou de desktop com base em suas propriedades.  

**Campos obrigatórios**

Operador | Valor com suporte
---------|----------------
Equals, não é igual a | Móvel, área de trabalho


### <a name="http-version"></a>Versão de HTTP

A condição de correspondência de versão HTTP identifica solicitações com base na versão HTTP à qual a solicitação chega.

**Campos obrigatórios**

Operador | Valor com suporte
---------|----------------
Equals, não é igual a | 2,0, 1,1, 1,0, 0,9, todos


### <a name="request-cookies"></a>Solicitar cookies

A condição de correspondência de cookies de solicitação identifica solicitações com base nas informações de cookie na solicitação de entrada.

**Campos obrigatórios**

Nome do cookie | Operador | Valor do cookie | Transformação de caso
------------|----------|--------------|---------------
Cadeia de caracteres | [Lista de operadores padrão](#standard-operator-list) | Cadeia de caracteres, int | Nenhuma transformação, para letras maiúsculas, para minúsculas

Principais informações
- Não há suporte para valores curinga, incluindo asteriscos (*), ao especificar um nome de cookie, somente as correspondências de nome de cookie exatas são elegíveis para comparação.
- Somente um nome de cookie único pode ser especificado por instância dessa condição de correspondência.
- Comparações de nome de cookie diferenciam maiusculas de minúsculas.
- Especifique vários valores de cookie delimitando cada um deles com um único espaço. 
- Os valores de cookie podem tirar proveito dos valores curinga.
- Se um valor curinga não tiver sido especificado, apenas uma correspondência exata atenderá a essa condição de correspondência. Por exemplo, a especificação de "valor" corresponderá a "valor", mas não a "value1". 

### <a name="post-argument"></a>Argumento post

**Campos obrigatórios**

Nome do argumento | Operador | Valor do argumento | Transformação de caso
--------------|----------|----------------|---------------
Cadeia de caracteres | [Lista de operadores padrão](#standard-operator-list) | Cadeia de caracteres, int | Nenhuma transformação, para letras maiúsculas, para minúsculas

### <a name="query-string"></a>Cadeia de Consulta

As condições de correspondência da cadeia de caracteres de consulta identifica as solicitações que contêm um parâmetro de cadeia de consulta especificado. Esse parâmetro é definido como um valor que corresponde a um padrão especificado. Parâmetros de cadeia de caracteres de consulta (por exemplo, parâmetro = valor) na URL de solicitação determinam se essa condição é atendida. Essa condição de correspondência identifica um parâmetro de cadeia de caracteres de consulta por seu nome e aceita um ou mais valores para o valor do parâmetro.

**Campos obrigatórios**

Operador | Cadeia de Consulta | Transformação de caso
---------|--------------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de caracteres, int | Nenhuma transformação, para letras maiúsculas, para minúsculas

### <a name="remote-address"></a>Endereço remoto

A condição de correspondência de endereço remoto identifica solicitações com base no local ou endereço IP do solicitante.

**Campos obrigatórios**

Operador | Valores com suporte
---------|-----------------
Qualquer | N/D
Correspondência geográfica | Códigos de país
Correspondência de IP | Endereços IP (separado pelo espaço)
Não qualquer | N/D
Não correspondência geográfica | Códigos de país
Não correspondência de IP | Endereços IP (separado pelo espaço)

Informações de chave:

- Use a notação CIDR.
- Especifique vários endereços IP e/ou blocos de endereços IP delimitando cada um com um único espaço. Por exemplo:
  - **Exemplo de IPv4**: 1.2.3.4 10.20.30.40 corresponde a qualquer solicitação que chegue do endereço 1.2.3.4 ou 10.20.30.40.
  - **Exemplo de IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 corresponde a qualquer solicitação que chegue do endereço 1:2:3:4:5:6:7:8 ou 10:20:30:40:50:60:70:80.
- A sintaxe para um bloco de endereços IP é o endereço IP básico seguido por uma barra invertida e o tamanho do prefixo. Por exemplo:
  - **Exemplo de IPv4**: 5.5.5.64/26 corresponde a qualquer solicitação que chegue do endereço 5.5.5.64 ou 5.5.5.127.
  - **Exemplo de IPv6**: 1:2:3:/48 corresponde a qualquer solicitação que chegue dos endereços 1:2:3:0:0:0:0:0 por meio de 1:2:3:ffff:ffff:ffff:ffff:ffff.

### <a name="request-body"></a>Corpo da solicitação

**Campos obrigatórios**

Operador | Corpo da solicitação | Transformação de caso
---------|--------------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de caracteres, int | Nenhuma transformação, para letras maiúsculas, para minúsculas

### <a name="request-header"></a>Cabeçalho da Solicitação

**Campos obrigatórios**
Nome do cabeçalho | Operador | Valor do cabeçalho | Transformação de caso
------------|----------|--------------|---------------
Cadeia de caracteres | [Lista de operadores padrão](#standard-operator-list) | Cadeia de caracteres, int | Nenhuma transformação, para letras maiúsculas, para minúsculas

### <a name="request-method"></a>Método de Solicitação

**Campos obrigatórios**

Operador | Valor com suporte
---------|----------------
Equals, não é igual a | OBTER, POSTAR, COLOCAR, EXCLUIR, CABEÇALHO, OPÇÕES, RASTREAMENTO

Informações de chave:

- Somente o método GET Request pode gerar conteúdo armazenado em cache na CDN. Todos os outros métodos de solicitação são solicitados por proxy através da rede. 

### <a name="request-protocol"></a>Protocolo de Solicitação

**Campos obrigatórios**

Operador | Valor com suporte
---------|----------------
Equals, não é igual a | HTTP, HTTPS

### <a name="request-url"></a>URL de Solicitação

**Campos obrigatórios**

Operador | URL de Solicitação | Transformação de caso
---------|-------------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de caracteres, int | Nenhuma transformação, para letras maiúsculas, para minúsculas

Informações de chave:

- Ao inserir a URL de solicitação, certifique-se de incluir informações de protocolo. Por exemplo, "https://www. [seudomínio]. com "

### <a name="url-file-extension"></a>Extensão de arquivo de URL

**Campos obrigatórios**

Operador | Extensão | Transformação de caso
---------|-----------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de caracteres, int | Nenhuma transformação, para letras maiúsculas, para minúsculas

Informações de chave:

- Para a extensão, não inclua um ponto à esquerda; por exemplo, use HTML em vez de. html.

### <a name="url-file-name"></a>Nome do arquivo da URL

**Campos obrigatórios**

Operador | Nome do arquivo | Transformação de caso
---------|-----------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de caracteres, int | Nenhuma transformação, para letras maiúsculas, para minúsculas

Informações de chave:

- Para especificar vários nomes de arquivo, separe cada nome de arquivo com um único espaço. 

### <a name="url-path"></a>Caminho da URL

**Campos obrigatórios**

Operador | Valor | Transformação de caso
---------|-------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de caracteres, int | Nenhuma transformação, para letras maiúsculas, para minúsculas

Informações de chave:

- Um valor de nome de arquivo pode aproveitar os valores curinga. Por exemplo, cada padrão de nome de arquivo pode consistir em um ou mais asteriscos (*), onde cada asterisco corresponde a uma sequência de um ou mais caracteres.

## <a name="reference-for-rules-engine-match-conditions"></a>Referência para condições de correspondência do mecanismo de regras

### <a name="standard-operator-list"></a>Lista de operadores padrão

Para regras que contêm a lista de operadores padrão, os seguintes operadores são válidos:

- Qualquer
- É igual a 
- Contém: 
- Começa com 
- Termina com 
- Menor que
- Menor ou igual a
- Maior que
- Maior ou igual a
- Não qualquer
- Não contém
- Não começa com 
- Não termina com 
- Não é menor que
- Não é menor ou igual a
- Não é maior que
- Não é maior ou igual a

Para operadores numéricos, como "menor que" ou "maior que ou igual a", a comparação usada será baseada no comprimento. Nesse caso, o valor na condição de correspondência deve ser um inteiro, igual ao comprimento que você deseja comparar. 

---

[Voltar ao início](#match-conditions)

</br>

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da Rede de Distribuição de Conteúdo do Azure](cdn-overview.md)
- [Referência do mecanismo de regras](cdn-standard-rules-engine-reference.md)
- [Ações do mecanismo de regras](cdn-standard-rules-engine-actions.md)
- [Impor HTTPS usando o mecanismo de regras padrão](cdn-standard-rules-engine.md)
