---
title: Sobre chaves, segredos e certificados - Azure Key Vault
description: Visão geral dos detalhes de interface e desenvolvedor de REST do Azure Key Vault para chaves, segredos e certificados.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: 241efab246dc903981da570a4191f93cc744bca7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727443"
---
# <a name="about-keys-secrets-and-certificates"></a>Sobre Chaves, Segredos e Certificados

O Azure Key Vault permite que aplicativos do Microsoft Azure e usuários armazenem e utilizem vários tipos de dados de chave/segredo:

- Chaves de criptografia: Dá suporte a vários tipos de chave e algoritmos e permite o uso de módulos de segurança de Hardware (HSM) para chaves de alto valor. Para obter mais informações, confira [Sobre chaves](../keys/about-keys.md).
- Segredos: Fornece armazenamento seguro de segredos, como senhas e cadeias de conexão de banco de dados. Para obter mais informações, confira [Sobre segredos](../secrets/about-secrets.md).
- Certificados: Oferece suporte a certificados, que são criados sobre chaves e segredos e adicionam um recurso de renovação automática. Para obter mais informações, confira [Sobre certificados](../certificates/about-certificates.md).
- Armazenamento do Microsoft Azure: Pode gerenciar chaves de uma conta de Armazenamento do Microsoft Azure para você. Internamente, o Key Vault pode listar (sincronizar) chaves com uma conta de Armazenamento do Azure e gerar novamente (gira) as chaves periodicamente. Para obter mais informações, confira [Gerenciar chaves de acesso da conta de armazenamento com o Key Vault](../secrets/overview-storage-keys.md).

Para obter mais informações gerais sobre o Key Vault, confira [Sobre o Azure Key Vault](overview.md).

## <a name="data-types"></a>Tipos de dados

Consulte as especificações JOSE para tipos de dados relevantes para as chaves, criptografia e assinatura.  

-   **algoritmo** - um algoritmo com suporte para uma operação de chave, por exemplo, RSA1_5  
-   **valor de texto cifrado** - octetos de texto cifrado, codificado usando Base64URL  
-   **valor de Digest** - a saída de um algoritmo de hash, codificado usando Base64URL  
-   **tipo de chave** - um dos tipos de chave com suporte, por exemplo, RSA (Rivest-Shamir-Adleman).  
-   **valor de texto sem formatação** - octetos de texto sem formatação, codificado usando Base64URL  
-   **valor de assinatura** - a saída de um algoritmo de assinatura, codificado usando Base64URL  
-   **base64URL** - um Base64URL [RFC4648] codificado valor binário  
-   **boolean** - seja true ou false  
-   **Identity** - uma identidade do Azure Active Directory (AAD).  
-   **IntDate** - um valor decimal de JSON que representa o número de segundos desde 1970-01-01T0:0:0Z UTC até a data/hora de UTC especificada. Consulte RFC3339 para obter detalhes sobre data/hora em geral e o UTC em particular.  

## <a name="objects-identifiers-and-versioning"></a>Objetos, identificadores e controle de versão

Objetos armazenados no Key Vault são submetidos ao controle de versão sempre que uma nova instância de um objeto é criada. Cada versão é atribuída a um identificador exclusivo e a uma URL. Quando um objeto é criado pela primeira vez, ele recebe um identificador de versão exclusivo e é marcado como a versão atual do objeto. A criação de uma nova instância com o mesmo nome de objeto fornece ao novo objeto um identificador de versão exclusivo, fazendo com que ele se torne a versão atual.  

Objetos no Key Vault podem ser endereçados usando o identificador atual ou um identificador específico da versão. Por exemplo, dada uma chave com o nome `MasterKey`, executar operações com o identificador atual faz com que o sistema use a versão mais recente disponível. Executar operações com o identificador de versão específico faz com que o sistema use essa versão específica do objeto.  

Os objetos são identificados de forma exclusiva no Key Vault usando uma URL. Não há dois objetos no sistema com a mesma URL, independentemente da localização geográfica. A URL completa para um objeto é chamada de Identificador do Objeto. A URL consiste em um prefixo que identifica o Key Vault, o tipo de objeto, o Nome do Objeto fornecido pelo usuário e uma Versão do Objeto. O nome do objeto é imutável e diferencia maiusculas de minúsculas. Identificadores que não incluem a versão do objeto são chamados de Identificadores de Base.  

Para mais informações, consulte [Autenticação, solicitações e respostas](authentication-requests-and-responses.md)

Um identificador de objeto tem o seguinte formato geral:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Em que:  

|||  
|-|-|  
|`keyvault-name`|O nome de um cofre de chaves no serviço do Microsoft Azure Key Vault.<br /><br /> Nomes de Cofre de Chaves são selecionados pelo usuário e são globalmente exclusivos.<br /><br /> O nome do Key Vault deve ser uma sequência de 3 a 24 caracteres que contenha somente 0 a 9, a a z, A a Z, e -.|  
|`object-type`|O tipo do objeto, "chaves", "segredos" ou "certificados".|  
|`object-name`|Um `object-name` é um nome fornecido por usuário e deve ser exclusivo em um Cofre de Chaves. O nome deve ser uma sequência de 1 a 127 caracteres que contenha somente 0 a 9, a a z, A a Z, e -.|  
|`object-version`|Um `object-version` é um identificador de cadeia de caracteres de 32 caracteres gerado pelo sistema, que é opcionalmente usado para direcionar uma versão exclusiva de um objeto.|  

## <a name="next-steps"></a>Próximas etapas

- [Sobre as chaves](../keys/about-keys.md)
- [Sobre segredos](../secrets/about-secrets.md)
- [Sobre certificados](../certificates/about-certificates.md)
- [Autenticação, solicitações e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Desenvolvedor do Cofre de Chaves](../general/developers-guide.md)