---
title: Registrar-se no Azure NetApp Files | Microsoft Docs
description: Descreve como se registrar para usar arquivos do Azure NetApp.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274054"
---
# <a name="register-for-azure-netapp-files"></a>Registro no Azure NetApp Files

> [!IMPORTANT] 
> Antes de registrar o provedor de recursos Azure NetApp Files, você deve ter recebido um e-mail da equipe de Arquivos do Azure NetApp confirmando que foi concedido acesso ao serviço. 

Neste artigo, saiba como se cadastrar no Azure NetApp Files para que você possa começar a usar o serviço.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Envie uma solicitação de lista de espera para acessar o serviço

1. Envie uma solicitação de lista de espera para acessar o serviço Azure NetApp Files através da página de envio de [lista de espera do Azure NetApp Files](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u). 

    A inscrição na lista de espera não garante acesso imediato ao serviço. 

2. Aguarde um e-mail oficial de confirmação da equipe de Arquivos do Azure NetApp antes de continuar com outras tarefas. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>Registrar o Provedor de recursos do NetApp

Para usar o serviço, é necessário registrar o Provedor de recursos do Azure no Azure NetApp Files.

> [!NOTE] 
> Você poderá registrar com sucesso o Provedor de Recursos netapp mesmo sem ter acesso ao serviço. No entanto, sem autorização de acesso, qualquer portal ou aPI do Azure para criar uma conta netapp ou qualquer outro recurso do Azure NetApp Files será rejeitado com o seguinte erro:  
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

    Se você não ver `Microsoft.NetApp/ANFGA`o nome do recurso, você não terá acesso ao serviço. Pare nesse passo. Siga as instruções em [Enviar uma solicitação de lista de espera para acessar o serviço](#waitlist) para solicitar acesso ao serviço antes de continuar. 

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