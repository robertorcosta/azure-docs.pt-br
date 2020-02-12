---
title: Autenticação, solicitações e respostas
description: Autenticar no AD para usar o Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: b023b49955f642f1cafcb5f26ae67e657718bcd6
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77148225"
---
# <a name="authentication-requests-and-responses"></a>Autenticação, solicitações e respostas

O Azure Key Vault oferece suporte a solicitações e respostas no formato JSON. As solicitações para o Azure Key Vault são direcionadas para uma URL válida do Azure Key Vault usando HTTPS com alguns parâmetros de URL e corpos de solicitação e resposta codificados com JSON.

Este tópico aborda informações específicas para o serviço do Azure Key Vault. Para obter informações gerais sobre o uso de interfaces de REST do Azure, incluindo autenticação/autorização e como adquirir um token de acesso, consulte a [Referência da API REST do Azure](https://docs.microsoft.com/rest/api/azure).

## <a name="request-url"></a>URL de Solicitação  
 As principais operações de gerenciamento usam HTTP DELETE, GET, PATCH, PUT e HTTP POST e operações criptográficas em relação a objetos de chave existentes usam HTTP POST. Os clientes que não suportam verbos HTTP específicos também podem usar HTTP POST usando o cabeçalho X-HTTP-REQUEST para especificar o verbo pretendido; solicitações que normalmente não exigem um corpo devem incluir um corpo vazio quando usando HTTP POST, por exemplo, ao usar POST em vez de DELETE.  

 Para trabalhar com objetos no Azure Key Vault, a seguir estão exemplos de URLs:  

- Para criar (CREATE) uma chave chamada TESTKEY em um Key Vault use - `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- Para importar (IMPORTAR) uma chave chamada IMPORTEDKEY em um Key Vault use - `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- Para obter (GET) uma chave chamada MYSECRET em um Key Vault use - `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Para assinar (SIGN) uma chave chamada TESTKEY em um Key Vault use - `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

  A autoridade para uma solicitação para um Key Vault sempre é como segue,  `https://{keyvault-name}.vault.azure.net/`  

  As chaves são sempre armazenadas no caminho /keys, os segredos são sempre armazenados no caminho /secrets.  

## <a name="api-version"></a>Versão da API  
 O serviço do Azure Key Vault oferece suporte ao controle de versão de protocolo para fornecer compatibilidade com clientes de nível inferior, embora nem todos os recursos estejam disponíveis para esses clientes. Os clientes devem usar o parâmetro de cadeia de consulta `api-version` para especificar a versão do protocolo que oferece suporte, pois não há nenhum padrão.  

 As versões do protocolo do Azure Key Vault seguem um esquema de numeração de data usando o formato {AAAA}.{MM}.{DD}.  

## <a name="request-body"></a>Corpo da solicitação  
 De acordo com a especificação de HTTP, operações GET NÃO devem ter um corpo de solicitação e operações POST e PUT devem ter um corpo de solicitação. O corpo em operações DELETE é opcional no HTTP.  

 Salvo indicação em contrário na descrição da operação, o tipo de conteúdo de corpo de solicitação deve ser application/json e deve conter uma objeto JSON serializado em conformidade com o tipo de conteúdo.  

 Salvo indicação em contrário na descrição da operação, o cabeçalho de solicitação Accept deve conter o tipo de mídia application/json.  

## <a name="response-body"></a>Corpo da resposta  
 Salvo indicação em contrário na descrição da operação, o tipo de conteúdo do corpo de resposta de operações bem-sucedidas e com falhas será application/json e contém informações de erro detalhadas.  

## <a name="using-http-post"></a>Usar HTTP POST  
 Alguns clientes não poderão usar determinados verbos HTTP, como PATCH ou DELETE. O Azure Key Vault oferece suporte a HTTP POST como uma alternativa para esses clientes desde que o cliente também inclua o cabeçalho “X-HTTP-METHOD” para especificar verbo HTTP original. O suporte para HTTP POST é registrado para cada API definida neste documento.  

## <a name="error-responses"></a>Respostas de erro  
 O tratamento de erros usará códigos de status HTTP. Os resultados normais são:  

- 2xx – Êxito: Usado para operação normal. O corpo da resposta conterá o resultado esperado  

- 3xx – Redirecionamento: O código 304 “Não modificado” pode ser retornada para preencher o GET condicional. Outros códigos de 3xx podem ser usados no futuro para indicar as alterações de DNS e caminho.  

- 4xx – erro de cliente: usado para solicitações inválidas, chaves ausentes, erros de sintaxe, parâmetros inválidos, erros de autenticação, etc. O corpo da resposta conterá uma explicação detalhada do erro.  

- 5xx – Erro de servidor: Usado para erros de servidor interno. O corpo da resposta conterá explicações resumidas do erro.  

  O sistema foi projetado para funcionar por trás de um firewall ou proxy. Portanto, um cliente pode receber outros códigos de erro.  

  O Azure Key Vault também retorna informações de erro no corpo da resposta quando ocorre um problema. O corpo da resposta é formatado em JSON e assume a forma:  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>Autenticação  
 Todas as solicitações para o Azure Key Vault DEVEM ser autenticadas. O Azure Key Vault oferece suporte a tokens de acesso do Azure Active Directory que podem ser obtidos usando OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)]. 
 
 Para obter mais informações sobre como registrar seu aplicativo e sobre a autenticação para usar o Azure Key Vault, consulte [Registrar seu aplicativo cliente com o Azure AD](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad).
 
 Tokens de acesso devem ser enviados para o serviço usando o cabeçalho de autorização HTTP:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Quando não for fornecido um token de acesso, ou quando um token não é aceito pelo serviço, um erro HTTP 401 será retornado ao cliente e incluirá o cabeçalho WWW-Authenticate, por exemplo:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Os parâmetros no cabeçalho WWW-Authenticate são:  

-   authorization: O endereço do serviço de autorização OAuth2 que pode ser usado para obter um token de acesso para a solicitação.  

-   recurso: o nome do recurso (https://vault.azure.net) para usar na solicitação de autorização.  

## <a name="see-also"></a>Consulte Também  
 [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
