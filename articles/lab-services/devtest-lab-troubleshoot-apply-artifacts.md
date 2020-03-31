---
title: Problemas de solução de problemas com artefatos no Azure DevTest Labs | Microsoft Docs
description: Aprenda a solucionar problemas que ocorrem ao aplicar artefatos em uma máquina virtual Do Zure DevTest Labs.
services: devtest-lab
documentationcenter: na
author: spelluru
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: fc5051667100a2ebaa01b7815f825fadd766b08f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456982"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Solucionando problemas ao aplicar artefatos em uma máquina virtual Azure DevTest Labs
A aplicação de artefatos em uma máquina virtual pode falhar por várias razões. Este artigo orienta você através de alguns dos métodos para ajudar a identificar possíveis causas.

Se você precisar de mais ajuda em qualquer ponto deste artigo, você pode entrar em contato com os especialistas do Azure DevTest Labs (DTL) nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte.   

> [!NOTE]
> Este artigo se aplica tanto a máquinas virtuais Windows quanto não-Windows. Embora existam algumas diferenças, elas serão explicitamente chamadas neste artigo.

## <a name="quick-troubleshooting-steps"></a>Etapas rápidas para solucionar problemas
Verifique se a VM está funcionando. O DevTest Labs exige que a VM esteja em execução e que o [Agente de Máquina Virtual (VM Agent) do Microsoft Azure](../virtual-machines/extensions/agent-windows.md) esteja instalado e pronto.

> [!TIP]
> No **portal Azure,** navegue até a página **Gerenciar artefatos** para a VM para ver se a VM está pronta para aplicar artefatos. Você vê uma mensagem no topo dessa página. 
> 
> Usando **o Azure PowerShell,** inspecione o sinalizador **canApplyArtifacts**, que é devolvido somente quando você expande em uma operação GET. Veja o comando de exemplo a seguir:

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>Formas de solucionar problemas 
Você pode solucionar problemas de VMs criados usando o DevTest Labs e o modelo de implantação do Gerenciador de Recursos usando um dos seguintes métodos:

- **Portal Azure** - ótimo se você precisar obter rapidamente uma dica visual do que pode estar causando o problema.
- **Azure PowerShell** - se você estiver confortável com um prompt PowerShell, consulta rapidamente os recursos do DevTest Labs usando os cmdlets do Azure PowerShell.

