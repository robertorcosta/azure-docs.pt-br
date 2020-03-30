---
title: Habilite a virtualização aninhada em um modelo VM no Azure Lab Services (Script) | Microsoft Docs
description: Aprenda a criar um modelo vm com várias VMs dentro.  Em outras palavras, habilite a virtualização aninhada em um modelo VM no Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 56e5ad21f94521565b4df193b2450a1c994b66f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503030"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Habilite a virtualização aninhada em uma máquina virtual de modelo no Azure Lab Services usando um script

A virtualização aninhada permite criar um ambiente multi-VM dentro da máquina virtual de modelo de um laboratório. A publicação do modelo fornecerá a cada usuário no laboratório uma máquina virtual configurada com várias VMs dentro dele.  Para obter mais informações sobre virtualização aninhada e serviços do Azure Lab, consulte [Ativar a virtualização aninhada em uma máquina virtual de modelo no Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

As etapas deste artigo se concentram na configuração da virtualização aninhada para o Windows Server 2016 ou o Windows Server 2019. Você usará um script para configurar a máquina de modelo com hyper-V.  As etapas a seguir irão guiá-lo sobre como usar os [scripts Hyper-V](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)do Lab Services .

>[!IMPORTANT]
>Selecione **Grande (virtualização aninhada)** ou **Média (virtualização aninhada)** para o tamanho da máquina virtual ao criar o laboratório.  A virtualização aninhada não funcionará de outra forma.  

## <a name="run-script"></a>Executar script

1. Se você estiver usando o Internet Explorer, você pode ter que adicionar `https://github.com` à lista de sites confiáveis.
    1. Abra o Internet Explorer.
    1. Selecione o ícone de engrenagem e escolha **opções de Internet**.  
    1. Quando a caixa de diálogo **Opções** da Internet for exibida, selecione **Segurança,** **selecione Sites confiáveis,** clique em **Sites.**
    1. Quando a caixa de `https://github.com` diálogo **sites confiáveis** aparecer, adicione à lista de sites confiáveis e selecione **Fechar**.

        ![Sites confiáveis](../media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Baixe os arquivos do repositório do Git conforme descrito nas etapas a seguir.
    1. Vá [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)para.
    1. Clique no botão **Clone ou Download.**
    1. Clique **em Baixar ZIP**.
    1. Extrair o arquivo ZIP

    >[!TIP]
    >Você também pode clonar o repositório Git em [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git).

1. Inicie **o PowerShell** no modo **administrador.**
1. Na janela PowerShell, navegue até a pasta com o script baixado. Se você estiver navegando a partir da pasta superior dos arquivos do `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`repositório, o script está localizado em .
1. Você pode ter que alterar a política de execução para executar com sucesso o script. Execute o comando a seguir:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Executar o script:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > O script pode exigir que a máquina seja reiniciada. Siga as instruções do script e execute o script até que o **script concluído** seja visto na saída.
1. Não se esqueça de redefinir a política de execução. Execute o comando a seguir:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Conclusão

Agora sua máquina de modelo está pronta para criar máquinas virtuais Hyper-V. Consulte [Criar uma máquina virtual em Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) para obter instruções sobre como criar máquinas virtuais Hyper-V. Além disso, consulte [o Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) para verificar os sistemas operacionais e software disponíveis.  

## <a name="next-steps"></a>Próximas etapas

Os próximos passos são comuns à criação de qualquer laboratório.

- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Defina um cronograma](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição por e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)