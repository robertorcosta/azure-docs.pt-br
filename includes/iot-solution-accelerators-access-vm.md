---
title: incluir arquivo
description: incluir arquivo
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a58e408feadd10e6dbc9d6878b82a4d045918ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68781424"
---
## <a name="access-the-virtual-machine"></a>Acessar a máquina virtual

As etapas seguintes usam o Azure CLI no Azure Cloud Shell. Se preferir, você pode [instalar o Azure CLI](/cli/azure/install-azure-cli) em sua máquina de desenvolvimento e executar os comandos localmente.

As etapas a seguir mostram como configurar a máquina virtual do Azure para permitir o acesso de **SSH**. As etapas mostradas levam em conta que o nome escolhido para o acelerador de solução é **contoso-simulation** -- substitua esse valor pelo nome da sua implantação:

1. Liste o conteúdo do grupo de recursos que contém os recursos do acelerador de solução:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Anote o nome da máquina virtual, o endereço IP público e o grupo de segurança de rede - você precisará desses valores mais tarde.

1. Atualize o grupo de segurança de rede para permitir o acesso de SSH. O comando a seguir pressupõe que o nome do grupo de segurança de rede seja **contoso-simulation-nsg** - substitua esse valor pelo nome do seu grupo de segurança de rede:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Habilite somente o acesso de SSH durante o desenvolvimento e teste. Se você ativar o SSH, [você deve desabilitá-lo novamente o mais rápido possível](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices#disable-rdpssh-access-to-virtual-machines).

1. Atualize a senha para a conta **azureuser** na máquina virtual para uma senha de seu conhecimento. Escolha sua própria senha quando executar o comando a seguir:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Localize o endereço IP público de sua máquina virtual. O comando a seguir pressupõe que o nome da máquina virtual seja **vikxv vm** -- substitua esse valor pelo nome da máquina virtual que você anotou anteriormente:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Anote endereço IP público de sua máquina virtual.
