---
title: Registrar-se no Azure NetApp Files | Microsoft Docs
description: Descreve como registrar-se para usar o Azure NetApp Files.
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
ms.date: 05/06/2019
ms.author: b-juche
ms.openlocfilehash: 6f5d84dea2e835fd12a062b628181354295ed9f6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274054"
---
# <a name="register-for-azure-netapp-files"></a>Registro no Azure NetApp Files

> [!IMPORTANT] 
> Antes de registrar o provedor de recursos de Azure NetApp Files, você deve ter recebido um email da equipe de Azure NetApp Files confirmando que recebeu acesso ao serviço. 

Neste artigo, saiba como registrar-se para Azure NetApp Files para que você possa começar a usar o serviço.

## <a name="waitlist"></a>Enviar uma solicitação Waitlist para acessar o serviço

1. Envie uma solicitação Waitlist para acessar o serviço de Azure NetApp Files por meio da [página de envio Azure NetApp files Waitlist](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u). 

    A inscrição de Waitlist não garante o acesso imediato ao serviço. 

2. Aguarde um email de confirmação oficial da equipe de Azure NetApp Files antes de continuar com outras tarefas. 

## <a name="resource-provider"></a>Registrar o provedor de recursos do NetApp

Para usar o serviço, é necessário registrar o Provedor de recursos do Azure no Azure NetApp Files.

> [!NOTE] 
> Você poderá registrar o provedor de recursos da NetApp com êxito, mesmo sem ter acesso ao serviço. No entanto, sem autorização de acesso, qualquer portal do Azure ou solicitação de API para criar uma conta do NetApp ou qualquer outro recurso de Azure NetApp Files será rejeitada com o seguinte erro:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. No portal do Azure, clique no ícone do Azure Cloud Shell no canto superior direito:

      ![ícone do Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Se você tiver várias assinaturas em sua conta do Azure, selecione a que foi incluída na lista de permissões para o Azure NetApp Files:
    
        az account set --subscription <subscriptionId>

3. No console do Azure Cloud Shell, insira o seguinte comando para verificar se sua assinatura foi incluída na lista de permissões:
    
        az feature list | grep NetApp

   A saída do comando é exibida da seguinte maneira:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
       "name": "Microsoft.NetApp/ANFGA" 
       
   `<SubID>` é a ID da assinatura.

    Se você não vir o nome do recurso `Microsoft.NetApp/ANFGA`, você não terá acesso ao serviço. Pare nesta etapa. Siga as instruções em [Enviar uma solicitação Waitlist para acessar o serviço](#waitlist) para solicitar acesso ao serviço antes de continuar. 

4. No console do Azure Cloud Shell, insira o seguinte comando para registrar o Provedor de recursos do Azure: 
    
        az provider register --namespace Microsoft.NetApp --wait

   O parâmetro `--wait` instrui o console a aguardar a conclusão do registro. O processo de registro pode levar algum tempo para ser concluído.

5. No console do Azure Cloud Shell, insira o seguinte comando para verificar se o Provedor de recursos do Azure foi registrado: 
    
        az provider show --namespace Microsoft.NetApp

   A saída do comando é exibida da seguinte maneira:
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>` é a ID da assinatura.  O valor do parâmetro `state` indica `Registered`.

6. No portal do Azure, clique na folha **Assinaturas**.
7. Na folha Assinaturas, clique na ID de assinatura. 
8. Nas configurações da assinatura, clique em **Provedores de recursos** para verificar se o Provedor do Microsoft.NetApp indica o status Registrado: 

      ![Microsoft.NetApp registrado](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Próximas etapas

[Criar uma conta do NetApp](azure-netapp-files-create-netapp-account.md)