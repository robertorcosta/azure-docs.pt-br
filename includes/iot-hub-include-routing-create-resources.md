---
title: Arquivo de inclusão
description: Arquivo de inclusão
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: c97c8231265cf87f52333a56d21d6fb13180c554
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808865"
---
## <a name="create-base-resources"></a>Criar recursos de base

Antes de configurar o roteamento de mensagens, você precisa criar um hub IoT, uma conta de armazenamento e uma fila do Barramento de Serviço. Esses recursos podem ser criados usando um dos quatro artigos disponíveis para a Parte 1 deste tutorial: o portal do Azure, um modelo do Azure Resource Manager, a CLI do Azure ou o Azure PowerShell.

Use o mesmo grupo de recursos e o local para todos os recursos. Então, no final, você pode remover todos os recursos em uma única etapa excluindo o grupo de recursos.

Abaixo há um resumo das etapas a serem seguidas nestas seções: 

1. Crie um [grupo de recursos](../articles/azure-resource-manager/resource-group-overview.md).

2. Criar um Hub IoT na camada S1. Adicionar um grupo de consumidores ao Hub IoT. O grupo de consumidores é usado pelo Azure Stream Analytics ao recuperar dados.

   > [!NOTE]
   > Você precisa usar um hub IoT em uma faixa paga para concluir este tutorial. A faixa gratuita só permite que você configure um ponto de extremidade e este tutorial requer vários pontos de extremidade.
   > 

3. Crie uma conta de armazenamento padrão V1 com replicação Standard_LRS.

4. Criar um namespace do Barramento de Serviço e da fila.

5. Crie uma identidade de dispositivo para o dispositivo simulado que envia mensagens para o hub. Salve a chave para a fase de teste. (Se estiver criando um modelo do Resource Manager, isso será feito depois de implantar o modelo.)