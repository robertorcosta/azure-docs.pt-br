---
title: Entidades nomeadas para informações pessoais
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: d678a29de9dea8a5a2f6d0259a452ca4c69feb03
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "77086912"
---
## <a name="personal-information-entity-types"></a>Tipos de entidade de informações pessoais:

### <a name="person"></a>Person
Reconhece nomes de pessoas no texto.

Linguagens:
* Visualização pública:`English`

| Nome do subtipo | Descrição                                               | Disponível a partir da versão do modelo |
|--------------|-----------------------------------------------------------|----------------------------------------|
| N/D          | Nomes reconhecidos, `Bill Gates`por exemplo,`Marie Curie` | `2020-02-01`                           |

### <a name="organization"></a>Organização  

Reconheça organizações, corporações, agências, empresas, clubes e outros grupos de pessoas.

Linguagens: 

* Visualização pública:`English`

| Nome do subtipo | Descrição                                                                                       | Disponível a partir da versão do modelo|
|--------------|---------------------------------------------------------------------------------------------------|--------------|
| N/D          | organizações, por exemplo `Microsoft` `NASA`,,`National Oceanic and Atmospheric Administration` | `2020-02-01` |

### <a name="phone-number"></a>Número do telefone

Números de telefone (somente números de telefone dos EUA). 

Linguagens:

* Visualização pública:`English`

| Nome do subtipo | Descrição                                    | Disponível a partir da versão do modelo |
|--------------|------------------------------------------------|----------------------------------------|
| N/D          | Números de telefone dos EUA, por exemplo`(312) 555-0176` | `2020-02-01`                           |

### <a name="email"></a>Email

Endereço de email. 

Linguagens:

* Visualização pública:`English`

| Nome do subtipo | Descrição                                      | Disponível a partir da versão do modelo |
|--------------|--------------------------------------------------|----------------------------------------|
| N/D          | Endereço de email, por exemplo`support@contoso.com` | `2020-02-01`                           |

### <a name="url"></a>URL

URLs da Internet.

Linguagens:

* Visualização pública:`English`

| Nome do subtipo | Descrição                                          | Disponível a partir da versão do modelo |
|--------------|------------------------------------------------------|----------------------------------------|
| N/D          | URLs para sites, por exemplo`https://www.bing.com` | `2020-02-01`                           |

### <a name="ip-address"></a>Endereço IP

Endereço de protocolo de Internet

Linguagens:

* Visualização pública:`English`

| Nome do subtipo | Descrição                              | Disponível a partir da versão do modelo |
|--------------|------------------------------------------|----------------------------------------|
| N/D          | Endereço de rede, por exemplo`10.0.0.101` | `2020-02-01`                           |

### <a name="quantity"></a>Quantidade 

Quantidades numéricas

Linguagens:

* Visualização pública:`English`

| Nome do subtipo | Descrição                   | Disponível a partir da versão do modelo |
|--------------|-------------------------------|----------------------------------------|
| Idade          | `90 days old`, `30 years old` | `2020-02-01`                           |

### <a name="datetime"></a>Datetime

Entidades de data e hora

Linguagens:

* Visualização pública:`English`

| Nome do subtipo | Descrição                   | Disponível a partir da versão do modelo |
|--------------|-------------------------------|----------------------------------------|
| Data         | `May 2nd, 2017`, `05/02/2017` | `2020-02-01`                           |

### <a name="eu-gps-coordinates"></a>Coordenadas de GPS da UE

 Coordenadas de GPS para locais na União Europeia. 

Linguagens:

* Visualização pública:`English`

| Nome do subtipo | Descrição                               | Disponível a partir da versão do modelo |
|--------------|-------------------------------------------|----------------------------------------|
| N/D          | Coordenadas de GPS na União Europeia | `2019-10-01`                           |

### <a name="azure-information"></a>Informações do Azure

Informações identificáveis do Azure, incluindo informações de autenticação e cadeias de conexão. 

* Disponível a partir da versão `2019-10-01`do modelo.

Linguagens:

* Visualização pública:`English`

| Nome do subtipo                          | Descrição                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Chave de autenticação DocumentDB do Azure             | Chave de autorização para um servidor do Azure DocumentDB.                           |
| Cadeia de conexão do banco de dados IAAS do Azure | Cadeia de conexão para um banco de dados IaaS (infraestrutura como serviço) do Azure. |
| Cadeia de conexão SQL do Azure           | Cadeia de conexão para um banco de dados SQL do Azure.                                |
| Cadeia de conexão do IoT do Azure           | Cadeia de conexão para a IoT (Internet das coisas) do Azure.                        |
| Senha das configurações de publicação do Azure        | Senha para as configurações de publicação do Azure.                                        |
| Cadeia de conexão do Cache Redis do Azure   | Cadeia de conexão para um cache do Azure para Redis.                             |
| Azure SAS                             | Cadeia de conexão para o software como um serviço (SAS) do Azure.                     |
| Cadeia de conexão do Barramento de Serviço do Azure   | Cadeia de conexão para um barramento de serviço do Azure.                                |
| Chave de conta de armazenamento do Azure             | Chave de conta para uma conta de armazenamento do Azure.                                   |
| Chave da conta de armazenamento do Azure (genérica)   | Chave de conta genérica para uma conta de armazenamento do Azure.                           |
| Cadeia de conexão do SQL Server          | Cadeia de conexão para um SQL Server.                                         |

