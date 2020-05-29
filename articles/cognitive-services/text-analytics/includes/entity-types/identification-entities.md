---
title: Entidades de identificação
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: f07b71bf8996612798b87d32a21a15ec72db0b32
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140899"
---
Essa categoria de entidade inclui informações financeiras e formulários oficiais de identificação. Disponível a partir da versão do modelo `2019-10-01` . Os subtipos são listados abaixo. 

### <a name="financial-account-identification"></a>Identificação da conta financeira

| Nome do subtipo               | Descrição                                                                |
|----------------------------|----------------------------------------------------------------------------|
| Número do banco ABA        | Números de roteamento de trânsito da ABA (Associação Americana do Banker).                  |
| Código SWIFT                 | Códigos SWIFT para informações de instrução de pagamento.                           |
| Cartão de crédito                | Números de cartão de crédito.                                                       |
| IBAN (Número de Conta Bancária Internacional)                  | Códigos de IBAN para informações de instrução de pagamento.                            |


### <a name="government-and-countryregion-specific-identification"></a>Identificação específica do governo e do país/região

> [!NOTE]
> As seguintes entidades financeiras e específicas do país não são retornadas com o `domain=phi` parâmetro:
> * Números de passaporte
> * IDs de imposto

As entidades abaixo são agrupadas e listadas por país:

Argentina
* Número de identidade nacional da Argentina (DNI)

Austrália
* Número do passaporte da Austrália
* Número do arquivo de imposto da Austrália
* Número da licença do driver da Austrália
* Número da conta médica da Austrália
* Número da conta bancária da Austrália

Bélgica
* Número nacional da Bélgica

Brasil 
* CNPJ (número da entidade legal) do Brasil
* Número do CPF do Brasil
* Cartão de ID nacional do Brasil (RG)

Canadá
* Número de seguro social do Canadá
* Número da licença do driver do Canadá
* Número da conta bancária do Canadá
* Número do passaporte do Canadá
* Número de identificação de integridade pessoal do Canadá (PHIN)
* Número de Serviço de Integridade do Canadá

Chile
* Número do cartão de identidade 

China
* Número PRC (cartão de identidade residente) da China

Croácia
* Número do cartão de identidade da Croácia
* Número da OIB (identificação pessoal da Croácia)

República Tcheca
* Número de identidade pessoal tcheco

Dinamarca
* Número de identificação pessoal da Dinamarca

União Europeia (UE)
* Número de Identificação Nacional da UE
* Número de Passaporte da UE
* Número de Carteira de Habilitação da UE
* Número do Seguro Social (SSN) ou ID Equivalente da UE
* Número de Identificação Tributária da UE (TIN)
* Número do cartão de débito da UE

Finlândia
* ID nacional da Finlândia
* Número do passaporte da Finlândia

França
* Placa de ID nacional da França (CNI)
* INSEE (número de seguro social da França)
* Número do Passport da França
* Número da licença do driver da França

Alemanha
* Número do cartão de identidade da Alemanha
* Número do Passport alemão
* Número da licença do driver alemão

Grécia 
* Número do cartão da ID nacional da Grécia

RAE de Hong Kong
* Número de HKID (placa de identidade de Hong Kong)

Índia
* Número de conta permanente da Índia (PAN)
* Número da Aadhaar (identificação exclusiva da Índia)

Indonésia
* Número do KTP (cartão de identidade da Indonésia)

Irlanda
* Número do PPS (serviço público pessoal) da Irlanda

Israel
* ID nacional de Israel
* Número da conta bancária de Israel

Itália
* ID da licença do driver da Itália

Japão
* Número de registro residente do Japão
* Número de Cartão de Residência Japonês
* Número da licença do driver do Japão
* Número de seguro social (SIN)
* Número do passaporte do Japão
* Número da conta bancária do Japão

Malásia
* Número do cartão de identidade da Malásia

Países Baixos
* Número do serviço do cidadão (BSN) dos Países Baixos

Nova Zelândia
* Ministério da Nova Zelândia do número de saúde

Noruega
* Número de identidade Noruega

Filipinas
* Filipinas número de ID de multipropósito unificado

Polônia
* Cartão de identidade da Polônia
* ID nacional da Polônia (PESEL)
* Passport da Polônia

Portugal 
* Número do cartão do cidadão de Portugal

Arábia Saudita
* ID nacional da Arábia Saudita

Singapura
* Número do cartão de ID de Registro Nacional de Cingapura (NRIC)

África do Sul
* Número de identificação da África do Sul

Coreia do Sul
* Número de registro residente da Coreia do Sul

Espanha 
* Número do seguro social da Espanha (SSN)

Suécia
* ID nacional da Suécia
* Número do passaporte da Suécia

Taiwan 
* ID nacional de Taiwan
* Certificado residente em Taiwan (ARC/TARC)
* Número de passaporte de Taiwan

Tailândia
* Código de Identificação de População Tailandês

Reino Unido
* ID do passaporte
* Inglaterra Número da licença do driver
* Inglaterra Número de seguro nacional (NINO)
* Inglaterra Número de Serviço de Integridade nacional
* Inglaterra Número de reversão do eleitoral
* EUA/REINO UNIDO Número do passaporte

Estados Unidos
* Número do seguro social (SSN) dos EUA
* Número da licença do driver dos EUA
* EUA/REINO UNIDO Número do passaporte
* Número de identificação de contribuinte (certa) dos EUA
* Número da Agência de aplicação de drogas (DEA)
* Número da conta bancária dos EUA