> [!TIP]
> Para obter mais informações sobre como revisar a execução de artefatos dentro de uma VM, consulte [Diagnosticar falhas de artefatos no laboratório](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="symptoms-causes-and-potential-resolutions"></a>Sintomas, causas e possíveis resoluções 

### <a name="artifact-appears-to-hang"></a>Artefato parece ser enforcado   
Um artefato parece ser pendurado até que um período de tempo definido expira, e o artefato é marcado como **Falha**.

Quando um artefato parece ser enforcado, primeiro determine onde está preso. Um artefato pode ser bloqueado em qualquer uma das seguintes etapas durante a execução:

- **Durante a solicitação inicial.** O DevTest Labs cria um modelo do Azure Resource Manager para solicitar o uso do CSE (Custom Script Extension, extensão de script personalizado). Portanto, nos bastidores, uma implantação de grupo de recursos é acionada. Quando um erro nesse nível acontece, você recebe detalhes nos Registros de **atividades** do grupo de recursos para a VM em questão.  
    - Você pode acessar o registro de atividades da barra de navegação da página do laboratório VM. Ao selecioná-lo, você vê uma entrada para **aplicar artefatos à máquina virtual** (se a operação de artefatos aplicados foi acionada diretamente) ou adicionar ou modificar **máquinas virtuais** (se a operação de artefatos aplicados fazia parte do processo de criação de VM).
    - Procure por erros nestas entradas. Às vezes, o erro não será marcado de acordo, e você terá que investigar cada entrada.
    - Ao investigar os detalhes de cada entrada, certifique-se de revisar o conteúdo da carga útil JSON. Você pode ver um erro na parte inferior desse documento.
- **Ao tentar executar o artefato.** Pode ser por causa de problemas de rede ou armazenamento. Consulte a respectiva seção mais tarde neste artigo para obter detalhes. Também pode acontecer por causa da forma como o roteiro é de autoria. Por exemplo: 
    - Um script PowerShell tem **parâmetros obrigatórios,** mas não é necessário passar um valor para ele, seja porque você permite que o usuário o deixe em branco, ou porque você não tem um valor padrão para a propriedade no arquivo de definição artifactfile.json. O script será travado porque está aguardando a entrada do usuário.
    - Um script PowerShell **requer a entrada** do usuário como parte da execução. Os scripts devem ser escritos para funcionar silenciosamente sem exigir qualquer intervenção do usuário.
- **O agente vm demora muito para estar pronto.** Quando a VM é iniciada, ou quando a extensão de script personalizada é instalada pela primeira vez para atender à solicitação de aplicação de artefatos, a VM pode exigir a atualização do Agente VM ou esperar que o Agente VM seja inicializado. Pode haver serviços nos quais o Agente VM depende que estão levando muito tempo para inicializar. Nesses casos, consulte [a visão geral do Azure Virtual Machine Agent](../virtual-machines/extensions/agent-windows.md) para mais solução de problemas.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-script"></a>Para verificar se o artefato parece ser pendurado por causa do script

1. Faça login na máquina virtual em questão.
2. Copie o script localmente na máquina virtual ou `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>`localize-o na máquina virtual em . É o local onde os scripts dos artefatos são baixados.
3. Usando um prompt de comando elevado, execute o script localmente, fornecendo os mesmos valores de parâmetro usados para causar o problema.
4. Determine se o script sofre de algum comportamento indesejado. Nesse caso, solicite uma atualização para o artefato (se for do repo público); ou, faça as correções você mesmo (se for do seu repo privado).

> [!TIP]
> Você pode corrigir problemas com artefatos hospedados em nosso [repo público](https://github.com/Azure/azure-devtestlab) e enviar as alterações para nossa revisão e aprovação. Consulte a seção **Contribuições** no documento [README.md.](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md)
> 
> Para obter informações sobre como escrever seus próprios artefatos, consulte [AUTHORING.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) documento.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-vm-agent"></a>Para verificar se o artefato parece ser pendurado por causa do agente VM:
1. Faça login na máquina virtual em questão.
2. O uso do File Explorer navega para **C:\WindowsAzure\logs**.
3. Localizar e abrir o arquivo **WaAppAgent.log**.
4. Procure por entradas que mostram quando o Agente VM começa e quando ele está terminando a inicialização (ou seja, o primeiro batimento cardíaco é enviado). Favoreça entradas mais recentes ou especificamente as em torno do período de tempo para o qual você experimenta o problema.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    Neste exemplo, você pode ver que o tempo de início do VM Agent levou 10 minutos e 20 segundos porque um batimento cardíaco foi enviado. A causa neste caso foi o serviço OOBE demorando muito para começar.

> [!TIP]
> Para obter informações gerais sobre as extensões do Azure, consulte [extensões e recursos da máquina virtual Do Zure](../virtual-machines/extensions/overview.md).

## <a name="storage-errors"></a>Erros de armazenamento
O DevTest Labs requer acesso à conta de armazenamento do laboratório criada para armazenar artefatos. Quando o DevTest Labs aplica um artefato, ele lerá a configuração do artefato e seus arquivos a partir dos repositórios configurados. Por padrão, o DevTest Labs configura o acesso ao **repo de artefatos públicos**.

Dependendo de como uma VM está configurada, ela pode não ter acesso direto a este repo. Portanto, por design, o DevTest Labs armazena os artefatos em uma conta de armazenamento que é criada quando o laboratório é inicializado pela primeira vez.

Se o acesso a essa conta de armazenamento for bloqueado de alguma forma, como quando o tráfego estiver bloqueado da VM para o serviço de armazenamento Azure, você poderá ver um erro semelhante ao seguinte:

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

O erro acima apareceria na seção **Mensagem de implantação** na página **de resultados do Artefato** em **Gerenciar artefatos**. Ele também aparecerá nos **Registros de Atividades** o grupo de recursos da máquina virtual em questão.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Para garantir que a comunicação com o serviço de armazenamento do Azure não esteja bloqueada:

- **Verifique se há grupos adicionais de segurança de rede (NSG)**. Pode ser que uma política de assinatura tenha sido adicionada onde os NSGs são configurados automaticamente em todas as redes virtuais. Também afetaria a rede virtual padrão do laboratório, se usada, ou outra rede virtual configurada em seu laboratório, usada para a criação de VMs.
- **Verifique a conta de armazenamento do laboratório padrão** (ou seja, a primeira conta de armazenamento criada quando o laboratório foi criado, cujo\<nome\>geralmente começa com a letra "a" e termina com um número de vários dígitos, ou seja, um nome de laboratório #).
    1. Navegue até o grupo de recursos do laboratório.
    2. Localize o recurso da conta de **armazenamento**de tipo , cujo nome corresponde à convenção.
    3. Navegue até a página da conta de armazenamento chamada **Firewalls e redes virtuais.**
    4. Certifique-se de que está configurado para **todas as redes**. Se a opção **Redes selecionadas** for selecionada, certifique-se de que as redes virtuais do laboratório usadas para criar VMs sejam adicionadas à lista.

Para obter uma solução de problemas mais aprofundada, consulte [Configure firewalls de armazenamento Azure e redes virtuais](../storage/common/storage-network-security.md).

> [!TIP]
> **Verifique as regras do grupo de segurança da rede**. Use [verificação de fluxo IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) para confirmar se uma regra em um grupo de segurança de rede está bloqueando o tráfego para ou a partir de uma máquina virtual. Você também pode rever as regras eficazes do grupo de segurança para garantir que a regra **Allow** NSG de entrada exista. Para saber mais, confira [Usar regras de segurança efetivas para solucionar problemas de fluxo de tráfego de VM](../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="other-sources-of-error"></a>Outras fontes de erro
Existem outras fontes de erro menos freqüentes. Certifique-se de avaliar cada um para ver se isso se aplica ao seu caso. Aqui está um deles: 

- **Token de acesso pessoal expirado para o repo privado**. Quando expirado, o artefato não será listado e quaisquer scripts que se refiram a artefatos de um repositório com um token de acesso privado expirado falharão em conformidade.

## <a name="next-steps"></a>Próximas etapas
Se nenhum desses erros ocorreu e você ainda não pode aplicar artefatos, você pode registrar um incidente de suporte ao Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.

