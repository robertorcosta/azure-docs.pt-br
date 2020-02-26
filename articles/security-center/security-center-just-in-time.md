---
title: Acesso JIT à máquina virtual na Central de Segurança do Azure | Microsoft Docs
description: Este documento demonstra como o acesso Just-In-Time à VM na Central de Segurança do Azure ajuda a controlar o acesso às suas máquinas virtuais do Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 4b2b388fb736997010a6cbbdf93b23b77c7ef3a3
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603977"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Proteja suas portas de gerenciamento com acesso just-in-time

Se você estiver no tipo de preço Standard da central de segurança (consulte [preços](/azure/security-center/security-center-pricing)), você pode bloquear o tráfego de entrada para suas VMs do Azure com acesso de VM (máquina virtual) JIT (just-in-time). Isso reduz a exposição a ataques e, ao mesmo tempo, fornece acesso fácil para se conectar às VMs quando necessário.

> [!NOTE]
> O acesso JIT à VM da Central de Segurança atualmente oferece suporte somente a VMs implantadas por meio do Azure Resource Manager. Para saber mais sobre os modelos de implantação clássica e do Resource Manager, consulte [Azure Resource Manager vs implantação clássica](../azure-resource-manager/management/deployment-models.md).

[!INCLUDE [security-center-jit-description](../../includes/security-center-jit-description.md)]

## <a name="configure-jit-on-a-vm"></a>Configurar o JIT em uma VM

Há três maneiras de configurar uma política JIT em uma VM:

