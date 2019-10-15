---
title: Download e notas de versão do emulador do Azure Cosmos
description: Leia as notas de versão do emulador do Azure Cosmos e baixe-o.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: 03163d75681bee7434e2b7576a678b1094d47b94
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973707"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Emulador do Azure Cosmos – Notas sobre a versão e informações de download

Este artigo mostra as notas de versão do emulador do Azure Cosmos com uma lista de atualizações de recursos que foram feitas em cada versão. Também lista a versão mais recente do emulador para baixar e usar.

## <a name="download"></a>Baixar

| | |
|---------|---------|
|**Download do MSI**|[Centro de Download da Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Introdução**|[Desenvolver localmente com o emulador do Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Notas de versão

### <a name="246"></a>2.4.6

- Esta versão fornece paridade com os recursos do serviço Azure Cosmos a partir de julho de 2019, com as exceções indicadas em [Desenvolvimento local com o emulador do Azure Cosmos](local-emulator.md). Ela também corrige vários bugs relacionados ao desligamento do emulador quando invocada pela linha de comando e substituições de endereço IP interno para clientes do SDK que usam a conectividade de modo direto.

### <a name="243"></a>2.4.3

- Inicialização do serviço MongoDB desabilitada por padrão. Somente o ponto de extremidade do SQL está habilitado por padrão. O usuário deve iniciar o ponto de extremidade manualmente usando a opção de linha de comando /EnableMongoDbEndpoint" do emulador. Agora, é como todos os outros pontos de extremidade, como Gremlin, Cassandra e tabela.
- Corrigido um bug no emulador ao iniciar com /AllowNetworkAccess", em que os pontos de extremidade de Gremlin, Cassandra e tabela não processavam corretamente as solicitações de clientes externos.
- Adicione portas de conexão direta às configurações de regras de firewall.

### <a name="240"></a>2.4.0

- Corrigido um problema de falha do emulador ao iniciar quando aplicativos de monitoramento de rede, como cliente do pulso, estão presentes no computador host.
