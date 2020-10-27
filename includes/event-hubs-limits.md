---
title: incluir arquivo
description: incluir arquivo
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ea1ac064799b0cede1de82851a514a2b389f20aa
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92499154"
---
A tabela a seguir fornece cotas e limites específicos para os [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/). Para saber mais sobre os preços dos Hubs de Eventos, veja os [preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="common-limits-for-all-tiers"></a>Limites comuns para todas as camadas
Os limites a seguir são comuns em todas as camadas. 

| Limite |  Observações | Valor |
| --- |  --- | --- |
| Número de namespaces de Hubs de Eventos do Azure por assinatura |- |100 |
| Número de hubs de eventos por namespace | As solicitações subsequentes para a criação de um hub de eventos são rejeitadas. |10 |
| O número de partições por hub de eventos |- |32 |
| Tamanho do nome de um hub de eventos |- | 256 caracteres |
| Tamanho de um nome de grupo de consumidores |- | 256 caracteres |
| Número de destinatários sem época por grupo de consumidores |- |5 |
| Número de regras de autorização por namespace | As solicitações subsequentes de criação de regra de autorização são rejeitadas.|12 |
| Número de chamadas para o método GetRuntimeInformation |  - | 50 por segundo | 
| Número de regras de VNet (rede virtual) e de configuração de IP | - | 128 | 


### <a name="basic-and-standard-tiers"></a>Camadas Básica e Standard
A tabela a seguir mostra os limites que podem ser diferentes para as camadas Basic e Standard. 

| Limite | Observações | Basic | Standard |
| --- |  --- | -- | --- |
| Tamanho máximo de eventos de Hubs de Eventos| &nbsp; | 256 KB | 1 MB |
| Número de grupos de consumidores por hub de eventos | &nbsp; |1 |20 |
| Número de conexões AMQP por namespace | Solicitações subsequentes de conexões adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. |100 |5\.000|
| Período de retenção máximo dos dados do evento | &nbsp; |1 dia |Um a sete dias |
| Unidades de produtividade máxima |Exceder o limite de unidades de produtividade vai causar a limitação dos seus dados e vai gerar uma [exceção de servidor ocupado](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Para solicitar um número maior de unidades de produtividade para um nível Standard, preencha uma [solicitação de suporte](/azure/azure-portal/supportability/how-to-create-azure-support-request). As [unidades de produtividade adicionais](../articles/event-hubs/event-hubs-auto-inflate.md) estão disponíveis em blocos de 20, em uma base de compra garantida. |20 | 20 | 

### <a name="dedicated-tier"></a>Camada dedicada
A oferta de Hubs de Eventos Dedicados é cobrada a um preço mensal fixo, com um mínimo de quatro horas de uso. O nível Dedicado oferece todos os recursos do plano Standard, mas com capacidade em escala empresarial e limites para clientes com cargas de trabalho exigentes. 

Confira este [documento](https://docs.microsoft.com/azure/event-hubs/event-hubs-dedicated-cluster-create-portal) sobre como criar um cluster de Hubs de Eventos dedicados usando o portal do Azure.

| Recurso | limites |
| --- | ---|
| Largura de banda |  20 CUs |
| Namespaces | 50 por CU |
| Hubs de Eventos |  1\.000 por namespace |
| Tamanho da mensagem | 1 MB |
| Partições | 2\.000 por CU |
| Grupos de consumidores | Nenhum limite por CU, 1000 por hub de eventos |
| Conexões orientadas | 100.000 incluídos |
| Tempo de retenção da mensagem | 90 dias, 10 TB incluídos por CU |
| Eventos de entrada | Incluso |
| Capturar | Incluso |


### <a name="schema-registry-limitations"></a>Limitações do registro de esquema

#### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>Os limites são os mesmos para as camadas **standard** e **dedicada** 
| Recurso | Limite | 
| --- |  --- | -- |
| Comprimento máximo de um nome de grupo de esquemas | 50 |  
| Comprimento máximo de um nome de esquema | 100 |    
| Tamanho em bytes por esquema | 1 MB |   
| Número de propriedades por grupo de esquemas | 1024 |
| Tamanho em bytes por chave de propriedade de grupo | 256 | 
| Tamanho em bytes por valor da propriedade de grupo | 1024 | 


#### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>Os limites são os diferentes para as camadas **standard** e **dedicada** 

| Limite | Standard | Dedicado | 
| --- |  --- | -- | --- |
| Tamanho do registro de esquema (namespace) em megabytes | 25 |  1024 |
| Número de grupos de esquema em um registro de esquema (namespace)| 1 (excluindo o padrão) | 1000 |
| Número de versões de esquema em todos os grupos de esquema | 25 | 10000 |