- [Configurar o acesso JIT na central de segurança do Azure](#jit-asc)
- [Configurar o acesso JIT em uma página de VM do Azure](#jit-vm)
- [Configurar uma política JIT em uma VM programaticamente](#jit-program)

## <a name="configure-jit-in-azure-security-center"></a>Configurar o JIT na central de segurança do Azure

Na central de segurança, você pode configurar uma política JIT e solicitar o acesso a uma VM usando uma política JIT

### Configurar o acesso JIT em uma VM na central de segurança<a name="jit-asc"></a>

1. Abra o painel **Central de Segurança**.

1. No painel à esquerda, selecione **Acesso à VM Just-In-Time**.

    ![Bloco de acesso JIT à VM](./media/security-center-just-in-time/just-in-time.png)

    A janela de **acesso da VM just-in-time** é aberta e mostra informações sobre o estado de suas VMs:

    - **Configurado**: VMs que foram configuradas para dar suporte ao acesso JIT à VM. Os dados apresentados são da última semana e incluem, para cada VM, o número de solicitações aprovadas, a data e a hora do último acesso e o último usuário.
    - **Recomendado** – VMs que podem dar suporte ao acesso à VM just-in-time, mas não foram configuradas para. Recomenda-se que você habilite para essas VMs o controle de acesso JIT à VM.
    - **Nenhuma recomendação** – as razões que podem fazer com que uma VM não seja recomendada são:
      - NSG ausente: a solução Just-In-Time exige que um NSG esteja em vigor.
      - VM Clássica: o acesso Just-In-Time à VM pela Central de Segurança atualmente oferece suporte apenas às VMs implantadas por meio do Azure Resource Manager. Não há suporte para uma implantação clássica na solução Just-In-Time. 
      - Outro – uma VM estará nessa categoria se a solução just-in-time estiver desativada na política de segurança da assinatura ou no grupo de recursos, ou se a VM não tiver um IP público e não tiver um NSG em vigor.

1. Selecione a guia **Recomendado**.

1. Em **máquina virtual**, clique nas VMs que você deseja habilitar. Isso coloca uma marca de seleção ao lado de uma VM.

      ![Habilitar acesso Just-In-Time](./media/security-center-just-in-time/enable-just-in-time.png)

1. Clique em **habilitar JIT em VMs**. Um painel é aberto exibindo as portas padrão recomendadas pela central de segurança do Azure:
    - 22: SSH
    - 3389: RDP
    - 5985: WinRM 
    - 5986: WinRM
1. Opcionalmente, você pode adicionar portas personalizadas à lista:

      1. Clique em **Adicionar**. A janela **Adicionar configuração de porta** é aberta.
      1. Para cada porta que você optar por configurar, tanto padrão quanto personalizada, você pode personalizar as seguintes configurações:
            - **Tipo de protocolo**: o protocolo permitido nesta porta quando uma solicitação for aprovada.
            - **Endereços IP de origem permitida**: os intervalos de IP permitidos nesta porta quando uma solicitação for aprovada.
            - **Tempo máximo de solicitação**: o intervalo de tempo máximo durante o qual uma porta específica pode ficar aberta.

     1. Clique em **OK**.

1. Clique em **Save** (Salvar).

> [!NOTE]
>Quando o acesso à VM JIT está habilitado para uma VM, a central de segurança do Azure cria as regras "negar todo o tráfego de entrada" para as portas selecionadas nos grupos de segurança de rede associados e no firewall do Azure com ele. Se outras regras tiverem sido criadas para as portas selecionadas, as regras existentes têm prioridade sobre as novas regras "negar todo o tráfego de entrada". Se não houver nenhuma regra existente nas portas selecionadas, a nova regra "negar todo o tráfego de entrada" assumirá a prioridade mais alta nos grupos de segurança de rede e no firewall do Azure.


## <a name="request-jit-access-via-security-center"></a>Solicitar acesso JIT por meio da central de segurança

Para solicitar acesso a uma VM por meio da central de segurança:

1. Em **Acesso à VM Just-In-Time**, selecione a guia **Configurado**.

1. Em **máquina virtual**, clique nas VMs às quais você deseja solicitar acesso. Isso coloca uma marca de seleção ao lado da VM.

    - O ícone na coluna **detalhes da conexão** indica se o JIT está habilitado no NSG ou no FW. Se ele estiver habilitado em ambos, somente o ícone de firewall será exibido.

    - A coluna **detalhes da conexão** fornece as informações necessárias para conectar a VM e suas portas abertas.

      ![Solicitar acesso just-in-time](./media/security-center-just-in-time/request-just-in-time-access.png)

1. Clique em **solicitar acesso**. A janela **solicitar acesso** é aberta.

      ![Detalhes do JIT](./media/security-center-just-in-time/just-in-time-details.png)

1. Em **Solicitar acesso**, configure para cada VM as portas a abrir, os endereços IP de origem para os quais a porta fica aberta e o intervalo de tempo durante o qual a porta fica aberta. Só será possível solicitar acesso às portas configuradas na política just-in-time. Cada porta tem um tempo máximo permitido derivado da política Just-In-Time.

1. Clique em **abrir portas**.

> [!NOTE]
> Se um usuário que está solicitando acesso estiver atrás de um proxy, a opção **Meu IP** pode não funcionar. Pode ser que você precise definir o intervalo completo de endereços IP da organização.



## <a name="edit-a-jit-access-policy-via-security-center"></a>Editar uma política de acesso JIT por meio da central de segurança

Você pode alterar a política Just-In-Time existente de uma VM adicionando e configurando uma nova porta a ser aberta para essa VM ou alterando qualquer outro parâmetro relacionado a uma porta já protegida.

Para editar a política Just-In-Time existente de uma VM:

1. Em **Configurado**, sob **VMs**, selecione a VM à qual será adicionada uma porta clicando nos três pontos dentro da linha dessa VM. 

1. Selecione **Editar**.

1. Em **Configuração de acesso JIT à VM**, você pode editar as configurações existentes de uma porta já protegida ou pode adicionar uma nova porta personalizada. 
  ![Acesso JIT à VM](./media/security-center-just-in-time/edit-policy.png)



## <a name="audit-jit-access-activity-in-security-center"></a>Auditar atividade de acesso JIT na central de segurança

Você pode obter informações sobre as atividades de VM usando a pesquisa de logs. Para exibir os logs:

1. Em **Acesso à VM Just-In-Time**, selecione a guia **Configurado**.
2. Em **VMs**, selecione uma VM para exibir informações sobre como clicar nos três pontos dentro da linha dessa VM e selecione log de **atividades** no menu. O **log de atividades** é aberto.

   ![Selecionar o log de atividades](./media/security-center-just-in-time/select-activity-log.png)

   **Log de atividades** fornece uma exibição filtrada das operações anteriores dessa VM junto com a hora, a data e a assinatura.

Você pode baixar as informações de log selecionando **Clique aqui para baixar todos os itens como CSV**.

Modifique os filtros e clique em **aplicar** para criar uma pesquisa e um log.



## Configurar o acesso JIT na página de uma VM do Azure<a name="jit-vm"></a>

Para sua conveniência, você pode se conectar a uma VM usando o JIT diretamente de dentro da página da VM na central de segurança.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>Configurar o acesso JIT em uma VM por meio da página VM do Azure

Para tornar mais fácil distribuir acesso Just-In-Time entre suas VMs, você pode definir uma VM para permitir apenas o acesso Just-In-Time diretamente de dentro da VM.

1. No [portal do Azure](https://ms.portal.azure.com), procure e selecione **máquinas virtuais**. 
2. Selecione a máquina virtual que você deseja limitar ao acesso just-in-time.
3. No menu, selecione **configuração**.
4. Em **acesso just-in-time**, selecione **habilitar just-in-time**. 

Isso permite o acesso Just-In-Time para a VM usando as seguintes configurações:

- Servidores Windows:
    - RDP porta 3389
    - Três horas de acesso máximo permitido
    - Os endereços IP de origem permitidos estão definidos como qualquer
- Servidores Linux:
    - SSH porta 22
    - Três horas de acesso máximo permitido
    - Os endereços IP de origem permitidos estão definidos como qualquer
     
Se uma VM já tiver o Just-In-Time habilitado quando você acessar a página de configuração, você poderá ver que o Just-In-Time está habilitado e você pode usar o link para abrir a política na Central de Segurança do Azure para exibir e alterar as configurações.

![configuração de JIT na vm](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-an-azure-vms-page"></a>Solicitar acesso JIT a uma VM por meio da página de uma VM do Azure

No portal do Azure, ao tentar se conectar a uma VM, o Azure verifica se você configurou uma política de acesso Just-In-Time naquela VM.

- Se você tiver uma política JIT configurada na VM, poderá clicar em **solicitar acesso** para conceder acesso de acordo com a política JIT definida para a VM. 

  >![solicitação JIT](./media/security-center-just-in-time/jit-request.png)

  O acesso é solicitado com os seguintes parâmetros padrão:

  - **IP de origem**: ' any ' (*) (não pode ser alterado)
  - **intervalo de tempo**: três horas (não pode ser alterado) <!--Isn't this set in the policy-->
  - **número da porta** Porta RDP 3389 para Windows/porta 22 para Linux (pode ser alterada)

    > [!NOTE]
    > Depois que uma solicitação for aprovada para uma VM protegida pelo firewall do Azure, a central de segurança fornecerá ao usuário os detalhes de conexão apropriados (o mapeamento de porta da tabela DNAT) a ser usada para se conectar à VM.

- Se você não tiver o JIT configurado em uma VM, será solicitado que você configure uma política JIT nele.

  ![Solicitação de JIT](./media/security-center-just-in-time/jit-prompt.png)

## Configurar uma política JIT em uma VM programaticamente<a name="jit-program"></a>

Você pode configurar e usar Just-In-Time por meio de APIs REST e do PowerShell.

### <a name="jit-vm-access-via-rest-apis"></a>Acesso à VM JIT por meio de APIs REST

O recurso de acesso Just-In-Time à VM pode ser usado por meio da API da Central de Segurança do Azure. Você pode obter informações sobre VMs configuradas, adicionar novos, solicitar o acesso a uma VM e mais, por meio dessa API. Consulte [Políticas de acesso à rede JIT](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies) para saber mais sobre a API REST de Just-In-Time.

### <a name="jit-vm-access-via-powershell"></a>Acesso à VM JIT por meio do PowerShell

Para usar a solução de acesso Just-In-Time à VM por meio do PowerShell, use os cmdlets oficiais do PowerShell da Central de Segurança do Azure e, especificamente, `Set-AzJitNetworkAccessPolicy`.

O exemplo a seguir define uma política de acesso Just-In-Time à VM para uma VM específica e define o seguinte:

1.  Fechar as portas 22 e 3389.

2.  Definir uma janela de tempo máximo de 3 horas para cada, de modo que possam ser abertas por solicitação aprovada.
3.  Permitir que o usuário que está solicitando acesso controle os endereços IP de origem e permitir que o usuário estabeleça uma sessão com êxito mediante uma solicitação de acesso Just-In-Time aprovada.

Para fazer isso, execute o seguinte no PowerShell:

1.  Atribuir uma variável que contenha a política de acesso Just-In-Time à VM para uma VM:

        $JitPolicy = (@{
         id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})

2.  Inserir a política de acesso Just-In-Time à VM em uma matriz:
    
        $JitPolicyArr=@($JitPolicy)

3.  Configurar a política de acesso Just-In-Time à VM na VM selecionada:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

### <a name="request-access-to-a-vm-via-powershell"></a>Solicitar acesso a uma VM por meio do PowerShell

No exemplo a seguir, é possível ver uma solicitação de acesso Just-In-Time à VM para uma VM específica, na qual é solicitada a abertura da porta 22 para um endereço IP específico e por um período de tempo específico:

Execute o seguinte no PowerShell:
1.  Configurar as propriedades de acesso de solicitação da VM

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Insira os parâmetros de solicitação de acesso da VM em uma matriz:

        $JitPolicyArr=@($JitPolicyVm1)
3.  Enviar o acesso da solicitação (use a ID do recurso que você recebeu na etapa 1)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Para obter mais informações, consulte a [documentação do cmdlet do PowerShell](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview).


## <a name="automatic-cleanup-of-redundant-jit-rules"></a>Limpeza automática de regras JIT redundantes 

Sempre que você atualiza uma política JIT, uma ferramenta de limpeza é executada automaticamente para verificar a validade de todo o conjunto de regras. A ferramenta procura incompatibilidades entre as regras em sua política e as regras no NSG. Se a ferramenta de limpeza encontrar uma incompatibilidade, ela determinará a causa e, quando for seguro, removerá as regras internas que não são mais necessárias. O limpador nunca exclui as regras que você criou.

Exemplos de cenários quando o limpador pode remover uma regra interna:

- Quando existem duas regras com definições idênticas e uma tem uma prioridade mais alta do que a outra (ou seja, a regra de prioridade mais baixa nunca será usada)
- Quando uma descrição de regra inclui o nome de uma VM que não corresponde ao IP de destino na regra 


## <a name="next-steps"></a>Próximas etapas

Nesse artigo você aprendeu como o acesso Just-In-Time à VM na Central de Segurança o ajuda a controlar o acesso às suas máquinas virtuais do Azure.

Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configurando políticas de segurança](tutorial-security-policy.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciar recomendações de segurança](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger seus recursos do Azure.
- [Monitoramento da integridade da segurança](security-center-monitoring.md) – saiba como monitorar a integridade dos seus recursos do Azure.