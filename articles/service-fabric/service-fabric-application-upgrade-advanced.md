---
title: Tópicos avançados de atualização de aplicativos
description: Este artigo aborda alguns tópicos avançados relativos à atualização de um aplicativo do Service Fabric.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 09f3fdf1f26a13c6722eb039e132256f33be38ff
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845435"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Atualização do aplicativo Service Fabric: Tópicos avançados

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>Adicionar ou remover tipos de serviço durante uma atualização de aplicativo

Se um novo tipo de serviço é adicionado a um aplicativo publicado como parte de uma atualização, o novo serviço é adicionado ao aplicativo implantado. Essa atualização não afeta nenhuma das instâncias de serviço que já fazem parte do aplicativo, mas uma instância do tipo de serviço que foi adicionada deve ser criada para o novo tipo de serviço estar ativo (consulte [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Da mesma forma, os tipos de serviços também podem ser removidos de um aplicativo como parte de uma atualização. No entanto, todas as instâncias de serviço do tipo de serviço a ser removido devem ser removidas antes de prosseguir com a atualização (consulte [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime-preview"></a>Evitar quedas de conexão durante o tempo de inatividade planejado do serviço sem estado (versão prévia)

Para tempos de inatividade planejados de instância sem monitoração de estado, como atualização de aplicativo/cluster ou desativação de nó, as conexões podem ser descartadas devido ao ponto de extremidade exposto ser removido depois de ficar inativo.

Para evitar isso, configure o recurso *RequestDrain* (versão prévia) adicionando uma *duração de atraso de fechamento da instância* de réplica na configuração do serviço. Isso garante que o ponto de extremidade anunciado pela instância sem estado seja removido *antes que* o temporizador de atraso seja iniciado para fechar a instância. Esse atraso permite que as solicitações existentes sejam descarregadas normalmente antes que a instância realmente fique inativa. Os clientes são notificados sobre a alteração do ponto de extremidade pela função de retorno de chamada, para que possam reresolver o ponto de extremidade e evitar o envio de novas solicitações para a instância ficar inativa.

### <a name="service-configuration"></a>Configuração do serviço

Há várias maneiras de configurar o atraso no lado do serviço.

 * **Ao criar um novo serviço**, especifique um `-InstanceCloseDelayDuration`:

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>`
    ```

 * **Ao definir o serviço na seção padrões no manifesto do aplicativo**, atribua a propriedade `InstanceCloseDelayDurationSeconds`:

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **Ao atualizar um serviço existente**, especifique um `-InstanceCloseDelayDuration`:

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

### <a name="client-configuration"></a>Configuração do cliente

Para receber uma notificação quando um ponto de extremidade for alterado, os clientes poderão registrar um retorno de chamada (`ServiceManager_ServiceNotificationFilterMatched`) da seguinte maneira: 

```csharp
    var filterDescription = new ServiceNotificationFilterDescription
    {
        Name = new Uri(serviceName),
        MatchNamePrefix = true
    };
    fbClient.ServiceManager.ServiceNotificationFilterMatched += ServiceManager_ServiceNotificationFilterMatched;
    await fbClient.ServiceManager.RegisterServiceNotificationFilterAsync(filterDescription);

private static void ServiceManager_ServiceNotificationFilterMatched(object sender, EventArgs e)
{
      // Resolve service to get a new endpoint list
}
```

A notificação de alteração é uma indicação de que os pontos de extremidade foram alterados, o cliente deve reresolver os pontos de extremidade e não usar os pontos de extremidade que não são mais anunciados, pois eles ficarão inativos em breve.

### <a name="optional-upgrade-overrides"></a>Substituições de atualização opcionais

Além de definir as durações de atraso padrão por serviço, você também pode substituir o atraso durante a atualização de aplicativo/cluster usando a mesma opção (`InstanceCloseDelayDurationSec`):

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

A duração do atraso só se aplica à instância de atualização invocada e não altera as configurações de atraso de serviço individual. Por exemplo, você pode usar isso para especificar um atraso de `0` a fim de ignorar quaisquer atrasos de atualização pré-configurados.

## <a name="manual-upgrade-mode"></a>Modo de atualização manual

> [!NOTE]
> O modo de atualização *Monitorado* é recomendado para todas as atualizações do Service Fabric.
> O modo de atualização *UnmonitoredManual* só deve ser considerado em caso de atualização com falha ou suspensa. 
>
>

Em modo *Monitorado*, o Service Fabric aplica políticas de integridade para garantir que o aplicativo esteja íntegro conforme o andamento da atualização. Se as políticas de integridade forem violadas, a atualização será suspensa ou revertida automaticamente, dependendo do *FailureAction* especificado.

Em modo *UnmonitoredManual*, o administrador do aplicativo tem controle total sobre o andamento da atualização. Esse modo é útil durante a aplicação de políticas de avaliação de integridade personalizadas ou a realização de atualizações não convencionais para ignorar completamente o monitoramento de integridade (por exemplo, o aplicativo já está perdendo dados). Uma atualização em execução nesse modo se suspenderá depois de concluir cada UD e deverá ser retomada explicitamente usando-se [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). Quando uma atualização está suspensa e pronta para ser retomada pelo usuário, o estado de atualização mostrará *RollforwardPending* (consulte [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Por fim, o modo *UnmonitoredAuto* é útil para realizar iterações de atualização rápidas durante o desenvolvimento ou o teste do serviço porque nenhuma entrada do usuário é necessária e nenhuma política de integridade do aplicativo é avaliada.

## <a name="upgrade-with-a-diff-package"></a>Atualizar usando um pacote diff

Em vez de provisionar um pacote de aplicativos completo, as atualizações também podem ser realizadas pelo provisionamento de pacotes diferentes que contenham apenas os pacotes de dados/configurações/códigos atualizados com o manifesto do aplicativo completo e os manifestos do serviço completo. Os pacotes de aplicativo completos só são necessários para a instalação inicial de um aplicativo no cluster. Atualizações subsequentes podem ser de pacotes de aplicativos completos ou diferentes.  

Qualquer referência no manifesto do aplicativo ou nos manifestos do serviço de um pacote diferente que não possa ser encontrada no pacote de aplicativos é automaticamente substituída pela versão provisionada no momento.

Os cenários para usar um pacote diferente são:

* Quando você tiver um pacote de aplicativo grande que faça referência a vários arquivos de manifesto do serviço e/ou a vários pacotes de código, de configuração ou de dados.
* Quando você tiver um sistema de implantação que gere o layout de compilação diretamente do seu processo de compilação do aplicativo. Nesse caso, embora o código não tenha mudado, assemblies recém-criados têm uma soma de verificação diferente. O uso de um pacote de aplicativo completo exige que você atualize a versão de todos os pacotes de código. Com o uso de um pacote diff, você fornece somente os arquivos alterados e os arquivos de manifesto em que a versão foi alterada.

Quando um aplicativo é atualizado usando o Visual Studio, um pacote diff é publicado automaticamente. Para criar um pacote diff manualmente, o manifesto do aplicativo e os manifestos do serviço devem ser atualizados, mas apenas os pacotes alterados devem ser incluídos no pacote de aplicativos final.

Por exemplo, vamos começar com o aplicativo a seguir (números de versão fornecidos para facilitar a compreensão):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Vamos supor que você quisesse atualizar apenas o pacote de códigos de service1 usando um pacote diff. Seu aplicativo atualizado tem as seguintes alterações feitas na versão:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Nesse caso, você atualiza o manifesto do aplicativo para 2.0.0 e o manifesto do serviço para service1 para refletir a atualização do pacote de códigos. A pasta para o pacote de aplicativos teria a seguinte estrutura:

```text
app1/
  service1/
    code/
```

Em outras palavras, crie um pacote de aplicativos completo normalmente e, em seguida, remova todas as pastas de códigos/configurações/pacotes de dados para as quais a versão não foi alterada.

## <a name="upgrade-application-parameters-independently-of-version"></a>Atualizar parâmetros do aplicativo independentemente da versão

Às vezes, é desejável alterar os parâmetros de um aplicativo Service Fabric sem alterar a versão do manifesto. Isso pode ser feito convenientemente usando o sinalizador **-ApplicationParameter** com o cmdlet **Start-ServiceFabricApplicationUpgrade** do PowerShell do Azure Service Fabric. Suponha um aplicativo Service Fabric com as seguintes propriedades:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Agora, atualize o aplicativo usando o cmdlet **Start-ServiceFabricApplicationUpgrade** . Este exemplo mostra uma atualização monitorada, mas uma atualização não monitorada também pode ser usada. Para ver uma descrição completa dos sinalizadores aceitos por esse cmdlet, consulte a [referência de módulo do PowerShell Service Fabric do Azure](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters)

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

Após a atualização, confirme se o aplicativo tem os parâmetros atualizados e a mesma versão:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>Reverter atualizações de aplicativos

Embora atualizações possam ser roladas para frente em um dos três modos (*Monitored*, *UnmonitoredAuto* ou *UnmonitoredManual*), elas só podem ser revertidas em modo *UnmonitoredAuto* ou *UnmonitoredManual*. A reversão em modo *UnmonitoredAuto* funciona da mesma forma que o roll forward, com a exceção de que o valor padrão de *UpgradeReplicaSetCheckTimeout* é diferente - consulte [Parâmetros de atualização do aplicativo](service-fabric-application-upgrade-parameters.md). A reversão em modo *UnmonitoredManual* funciona da mesma forma que o roll forward - a reversão se suspenderá depois de concluir cada UD e deve ser retomada explicitamente usando [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) para continuar a reversão.

As reversões poderão ser disparadas automaticamente quando as políticas de integridade de uma atualização em modo *Monitored* com um *FailureAction* de *Rollback* forem violadas (consulte [Parâmetros de atualização de aplicativo](service-fabric-application-upgrade-parameters.md)) ou usando-se explicitamente [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Durante a reversão, o valor de *UpgradeReplicaSetCheckTimeout* e o modo ainda podem ser alterados a qualquer momento usando-se [Update-ServiceFabricApplicationUpgrad](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Próximos passos
[Atualização do aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta você durante a atualização de aplicativo usando o Visual Studio.

[Atualização do aplicativo usando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você uma atualização de aplicativo usando o PowerShell.

Controle como seu aplicativo é atualizado usando [Parâmetros de Atualização](service-fabric-application-upgrade-parameters.md).

Torne suas atualizações de aplicativo compatíveis aprendendo a usar a [Serialização de Dados](service-fabric-application-upgrade-data-serialization.md).

Corrija problemas comuns em atualizações de aplicativo consultando as etapas em [Solução de problemas de atualizações de aplicativo](service-fabric-application-upgrade-troubleshooting.md).
