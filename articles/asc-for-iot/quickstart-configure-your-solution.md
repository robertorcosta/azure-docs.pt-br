---
title: 'Início Rápido: Configurar a solução Central de Segurança do Azure para IoT'
description: Neste início rápido, saiba como configurar sua solução IoT de ponta a ponta usando a Central de Segurança do Azure para IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: e670df359cc33c9eaca089d0ed8f9614ef8c0468
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904148"
---
# <a name="quickstart-configure-your-iot-solution"></a>Início Rápido: Configure a solução de IoT

Este artigo fornece uma explicação de como realizar a configuração inicial de sua solução de segurança da IoT usando a Central de Segurança do Azure para IoT. 

## <a name="azure-security-center-for-iot"></a>Central de Segurança do Azure para IoT

A Central de Segurança do Azure para IoT fornece segurança abrangente de ponta a ponta para soluções de IoT com base no Azure.

Com a Central de Segurança do Azure para IoT, é possível monitorar toda a solução de IoT em um painel, identificando todos os dispositivos IoT, as plataformas IoT e os recursos de back-end no Azure.

Uma vez habilitado em seu Hub IoT, a Central de Segurança do Azure para IoT identifica automaticamente outros serviços do Azure, também conectados ao seu hub IoT e relacionados à solução de IoT.

Além da detecção automática de relacionamento, também é possível selecionar e escolher outros grupos de recursos do Azure para marcar como parte da solução de IoT. 

Suas seleções permitem adicionar assinaturas, grupos de recursos ou recursos únicos inteiros. 

Depois de definir todas as relações de recursos, a Central de Segurança do Azure para IoT aproveita a Central de Segurança do Azure para fornecer recomendações de segurança e alertas para esses recursos.

## <a name="add-azure-resources-to-your-iot-solution"></a>Adicionar recursos do Azure à solução de IoT

Para adicionar novos recursos à solução de IoT, faça o seguinte: 

1. Abra o **Hub IoT** no portal do Azure. 
1. No menu à esquerda, selecione e abra **Recursos** em **Segurança**. 
1. Selecione **Editar** e escolha os grupos de recursos que pertencem à solução de IoT.
1. Clique em **Adicionar**. 

Parabéns! Você adicionou um novo grupo de recursos à solução de IoT.

Como parte da solução de IoT, a Central de Segurança do Azure para IoT agora monitora os recursos recentemente adicionados e identifica recomendações e alertas de segurança relevantes.

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo artigo para aprender como criar módulos de segurança...

> [!div class="nextstepaction"]
> [Criar Módulos de Segurança](quickstart-create-security-twin.md)