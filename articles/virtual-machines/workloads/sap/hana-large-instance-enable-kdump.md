---
title: Script para habilitar kdump em SAP HANA (instâncias grandes) | Microsoft Docs
description: Script para habilitar kdump no tipo de SAP HANA (instâncias grandes) do HLI Type I, HLI tipo II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16dc15b4369904643d0138a4b8e5b94c47868d31
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204930"
---
# <a name="enable-kdump-service"></a>Habilitar serviço kdump

Este documento descreve os detalhes sobre como habilitar o serviço kdump no Azure HANA em instâncias grandes (**tipo I e tipo II**)

## <a name="supported-skus"></a>SKUs com suporte

|  Tipo de instância grande do Hana   |  Fornecedor do sistema operacional   |  Versão do pacote do so   |  SKU        |
|-----------------------------|--------------|-----------------------|-------------|
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Tipo II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Tipo II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Tipo II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Tipo II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |

## <a name="prerequisites"></a>Pré-requisitos

- O serviço kdump `/var/crash` usa o diretório para gravar despejos, verifique se a partição correspondente a esse diretório tem espaço suficiente para acomodar despejos.

## <a name="setup-details"></a>Detalhes de configuração

- O script para habilitar kdump pode ser encontrado [aqui](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh)

- Execute este script no SAP HANA em instâncias grandes usando o comando abaixo

    > [!NOTE]
    > privilégio sudo necessário para executar este comando.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Se o comando gerar kdump for habilitado com êxito, reinicialize o sistema para aplicar a alteração, o kdump será habilitado com êxito. Reinicialize o sistema para aplicar as alterações.

- Se a saída do comando falhar ao executar determinada operação, saindo!!!!, o serviço kdump não estiver habilitado. Consulte a seção [problema de suporte](#support-issue).

## <a name="test-kdump"></a>Testar kdump

> [!NOTE]
>  A operação abaixo disparará uma falha de kernel e uma reinicialização do sistema.

- Disparar uma falha de kernel

    ```bash
    echo 1 > /proc/sys/kernel/sysrq
    echo c > /proc/sysrq-trigger
    ```

- Depois que o sistema for reinicializado com êxito, `/var/crash` Verifique o diretório para os logs de falhas do kernel.

- Se o `/var/crash` diretório tiver a data atual, o kdump será habilitado com êxito.

## <a name="support-issue"></a>Problema de suporte

Se o script falhar com um erro ou kdump não estiver habilitado, gere a solicitação de serviço com a equipe de suporte da Microsoft com os seguintes detalhes.

* ID da assinatura do HLI

* Nome do servidor

* Fornecedor do sistema operacional

* Versão do SO

* Versão do kernel
