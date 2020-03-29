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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77086912"
---
## <a name="personal-information-entity-types"></a>Tipos de entidades de informações pessoais:

### <a name="person"></a>Person
Reconheça os nomes das pessoas no texto.

Linguagens:
* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                               | Disponível a partir da versão do modelo |
|--------------|-----------------------------------------------------------|----------------------------------------|
| N/D          | Nomes reconhecidos, `Bill Gates`por exemplo,`Marie Curie` | `2020-02-01`                           |

### <a name="organization"></a>Organização  

Reconhecer organizações, corporações, agências, empresas, clubes e outros grupos de pessoas.

Linguagens: 

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                                                                       | Disponível a partir da versão do modelo|
|--------------|---------------------------------------------------------------------------------------------------|--------------|
| N/D          | organizações, `Microsoft`por `NASA`exemplo,`National Oceanic and Atmospheric Administration` | `2020-02-01` |

### <a name="phone-number"></a>Número do telefone

Números de telefone (apenas números de telefone dos EUA). 

Linguagens:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                    | Disponível a partir da versão do modelo |
|--------------|------------------------------------------------|----------------------------------------|
| N/D          | Números de telefone dos EUA, por exemplo`(312) 555-0176` | `2020-02-01`                           |

### <a name="email"></a>Email

Endereço de email. 

Linguagens:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                      | Disponível a partir da versão do modelo |
|--------------|--------------------------------------------------|----------------------------------------|
| N/D          | Endereço de e-mail, por exemplo`support@contoso.com` | `2020-02-01`                           |

### <a name="url"></a>URL

URLs da Internet.

Linguagens:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                          | Disponível a partir da versão do modelo |
|--------------|------------------------------------------------------|----------------------------------------|
| N/D          | URLs para sites, por exemplo`https://www.bing.com` | `2020-02-01`                           |

### <a name="ip-address"></a>Endereço IP

Endereço do Protocolo da Internet

Linguagens:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                              | Disponível a partir da versão do modelo |
|--------------|------------------------------------------|----------------------------------------|
| N/D          | Endereço de rede, por exemplo,`10.0.0.101` | `2020-02-01`                           |

### <a name="quantity"></a>Quantidade 

Quantidades numéricas

Linguagens:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                   | Disponível a partir da versão do modelo |
|--------------|-------------------------------|----------------------------------------|
| Idade          | `90 days old`, `30 years old` | `2020-02-01`                           |

### <a name="datetime"></a>Datetime

Entidades de Data e Hora

Linguagens:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                   | Disponível a partir da versão do modelo |
|--------------|-------------------------------|----------------------------------------|
| Data         | `May 2nd, 2017`, `05/02/2017` | `2020-02-01`                           |

### <a name="eu-gps-coordinates"></a>Coordenadas de GPS da UE

 Coordenadas GPS para locais dentro da União Europeia. 

Linguagens:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                               | Disponível a partir da versão do modelo |
|--------------|-------------------------------------------|----------------------------------------|
| N/D          | Coordenadas gps dentro da União Europeia | `2019-10-01`                           |

### <a name="azure-information"></a>Informações do Azure

Informações identificáveis do Azure, incluindo informações de autenticação e strings de conexão. 

* Disponível a `2019-10-01`partir da versão do modelo .

Linguagens:

* Pré-visualização pública:`English`

| Nome do subtipo                          | Descrição                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Chave de autenticação DocumentDB do Azure             | Chave de autorização para um servidor Azure DocumentDB.                           |
| String de conexão de banco de dados Azure IAAS | String de conexão para um banco de dados Azure Infrastructure as a service (IaaS). |
| String de conexão SQL do Azure           | String de conexão para um banco de dados SQL do Azure.                                |
| Cadeia de conexão do IoT do Azure           | String de conexão para Internet das coisas Azure (IoT).                        |
| Senha das configurações de publicação do Azure        | Senha para configurações do Azure Publish.                                        |
| Cadeia de conexão do Cache Redis do Azure   | String de conexão para um Cache Azure para Redis.                             |
| Azure SAS                             | String de conexão para Azure Software as a Service(SAS).                     |
| Cadeia de conexão do Barramento de Serviço do Azure   | String de conexão para um ônibus de serviço Azure.                                |
| Chave da conta de armazenamento do Azure             | A chave da conta para uma conta de armazenamento do Azure.                                   |
| Chave da conta de armazenamento do Azure (genérica)   | Chave de conta genérica para uma conta de armazenamento Do Zure.                           |
| Cadeia de conexão do SQL Server          | String de conexão para um servidor SQL.                                         |

