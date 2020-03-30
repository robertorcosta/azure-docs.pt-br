---
title: Patch do sistema operacional Windows em seu cluster do Service Fabric
description: Este artigo discute como automatizar o patch do sistema operacional em um cluster de malha de serviço usando o Patch Orchestration Application.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: atsenthi
ms.openlocfilehash: 857a4da0b24d600ecc572933af578e2e8faf501a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366315"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Patch do sistema operacional Windows em seu cluster do Service Fabric

> 
> [!IMPORTANT]
> A partir de 30 de abril de 2019, a versão 1.2.* do Patch Orchestration Application não é mais suportada. Certifique-se de atualizar para a versão mais recente.

> [!NOTE]
> Obter [upgrades automáticos de imagem do sistema operacional no conjunto de escalas da máquina virtual](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) é a melhor prática para manter seu sistema operacional corrigido no Azure. Os upgrades automáticos de imagem do sistema operacional baseados em escala de máquina virtual exigirão uma durabilidade de prata ou maior em um conjunto de escalas.
>

 O Poa (Patch Orchestration Application, aplicativo de orquestração de patches) é um invólucro em torno do serviço Azure Service Fabric Repair Manager, que permite o agendamento de patches do sistema operacional baseado em configuração para clusters hospedados não-Azure. O POA não é necessário para clusters não hospedados no Azure, mas o agendamento da instalação de patches por domínio de atualização é necessário para corrigir hosts de cluster service fabric sem incorrer em tempo de inatividade.

POA é um aplicativo de malha de serviço que automatiza o patch de sistema operacional em um cluster de malha de serviço sem incorrer em tempo de inatividade.

O POA fornece os seguintes recursos:

- **Instalação da atualização automática do sistema operacional**. Atualizações do sistema operacional são baixadas e instaladas automaticamente. Os nós de cluster são reinicializados conforme necessário sem incorrer no tempo de inatividade do cluster.

- **Aplicação de patch com suporte a cluster e integração de integridade**. Enquanto o POA está aplicando atualizações, ele monitora a saúde dos nódulos de cluster. Os nós de cluster são atualizados um nó de cada vez ou um domínio de atualização por vez. Se a saúde do cluster cair por causa do processo de correção, o patch ingestão é interrompido para evitar o agravamento do problema.

## <a name="internal-details-of-poa"></a>Detalhes internos do POA

Poa é composto pelos seguintes subcomponentes:

- **Serviço de Coordenador**: este serviço com estado é responsável por:
    - Coordenar o trabalho do Windows Update em todo o cluster.
    - Armazenar o resultado das operações concluídas do Windows Update.

- **Serviço de Agente do Nó**: é um serviço sem estado, executado em todos os nós de cluster do Service Fabric. O serviço é responsável por:
    - Inicialização de NTService do Agente do Nó.
    - Monitoramento do NTService do Agente do Nó.

- **NTService do Agente do Nó**: este serviço Windows NT é executado em um privilégio de nível superior (SISTEMA). Em comparação, o Serviço de Agente do Nó e o Serviço de Coordenador são executados em um nível inferior de privilégio (SERVIÇO DE REDE). O serviço é responsável por executar os seguintes trabalhos do Windows Update em todos os nós de cluster:
    - Desabilitando as atualizações automáticas do Windows no nó.
    - Baixar e instalar atualizações do Windows de acordo com a política que o usuário forneceu.
    - Reiniciar a instalação de atualizações pós-Windows da máquina.
    - Carregar os resultados das atualizações do Windows para o Serviço do Coordenador.
    - Informando se uma operação falhou depois de esgotar todas as tentativas.

> [!NOTE]
> O POA usa o serviço Service Fabric Repair Manager para desativar ou habilitar o nó e realizar verificações de saúde. A tarefa de reparo criada pelo POA rastreia o progresso do Windows Update para cada nó.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> A versão mínima .NET Framework necessária é 4.6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Habilite o serviço do Gerenciador de Reparos (se ele ainda não estiver em execução)

O POA requer que o serviço de Gerenciador de Reparos seja habilitado no cluster.

#### <a name="azure-clusters"></a>Clusters do Azure