### <a name="identification"></a>Identificação

* Disponível a partir da versão `2019-10-01`do modelo.

Linguagens:

* Visualização pública:`English`

#### <a name="financial-account-identification"></a>Identificação da conta financeira

| Nome do subtipo               | Descrição                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABAr números de roteamento        | Números de roteamento de trânsito da ABA (Associação Americana do Banker).                  |
| Código SWIFT                 | Códigos SWIFT para informações de instrução de pagamento.                           |
| Cartão de crédito                | Números de cartão de crédito.                                                       |
| Código do IBAN                  | Códigos de IBAN para informações de instrução de pagamento.                            |

#### <a name="government-and-country-specific-identification"></a>Identificação específica do governo e do país

As entidades abaixo são agrupadas e listadas por país:

Argentina
* Número de identidade nacional (DNI)

Austrália
* Número do arquivo de imposto 
* ID da licença do driver
* ID do passaporte
* Número da conta médica
* números de conta bancária (por exemplo, contas de verificação, poupança e débito)

Bélgica
* Número nacional

Brasil
* Número da entidade legal (CNPJ)
* Número do CPF
* Cartão de ID nacional (RG)

Canada
* ID do passaporte
* ID da licença do driver
* Número de seguro de integridade
* Número de ID de integridade pessoal (PHIN)
* Cadastro de Pessoa Física
* números de conta bancária (por exemplo, contas de verificação, poupança e débito)

Chile
* Número do cartão de identidade 

China
* Número do cartão de identidade
* Número de placa de ID residente (PRC)

Croácia
* Número do cartão de ID
* Número da ID pessoal (OIB)

República Tcheca
* Número do cartão da ID nacional

Dinamarca
* Número da ID pessoal

União Europeia (UE)
* Número de ID nacional
* ID do passaporte
* ID da licença do driver
* Número do seguro social (SSN) ou ID equivalente
* Número de Identificação Tributária da UE (TIN)
* Número do cartão de débito da UE

Finlândia
* Número de ID nacional
* ID do passaporte

França
* Placa de ID nacional (CNI)
* Número do seguro social (INSEE)
* ID do passaporte
* ID da licença do driver

Alemanha
* Número do cartão de ID
* ID do passaporte
* ID da licença do driver

Grécia 
* Número do cartão da ID nacional

RAE de Hong Kong
* Número do cartão de ID (HKID)

Índia
* Número da conta permanente (PAN)
* Número de ID exclusiva (Aadhaar)

Indonésia
* Número do cartão de ID (KTP)

Irlanda
* Número do PPS (serviço público pessoal)

Israel
* ID nacional
* números de conta bancária (por exemplo, contas de verificação, poupança e débito)

Itália
* ID da licença do driver

Japão
* Número de registro residente
* Número da placa de residência
* ID da licença do driver
* Número de seguro social (SIN)
* ID do passaporte
* números de conta bancária (por exemplo, contas de verificação, poupança e débito)

Malásia
* Número do cartão de ID

Países Baixos
* Número de serviço do cidadão (BSN)

Nova Zelândia
* Ministério do número de integridade

Noruega
* Número do cartão de ID

Filipinas
* Número de ID de multipropósito unificado

Polônia
* Número do cartão de ID
* ID nacional (PESEL)
* ID do passaporte

Portugal 
* Número do cartão do cidadão

Arábia Saudita
* ID nacional

Singapura
* Número do NRIC (ID de Registro Nacional)

África do Sul
* Número da ID
* Número de registro residente

Coreia do Sul
* Número de registro residente

Espanha 
* CPF

Suécia
* ID nacional
* ID do passaporte

Taiwan 
* ID nacional
* Número do certificado residente (arco/TARC)
* ID do passaporte

Tailândia
* Código de identificação da população

United Kingdom
* ID do passaporte
* ID da licença do driver
* Número de seguro nacional (NINO)
* Número de Serviço de Integridade nacional (NHS)

Estados Unidos
* CPF
* ID da licença do driver
* ID do passaporte
* Número de reversão do eleitoral
* Número de ID de imposto individual (certa)
* Número da Agência de aplicação de drogas (DEA)
* números de conta bancária (por exemplo, contas de verificação, poupança e débito)