### <a name="identification"></a>Identificação

* Disponível a `2019-10-01`partir da versão do modelo .

Linguagens:

* Pré-visualização pública:`English`

#### <a name="financial-account-identification"></a>Identificação de Contas Financeiras

| Nome do subtipo               | Descrição                                                                |
|----------------------------|----------------------------------------------------------------------------|
| Números de roteamento ABA        | Números de roteamento de trânsito da American Banker Association (ABA).                  |
| Código SWIFT                 | Códigos SWIFT para informações de instrução de pagamento.                           |
| Cartão de crédito                | Números de cartão de crédito.                                                       |
| Código IBAN                  | Códigos IBAN para informações de instrução de pagamento.                            |

#### <a name="government-and-country-specific-identification"></a>Identificação específica do governo e do país

As entidades abaixo são agrupadas e listadas por país:

Argentina
* Número de Identidade Nacional (DNI)

Austrália
* Número do arquivo fiscal 
* Carteira de Motorista
* ID do passaporte
* Número da conta médica
* números de contas bancárias (por exemplo, contas de cheques, poupanças e débito)

Bélgica
* Número nacional

Brasil
* Número de Pessoa Jurídica (CNPJ)
* Número do CPF
* Carteira de Identidade Nacional (RG)

Canada
* ID do passaporte
* Carteira de Motorista
* Número do seguro de saúde
* Número de ID de Saúde Pessoal (PHIN)
* Cadastro de Pessoa Física
* números de contas bancárias (por exemplo, contas de cheques, poupanças e débito)

Chile
* Número da carteira de identidade 

China
* Número da carteira de identidade
* Número do cartão de identificação residente (RPC)

Croácia
* Número do cartão de identificação
* Número de ID Pessoal (OIB)

República Tcheca
* Número da carteira de identidade nacional

Dinamarca
* Número de ID pessoal

União Europeia (UE)
* Número de ID Nacional
* ID do passaporte
* Carteira de Motorista
* Número da Previdência Social (SSN) ou ID equivalente
* Número de Identificação Tributária da UE (TIN)
* Número do cartão de débito da UE

Finlândia
* Número de ID Nacional
* ID do passaporte

França
* Carteira Nacional de Identificação (CNI)
* Número da Previdência Social (INSEE)
* ID do passaporte
* Carteira de Motorista

Alemanha
* Número do cartão de identificação
* ID do passaporte
* Carteira de Motorista

Grécia 
* Número da carteira de identidade nacional

RAE de Hong Kong
* Número do cartão de identificação (HKID)

Índia
* Número da Conta Permanente (PAN)
* Número de Identificação Única (Aadhaar)

Indonésia
* Número do cartão de identificação (KTP)

Irlanda
* Número do Serviço Público Pessoal (PPS)

Israel
* ID Nacional
* números de contas bancárias (por exemplo, contas de cheques, poupanças e débito)

Itália
* Carteira de Motorista

Japão
* Número de registro de residente
* Número do cartão de residência
* Carteira de Motorista
* Número do Seguro Social (SIN)
* ID do passaporte
* números de contas bancárias (por exemplo, contas de cheques, poupanças e débito)

Malásia
* Número do cartão de identificação

Países Baixos
* Número do Serviço de Atendimento ao Cidadão (BSN)

Nova Zelândia
* Número do Ministério da Saúde

Noruega
* Número do cartão de identificação

Filipinas
* Número de ID Multiuso Unificado

Polônia
* Número do cartão de identificação
* ID Nacional (PESEL)
* ID do passaporte

Portugal 
* Número do Cartão Cidadão

Arábia Saudita
* ID Nacional

Singapura
* Número da Carteira Nacional de Identificação de Registro (NRIC)

África do Sul
* Número de ID
* Número de registro de residente

Coreia do Sul
* Número de Registro de Residente

Espanha 
* CPF

Suécia
* ID Nacional
* ID do passaporte

Taiwan 
* ID Nacional
* Número do Certificado de Residente (ARC/TARC)
* ID do passaporte

Tailândia
* Código de Identificação Populacional

United Kingdom
* ID do passaporte
* Carteira de Motorista
* Número do Seguro Nacional (NINO)
* Número do Serviço Nacional de Saúde (NHS)

Estados Unidos
* CPF
* Carteira de Motorista
* ID do passaporte
* Número do rolo eleitoral
* Número de ID Fiscal Individual (ITIN)
* Número da Agência antidrogas (DEA)
* números de contas bancárias (por exemplo, contas de cheques, poupanças e débito)
