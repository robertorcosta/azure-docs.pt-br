---
title: Como migrar para contêineres de OCR de Leitura v3.x
titleSuffix: Azure Cognitive Services
description: Saiba como migrar para os contêineres de OCR de Leitura v3
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 09/21/2020
ms.author: aahi
ms.openlocfilehash: 714a4709eceea875798940de962716d34437f2a9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91530452"
---
# <a name="migrate-to-the-read-v3x-ocr-containers"></a>Migrar para contêineres de OCR de Leitura v3.x

Caso esteja usando a versão 2 do contêiner de OCR de Leitura da Pesquisa Visual Computacional, use este artigo para saber mais sobre como atualizar seu aplicativo para usar a versão 3.x do contêiner. 


## <a name="configuration-changes"></a>Alterações de configuração

* `ReadEngineConfig:ResultExpirationPeriod` não é mais suportada. O contêiner de Leitura tem um trabalho Cron integrado que remove resultados e metadados associados a uma solicitação após 48 horas.
* `Cache:Redis:Configuration` não é mais suportada. O Cache não é usado nos contêineres v3.x, portanto, não será necessário defini-lo.

## <a name="api-changes"></a>Alterações de API

Os contêineres da Leitura v3.x usam a versão 3 da API da Pesquisa Visual Computacional e têm os seguintes pontos de extremidade:

#### <a name="version-31-preview"></a>[Versão 3.1 – versão prévia](#tab/version-3-1)

* `/vision/v3.1-preview.2/read/analyzeResults/{operationId}`
* `/vision/v3.1-preview.2/read/analyze`
* `/vision/v3.1-preview.2/read/syncAnalyze`

#### <a name="version-30-preview"></a>[Versão prévia 3.0](#tab/version-3)

* `/vision/v3.0/read/analyzeResults/{operationId}`
* `/vision/v3.0/read/analyze`
* `/vision/v3.0/read/syncAnalyze`

---

Confira o [guia de migração da API REST da Pesquisa Visual Computacional v3](https://docs.microsoft.com/azure/cognitive-services/computer-vision/upgrade-api-versions) para obter informações detalhadas sobre como atualizar seus aplicativos para usar a versão 3 da API de Leitura baseada em nuvem. Essas informações também se aplicarão ao contêiner. Observe que as operações de sincronização são compatíveis somente com contêineres.

## <a name="memory-requirements"></a>Requisitos de memória

Os requisitos e as recomendações são baseados em parâmetros de comparação com uma solicitação por segundo, usando uma imagem de 8 MB de uma carta empresarial digitalizada que contém 29 linhas e 803 caracteres no total. A tabela a seguir descreverá a alocação mínima e recomendada de recursos para cada contêiner de Leitura.

|Contêiner  |Mínimo | Recomendadas  |
|---------|---------|------|
|Leitura 3.0 – versão prévia     | 8 núcleos, 16 GB de memória         | 8 núcleos, 24 GB de memória
|Leitura 3.1 – versão prévia | 8 núcleos, 16 GB de memória         | 8 núcleos, 24 GB de memória

Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.

O núcleo e a memória correspondem às configurações `--cpus` e `--memory`, que são usadas como parte da opção Executar Comando do Docker.

## <a name="storage-implementations"></a>Implementações de armazenamento

>[!NOTE]
> O MongoDB não é mais compatível com as versões 3.x do contêiner. Em vez disso, os contêineres são compatíveis com o Armazenamento do Azure e com sistemas de arquivos offline.

| Implementação |  Argumento(s) de runtime necessário(s) |
|---------|---------|
|Nível de arquivo (padrão)   | Nenhum argumento de runtime é necessário. O diretório `/share` será usado. |
|Blob do Azure | `Storage:ObjectStore:AzureBlob:ConnectionString={AzureStorageConnectionString}` |

## <a name="queue-implementations"></a>Implementações de fila

Na v3.x do contêiner, o RabbitMQ não é compatível no momento. As implementações de apoio compatíveis são:

| Implementação | Argumento(s) de Runtime | Uso pretendido |
|---------|---------|-------|
| Na memória (padrão) | Nenhum argumento de runtime é necessário. | Desenvolvimento e teste |
| Filas do Azure | `Queue:Azure:ConnectionString={AzureStorageConnectionString}` | Produção |
| RabbitMQ  | Indisponível | Produção |

Para obter uma redundância adicionada, o contêiner de Leitura v3.x usa um temporizador de visibilidade para garantir que as solicitações possam ser processadas com êxito em caso de falha, durante a execução em uma configuração de vários contêineres. 

Defina o temporizador com `Queue:Azure:QueueVisibilityTimeoutInMilliseconds`, que definirá o tempo para uma mensagem ficar invisível quando outro trabalho a estiver processando. Para evitar que as páginas sejam processadas de modo redundante, recomendamos definir o período de tempo limite como 120 segundos. O valor padrão é 30 segundos.

| Valor padrão | Valor recomendado |
|---------|---------|
| 30000 |   120000 |


## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](computer-vision-resource-container-config.md) para configurações
* Examinar [Visão geral da Pesquisa Visual Computacional](overview.md) para saber mais sobre como reconhecer texto impresso e manuscrito
* Veja a [API da Pesquisa Visual Computacional](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) para obter detalhes sobre os métodos compatíveis com o contêiner.
* Veja as [Perguntas frequentes](FAQ.md) para resolver problemas relacionados à funcionalidade de Pesquisa Visual Computacional.
* Use mais [Contêineres de Serviços Cognitivos](../cognitive-services-container-support.md)
