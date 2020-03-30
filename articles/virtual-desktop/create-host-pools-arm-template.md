---
title: Pool de host seleto de desktop virtual do Windows Azure - Azure
description: Como criar um pool de host sind., no Windows Virtual Desktop, com um modelo do Azure Resource Manager.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9ce6440989cbf962c474de2a6c90db4c485bf4a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292320"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Criar um pool de host com um modelo do Azure Resource Manager

Pools de hosts são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de locatário da Área de Trabalho Virtual do Windows. Cada pool de hosts pode conter um grupo de aplicativo com o qual os usuários podem interagir como se eles estivessem em uma área de trabalho física.

Siga as instruções desta seção para criar um pool de host para um inquilino do Windows Virtual Desktop com um modelo do Azure Resource Manager fornecido pela Microsoft. Este artigo lhe dirá como criar um pool de host no Windows Virtual Desktop, criar um grupo de recursos com VMs em uma assinatura do Azure, juntar essas VMs ao domínio AD e registrar as VMs com o Windows Virtual Desktop.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>O que é necessário para executar o modelo do Azure Resource Manager

Certifique-se de saber as seguintes coisas antes de executar o modelo do Azure Resource Manager:

- Onde está a fonte da imagem que você deseja usar. É da Galeria Azure ou é personalizado?
- Seu domínio junta credenciais.
- Suas credenciais de Área de Trabalho Virtual do Windows.

Quando você cria um pool de hosts do Windows Virtual Desktop com o modelo Azure Resource Manager, você pode criar uma máquina virtual a partir da galeria Azure, uma imagem gerenciada ou uma imagem não gerenciada. Para saber mais sobre como criar imagens em VM, consulte [Prepare um VHD ou VHDX do Windows para carregar no Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) e [Criar uma imagem gerenciada de uma VM generalizada no Azure](../virtual-machines/windows/capture-image-resource.md).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Execute o modelo do Azure Resource Manager para provisionar um novo pool de host

Para começar, vá para [esta URL do GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Implantar o modelo no Azure

Se você estiver implantando em uma assinatura Enterprise, role para baixo e **selecione Implantar para o Azure,** então pule adiante preencha os parâmetros com base na fonte de imagem.

Se você estiver implantando em uma assinatura do Cloud Solution Provider, siga essas etapas para implantar no Azure:

1. Role para baixo e clique com o botão direito do mouse em **Implantar no Azure** e, em seguida, selecione **Copiar o link**.
2. Abra um editor de texto, como o Bloco de Notas, e cole o link nele.
3. Logo apóshttps://portal.azure.com/" " e antes da hashtag (#) digite um sinal de entrada (@) seguido pelo nome de domínio do inquilino. Aqui está um exemplo do formato `https://portal.azure.com/@Contoso.onmicrosoft.com#create/`que você deve usar: .
4. Entre no portal do Azure como um usuário com permissões de Administrador/Colaborador para a assinatura do Provedor de Soluções de Nuvem.
5. Cole o link copiado para o editor de texto na barra de endereços.

Para obter orientação sobre quais parâmetros você deve inserir para o seu cenário, consulte o arquivo Windows Virtual Desktop [Readme](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md). O Readme está sempre atualizado com as últimas mudanças.

## <a name="assign-users-to-the-desktop-application-group"></a>Atribuir usuários ao grupo de aplicativos de desktop

Depois que o modelo do GitHub Azure Resource Manager for concluído, atribua acesso ao usuário antes de começar a testar os desktops de sessão completos em suas máquinas virtuais.

Primeiro, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso.

Para atribuir os usuários ao grupo de aplicativos de desktop, abra uma janela PowerShell e execute este cmdlet para fazer login no ambiente de desktop virtual do Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Depois disso, adicione os usuários ao grupo de aplicativos de desktop com este cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

A UPN do usuário deve corresponder à identidade do usuário no Azure Active Directory (por exemplo, user1@contoso.com). Se você desejar adicionar vários usuários, deverá executar este cmdlet para cada um.

Após concluir essas etapas, os usuários adicionados ao grupo de aplicativo de área de trabalho poderão entrar na Área de Trabalho Virtual do Windows com os clientes de Área de Trabalho Remota com suporte e verão um recurso para uma área de trabalho de sessão.

>[!IMPORTANT]
>Para ajudar a proteger seu ambiente da Área de Trabalho Virtual do Windows no Azure, recomendamos que você não abra a porta de entrada 3389 nas VMs. A Área de Trabalho Virtual do Windows não exige uma porta de entrada 3389 aberta para que os usuários acessem as VMs do pool de hosts. Caso você precise abrir a porta 3389 para fins de solução de problemas, recomendamos o uso do [acesso just-in-time à VM](../security-center/security-center-just-in-time.md).