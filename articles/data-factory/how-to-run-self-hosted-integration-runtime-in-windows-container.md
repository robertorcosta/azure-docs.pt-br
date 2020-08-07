---
title: Como executar Integration Runtime auto-hospedados no contêiner do Windows
description: Saiba mais sobre como executar Integration Runtime auto-hospedados no contêiner do Windows.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: d6f292ff89a70de90e6b86f19f73de26963d997f
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927544"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Como executar Integration Runtime auto-hospedados no contêiner do Windows

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explicará como executar Integration Runtime auto-hospedados no contêiner do Windows.
Azure Data Factory estão fornecendo o suporte ao contêiner do Windows oficial de Integration Runtime hospedados internamente. Você pode baixar o código-fonte de Build do Docker e combinar o processo de criação e execução em seu próprio pipeline de entrega contínua. 

## <a name="prerequisites"></a>Pré-requisitos 
- [Requisitos do contêiner do Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker versão 2,3 e posterior 
- Auto-hospedado Integration Runtime versão 4.11.7512.1 e posterior 
## <a name="get-started"></a>Introdução 
1.  Instalar o Docker e habilitar o contêiner do Windows 
2.  Baixar o código-fonte em https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container
3.  Baixe a versão mais recente do SHIR na pasta ' SHIR ' 
4.  Abra a pasta no Shell: 
```console
cd "yourFolderPath"
```

5.  Crie a imagem do Docker do Windows: 
```console
docker build . -t "yourDockerImageName" 
```
6.  Executar contêiner do Docker: 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> AUTH_KEY é obrigatório para este comando. NODE_NAME, ENABLE_HA e HA_PORT são opcionais. Se você não definir o valor, o comando usará valores padrão. O valor padrão de ENABLE_HA é false e HA_PORT é 8060.

## <a name="container-health-check"></a>Verificação de integridade do contêiner 
Após um período de inicialização de 120 segundos, o verificador de integridade será executado periodicamente a cada 30 segundos. Ele fornecerá o status de integridade de IR para o mecanismo de contêiner. 

## <a name="limitations"></a>Limitações
No momento, não há suporte para os recursos abaixo ao executar o Integration Runtime auto-hospedado no contêiner do Windows:
- Proxy HTTP 
- Comunicação de nó de nó criptografado com certificado TLS/SSL 
- Gerar e importar backup 
- Serviço daemon 
- Atualização automática 

### <a name="next-steps"></a>Próximas etapas
- Revise os [conceitos de runtime de integração no Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime).
- Saiba como [criar um runtime de integração auto-hospedada no portal do Azure](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).


