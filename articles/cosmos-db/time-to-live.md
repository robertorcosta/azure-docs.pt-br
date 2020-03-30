---
title: Expirar dados no Azure Cosmos DB com Vida Útil
description: Com a TTL, o Microsoft Azure Cosmos DB fornece a capacidade de limpar documentos automaticamente do sistema após determinado período.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.reviewer: sngun
ms.openlocfilehash: 5407c38f33d167ff5114cd55878e3470e7248d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188711"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Vida útil no Azure Cosmos DB 

Com **o Time to Live** ou o TTL, o Azure Cosmos DB oferece a capacidade de excluir itens automaticamente de um contêiner após um determinado período de tempo. Por padrão, é possível definir a Vida Útil no nível do contêiner e substituir o valor em uma base por item. Após definir a Vida Útil em um nível de item ou contêiner, o Azure Cosmos DB removerá automaticamente esses itens após o período de tempo, desde a hora em que foram modificados pela última vez. O valor de Vida Útil é configurado em segundos. Ao configurar o TTL, o sistema excluirá automaticamente os itens expirados com base no valor TTL, sem precisar de uma operação de exclusão que seja explicitamente emitida pelo aplicativo cliente.

A exclusão de itens expirados é uma tarefa de fundo que consome unidades de [solicitação](request-units.md)restantes, ou seja, Unidades de solicitação que não foram consumidas por solicitações do usuário. Mesmo depois que o TTL expirar, se o contêiner estiver sobrecarregado com solicitações e se não houver RU suficiente disponível, a exclusão de dados está atrasada. Os dados são excluídos uma vez que há RUs suficientes disponíveis para executar a operação de exclusão. Embora a exclusão de dados esteja atrasada, os dados não são retornados por quaisquer consultas (por qualquer API) após o término do TTL.

## <a name="time-to-live-for-containers-and-items"></a>Vida Útil para contêineres e itens

O valor de vida é definido em segundos, e é interpretado como um delta a partir do momento em que um item foi modificado pela última vez. É possível definir a Vida Útil em um contêiner ou um item dentro do contêiner:

1. **Vida Útil em um contêiner** (definir usando `DefaultTimeToLive`):

   - Se ausentes (ou definidos como nulo), os itens não serão expirados automaticamente.

   - Se presente e o valor for definido como "-1", ele é igual ao infinito, e os itens não expiram por padrão.

   - Se presente e o valor for definido como número *"n"* – os itens expirarão *"n"* segundos após o último tempo modificado.

2. **Vida Útil em um item** (definir usando `ttl`):

   - Essa Propriedade será aplicável somente se `DefaultTimeToLive` estiver presente e não estiver definido como nulo para o contêiner pai.

   - Se presente, substituirá o valor `DefaultTimeToLive` do contêiner pai.

## <a name="time-to-live-configurations"></a>Configurações de Vida Útil

* Se o TTL estiver definido como *"n"* em um recipiente, os itens desse recipiente expirarão após *n segundos.*  Se houver itens no mesmo contêiner que tenham seu próprio tempo de vida, definido como -1 (indicando que eles não expiram) ou se alguns itens tiverem substituído o tempo de configuração ao vivo com um número diferente, esses itens expiram com base no seu próprio valor TTL configurado. 

* Se a TTL não estiver definida em um contêiner, o tempo de vida de um item nesse contêiner não terá efeito. 

* Se a TTL em um contêiner for definida como -1, um item nesse contêiner que tenha a vida útil definida como n expirará após n segundos, e os itens restantes não expirarão.

## <a name="examples"></a>Exemplos

Esta seção mostra alguns exemplos com diferentes tempos para viver valores atribuídos a contêineres e itens:

### <a name="example-1"></a>Exemplo 1

TTL no contêiner está definido como nulo (DefaultTimeToLive = nulo)

|TTL no item| Result|
|---|---|
|ttl = nulo|    TTL está desativado. O item nunca expirará (padrão).|
|ttl = -1   |TTL está desativado. O item nunca expirará.|
|ttl = 2000 |TTL está desativado. O item nunca expirará.|


### <a name="example-2"></a>Exemplo 2

TTL no contêiner está definido como -1 (DefaultTimeToLive = -1)

|TTL no item| Result|
|---|---|
|ttl = nulo |O TTL está habilitado. O item nunca expirará (padrão).|
|ttl = -1   |O TTL está habilitado. O item nunca expirará.|
|ttl = 2000 |O TTL está habilitado. O item expirará após 2000 segundos.|


### <a name="example-3"></a>Exemplo 3

TTL no contêiner é definido como 1000 (DefaultTimeToLive = 1000)

|TTL no item| Result|
|---|---|
|ttl = nulo|    O TTL está habilitado. O item expirará após 1000 segundos (padrão).|
|ttl = -1   |O TTL está habilitado. O item nunca expirará.|
|ttl = 2000 |O TTL está habilitado. O item expirará após 2000 segundos.|

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar o Tempo de Viver nos seguintes artigos:

* [Como configurar a Vida Útil](how-to-time-to-live.md)
