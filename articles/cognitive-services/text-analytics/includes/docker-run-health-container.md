---
title: Exemplo de execução de contêiner do comando de execução do Docker
titleSuffix: Azure Cognitive Services
description: Comando de execução do Docker para o contêiner de integridade
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 72a877ab8cfda126016376017c0a7eb42b1186d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86108883"
---
Para executar o contêiner, primeiro localize sua ID de imagem:
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

Execute o comando a seguir `docker run` . Substitua os espaços reservados abaixo pelos seus próprios valores:

| Espaço reservado | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave para o recurso de Análise de Texto. Você pode encontrá-lo na página de **ponto de extremidade e chave** do recurso, na portal do Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | O ponto de extremidade para acessar o API de Análise de Texto. Você pode encontrá-lo na página de **ponto de extremidade e chave** do recurso, na portal do Azure. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | A ID da imagem do seu contêiner. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | O diretório de entrada para o contêiner. | Windows: `C:\healthcareMount` <br> Linux/MacOS: `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

Esse comando:

- Supõe que o diretório de entrada exista no computador host
- Executa um Análise de Texto para o contêiner de integridade da imagem de contêiner
- Aloca 6 núcleos de CPU e 12 gigabytes (GB) de memória
- Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner
- Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.
