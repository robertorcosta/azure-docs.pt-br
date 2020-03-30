---
title: Configure protocolos de API - Azure Event Grid IoT Edge | Microsoft Docs
description: Configure protocolos de API expostos pela Event Grid no IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 908bc941ee7379de067621e10adf5fd6ee6df559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841803"
---
# <a name="configure-event-grid-api-protocols"></a>Configure protocolos de API da Grade de Eventos

Este guia dá exemplos das possíveis configurações de protocolo de um módulo Event Grid. O módulo Event Grid expõe a API por sua gestão e operações em tempo de execução. A tabela a seguir captura os protocolos e portas.

| Protocolo | Porta | Descrição |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Desligado por padrão. Útil apenas durante os testes. Não é adequado para cargas de trabalho de produção.
| HTTPS | 4438 | Padrão

Consulte o guia [de segurança e autenticação](security-authentication.md) para todas as configurações possíveis.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>Exponha HTTPS a módulos IoT na mesma rede de borda

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>Habilite https para outros módulos de IoT e cargas de trabalho não IoT

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> A seção **PortBindings** permite mapear portas internas para portas do host de contêineres. Esse recurso permite alcançar o módulo Event Grid de fora da rede de contêineres IoT Edge, se o dispositivo de borda IoT for acessível publicamente.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>Exponha os módulos HTTP e HTTPS para IoT na mesma rede de borda

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>Habilite HTTP e HTTPS para outros módulos de IoT e cargas de trabalho não IoT

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> Por padrão, cada módulo IoT faz parte do tempo de execução do IoT Edge criado pela rede de ponte. Ele permite que diferentes módulos de IoT na mesma rede se comuniquem entre si. **PortBindings** permite mapear uma porta interna de contêiner na máquina host, permitindo assim que qualquer pessoa possa acessar a porta do módulo Event Grid do lado de fora.

>[!IMPORTANT]
> Embora as portas possam ser acessíveis fora da rede IoT Edge, a autenticação do cliente impõe quem é realmente autorizado a fazer chamadas para o módulo.
