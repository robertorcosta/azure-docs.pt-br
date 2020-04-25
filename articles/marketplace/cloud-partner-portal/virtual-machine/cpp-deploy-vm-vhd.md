---
title: Implantar uma VM de seus VHDs para o Azure Marketplace
description: Explica como registrar uma VM de um VHD do Azure implantado.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: dsindona
ms.openlocfilehash: b02fda545ac135735186885d7db597885bf6cc21
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147961"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Implantar uma VM por meio dos seus VHDs

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos o gerenciamento de movimentação de suas ofertas de máquina virtual do Azure para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções em [criar seus ativos técnicos da máquina virtual do Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) para gerenciar suas ofertas migradas.

Esta seção explica como implantar uma VM (máquina virtual) por meio de um VHD (disco rígido virtual) implantado no Azure.  Ela lista as ferramentas necessárias e como usá-las para criar uma imagem de VM do usuário e, em seguida, implantá-la no Azure usando scripts do PowerShell.

Depois de carregar seus discos rígidos virtuais (VHDs) - o VHD do sistema operacional generalizado e zero ou mais VHDs de disco de dados - para uma conta de armazenamento do Azure, você pode registrá-los como uma imagem VM do usuário. Em seguida, você pode testar essa imagem. Como seu VHD do sistema operacional é generalizado, você não pode implantar a VM diretamente fornecendo a URL do VHD.

Para obter mais informações sobre imagens de VM consulte os posts de blog abaixo:

- [Image da VM](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Instruções "como" do PowerShell de imagem de VM](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>Pré-requisito: instalar as ferramentas necessárias

Se você ainda não fez isso, instale o Azure PowerShell e CLI do Azure, usando as instruções a seguir:

- [Instale o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Instalar a CLI do Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>Etapas de implantação

Você usará as seguintes etapas para criar e implantar uma imagem de VM do usuário:

1. Criar a imagem de VM do usuário, que envolve a captura e a generalização da imagem. 
2. Criar certificados e armazená-los em um novo Azure Key Vault. Um certificado é necessário para estabelecer uma conexão segura do WinRM com a VM.  Um modelo do Azure Resource Manager e um script do Azure PowerShell são fornecidos. 
3. Implante a VM com base em uma imagem de VM do usuário, usando o modelo e o script fornecidos.

Depois que a VM for implantada, você estará pronto para [certificar a imagem de VM](./cpp-certify-vm.md).

1. Clique em **Novo** e pesquise a **Implantação de modelo**, em seguida, selecione **Criar seu próprio modelo no Editor**.  <br/>
   ![Criar modelo de implantação de VHD no portal do Azure](./media/publishvm_021.png)

1. Copie e cole este [modelo JSON](./cpp-deploy-json-template.md) no editor e clique em **Salvar**. <br/>
   ![Salve o modelo de implantação do VHD no portal do Azure](./media/publishvm_022.png)

1. Forneça os valores de parâmetro para as páginas de propriedades **Implantação Personalizada**.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Parâmetro**              |   **Descrição**                                                            |
   |  -------------              |   ---------------                                                            |
   | Nome de Conta de Armazenamento do Usuário   | Nome da conta de armazenamento onde se encontra o VHD generalizado                    |
   | Nome do Contêiner de Armazenamento de Usuário | Nome do contêiner onde se encontra o VHD generalizado                          |
   | Nome DNS para o IP público      | Nome DNS do IP público. O nome DNS é da VM, você irá defini-lo no portal do Azure, depois que a oferta for implantada.  |
   | Nome de Usuário do Administrador             | Nome de usuário da conta do administrador para a nova VM                                  |
   | Senha do Administrador              | Senha da conta Administrador para nova VM                                  |
   | Tipo de SO                     | Sistema operacional da VM `Windows` \| :`Linux`                                    |
   | ID da assinatura             | Identificador para a assinatura selecionada                                      |
   | Local                    | Localização geográfica da implantação                                        |
   | Tamanho da VM                     | [Tamanho da VM do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), por exemplo `Standard_A2` |
   | Nome do endereço IP público      | Nome do seu endereço IP público                                               |
   | Nome da VM                     | Nome da nova VM                                                           |
   | Nome da VNET        | Nome da rede virtual usada pela VM                                   |
   | Nome da NIC                    | Nome da placa de interface de rede em execução na rede virtual               |
   | URL do VHD                     | Conclua a URL do VHD de disco do sistema operacional                                                     |
   |  |  |
            
1. Depois de fornecer esses valores, clique em **Comprar**. 

O Azure começará a implantação: ele cria uma nova VM com o VHD não gerenciado especificado, no caminho de conta de armazenamento especificada.  Você pode acompanhar o progresso no portal do Azure clicando em **Máquinas Virtuais** no lado esquerdo do portal.  Quando a VM tiver sido criada, o status será alterado de `Starting` para `Running`. 


### <a name="deploy-a-vm-from-powershell"></a>Implantar uma VM a partir do PowerShell

Para implantar uma VM grande, a partir da imagem VM generalizada recém-criada, use os cmdlets a seguir.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>Próximas etapas

Em seguida, você [criará uma imagem de VM do usuário](cpp-create-user-image.md) para sua solução.

