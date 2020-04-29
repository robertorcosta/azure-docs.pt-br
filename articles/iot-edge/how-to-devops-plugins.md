---
title: Habilitar o CI/CD com plug-in do Jenkins – Azure IoT Edge | Microsoft Docs
description: A extensão Azure IoT Edge para Jenkins permite que você integre IoT Edge tarefas de desenvolvimento e implantação à sua solução DevOps existente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 75ed5f5eda48f10776b854ce4de4d2f855a53634
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76510236"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Integrar o Azure IoT Edge com os pipelines do Jenkins

O Azure IoT Edge tem suporte interno para Azure DevOps e Azure DevOps Projects, mas também fornece um plug-in para expandir a funcionalidade do DevOps para o Jenkins. O [Jenkins](https://jenkins.io/) é um servidor de automação de software livre que usa plug-ins para dar suporte a vários tipos de projetos de desenvolvimento e implantação, incluindo o IoT Edge.

O plug-in do Azure IoT Edge para Jenkins se concentra na integração e na implantação contínuas. Você pode criar um pipeline de build e um pipeline de push que compila módulos e efetua push de suas imagens de contêiner para o registro de contêiner. Em seguida, crie um pipeline de lançamento que implanta os módulos em seus dispositivos IoT Edge.

Antes de começar a usar o plug-in do IoT Edge para Jenkins, você precisará de um hub IoT no Azure e de um registro de contêiner para manter as imagens de contêiner. Use uma Entidade de Serviço do Azure para conceder permissões de Colaborador do Jenkins para o hub IoT para que o plug-in possa criar implantações para os dispositivos IoT Edge.

Se você estiver pronto para começar, encontre a instalação e os detalhes de uso do [Plug-in do Azure IoT Edge para Jenkins](https://plugins.jenkins.io/azure-iot-edge).
