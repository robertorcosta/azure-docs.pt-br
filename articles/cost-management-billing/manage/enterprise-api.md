---
title: APIs Enterprise de cobrança do Azure | Microsoft Docs
description: Saiba mais sobre as APIs de Relatório que permitem a clientes Enterprise do Azure efetuar pull dos dados de consumo de modo programático.
author: mumami
tags: billing
ms.service: cost-management-billing
ms.topic: reference
ms.date: 02/14/2020
ms.author: banders
ms.openlocfilehash: 9f9552c134fa7653e4100cae3c731e4c670d44e3
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560534"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Visão geral das APIs de Relatórios para clientes Enterprise
As APIs de Relatórios permitem que clientes Enterprise do Azure efetuem pull de modo programático dos dados de consumo e cobrança nas ferramentas preferidas de análise de dados. Os clientes Enterprise assinaram um [EA (Enterprise Agreement)](https://azure.microsoft.com/pricing/enterprise-agreement/) com o Azure para fazer compromissos financeiros negociados e obter acesso a preços personalizados para recursos do Azure.

Todos os parâmetros de data e hora necessários para as APIs devem ser representados como valores combinados de UTC (Tempo Universal Coordenado). Os valores retornados por APIs são mostrados em formato UTC.

## <a name="enabling-data-access-to-the-api"></a>Habilitando o acesso a dados para a API
* **Gerar ou recuperar a chave de API** - Faça logon no portal empresarial e navegue até Relatórios > Baixar uso > Chave de acesso de API para gerar ou recuperar a chave de API.
* **Passando chaves na API**: a chave de API precisa ser passada para cada chamada de Autenticação e Autorização. A propriedade a seguir precisa ser para os cabeçalhos HTTP

|Chave de cabeçalho da solicitação | Valor|
|-|-|
|Autorização| Especifique o valor neste formato: **portador {API_KEY}** <br/> Exemplo: portador e... 09|

## <a name="consumption-apis"></a>APIs de consumo
Um ponto de extremidade Swagger está disponível [aqui](https://consumption.azure.com/swagger/ui/index) para as APIs descritas abaixo, que devem permitir a fácil introspecção da API e a capacidade de gerar SDKs de cliente usando [AutoRest](https://github.com/Azure/AutoRest) ou [Swagger CodeGen](https://swagger.io/swagger-codegen/). Os dados a partir de 1º de maio de 2014 estão disponíveis por essa API.

* **Saldo e Resumo**: a [API Saldo e Resumo](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) oferece um resumo mensal de informações sobre saldos, novas compras, encargos de serviço do Azure Marketplace, ajustes e encargos de excedente.

* **Detalhes de Uso**: a [API Detalhes de Uso](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) oferece um detalhamento diário das quantidades consumidas e de encargos estimados por um registro. O resultado também inclui informações sobre instâncias, medidores e departamentos. A API pode ser consultada por período de cobrança ou por uma data de início e de término especificada.

* **Encargo de Armazenamento do Marketplace**: a [API Encargo de Armazenamento do Marketplace](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) retorna o detalhamento dos encargos do marketplace com base no uso por dia para o Período de Cobrança especificado ou as datas de início e término (taxas avulsas não estão incluídas).

* **Tabela de Preços**: a [API Tabela de Preços](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) fornece a taxa aplicável de cada Medidor para o Registro e o Período de Cobrança determinados.

* **Detalhes da Instância Reservada** – a [API de Uso da Instância Reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) retorna o uso das compras da instância reservada. A [API de encargos da instância reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) mostra as transações de cobrança feitas.

## <a name="data-freshness"></a>Atualização dos Dados
ETags serão retornadas na resposta da API acima. Uma alteração no Etag indica que os dados foram atualizados.  Nas chamadas subsequentes para a mesma API usando os mesmos parâmetros, passe o Etag capturado com a chave "If-None-Match" no cabeçalho da solicitação http. O código de status de resposta seria "NotModified" se os dados não foram mais atualizados e nenhum dado será retornado. A API retornará o conjunto de dados completo para o período necessário sempre que houver uma alteração de etag.

## <a name="helper-apis"></a>APIs auxiliares
 **Listar Períodos de Cobrança**: a [API Períodos de Cobrança](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) retorna uma lista de períodos de cobrança que têm dados de consumo para o Registro especificado em ordem cronológica inversa. Cada período contém uma propriedade que aponta para a rota da API dos quatro conjuntos de dados: BalanceSummary, UsageDetails, Encargos do Marketplace e Price Sheet.


## <a name="api-response-codes"></a>Códigos de resposta da API   
|Código de status de resposta|Mensagem|Descrição|
|-|-|-|
|200| OK|Nenhum erro|
|401| Não Autorizado| Chave de API não encontrada, inválida, expirada, etc.|
|404| Indisponível| Ponto de extremidade de relatório não encontrado|
|400| Solicitação incorreta| Parâmetros inválidos – intervalos de datas, números de EA, etc.|
|500| Erro de servidor| Erro inesperado ao processar a solicitação|
