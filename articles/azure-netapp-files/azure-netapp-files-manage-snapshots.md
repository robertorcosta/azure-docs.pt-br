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
ms.date: 09/04/2020
ms.author: b-juche
ms.openlocfilehash: 405d872c178a3172454943b7d40ea276ea5c017e
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89459039"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Gerenciar instantâneos por meio do Azure NetApp Files

Azure NetApp Files dá suporte à criação de instantâneos sob demanda e ao uso de políticas de instantâneo para agendar a criação automática de instantâneos.  Você também pode restaurar um instantâneo para um novo volume ou restaurar um único arquivo usando um cliente.  

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

### <a name="register-the-feature"></a>Registrar o recurso

O recurso de **política de instantâneo** está atualmente em visualização. Se você estiver usando esse recurso pela primeira vez, precisará registrar o recurso primeiro. 

1. Registre o recurso: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. Verifique o status do registro do recurso: 

    > [!NOTE]
    > O **RegistrationState** pode estar no `Registering` estado de até 60 minutos antes da alteração para `Registered` . Aguarde até que o status seja **registrado** antes de continuar.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```
Você também pode usar comandos de CLI do Azure [`az feature register`](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-register) e [`az feature show`](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-show) para registrar o recurso e exibir o status do registro. 

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
    * **Nome**   
        Especifique o nome para o volume que você está criando.  
        
        O nome precisa ser exclusivo em um grupo de recursos. Ele precisa ter, pelo menos, três caracteres.  Deve usar qualquer caractere alfanumérico.

    * **Cota**  
        Especifique a quantidade de armazenamento lógico que você deseja alocar para o volume.  

    ![Restaurar para novo volume](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Clique em **revisar + criar**.  Clique em **Criar**.   
    O novo volume usa o mesmo protocolo usado pelo instantâneo.   
    O novo volume para o qual o instantâneo é restaurado é exibido na folha Volumes.

## <a name="restore-a-file-from-a-snapshot-using-a-client"></a>Restaurar um arquivo de um instantâneo usando um cliente

Se você não quiser [restaurar o instantâneo inteiro para um volume](#restore-a-snapshot-to-a-new-volume), terá a opção de restaurar um arquivo de um instantâneo usando um cliente que tenha o volume montado.  

O volume montado contém um diretório de instantâneos chamado  `.snapshot` (em clientes NFS) ou `~snapshot` (em clientes SMB) que é acessível ao cliente. O diretório de instantâneo contém subdiretórios correspondentes aos instantâneos do volume. Cada subdiretório contém os arquivos do instantâneo. Se você excluir ou substituir um arquivo acidentalmente, poderá restaurá-lo para o diretório de leitura-gravação pai copiando o arquivo de um subdiretório de instantâneo para o diretório de leitura/gravação. 

Se você marcou a caixa de seleção Ocultar caminho do instantâneo quando criou o volume, o diretório do instantâneo ficará oculto. Você pode exibir o status ocultar caminho do instantâneo do volume selecionando o volume. Você pode editar a opção ocultar caminho do instantâneo clicando em **Editar** na página do volume.  

![Editar opções de instantâneo de volume](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

### <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Restaurar um arquivo usando um cliente NFS do Linux 

1. Use o `ls` comando do Linux para listar o arquivo que você deseja restaurar do `.snapshot` diretório. 

    Por exemplo:

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. Use o `cp` comando para copiar o arquivo para o diretório pai.  

    Por exemplo: 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

### <a name="restore-a-file-by-using-a-windows-client"></a>Restaurar um arquivo usando um cliente Windows 

1. Se o `~snapshot` diretório do volume estiver oculto, [mostre itens ocultos](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10) no diretório pai a serem exibidos `~snapshot` .

    ![Exibir itens ocultos](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. Navegue até o subdiretório no `~snapshot` para localizar o arquivo que você deseja restaurar.  Clique com o botão direito do mouse no arquivo. Selecione **Copiar**.  

    ![Copiar arquivo a ser restaurado](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. Retorne ao diretório pai. Clique com o botão direito do mouse no diretório pai e selecione `Paste` para colar o arquivo no diretório.

    ![Colar arquivo a ser restaurado](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. Você também pode clicar com o botão direito do mouse no diretório pai, selecionar **Propriedades**, clicar na guia **versões anteriores** para ver a lista de instantâneos e selecionar **restaurar** para restaurar um arquivo.  

    ![Propriedades versões anteriores](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="next-steps"></a>Próximas etapas

* [Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Limites de recursos do Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vídeo de instantâneos de Azure NetApp Files 101](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
