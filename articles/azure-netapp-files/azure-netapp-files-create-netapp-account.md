---
title: Criar uma conta do NetApp para acesso do Azure NetApp Files | Microsoft Docs
description: Descreve como acessar Azure NetApp Files e criar uma conta do NetApp para que você pode configurar um pool de capacidade e criar um volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 25cae58663f6fa7ef27995c10509eb33e49dd4c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70012580"
---
# <a name="create-a-netapp-account"></a>Criar uma conta do NetApp
Criar uma conta do NetApp permite que você configure um pool de capacidade e, subsequentemente, crie um volume. Você pode usar a folha de Azure NetApp Files para criar uma nova conta do NetApp.

## <a name="before-you-begin"></a>Antes de começar
Você deve ter recebido um e-mail da equipe de Arquivos do Azure NetApp confirmando que foi concedido acesso ao serviço. Consulte [Enviar uma solicitação de lista de espera para acessar o serviço](azure-netapp-files-register.md#waitlist).

Você também deve ter registrado sua assinatura para usar o Provedor de Recursos netapp. Consulte [Registrar o Provedor de Recursos netapp](azure-netapp-files-register.md#resource-provider).

## <a name="steps"></a>Etapas 

1. Entre no portal do Azure. 
2. Acesse a folha Azure NetApp Files usando um dos seguintes métodos:  
   * Procure os **Azure NetApp Files** na caixa de pesquisa do portal do Azure.  
   * Clique em **Todos os serviços** na navegação e, em seguida, filtre para o Azure NetApp Files.  

   Você pode "favoritar" a folha Azure NetApp Files clicando no ícone de estrela ao lado dele. 

3. Clique em **+ Adicionar** para criar uma nova conta do NetApp.  
   A janela Nova conta do NetApp é exibida.  

4. Forneça as seguintes informações para sua conta do NetApp: 
   * **Nome da Conta**  
     Especifique um nome exclusivo para a assinatura.
   * **Assinatura**  
     Selecione uma assinatura de suas assinaturas existentes.
   * **Grupo de recursos**   
     Use um Grupo de Recursos existente ou crie um novo.
   * **Local**  
     Selecione a região onde deseja que a conta e seus recursos filho a ser localizado.  

     ![Nova conta do NetApp](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Clique em **Criar**.     
   A conta do NetApp que você criou agora aparece na folha Azure NetApp Files. 

> [!NOTE] 
> Se você não tiver acesso ao serviço Azure NetApp Files, você receberá o seguinte erro ao tentar criar a primeira conta do NetApp:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`

## <a name="next-steps"></a>Próximas etapas  

[Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)

