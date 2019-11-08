---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e3b3d944508a4261b78def0b3bee13f7395a8bf0
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749009"
---
Para exibir e copiar as chaves de acesso ou a cadeia de conexão da conta de armazenamento do portal do Azure:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Localize sua cadeia de conexão.
3. Na seção **Configurações** da visão geral da conta de armazenamento, selecione **Chaves de acesso**. Suas chaves de acesso da conta são exibidas, bem como a cadeia de conexão completa para cada chave.
4. Encontre o valor da **Chave** em **key1** e clique no botão **Copiar** para copiar a chave de conta.
5. Como alternativa, você pode copiar a cadeia de conexão inteira. Encontre o valor da **Cadeia de conexão** em **key1** e clique no botão **Copiar** para copiar a cadeia de conexão.

    ![Captura de tela mostrando como exibir chaves de acesso no portal do Azure](media/storage-view-keys-include/portal-connection-string.png)

Você pode usar qualquer chave para acessar o armazenamento do Azure, mas, em geral, é uma boa prática usar a primeira chave e reservar o uso da segunda chave para quando você estiver girando as chaves.
