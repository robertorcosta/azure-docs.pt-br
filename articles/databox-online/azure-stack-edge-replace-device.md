---
title: Substituir seu dispositivo do Azure Stack Edge | Microsoft Docs
description: Descreve como obter um Azure Stack dispositivo de borda de substituição.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: 5d5e8cc3a959c7800019107d2c682e144449b46c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095823"
---
# <a name="replace-your-azure-stack-edge-device"></a>Substituir seu dispositivo do Azure Stack Edge

Este artigo descreve como obter uma substituição de Azure Stack dispositivo de borda. Um dispositivo de substituição é necessário quando o dispositivo existente tem uma falha de hardware ou precisa de uma atualização. 


Neste artigo, você aprenderá como:

> [!div class="checklist"]
>
> * Abrir um tíquete de suporte para o problema de hardware
> * Criar um novo recurso para o dispositivo de substituição no portal do Azure
> * Instalar, ativar o dispositivo de substituição
> * Retornar o dispositivo original

## <a name="open-a-support-ticket"></a>Abrir um tíquete de suporte

Se o dispositivo existente tiver uma falha de hardware, abra um tíquete de suporte. O Suporte da Microsoft determinará se uma FRU (unidade de substituição em campo) não está disponível para esta instância ou se o dispositivo precisa de uma atualização de hardware. Em ambos os casos, o suporte solicitará um dispositivo de substituição.

1. Abra um tíquete de suporte com Suporte da Microsoft indicando que você deseja devolver o dispositivo. Selecione o tipo de problema como **Hardware Azure Stack Edge**.

    ![Abrir tíquete de suporte](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Um engenheiro do Suporte da Microsoft entrará em contato com você. Informe os detalhes de envio.
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>Criar um recurso para o dispositivo de substituição

Siga estas etapas para criar um recurso.

1. Siga as etapas em [criar um novo recurso](azure-stack-edge-deploy-prep.md#create-a-new-resource) para criar um recurso para o dispositivo de substituição. 

2. Marque a caixa de seleção **Eu tenho um dispositivo Azure Stack Edge**. 

    ![Recurso para o dispositivo de substituição](media/azure-stack-edge-replace-device/replace-resource-1.png)  

## <a name="install-and-activate-the-replacement-device"></a>Instalar e ativar o dispositivo de substituição

Siga estas etapas para instalar e ativar o dispositivo de substituição:

1. [Instale seu dispositivo](azure-stack-edge-deploy-install.md).

2. [Ative seu dispositivo](azure-stack-edge-deploy-connect-setup-activate.md) com o novo recurso que você criou anteriormente.

## <a name="return-your-existing-device"></a>Retornar o dispositivo existente

Siga todas as etapas para devolver o dispositivo original:

1. [Apague os dados no dispositivo](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. [Inicie o retorno de dispositivo](azure-stack-edge-return-device.md#initiate-device-return) para o dispositivo original.
3. [Agende uma retirada](azure-stack-edge-return-device.md#schedule-a-pickup).
4. [Exclua o recurso](azure-stack-edge-return-device.md#delete-the-resource) associado ao dispositivo retornado.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [retornar um dispositivo de borda Azure Stack](azure-stack-edge-return-device.md).
