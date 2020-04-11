---
title: Guia para controlar o comportamento de desligamento do Windows no Azure Lab Services | Microsoft Docs
description: Passos para desligar automaticamente uma máquina virtual do Windows ociosa e remover o comando de desligamento do Windows.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 3/30/2020
ms.author: spelluru
ms.openlocfilehash: 7b839df5940ab26e5c1a99a1bda1fbd2545f8cc4
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113113"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Guia para controlar o comportamento de desligamento do Windows

O Azure Lab Services fornece vários controles de custos para garantir que as máquinas virtuais (VMs) do Windows não estejam funcionando inesperadamente:
 - [Defina um cronograma](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [Definir cotas para usuários](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [Habilitar desligamento automático ao desconectar](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

Mesmo com esses controles de custos, existem situações em que uma VM do Windows pode continuar a ser executada inesperadamente; e, como resultado, deduzir da cota do aluno:

- **Janela RDP é deixada aberta**
  
    Quando um aluno se conecta à sua VM usando RDP, ele pode inadvertidamente deixar a janela RDP aberta.  Enquanto a janela RDP permanecer aberta, o desligamento automático na configuração **de desconexão** nunca terá efeito, uma vez que só será acionado após a sessão RDP ser desconectada.

- **O comando de desligamento do Windows é usado para desativar a VM**
  
    Um aluno pode usar o comando de desligamento do Windows, ou outros mecanismos de desligamento fornecidos dentro do Windows, para desligar a VM em vez de usar o botão de [parada do Azure Lab Services](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm).  Quando isso acontece, na perspectiva do Azure Lab Services, a VM ainda está sendo usada.
    
Para ajudá-lo a evitar que essas situações aconteçam, este guia fornece etapas para desligar automaticamente uma VM ociosa do Windows e remover o comando de desligamento do Windows no menu **Iniciar.**  

> [!NOTE]
> Uma VM também pode deduzir inesperadamente da cota quando o aluno inicia sua VM, mas nunca se conecta a ela usando RDP.  Este guia *não* aborda atualmente este cenário.  Em vez disso, os alunos devem ser lembrados de se conectar imediatamente à sua VM usando RDP depois de iniciá-lo; ou, eles devem parar o VM.

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>Desconexão e desligamento automático de RDP para VM ocioso

O Windows fornece configurações **de diretiva de grupo local** que você pode usar para definir um limite de tempo para desconectar automaticamente uma sessão RDP quando ela ficar ociosa.  Uma sessão é determinada a ficar ociosa quando *não* há nenhuma entrada do mouse\teclado.  Qualquer atividade de longa duração que não envolva entrada de mouse\teclado faz com que a VM esteja em um estado ocioso.  Isso inclui a execução de uma longa consulta, streaming de vídeo, compilação, etc.  Dependendo das necessidades da sua classe, você pode optar por definir o limite de tempo ocioso para que seja longo o suficiente para lidar com esses tipos de atividades.  Por exemplo, você pode definir o limite de tempo ocioso para 1 ou mais horas, se necessário.

Aqui está a experiência do aluno ao configurar o **limite de sessão ociosa** em combinação com o desligamento automático na configuração [**de desconexão:**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)
 1. O aluno se conecta ao seu Windows VM usando RDP.
 2. Quando o aluno deixa sua janela RDP aberta e a VM fica ociosa para o **limite de sessão ociosa** que você especificou (como 5 minutos), o aluno verá o seguinte diálogo:

    ![Diálogo expirado do prazo ocioso](../media/how-to-windows-shutdown/idle-time-expired.png)

1. Se o aluno *não* clicar em **OK,** a sessão RDP será desligada automaticamente após 2 minutos.
2. Após a sessão RDP ser desconectada, uma vez que o prazo especificado para o desligamento automático na configuração **de desconexão** seja alcançado, a VM será automaticamente desligada pelos Serviços do Laboratório Azure.

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>Defina o limite de tempo da sessão ociosa do RDP no modelo VM

Para definir o limite de tempo ocioso da sessão RDP, você pode se conectar ao vm modelo e executar o script PowerShell abaixo.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 1000

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
Ou, você pode optar por seguir estas etapas manuais usando o modelo VM:

1. Pressione a tecla Windows, **digite gpedit**e selecione **Editar a diretiva de grupo (painel de controle)**.

1. Vá para **configuração de computador > modelos administrativos > componentes do Windows > serviços de desktop remoto> host de sessão de sessão remota da sessão de desktop > limites de tempo da sessão**.  

    ![Editor de política de grupo local](../media/how-to-windows-shutdown/group-policy-idle.png)
   
1. Clique com o botão direito do **mouse Definir o limite de tempo para sessões ativas, mas ociosas de serviços de desktop remoto,** e clique em **Editar**.

1. Insira as configurações a seguir e clique em **OK**:
   1. Selecione **Habilitado**.
   1. Em **Opções,** especifique **o limite da sessão ociosa**.

    ![Limite de sessão ociosa](../media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. Finalmente, para combinar esse comportamento com o desligamento automático na configuração **de desconexão,** você deve seguir as etapas do artigo como fazer: Habilitar o [desligamento automático de VMs na desconexão](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect).

> [!WARNING]
> Depois de configurar essa configuração usando o PowerShell para modificar a configuração de registro diretamente ou manualmente usando o editor de Diretiva de Grupo, você deve primeiro reiniciar a VM para que as configurações entrem em vigor.  Além disso, se você configurar a configuração usando o registro, o editor de Diretiva de Grupo nem sempre será atualizado para refletir alterações na configuração de registro; no entanto, a configuração do registro ainda entra em vigor como esperado e você verá a sessão RDP desconectada quando ociosa pelo tempo que você especificou.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Remover o comando de desligamento do Windows no menu Iniciar

As configurações **de diretiva de grupo local do** Windows também permitem remover o comando shutdown do menu **Iniciar.**

Para remover o comando de desligamento, você pode se conectar ao vm modelo e executar o script PowerShell abaixo.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

Ou, você pode optar por seguir estas etapas manuais usando o modelo VM:

1. Pressione a tecla Windows, **digite gpedit**e selecione **Editar a diretiva de grupo (painel de controle)**.

1. Vá para **configuração do computador > modelos administrativos > Menu Iniciar e Barra de Tarefas**.  

    ![Editor de política de grupo local](../media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Clique com o botão direito do mouse **Remover e impedir o acesso aos comandos Desligar, Reiniciar, Dormir e Hibernar**e clicar em **Editar**.

1. Selecione a **configuração Ativada** e clique em **OK**:
 
   ![Configuração de desligamento](../media/how-to-windows-shutdown/edit-shutdown.png)

1. Observe que o comando de desligamento não aparece mais no menu **Iniciar do** Windows; apenas o comando **Desconectar** é exibido.

    ![Comando de desligamento](../media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Próximas etapas
Veja o artigo sobre como preparar um Modelo Windows VM: [Guia para configurar uma máquina de modelo do Windows no Azure Lab Services](how-to-prepare-windows-template.md)