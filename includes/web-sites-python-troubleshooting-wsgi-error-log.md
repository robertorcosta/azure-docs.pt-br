---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "67172526"
---
Se o Python encontrar um erro ao iniciar o aplicativo, apenas uma página de erro simples será retornada (por exemplo, "a página não pode ser exibida porque ocorreu um erro interno do servidor.").

Para capturar erros de aplicativo Python:

1. No Portal do Azure, no seu aplicativo Web, selecione **Configurações**.
2. Na guia **Configurações**, selecione **Configurações do aplicativo**.
3. Em **Configurações do aplicativo**, digite o seguinte par de chave/valor:
    * Chave: WSGI_LOG
    * Valor: D:\home\site\wwwroot\logs.txt (Insira o nome de arquivo da sua escolha)

Agora você verá erros no arquivo logs.txt na pasta wwwroot.
