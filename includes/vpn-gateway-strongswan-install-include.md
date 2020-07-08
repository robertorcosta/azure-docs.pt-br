---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ddab6927044ce638e50ff3ad79aa3c35d046c820
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "69520811"
---
A configuração a seguir foi usada para as etapas a seguir:

  | | |
  |---|---|
  |Computador| Ubuntu Server 18.04|
  |Dependências| strongSwan |


Use os comandos a seguir para instalar a configuração necessária do strongSwan:

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

Use o seguinte comando para instalar a interface de linha de comando do Azure:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Instruções adicionais sobre como instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
