---
title: Criar e criptografar uma VM do Linux com o portal do Azure
description: Neste início rápido, você aprende a usar o portal do Azure para criar e criptografar uma máquina virtual Linux
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: e777387437b572eb11ebb7999d87a172b54738bb
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151260"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Início Rápido: Criar e criptografar uma máquina virtual com o portal do Azure

As máquinas virtuais (VM) do Azure podem ser criadas por meio do Portal do Azure. O portal do Azure é uma interface de usuário baseada em navegador para criar as VMS e seus recursos relacionados. Neste início rápido, você usará o portal do Azure para implantar uma VM (máquina virtual) do Linux executando o Ubuntu 18.04 LTS, criar um cofre de chaves para o armazenamento de chaves de criptografia e criptografar a VM.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [Portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Escolha **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Na página Novo, em Popular, selecione **Ubuntu Server 18.04 LTS**.
1. Na guia **Básico**, em **Detalhes do projeto**, verifique se a assinatura correta está selecionada.
1. Para **Grupo de recursos**, selecione o grupo de recursos que você criou ao fazer o cofre de chaves acima (por exemplo, **myResourceGroup**).
1. Para **Nome da máquina virtual**, insira *MyVM*.
1. Para **Região**, selecione a mesma região que você usou ao criar o cofre de chaves acima (por exemplo, **Leste dos EUA**).
1. Verifique se o **Tamanho** é *Standard D2s v3*.
1. Em **Conta do administrador**, selecione **Senha**. Digite um nome de usuário e uma senha.
    ![Tela de criação ResourceGroup](./media/disk-encryption/portal-qs-vm-creation.png)
1. Selecione a guia "Gerenciamento" e verifique se você tem uma Conta de Armazenamento de Diagnóstico. Se você não tiver contas de armazenamento, selecione "Criar", dê um nome à nova conta e selecione "OK" ![Tela de criação ResourceGroup](./media/disk-encryption/portal-qs-vm-creation-storage.png)
1. Clique em "Examinar + Criar".
1. Na página **Criar uma máquina virtual**, você pode ver os detalhes sobre a VM que você está prestes a criar. Quando estiver pronto, selecione **Criar**.

Levará alguns minutos para que sua VM seja implantada. Quando a implantação for concluída, vá para a próxima seção.

## <a name="encrypt-the-virtual-machine"></a>Criptografar a máquina virtual

1. Depois que a implantação da VM estiver concluída, selecione **Ir para o recurso**.
1. No lado esquerdo, selecione **Discos**.
1. Na tela Discos, selecione **Criptografia**. 

    ![seleção de discos e criptografia](../media/disk-encryption/portal-qs-disks-to-encryption.png)

1. Na tela criptografia, em **Discos para criptografar**, escolha **SO e discos de dados**.
1. Em **Configurações de criptografia**, escolha **Selecionar um cofre de chaves e uma chave para criptografia**.
1. Na tela **Selecionar chave no Azure Key Vault**, selecione **Criar**.

    ![seleção de discos e criptografia](../media/disk-encryption/portal-qs-keyvault-create.png)

1. Na tela **Criar cofre de chaves**, verifique se o Grupo de Recursos é o mesmo usado para criar a VM.
1. Dê um nome ao seu cofre de chaves.  Cada cofre de chaves no Azure deve ter um nome exclusivo.
1. Na guia **Políticas de Acesso**, marque a caixa **Azure Disk Encryption para a criptografia de volume**.

    ![seleção de discos e criptografia](../media/disk-encryption/portal-qs-keyvault-enable.png)

1. Selecione **Examinar + criar**.  
1. Depois que o cofre de chaves passar na validação, selecione **Criar**. Isso retornará você para a tela **Selecionar chave no Azure Key Vault**.
1. Deixe o campo **Chave** em branco e escolha **Selecionar**.
1. Na parte superior da tela de criptografia, clique em **Salvar**. Um pop-up avisará que a VM será reinicializada. Clique em **Sim**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando o grupo de recursos, a máquina virtual e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos da máquina virtual, selecione Excluir, em seguida, confirme o nome do grupo de recursos a excluir.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um Key Vault habilitado para chaves de criptografia, criou uma máquina virtual e habilitou a máquina virtual para criptografia.  

> [!div class="nextstepaction"]
> [Visão geral do Azure Disk Encryption](disk-encryption-overview.md)
