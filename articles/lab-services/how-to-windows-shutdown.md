---
title: Guia para controlar o comportamento de desligamento do Windows no Azure Lab Services | Microsoft Docs
description: Etapas para desligar automaticamente uma máquina virtual do Windows ociosa e remover o comando de desligamento do Windows.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e17f6e79c3d18d82dd206954dcfb0e06b02b4d53
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445161"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Guia para controlar o comportamento de desligamento do Windows

O Azure Lab Services fornece vários controles de custo para garantir que as VMs (máquinas virtuais) do Windows não estejam em execução inesperadamente:
 - [Definir um agendamento](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [Definir cotas para usuários](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [Habilitar desligamento automático ao desconectar](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

Mesmo com esses controles de custo, há situações em que uma VM Windows pode continuar a ser executada inesperadamente; e, como resultado, deduzir da cota do aluno:

- **A janela RDP é deixada aberta**
  
    Quando um aluno se conecta à sua VM usando o RDP, ele pode acidentalmente deixar a janela RDP aberta.  Desde que a janela RDP permaneça aberta, a configuração de **desligamento automático na desconexão** nunca entrará em vigor, pois ela só é disparada depois que a sessão RDP é desconectada.

- **O comando de desligamento do Windows é usado para desligar a VM**
  
    Um aluno pode usar o comando de desligamento do Windows ou outros mecanismos de desligamento fornecidos no Windows para desativar a VM em vez de usar [Azure Lab Services botão ' parar '](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm).  Quando isso acontece, da perspectiva do Azure Lab Services, a VM ainda está sendo usada.
    
Para ajudá-lo a evitar que essas situações ocorram, este guia fornece etapas para desligar automaticamente uma VM ociosa do Windows e remover o comando de desligamento do Windows do menu **Iniciar** .  

> [!NOTE]
> Uma VM também pode deduzir inesperadamente da cota quando o aluno inicia sua VM, mas nunca se conecta a ela usando o RDP.  Este guia *não* atende atualmente a esse cenário.  Em vez disso, os alunos devem ser lembrados para conectar-se imediatamente à sua VM usando o RDP depois de iniciá-lo; ou, eles devem parar a VM.

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>Desconexão e desligamento automático de RDP para VM ociosa

O Windows fornece configurações de **política de grupo locais** que você pode usar para definir um limite de tempo para desconectar automaticamente uma sessão RDP quando ela se tornar ociosa.  Uma sessão é considerada ociosa quando *não* há nenhuma entrada mouse\keyboard.  Todas as atividades de longa execução que não envolvem a entrada mouse\keyboard fazem com que a VM esteja em um estado ocioso.  Isso inclui a execução de uma consulta longa, transmissão de vídeo, compilação, etc.  Dependendo das necessidades da sua classe, você pode optar por definir o limite de tempo ocioso para que ele seja longo o suficiente para lidar com esses tipos de atividades.  Por exemplo, você pode definir o limite de tempo ocioso como 1 ou mais horas, se necessário.

Aqui está a experiência do aluno quando você configura o **limite de sessão ociosa** em combinação com o [**desligamento automático na configuração de desconexão**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect) :
 1. O aluno se conecta à sua VM do Windows usando o RDP.
 2. Quando o aluno deixa sua janela RDP aberta e a VM está ociosa pelo **limite de sessão ociosa** que você especificou (como 5 minutos), o aluno verá a seguinte caixa de diálogo:

    ![Caixa de diálogo limite de tempo ocioso expirado](./media/how-to-windows-shutdown/idle-time-expired.png)

1. Se o aluno não *clicar* em **OK**, sua sessão RDP será desconectada automaticamente após 2 minutos.
2. Depois que a sessão RDP é desconectada, uma vez que o período de tempo especificado para o **desligamento automático na configuração de desconexão** é atingido, a VM é desligada automaticamente pelo Azure Lab Services.

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>Definir o limite de tempo de sessão ociosa do RDP na VM do modelo

Para definir o limite de tempo ocioso da sessão RDP, você pode se conectar à VM do modelo e executar o script do PowerShell abaixo.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 1000

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
Ou, você pode optar por seguir estas etapas manuais usando a VM de modelo:

1. Pressione a tecla Windows, digite **gpedit**e, em seguida, selecione **Editar política de grupo (painel de controle)**.

1. Vá para **configuração do computador > Modelos Administrativos > componentes do Windows > serviços de área de trabalho remota**> host da sessão da área de trabalho remota > limites de tempo de sessão.  

    ![Editor de política de grupo local](./media/how-to-windows-shutdown/group-policy-idle.png)
   
1. Clique com o botão direito do mouse em **definir limite de tempo para sessões ativas, mas ociosas serviços de área de trabalho remota**e clique em **Editar**.

1. Insira as configurações a seguir e clique em **OK**:
   1. Selecione **Habilitado**.
   1. Em **Opções**, especifique o **limite de sessão ociosa**.

    ![Limite de sessão ociosa](./media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. Por fim, para combinar esse comportamento com o **desligamento automático na configuração de desconexão** , você deve seguir as etapas no artigo de instruções: [habilitar o desligamento automático de VMs ao desconectar](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect).

> [!WARNING]
> Depois de definir essa configuração usando o PowerShell para modificar a configuração do registro diretamente ou manualmente usando o editor de Política de Grupo, você deve primeiro reiniciar a VM para que as configurações entrem em vigor.  Além disso, se você definir a configuração usando o registro, o editor de Política de Grupo nem sempre será atualizado para refletir as alterações na configuração do registro; no entanto, a configuração do registro ainda entra em vigor conforme o esperado e você verá a sessão RDP desconectada quando estiver ociosa pelo período de tempo especificado.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Remover o comando de desligamento do Windows do menu iniciar

As configurações de **política de grupo local** do Windows também permitem que você remova o comando de desligamento do menu **Iniciar** .

Para remover o comando de desligamento, você pode se conectar à VM de modelo e executar o script do PowerShell abaixo.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

Ou, você pode optar por seguir estas etapas manuais usando a VM de modelo:

1. Pressione a tecla Windows, digite **gpedit**e, em seguida, selecione **Editar política de grupo (painel de controle)**.

1. Vá para **configuração do computador > Modelos Administrativos > menu iniciar e barra de tarefas**.  

    ![Editor de política de grupo local](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Clique com o botão direito do mouse em **remover e impeça o acesso aos comandos desligar, reiniciar, suspender e hibernar**e clique em **Editar**.

1. Selecione a configuração **habilitado** e clique em **OK**:
 
   ![Configuração de desligamento](./media/how-to-windows-shutdown/edit-shutdown.png)

1. Observe que o comando de desligamento não aparece mais no menu **Iniciar** do Windows; somente o comando **Disconnect** é exibido.

    ![Comando de desligamento](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo sobre como preparar uma VM de modelo do Windows: [guia para configurar um computador de modelo do Windows no Azure Lab Services](how-to-prepare-windows-template.md)