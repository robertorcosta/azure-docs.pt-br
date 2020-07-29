---
title: Gerenciar instantâneos por meio do Azure NetApp Files | Microsoft Docs
description: Descreve como criar e gerenciar instantâneos usando o Azure NetApp Files.
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
ms.topic: how-to
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: caa73b5a86c5c245aefd18de9b60ec49616b3b84
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281541"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Gerenciar instantâneos por meio do Azure NetApp Files

Azure NetApp Files dá suporte à criação de instantâneos sob demanda e ao uso de políticas de instantâneo para agendar a criação automática de instantâneos.  Você também pode restaurar um instantâneo para um novo volume.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Criar um instantâneo sob demanda para um volume

Você pode criar instantâneos de volume sob demanda. 

1.  Vá para o volume para o qual você deseja criar um instantâneo. Clique em **instantâneos**.

    ![Navegar para instantâneos](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Clique em **+ Adicionar instantâneo** para criar um instantâneo sob demanda para um volume.

    ![Adicionar instantâneo](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Na janela Novo Instantâneo, forneça um nome para o novo instantâneo que você está criando.   

    ![Novo instantâneo](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Clique em **OK**. 

## <a name="manage-snapshot-policies"></a>Gerenciar políticas de instantâneo

Você pode agendar para que os instantâneos de volume sejam feitos automaticamente usando políticas de instantâneo. Você também pode modificar uma política de instantâneo, conforme necessário, ou excluir uma política de instantâneo que não é mais necessária.  

> [!IMPORTANT] 
> O uso da funcionalidade de política de instantâneo requer a lista de permissões. Envie um email para anffeedback@microsoft.com com sua ID de assinatura para solicitar esse recurso.

### <a name="create-a-snapshot-policy"></a>Criar uma política de instantâneo 

Uma política de instantâneo permite que você especifique a frequência de criação de instantâneos em ciclos por hora, diários, semanais ou mensais. Você também precisa especificar o número máximo de instantâneos a serem retidos para o volume.  

1.  No modo de exibição de conta do NetApp, clique em **política de instantâneo**.

    ![Navegação de política de instantâneo](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  Na janela política de instantâneo, defina estado da política como **habilitado**. 

3.  Clique na guia por **hora**, **dia**, **semana**ou **mês** para criar políticas de instantâneo por hora, diárias, semanais ou mensais. Especifique o **número de instantâneos a serem mantidos**.  

    Consulte [limites de recursos para Azure NetApp files](azure-netapp-files-resource-limits.md) sobre o número máximo de instantâneos permitidos para um volume. 

    O exemplo a seguir mostra a configuração de política de instantâneo por hora. 

    ![Política de instantâneo por hora](../media/azure-netapp-files/snapshot-policy-hourly.png)

    O exemplo a seguir mostra a configuração de política de instantâneo diária.

    ![Política de instantâneo diária](../media/azure-netapp-files/snapshot-policy-daily.png)

    O exemplo a seguir mostra a configuração de política de instantâneo semanal.

    ![Política de instantâneo semanal](../media/azure-netapp-files/snapshot-policy-weekly.png)

    O exemplo a seguir mostra a configuração de política de instantâneo mensal.

    ![Política de instantâneo mensal](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  Clique em **Salvar**.  

Se você precisar criar políticas de instantâneo adicionais, repita a etapa 3.
As políticas que você criou aparecem na página política de instantâneo.

Se desejar que um volume use a política de instantâneo, você precisará [aplicar a política ao volume](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume). 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>Aplicar uma política de instantâneo a um volume

Se desejar que um volume use uma política de instantâneo que você criou, você precisará aplicar a política ao volume. 

1.  Vá para a página **volumes** , clique com o botão direito do mouse no volume ao qual você deseja aplicar uma política de instantâneo e selecione **Editar**.

    ![Menu com o botão direito em volumes](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  Na janela Editar, em **política de instantâneo**, selecione uma política a ser usada para o volume.  Clique em **OK** para aplicar a política.  

    ![Edição de política de instantâneo](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>Modificar uma política de instantâneo 

Você pode modificar uma política de instantâneo existente para alterar o estado da política, a frequência do instantâneo (por hora, diariamente, semanalmente ou mensalmente) ou o número de instantâneos a serem mantidos.  
 
1.  No modo de exibição de conta do NetApp, clique em **política de instantâneo**.

2.  Clique com o botão direito do mouse na política de instantâneo que você deseja modificar e selecione **Editar**.

    ![Política de instantâneo-menu do clique com o botão direito](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Faça as alterações na janela política de instantâneo exibida e clique em **salvar**. 

### <a name="delete-a-snapshot-policy"></a>Excluir uma política de instantâneo 

Você pode excluir uma política de instantâneo que não deseja mais manter.   

1.  No modo de exibição de conta do NetApp, clique em **política de instantâneo**.

2.  Clique com o botão direito do mouse na política de instantâneo que você deseja modificar e selecione **excluir**.

    ![Política de instantâneo-menu do clique com o botão direito](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Clique em **Sim** para confirmar que deseja excluir a política de instantâneo.   

    ![Confirmação de exclusão de política de instantâneo](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Restaurar um instantâneo para um novo volume

No momento, você pode restaurar um instantâneo somente para um novo volume. 
1. Selecione **instantâneos** na folha volume para exibir a lista de instantâneos. 
2. Clique com o botão direito do mouse no instantâneo a ser restaurado e selecione **restaurar para novo volume** na opção de menu.  

    ![Restaurar instantâneo para o novo volume](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. Na janela criar um volume, forneça informações para o novo volume:  
    * **Nomes**   
        Especifique o nome para o volume que você está criando.  
        
        O nome precisa ser exclusivo em um grupo de recursos. Ele precisa ter, pelo menos, três caracteres.  Deve usar qualquer caractere alfanumérico.

    * **Cota**  
        Especifique a quantidade de armazenamento lógico que você deseja alocar para o volume.  

    ![Restaurar para novo volume](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Clique em **revisar + criar**.  Clique em **Criar**.   
    O novo volume usa o mesmo protocolo usado pelo instantâneo.   
    O novo volume para o qual o instantâneo é restaurado é exibido na folha Volumes.

## <a name="next-steps"></a>Próximas etapas

* [Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Limites de recursos do Azure NetApp Files](azure-netapp-files-resource-limits.md)
