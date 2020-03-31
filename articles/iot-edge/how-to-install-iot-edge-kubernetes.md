---
title: Como instalar o IoT Edge no Kubernetes | Microsoft Docs
description: Saiba como instalar o IoT Edge no Kubernetes usando um ambiente de cluster de desenvolvimento local
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b2068c3944f9e7616b0666c7bafcafc68ee0cd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471278"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Como instalar o IoT Edge no Kubernetes (Visualização)

O IoT Edge pode se integrar com o Kubernetes usando-o como uma camada de infra-estrutura resiliente e altamente disponível. Aqui é onde este suporte se encaixa em uma solução de Alto Nível IoT Edge:

![introdução k8s](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Um bom modelo mental para essa integração é pensar no Kubernetes como outro ambiente operacional que os aplicativos IoT Edge podem ser executados além do Linux e Windows.

## <a name="architecture"></a>Arquitetura 
No Kubernetes, o IoT Edge fornece *CRD (Custom Resource Definition, definição de recursos* personalizados) para implantações de carga de trabalho de borda. O IoT Edge Agent assume o papel de um controlador de *CRD* que concilia o estado desejado gerenciado pela nuvem com o estado local do cluster.

A duração do módulo é gerenciada pelo agendador Kubernetes, que mantém a disponibilidade do módulo e escolhe sua colocação. O IoT Edge gerencia a plataforma de aplicação de borda em execução no topo, conciliando continuamente o estado desejado especificado no IoT Hub com o estado no cluster de borda. O modelo de aplicação ainda é o modelo familiar baseado em módulos e rotas IoT Edge. O controlador ioT Edge Agent executa o modelo de aplicação do IoT Edge de tradução *automática* para os construtos nativos do Kubernetes, como pods, implantações, serviços etc.

Aqui está um diagrama de arquitetura de alto nível:

![kubernetes arco](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Cada componente da implantação de borda é escopo de um namespace kubernetes específico para o dispositivo, tornando possível compartilhar os mesmos recursos de cluster entre vários dispositivos de borda e suas implantações.

>[!NOTE]
>IoT Edge on Kubernetes está em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="tutorials-and-references"></a>Tutoriais e referências 

Consulte o [mini-site ioT Edge on Kubernetes para](https://aka.ms/edgek8sdoc) obter mais informações, incluindo tutoriais e referências em profundidade.
