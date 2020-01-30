---
title: Configure um laboratório para ensinar o desenvolvimento de aplicativos móveis com o Android Studio
titleSuffix: Azure Lab Services
description: Saiba como configurar um laboratório para ensinar a classe de desenvolvimento de aplicativos móveis de dados que usa Android Studio.  O artigo também discutirá os ajustes a serem feitos ao usar Android Studio em uma máquina virtual no Azure.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849780"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Configure um laboratório para ensinar o desenvolvimento de aplicativos móveis de dados com o Android Studio

Este artigo mostrará como configurar uma classe introdutória de desenvolvimento de aplicativos móveis.  Essa classe se concentra em aplicativos móveis Android que podem ser publicados no [Google Play Store](https://play.google.com/store/apps).  Os alunos aprendem a usar [Android Studio](https://developer.android.com/studio) para criar aplicativos.  O [emulador do Visual Studio para Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) é usado para testar o aplicativo localmente.

## <a name="lab-configuration"></a>Configuração do laboratório

Para configurar este laboratório, você precisa de uma assinatura do Azure e uma conta de laboratório para começar. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de obter uma assinatura do Azure, você poderá criar uma nova conta de laboratório no Azure Lab Services. Para obter mais informações sobre como criar uma nova conta de laboratório, consulte [tutorial para configurar uma conta de laboratório](tutorial-setup-lab-account.md).  Você também pode usar uma conta de laboratório existente.

Siga o [tutorial configurar laboratório de sala de aula](tutorial-setup-classroom-lab.md) para criar um novo laboratório e, em seguida, aplicar as seguintes configurações:

| Tamanho da máquina virtual | Imagem |
| -------------------- | ----- |
| Médio (virtualização aninhada) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>Configuração da máquina de modelo

Quando a criação da máquina de modelo for concluída, [inicie o computador e conecte-o](how-to-create-manage-template.md#update-a-template-vm) para concluir as seguintes tarefas:

1. Adicionar função do Hyper-V
2. Baixe e instale o Java.  
3. Baixe e instale o emulador do Visual Studio para Android.
4. Baixe e instale o Android Studio.
5. Configure o emulador do Visual Studio para Android Studio.

## <a name="add-hyper-v-role"></a>Adicionar função do Hyper-V

O Hyper-V deve estar habilitado para a instalação bem-sucedida do emulador do Visual Studio para Android.  Siga as instruções no artigo [como habilitar a virtualização aninhada em um modelo de máquina virtual](how-to-enable-nested-virtualization-template-vm.md) .

## <a name="install-java"></a>Instalar o Java

Android Studio requer Java.  Siga as etapas abaixo para baixar a versão mais recente do Java.

1. Navegue até a [página de download do Java](https://www.java.com/download/). Clique no botão **download do Java** .
2. Na página da Web do Windows de 64 bits para Java, clique no botão chamado **concordo e inicie o download gratuito**.
3. Quando o instalador da **instalação do Java** for exibido, clique em **instalar**.
4. Aguarde até que o título do instalador mude para a **instalação do Java – completo**.  Clique no botão **fechar** .

## <a name="install-visual-studio-emulator-for-android"></a>Instalar o emulador do Visual Studio para Android

Para testar um aplicativo Android localmente, ele deve usar uma versão virtualizada de um dispositivo Android.  Há alguns emuladores do Android disponíveis que permitirão que um desenvolvedor teste seu aplicativo em seus computadores.  Estamos usando o emulador do Visual Studio para Android porque ele é um emulador que dá suporte à virtualização aninhada.  Como a VM do serviço de laboratório já é uma máquina virtual, precisamos de um emulador que ofereça suporte à virtualização aninhada.  O emulador interno para Android Studio não oferece suporte à virtualização aninhada.  Para ver quais emuladores oferecem suporte à virtualização aninhada, consulte [aceleração de hardware para desempenho de emulador (Hyper-V & HAXM)](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration).

Use as instruções a seguir para baixar e instalar o emulador do Visual Studio para Android.

1. Navegue até o [emulador do Visual Studio para Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) Home Page.
2. Clique no botão **baixar o emulador** .
3. Quando vs_emulatorsetup. exe for baixado, execute o executável.
4. Quando a caixa de diálogo de instalação do Visual Studio for exibida, clique no botão **instalar** .
5. Aguarde a conclusão do instalador.  Clique no botão **reiniciar agora** para reiniciar o computador e concluir a instalação.

Inicie o emulador primeiro antes de implantar seu aplicativo usando Android Studio.  Para obter mais informações sobre o emulador do Visual Studio para Android, consulte a [documentação do emulador do Visual Studio para Android](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android).

## <a name="install-android-studio"></a>Instalar Android Studio

Siga as instruções abaixo para baixar e instalar o [Android Studio](https://developer.android.com/studio).

1. Navegue até [Android Studio página de download](https://developer.android.com/studio#downloads).  
    > [!NOTE]
    > Não há suporte para o Internet Explorer neste site.
2. Clique no pacote executável do Windows (64 bits) Android Studio.
3. Leia os termos legais escritos no pop-up.  Quando estiver pronto para continuar, marque **eu li e concordo com a caixa de seleção de termos e condições acima** e clique no botão **baixar Android Studio para Windows** .
4. Quando o executável da instalação do Android Studio for baixado, execute o executável.
5. Na página **Bem-vindo à instalação do Android Studio** do instalador da **instalação do Android Studio** , clique em **Avançar**.
6. Na página **definições de configuração** , clique em **Avançar**.
7. Na página **escolher pasta do menu iniciar** , clique em **instalar**.
8. Aguarde a conclusão da instalação.
9. Na página **instalação concluída** , clique em **Avançar**.
10. Na página **concluindo Android Studio instalação** .  Clique em **Concluir**.
11. Android Studio será iniciado automaticamente depois que a instalação for concluída.
12. Na caixa de diálogo **Importar configurações do Android de...** , selecione não **Importar configurações**. Clique em **OK**.
13. Na página **Bem-vindo** do **Assistente de instalação do Android Studio**, clique em **Avançar**.
14. Na página **tipo de instalação** , escolha **padrão**. Clique em **Próximo**.
15. Na página **Selecionar tema da interface do usuário** , selecione tema desejado. Clique em **Próximo**.
16. Na página **verificar configurações** , clique em **Avançar**.
17. Na página **baixar componentes** , aguarde até que todos os componentes sejam baixados.  Clique em **Concluir**.

    > [!IMPORTANT]
    > Espera-se que a instalação do HAXM falhe.  O HAXM não dá suporte à virtualização aninhada, que é por isso que instalamos o emulador do Visual Studio para Android anteriormente neste artigo.

18. A caixa de diálogo **Bem-vindo ao Android Studio** será exibida quando o assistente de instalação for concluído.

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Configurar o emulador do Android Studio e do Visual Studio para Android

Android Studio está quase pronto para uso.  Ainda precisamos dizer ao Visual Studio Emulator para Android onde o SDK do Android está instalado.  Isso fará com que todos os emuladores em execução no Visual Studio para Android sejam exibidos como destinos de implantação para Android Studio depuração.

Precisamos definir uma chave de Registro específica para informar ao emulador do Visual Studio para Android onde o SDK do Android está localizado.  Para definir a chave do Registro necessária, execute o script abaixo.  O script do PowerShell abaixo pressupõe o local de instalação padrão para o SDK do Android.  Se você instalou o SDK do Android em outro local, modifique o valor de `$androidSdkPath` antes de executar o script.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Reinicie o emulador do Visual Studio para Android e Android Studio para que a nova configuração seja usada.

Inicie a versão necessária no emulador do Visual Studio.  Ele será exibido como um destino de implantação para seu aplicativo Android no Android Studio.  A versão mínima para o projeto de Android Studio deve dar suporte à versão em execução no emulador do Visual Studio para Android.  Agora você está pronto para criar e depurar projetos usando o Android Studio e o emulador do Visual Studio para Android.  Se você tiver problemas, consulte solução de problemas do Android Emulator.

## <a name="cost"></a>Custo

Se desejar estimar o custo deste laboratório, você poderá seguir o exemplo abaixo.
Para uma classe de 25 alunos com 20 horas de tempo de classe agendada e 10 horas de cota para a casa ou as atribuições, o preço do laboratório seria  

25 alunos \* (20 cotas agendadas + 10) horas * 55 unidades de laboratório * 0, 1 USD por hora = 412,5 USD

Mais detalhes sobre preços, consulte [preços de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Próximos passos

As próximas etapas são comuns à configuração de qualquer laboratório.

- [Criar e gerenciar um modelo](how-to-create-manage-template.md)
- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de registro de email para alunos](how-to-configure-student-usage.md#send-invitations-to-users)