Os clusters Azure no nível de durabilidade de prata têm o serviço Repair Manager ativado por padrão. Os clusters azure no nível de durabilidade do ouro podem ou não ter o serviço de Gerenciador de Reparos ativado, dependendo de quando esses clusters foram criados. Os clusters azure no nível de durabilidade de bronze, por padrão, não têm o serviço Repair Manager ativado. Se o serviço já estiver habilitado, você poderá vê-lo sendo executado na seção de serviços do sistema no Service Fabric Explorer.

##### <a name="the-azure-portal"></a>O Portal do Azure
Você pode ativar o Gerenciador de Reparos do portal Azure quando configurar o cluster. Ao configurar o cluster, selecione a opção **Incluir o Gerenciador de reparos** em **recursos adicionais**.

![Imagem de habilitação do Gerenciador de Reparos do portal Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>O modelo de implantação do Azure Resource Manager
Alternativamente, você pode usar o [modelo de implantação do Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) para habilitar o serviço do Gerenciador de Reparos em clusters de malha de serviço novos e existentes. Obtenha o modelo para o cluster que você deseja implantar. Você pode usar os modelos de exemplo ou criar um modelo de implantação do Azure Resource Manager personalizado. 

Para habilitar o serviço do Gerenciador de reparos usando o [modelo de modelo de implantação do Azure Resource Manager,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)faça o seguinte:

1. Verifique se `apiVersion` está definido como *visualização de 2017-07-01* para o recurso *Microsoft.ServiceFabric/clusters.* Se for diferente, você precisa `apiVersion` atualizar para *2017-07-01-preview* ou posterior:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. Habilite o serviço do `addonFeatures` Gerenciador `fabricSettings` de reparos adicionando a seguinte seção após a seção:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Depois de atualizar seu modelo de cluster com essas alterações, aplique-as e deixe a atualização terminar. Agora você pode ver o serviço de gerenciador de reparos sendo executado em seu cluster. Chama-se *fabric:/System/RepairManagerService* na seção de serviços do sistema no Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Clusters locais autônomos

Para habilitar o serviço do Gerenciador de reparos em um cluster de malha de serviço novo ou existente, você pode usar as [configurações de configuração para cluster windows autônomo](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Para habilitar o serviço do Gerenciador de Reparos:

1. Verifique se `apiVersion` nas [configurações gerais de cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) está definida como *04-2017* ou posterior, conforme mostrado aqui:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. Habilite o serviço do `addonFeatures` Gerenciador `fabricSettings` de reparos adicionando a seguinte seção após a seção, conforme mostrado aqui:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. Atualize seu manifesto de cluster com essas alterações usando o manifesto de cluster [atualizado, crie um novo cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) ou [atualize a configuração do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). 

   Depois que o cluster estiver sendo executado com um manifesto de cluster atualizado, você poderá ver o serviço Repair Manager em execução em seu cluster. Chama-se *fabric:/System/RepairManagerService*, e está na seção de serviços do sistema no Service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Configure atualizações do Windows para todos os nós

Atualizações automáticas do Windows podem levar à perda de disponibilidade, porque vários nós de cluster podem ser reiniciados ao mesmo tempo. PoA, por padrão, tenta desativar as atualizações automáticas do Windows em cada nó de cluster. No entanto, se as configurações forem gerenciadas por um administrador ou por uma Diretiva de Grupo, recomendamos definir a diretiva Windows Update para "Notificar antes de baixar" explicitamente.

## <a name="download-the-application-package"></a>Baixe o pacote de aplicativos

Para baixar o pacote do aplicativo, vá para a [página de versão do Aplicativo de Orquestração de Patches](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) no GitHub.

## <a name="configure-poa-behavior"></a>Configurar o comportamento poa

Você pode configurar o comportamento poa para atender às suas necessidades. Anular os valores padrão passando no parâmetro do aplicativo enquanto você está criando ou atualizando o aplicativo. Você pode fornecer parâmetros `ApplicationParameter` de `Start-ServiceFabricApplicationUpgrade` `New-ServiceFabricApplication` aplicação especificando para os cmdlets.

| Parâmetro        | Type                          | Detalhes |
|:-|-|-|
|MaxResultsToCache    |long                              | O número máximo de resultados do Windows Update que devem ser armazenados em cache. <br><br>O valor padrão é 3000, assumindo que: <br> &nbsp;&nbsp;- O número de nódulos é 20. <br> &nbsp;&nbsp;- O número de atualizações para um nó por mês é de 5. <br> &nbsp;&nbsp;- O número de resultados por operação pode ser de 10. <br> &nbsp;&nbsp;- Os resultados dos últimos três meses devem ser armazenados. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |A TaskApprovalPolicy indica a política a ser usada pelo Serviço do Coordinator para instalar atualizações do Windows em todos os nós de cluster do Service Fabric.<br><br>Os valores permitidos são: <br>*NodeWise*: As atualizações do Windows são instaladas um nó por vez. <br> *UpgradeDomainWise*: As atualizações do Windows são instaladas um domínio de atualização por vez. (No máximo, todos os nós pertencentes a um domínio de atualização podem ir para uma atualização do Windows.)<br><br> Para ajudar a decidir qual política é mais adequada para o seu cluster, consulte a seção [FAQ.](#frequently-asked-questions)
|LogsDiskQuotaInMB   |long  <br> (Padrão: *1024*)               | O tamanho máximo do aplicativo de orquestração de patches faz logon em MB, que pode ser persistido localmente em nomes.
| WUQuery               | string<br>(Padrão: *IsInstalled=0*)                | Consulta para obter atualizações do Windows. Para obter mais informações, consulte [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | *Boolean* <br> (padrão: false)                 | Use esse sinalizador para controlar quais atualizações devem ser baixadas e instaladas. Os seguintes valores são permitidos <br>true – instala somente as atualizações do sistema operacional Windows.<br>false – instala todas as atualizações disponíveis no computador.          |
| WUOperationTimeOutInMinutes | Int <br>(Padrão: *90*)                   | Especifica o tempo limite para qualquer operação do Windows Update (pesquisar, baixar ou instalar). Se a operação não for concluída dentro do tempo limite especificado, ela será anulada.       |
| WURescheduleCount     | Int <br> (Padrão: *5*)                  | O número máximo de vezes que o serviço remarca a atualização do Windows se uma operação falhar persistentemente.          |
| WURescheduleTimeInMinutes | Int <br>(Padrão: *30*) | O intervalo no qual o serviço reagenda as atualizações do Windows se a falha persistir. |
| WUFrequency           | Cadeia separada por comma (Padrão: *Semanal, quarta-feira, 7:00:00*)     | A freqüência para instalar atualizações do Windows. O formato e os valores possíveis são: <br>&nbsp;&nbsp;- Mensal: DD, HH:MM:SS (por exemplo, *Mensal, 5,12:22:32*)<br>Os valores permitidos para o campo DD (dia) são números de 1 a 28 e "último". <br> &nbsp;&nbsp;- Semanal, DAY, HH:MM:SS (por exemplo, *Semanal, terça-feira, 12:22:32*)  <br> &nbsp;&nbsp;- Diariamente, HH:MM:SS (por exemplo, *Diariamente, 12:22:32*)  <br> &nbsp;&nbsp;-  *Nenhum* indica que as atualizações do Windows não devem ser feitas.  <br><br> Os tempos estão na UTC.|
| AcceptWindowsUpdateEula | Boolean <br>(Padrão: *verdadeiro)* | Ao definir esse sinalizador, o aplicativo aceita o Contrato de licença do usuário final para o Windows Update em nome do proprietário do computador.              |

> [!TIP]
> Se você quiser que as atualizações `WUFrequency` do Windows aconteçam imediatamente, defina em relação ao tempo de implantação do aplicativo. Por exemplo, suponha que você tem um cluster de teste de cinco nós e planeja implantar o aplicativo em torno de 5:00 PM UTC. Se você assumir que a atualização ou implantação do aplicativo leva no máximo 30 minutos, defina a WUFrequency como *Diariamente, 17:30:00*.

## <a name="deploy-poa"></a>Implantar POA

1. Conclua todas as etapas necessárias para preparar o cluster.
1. Implantar POA como qualquer outro aplicativo de malha de serviço. Para implantá-lo usando o PowerShell, consulte [Implantar e remover aplicativos usando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
1. Para configurar o aplicativo no momento da implantação, passe o `ApplicationParameter` para o cmdlet `New-ServiceFabricApplication`. Para sua conveniência, fornecemos o script Deploy.ps1 juntamente com o aplicativo. Para usar o script:

    - Conectar a um cluster do Service Fabric usando `Connect-ServiceFabricCluster`.
    - Execute o script do PowerShell Deploy.ps1 com o valor `ApplicationParameter` apropriado.

> [!NOTE]
> Mantenha o script e a pasta de aplicativos *PatchOrchestrationApplication* no mesmo diretório.

## <a name="upgrade-poa"></a>Upgrade POA

Para atualizar sua versão POA usando o PowerShell, siga as instruções na atualização do [aplicativo Service Fabric usando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-poa"></a>Remover POA

Para remover o aplicativo, siga as instruções em [Implantar e remova os aplicativos usando o PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Para sua conveniência, fornecemos o script Undeploy.ps1 juntamente com o aplicativo. Para usar o script:

  - Conectar a um cluster do Service Fabric usando ```Connect-ServiceFabricCluster```.
  - Execute o script do PowerShell Undeploy.ps1.

> [!NOTE]
> Mantenha o script e a pasta de aplicativos *PatchOrchestrationApplication* no mesmo diretório.

## <a name="view-the-windows-update-results"></a>Exibir os resultados do Windows Update

O POA expõe ASAPIs REST para exibir os resultados históricos aos usuários. Aqui está um exemplo do resultado JSON:

```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

Os campos JSON são descritos na tabela a seguir:

Campo | Valores | Detalhes
-- | -- | --
OperationResult | 0 - Êxito<br> 1 - Êxito com erros<br> 2 - Falha<br> 3 - Anulado<br> 4 - Anulado com tempo limite | Indica o resultado da operação global, que normalmente envolve a instalação de uma ou mais atualizações.
ResultCode | O mesmo que OperationResult | Este campo indica o resultado da operação de instalação para uma atualização individual.
OperationType | 1 - Instalação<br> 0 - Pesquisa e Download| Por padrão, a instalação é a única OperaçãoTipo que é mostrada nos resultados.
WindowsUpdateQuery | O padrão é "IsInstalled=0" | A consulta do Windows Update que foi usada para procurar atualizações. Para obter mais informações, consulte [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
RebootRequired | true - a reinicialização foi necessária<br> false - a reinicialização não foi necessária | Indica se uma reinicialização foi necessária para concluir a instalação de atualizações.
OperaçãoStartTime | Datetime | Indica a hora em que a operação (Download/Instalação) foi iniciada.
OperaçãoTempo | Datetime | Indica o tempo em que a operação (Download/Instalação) foi concluída.
HResult | 0 - Bem sucedido<br> outro - fracasso| Indica o motivo da falha da atualização do Windows com a atualizaçãoID "7392acaf-6a85-427c-8a8d-058c25beb0d6".

Se nenhuma atualização estiver agendada ainda, o resultado JSON estará vazio.

Faça login no cluster para consultar os resultados do Windows Update. Descubra o endereço IP de réplica para o endereço principal do Serviço de&lt;Coordenador&gt;e&lt;abra&gt;a seguinte URL do navegador: http:// REPLICA-IP : ApplicationPort /PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

O ponto de extremidade REST para o Serviço do Coordenador tem uma porta dinâmica. Para verificar a URL exata, consulte Service Fabric Explorer. Por exemplo, os resultados *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults*estão disponíveis em .

![Imagem do ponto final REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

Se o proxy reverso estiver ativado no cluster, você também poderá acessar a URL de fora do cluster.

O ponto final que você precisa acertar é *http://&lt;&gt;SERVERURL :&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults*.

Para habilitar o proxy reverso no cluster, siga as instruções em [proxy reverso no Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Depois que o proxy reverso é configurado, todos os microserviços no cluster que expõem um ponto final HTTP são endereçados de fora do cluster.

## <a name="diagnostics-and-health-events"></a>Diagnósticos e eventos de saúde

Esta seção discute como depurar ou diagnosticar problemas com atualizações de patches através de POA em clusters de malha de serviço.

> [!NOTE]
> Para obter muitas das seguintes melhorias de autodiagnóstico, você deve ter a versão POA 1.4.0 ou posterior instalada.

O Agente de Nó NTService cria [tarefas de reparo](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) para instalar atualizações nos nós. Cada tarefa é então preparada pelo Serviço de Coordenadorde acordo com a política de aprovação da tarefa. Finalmente, as tarefas preparadas são aprovadas pelo Gerente de Reparos, que não aprova nenhuma tarefa se o cluster estiver em um estado insalubre. 

Para ajudá-lo a entender como as atualizações procedem em um nó, vamos passo a passo:

1. NodeAgentNTService, em execução em todos os nades, procura atualizações disponíveis do Windows no horário programado. Se as atualizações estiverem disponíveis, ele as baixa no nó.

1. Depois que as atualizações são baixadas, o Node Agent NTService cria uma tarefa de reparo correspondente para o nó com o nome *POS___\<unique_id>*. Você pode visualizar essas tarefas de reparo usando o [cmdlet Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) ou usando sfx na seção de detalhes do nó. Depois que a tarefa de reparo é criada, ela rapidamente se move para o estado [ *reivindicado* ](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet).

1. O Serviço de Coordenador procura periodicamente tarefas de reparo no estado *reclamado* e, em seguida, atualiza-as para *o Estado de preparação* com base na Política de Aprovação de Tarefas. Se a TaskApprovalPolicy estiver configurada como NodeWise, uma tarefa de reparo que corresponde a um nó será preparada somente se nenhuma outra tarefa de reparo estiver atualmente em *Estado de Preparação,* *Aprovação, Execução*ou *Restauração.* *Approved* 

   Da mesma forma, no caso do UpgradeWise TaskApprovalPolicy, existem tarefas nos estados anteriores apenas para nós que pertencem ao mesmo domínio de atualização. Depois que uma tarefa de reparo é movida para o estado *de preparação,* o nó de malha de serviço correspondente é [desativado](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) com a intenção definida como *Restart*.

   As versões POA 1.4.0 e posteriormente postar eventos com a propriedade ClusterPatchingStatus no CoordinatorService para exibir os nós que estão sendo corrigidos. As atualizações são instaladas no _poanode_0, conforme mostrado na imagem a seguir:

    [![Imagem do status de patching de cluster](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. Depois que o nó é desativado, a tarefa de reparo é transferida para o estado *de execução.* 
   
   > [!NOTE]
   > Um nó preso no estado *desativado* pode bloquear uma nova tarefa de reparo, que interrompe a operação de remendo no cluster.

1. Quando a tarefa de reparo estiver em estado *de execução,* a instalação de patch nesse nó começa. Depois que o patch for instalado, o nó pode ou não ser reiniciado, dependendo do patch. Em seguida, a tarefa de reparo é movida para o estado *de restauração,* que reativa o nó. A tarefa de reparo é então marcada como concluída.

   Nas versões POA 1.4.0 e posteriores, você pode encontrar o status da atualização visualizando os eventos de saúde no NodeAgentService com a propriedade WUOperationStatus-NodeName\<>. As seções destacadas nas imagens a seguir mostram o status das atualizações do Windows nos *poanode_0* e *poanode_2:*

   [![Status de operação da imagem do Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Status de operação da imagem do Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Você também pode obter os detalhes usando powershell. Para isso, você se conecta ao cluster e busca o estado da tarefa de reparo usando [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps). 
   
   No exemplo a seguir, a tarefa "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" está no estado *DownloadComplete.* Isso significa que as atualizações foram baixadas no *nó poanode_2,* e a instalação será tentada quando a tarefa for para o estado *de execução.*

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Se mais problemas permanecerem a serem encontrados, faça login em sua máquina virtual (VM) ou VMs e aprenda sobre eles usando registros de eventos do Windows. A tarefa de reparo mencionada anteriormente pode existir apenas nos seguintes subestados executores:

      ExecutorSubEstado | Descrição
    -- | -- 
      None=1 |  Implica que não havia uma operação em andamento no nó. O Estado pode estar em transição.
      DownloadConcluído=2 | Implica que a operação de download foi concluída com sucesso, falha parcial ou falha.
      Instalaçãoaprovada=3 | Implica que a operação de download foi concluída anteriormente e o Repair Manager aprovou a instalação.
      InstalaçãoInProgress=4 | Corresponde ao estado de execução da tarefa de reparo.
      Instalaçãoconcluída=5 | Implica que a instalação foi concluída com sucesso, sucesso parcial ou fracasso.
      Reinicialização solicitada=6 | Implica que a instalação do patch foi concluída e há uma ação de reinicialização pendente no nó.
      RestartNotNeeded=7 |  Implica que a reinicialização não foi necessária após a instalação do patch ser concluída.
      Reinicializaçãoconcluída=8 | Implica que a reinicialização foi concluída com sucesso.
      OperaçãoConcluída=9 | A operação Do Windows Update foi concluída com sucesso.
      OperaçãoAborted=10 | Implica que a operação Windows Update foi abortada.

1. Nas versões POA 1.4.0 e posteriores, quando uma tentativa de atualização de nó é concluída, um evento com a propriedade "WUOperationStatus-[NodeName]" é publicado no NodeAgentService para notificá-lo quando a próxima tentativa de download e instalação das atualizações do Windows será iniciada. Isso é exibido na seguinte imagem:

     [![Status de operação da imagem do Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>Logs de diagnóstico

Os registros do aplicativo de orquestração de patches são coletados como parte dos registros de tempo de execução do Service Fabric.

Você pode capturar logs usando a ferramenta de diagnóstico ou pipeline de sua escolha. O POA usa os seguintes IDs fixos do provedor para registrar eventos através da [fonte do evento:](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Relatórios de integridade

A POA também publica relatórios de saúde contra o Serviço de Agente de Nó ou o Serviço coordenador nos seguintes cenários:

* O NTService do Agente do Nó está inativo

   Se o NTService do Agente do Nó estiver inativo em um nó, o relatório de integridade no nível de aviso será gerado no Serviço de Agente do Nó.

* O serviço do Gerenciador de Reparos não está habilitado

   Se o serviço de Gerenciador de Reparos não for encontrado no cluster, um relatório de saúde em nível de aviso será gerado para o Serviço de Coordenador.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: Por que eu vejo meu cluster em um estado de erro quando poa está em execução?**

R: Durante o processo de instalação, o POA desativa ou reinicia os nós, o que pode resultar temporariamente em um cluster insalubre.

Dependendo da política do aplicativo, qualquer nó pode cair durante uma operação de patches *ou* um domínio de atualização inteiro pode cair de uma só vez.

No final da instalação das atualizações do Windows, os nós são reativados após a reinicialização.

No exemplo a seguir, o cluster foi para um estado de erro temporariamente porque dois nós estavam inativos e a política MaxPercentageUnhealthyNodes foi violada. O erro é temporário até que a operação de correção possa começar.

![Imagem do cluster não íntegro](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Caso o problema persista, consulte a seção de Solução de problemas.

**P: O que posso fazer se poa está em um estado de aviso?**

R: Verifique se um relatório de saúde publicado contra o aplicativo indica a causa raiz. Geralmente, o aviso contém detalhes do problema. Se o problema for transitório, espera-se que a aplicação se recupere automaticamente.

**P: O que posso fazer se meu cluster não é saudável e eu preciso fazer uma atualização urgente do sistema operacional?**

R: O POA não instala atualizações enquanto o cluster não é saudável. Tente trazer seu cluster para um estado saudável para desbloquear o fluxo de trabalho POA.

**P: Devo definir TaskApprovalPolicy como "NodeWise" ou "UpgradeDomainWise" para o meu cluster?**

A: A configuração "UpgradeDomainWise" acelera o reparo geral do cluster, corrigindo em paralelo todos os nós que pertencem a um domínio de atualização. Durante o processo, os nós que pertencem a um domínio de atualização inteiro não estão disponíveis (no estado [ *desabilitado* ](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)).

Em contraste, a configuração "NodeWise" corrige apenas um nó por vez, o que implicaria que o patch geral do cluster pode levar mais tempo. No entanto, apenas um nó no máximo estaria indisponível (no estado *de Disabled)* durante o processo de correção.

Se o cluster puder tolerar a execução em um número N-1 de domínios de atualização durante o ciclo de patches (onde N é o número total de domínios de atualização em seu cluster), você pode definir a política como "UpgradeDomainWise". Caso contrário, defina-o como "NodeWise".

**Q: Quanto tempo leva para remendar um nó?**

R: Corrigir um nó pode levar de [minutos](https://www.microsoft.com/en-us/wdsi/definitions)(por exemplo, atualizações de definição do Windows Defender) a horas (por exemplo, [atualizações cumulativas do Windows](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). O tempo necessário para corrigir um nó depende principalmente de: 
 - O tamanho das atualizações.
 - O número de atualizações, que devem ser aplicadas em uma janela de correção.
 - O tempo necessário para instalar as atualizações, reinicializar o nó (se necessário) e terminar as etapas de instalação pós-reinicialização.
 - O desempenho da VM ou máquina, e condições de rede.

**P: Quanto tempo leva para remendar um cluster inteiro?**

A: O tempo necessário para corrigir um cluster inteiro depende de:

- O tempo necessário para remendar um nó.

- A política do Serviço do Coordinator. A política padrão, "NodeWise", resulta em corrigir apenas um nó por vez, uma abordagem mais lenta do que o uso de "UpgradeDomainWise". 

   Por exemplo: Se um nó leva ~1 hora para ser corrigido, corrigir um cluster de 20 nós (mesmo tipo de nó) com 5 domínios de atualização, cada um contendo 4 nados, requer:
    - Para "NodeWise": ~20 horas.
    - Para "UpgradeDomainWise": ~5 horas.

- A carga do cluster. Cada operação de correção requer a realocação da carga de trabalho do cliente para outros nós disponíveis no cluster. Um nó que está sendo corrigido estaria em estado [ *de desativação* ](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) durante este tempo. Se o cluster estiver funcionando perto da carga máxima, o processo de desativação levaria mais tempo. Portanto, o processo geral de correção pode parecer lento em condições tão estressadas.

- Falhas de saúde do cluster durante o patch. Qualquer [degradação](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) na [saúde do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) interromperia o processo de correção. Este problema adicionaria ao tempo total necessário para corrigir todo o cluster.

**P: Por que vejo algumas atualizações nos resultados do Windows Update que são obtidas via API REST, mas não o histórico de atualizações do Windows na máquina?**

R: Algumas atualizações de produtos aparecem apenas em sua própria atualização ou histórico de patches. Por exemplo, as atualizações do Windows Defender podem ou não ser exibidas no histórico de atualizações do Windows no Windows Server 2016.

**P: O POA pode ser usado para corrigir meu cluster de v (cluster de um nó)?**

A: Não, poa não pode ser usado para corrigir um cluster de um nó. Essa limitação é por design, porque [serviços de sistema service fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) ou outros aplicativos de clientes incorreriam em tempo de inatividade. Portanto, os trabalhos de reparo de patches nunca seriam aprovados pelo Gerente de Reparos.

**P: Como faço para corrigir nós de cluster no Linux?**

R: Para saber como orquestrar atualizações no Linux, consulte [a escala da máquina virtual do Azure definir atualizações automáticas de imagem do Sistema Operacional](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

**P: Por que o ciclo de atualização está demorando tanto?**

R: Consulta para o resultado JSON, insira o ciclo de atualização para todos os nós e, em seguida, você pode tentar descobrir o tempo de instalação de atualização em cada nó usando OperationStartTime e OperationTime (OperationCompletTime). 

Se houver uma grande janela de tempo na qual nenhuma atualização está ocorrendo, o cluster pode estar em um estado de erro e, consequentemente, o Gerenciador de Reparos não pode aprovar nenhuma tarefa de reparo poa. Se a instalação da atualização estiver demorando muito em qualquer nó, esse nó pode não ter sido atualizado há algum tempo. Muitas atualizações podem estar pendentes de instalação, o que pode resultar em atrasos. 

Também pode ser possível que o patch de nó esteja bloqueado porque está preso no estado *de desabilitação.* Isso geralmente acontece porque desativar o nó pode levar a situações de quórum ou perda de dados.

**P: Por que o nó deve ser desativado quando poa está remendando-o?**

R: O POA desativa o nó com uma intenção *de Reiniciar,* que pára ou realoca todos os serviços de Malha de Serviço que estão sendo executados no nó. Poa faz isso para garantir que os aplicativos não acabem usando uma mistura de DLLs novos e antigos, por isso recomendamos não corrigir um nó sem desabilitá-lo.

**P: Qual é o número máximo de nós que podem ser atualizados usando POA?**

A: Poa usa service fabric repair manager para criar tarefas de reparo para nós para atualizações. No entanto, não mais de 250 tarefas de reparo podem estar presentes ao mesmo tempo. Atualmente, o POA cria tarefas de reparo para cada nó ao mesmo tempo, para que o POA não possa atualizar mais de 250 nós em um cluster. 

## <a name="disclaimers"></a>Avisos de Isenção de Responsabilidade

- O POA aceita o Contrato de Licença de Usuário Final para atualização do Windows em nome do usuário. A definição opcionalmente pode ser desativada na configuração do aplicativo.

- Poa coleta telemetria para acompanhar o uso e o desempenho. A telemetria do aplicativo segue a definição da configuração de telemetria do runtime do Service Fabric (ativada por padrão).

## <a name="troubleshooting"></a>Solução de problemas

Esta seção fornece possíveis soluções de solução de problemas para problemas com nós de correção.

### <a name="a-node-is-not-coming-back-to-up-state"></a>O nó não volta para o estado ativo

* O nó pode estar preso no estado *de desabilitação* porque:

  - Uma verificação de segurança está pendente. Para corrigir essa situação, verifique se há nós suficientes disponíveis em um estado íntegro.

* O nó pode estar preso no estado *de incapacidade* porque:

  - Foi desativado manualmente.
  - Foi desativado por causa de um trabalho de infra-estrutura do Azure em andamento.
  - Foi desativado temporariamente pela POA para corrigir o nó.

* O nó pode estar paralisado em um estado inativo porque:

  - Foi colocado em um estado de baixa manualmente.
  - Está passando por uma reinicialização (que pode ser acionada pelo POA).
  - Ele tem um VM ou máquina defeituoso, ou está tendo problemas de conectividade de rede.

### <a name="updates-were-skipped-on-some-nodes"></a>As atualizações foram ignoradas em alguns nós

O POA tenta instalar uma atualização do Windows de acordo com a política de reagendamento. O serviço tenta recuperar o nó e ignorar a atualização de acordo com a política do aplicativo.

Nesse caso, será gerado um relatório de integridade no nível de aviso em relação ao Serviço de Agente do Nó. O resultado do Windows Update também contém o possível motivo da falha.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>A saúde do cluster vai para o erro enquanto a atualização está sendo instalada

Uma atualização defeituosa do Windows pode reduzir a saúde de um aplicativo ou cluster em um determinado nó ou domínio de atualização. O POA descontinua qualquer operação subsequente do Windows Update até que o cluster esteja saudável novamente.

Um administrador deve intervir e determinar por que o aplicativo ou cluster se tornou insalubre por causa do Windows Update.

## <a name="poa-release-notes"></a>Notas de versão poa

>[!NOTE]
> Para as versões POA 1.4.0 e posteriores, você pode encontrar notas de versão e lançamentos na [página de versão do Patch Orchestration Application](https://github.com/microsoft/Service-Fabric-POA/releases/) no GitHub.

### <a name="version-110"></a>Version 1.1.0
- Versão pública

### <a name="version-111"></a>Versão 1.1.1
- Correção de bug no SetupEntryPoint de NodeAgentService que impediu a instalação de NodeAgentNTService.

### <a name="version-120"></a>Versão 1.2.0

- Correções de bugs em torno do fluxo de trabalho de reinício do sistema.
- Correção de bug na criação de tarefas RM devido à qual integridade seleção durante a preparação de tarefas de reparo não estava acontecendo conforme o esperado.
- Alterou o modo de inicialização do serviço Windows POANodeSvc de auto para automático atrasado.

### <a name="version-121"></a>Versão 1.2.1

- Correção de bug no fluxo de trabalho de redução de escala de cluster. Introduziu a lógica de coleta de lixo para tarefas de reparo POA pertencentes a nós inexistentes.

### <a name="version-122"></a>Versão 1.2.2

- Diversas correções de bugs.
- Binários agora são assinados.
- Adição do link do sfpkg para o aplicativo.

### <a name="version-130"></a>Versão 1.3.0

- A definição de InstallWindowsOSOnlyUpdates como falso agora instala todas as atualizações disponíveis.
- Alteração da lógica de desabilitação de atualizações automáticas. Isso corrige um bug em que as atualizações Automáticas não são desabilitadas no Server 2016 e posterior.
- Restrição de colocação parametrizada para ambos os microserviços de POA para casos de uso avançado.

### <a name="version-131"></a>Versão 1.3.1
- Corrigindo a regressão onde o POA 1.3.0 não funcionará no Windows Server 2012 R2 ou anteriormente devido a uma falha na desativação de atualizações automáticas. 
- Correção de bug, em que a configuração InstallWindowsOSOnlyUpdates sempre é escolhida como True.
- Alteração do valor padrão de InstallWindowsOSOnlyUpdates para False.

### <a name="version-132"></a>Versão 1.3.2
- Corrigindo um problema que afeta o ciclo de vida de correção em um nó, se houver nós com um nome que é um subconjunto do nome do nó atual. Para tais nós, é possível que o patch foi perdido ou uma reinicialização está pendente.
