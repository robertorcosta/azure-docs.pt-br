---
title: Tutorial – Saiba como acessar sua nuvem privada
description: Saiba como acessar uma nuvem privada da AVS (Solução VMware no Azure) do Azure
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 32dc5c173789996d2559eda1153b1509e10e5984
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497957"
---
# <a name="tutorial-learn-how-to-access-an-azure-vmware-solution-avs-private-cloud"></a>Tutorial: Saiba como acessar uma nuvem privada da AVS (Solução VMware no Azure) do Azure

Durante a versão prévia, a AVS não permite que você gerencie sua nuvem privada com o vCenter local. Você precisará realizar uma configuração e uma conexão adicional com uma instância do vCenter local por meio de uma caixa de salto. 

Neste tutorial, você cria uma máquina virtual do Windows para uma caixa de salto no grupo de recursos criado no tutorial anterior, [Tutorial: Configurar a rede para sua nuvem privada do VMware no Azure](tutorial-configure-networking.md) e entrar no vCenter. Essa VM fica na mesma rede virtual que você criou e fornece acesso ao vCenter e ao NSX Manager. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma máquina virtual do Windows para usar para se conectar ao vCenter
> * Fazer logon no vCenter por meio da máquina virtual

## <a name="create-a-new-windows-virtual-machine"></a>Criar uma máquina virtual do Windows

No grupo de recursos, selecione **+ Adicionar**, em seguida, pesquise e selecione **Microsoft Windows 10** e clique em **Criar**.

:::image type="content" source="./media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Adicionar uma nova VM do Windows 10 para uma caixa de salto" border="true":::

Insira as informações necessárias nos campos e selecione **Examinar + criar**. Para obter mais informações sobre os campos, confira a tabela a seguir.

| Campo | Valor |
| --- | --- |
| **Assinatura** | Este valor já está preenchido com a Assinatura à qual o Grupo de recursos pertence. |
| **Grupo de recursos** | Este valor já está preenchido para o Grupo de recursos atual. Ele deve ser o Grupo de recursos que você criou em um tutorial anterior. |
| **Nome da máquina virtual** | Insira um nome exclusivo para a VM. |
| **Região** | Selecione a localização geográfica da VM. |
| **Opções de disponibilidade** | Deixe o valor padrão selecionado. |
| **Imagem** | Selecione a imagem da VM. |
| **Tamanho** | Deixar o valor de tamanho padrão. |
| **Tipo de autenticação**  | Selecione **Senha**. |
| **Nome de usuário** | Insira o nome de usuário para fazer logon na VM. |
| **Senha** | Insira a senha para fazer logon na VM. |
| **Confirmar senha** | Insira a senha para fazer logon na VM. |
| **Portas de entrada públicas** | Selecione **Nenhum**. Se você selecionar Nenhum, poderá usar o [acesso JIT](../security-center/security-center-just-in-time.md#jit-configure) para controlar o acesso à VM somente quando quiser acessá-la.  |

Depois de inserir as informações adequadas, clique em **Examinar + criar**. Após a aprovação da validação, selecione **Criar** para iniciar o processo de criação da máquina virtual.

:::image type="content" source="./media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Criar uma VM do Windows 10 para uma caixa de salto" border="true":::

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Conectar-se ao vCenter local da nuvem privada

Na caixa de salto, entre no Cliente vSphere com o SSO do VMware vCenter. Entre no Cliente vSphere usando um nome de usuário administrador de nuvem, aceite o risco de segurança e continue quando vir um aviso sobre um potencial risco de segurança. Entre no VMware vCenter com as credenciais de logon único e verifique se a interface do usuário é exibida com êxito.

No portal do Azure, selecione a nuvem privada e, na exibição **Visão Geral**, selecione **Identidade > Padrão**. As URLs e as credenciais de logon da nuvem privada do vCenter e do NSX-T Manager são exibidas.

:::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Exibir URLs e credenciais da nuvem privada do vCenter e do NSX Manager" border="true":::

Navegue até a máquina virtual criada na etapa anterior e conecte-se a ela. Para ver as etapas detalhadas de como se conectar à máquina virtual, confira [conectar-se a uma máquina virtual](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine).

Na VM do Windows, abra um navegador e navegue até as URLs do vCenter e do NSX-T Manager em duas guias. Na guia do vCenter, insira as credenciais de usuário `cloudadmin@vmcp.local` da etapa anterior.

:::image type="content" source="./media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Entrar no vCenter da nuvem privada" border="true":::

:::image type="content" source="./media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="Portal do vCenter" border="true":::

Na segunda guia do navegador, entre no NSX-T Manager.

:::image type="content" source="./media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Página inicial do NSX Manager na nuvem privada local" border="true":::

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar uma máquina virtual do Windows para usar para se conectar ao vCenter
> * Fazer logon no vCenter por meio da máquina virtual

Continue para próximo tutorial para saber como escalar sua nuvem privada da AVS.

> [!div class="nextstepaction"]
> [Escalar uma nuvem privada da AVS](tutorial-scale-private-cloud.md)
