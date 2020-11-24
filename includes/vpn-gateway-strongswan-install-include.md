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
ms.openlocfilehash: f02fa49b62a2e3d617617a20518810209d3879b7
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556781"
---
A configuração a seguir foi usada para as etapas a seguir:

- Computador: Ubuntu Server 18, 4
- Dependências: strongSwan


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

[Instruções adicionais sobre como instalar o CLI do Azure](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)