---
title: Atualizar nós de cluster para usar o Azure Managed disks
description: Veja como atualizar um cluster de Service Fabric existente para usar o Azure Managed disks com pouco ou nenhum tempo de inatividade do cluster.
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: 152bdaea121e65de8332fcde8543b8158ff11714
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88717516"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Atualizar nós de cluster para usar o Azure Managed disks

Os [discos gerenciados do Azure](../virtual-machines/managed-disks-overview.md) são a oferta de armazenamento em disco recomendada para uso com máquinas virtuais do Azure para armazenamento persistente de dados. Você pode melhorar a resiliência de suas cargas de trabalho de Service Fabric atualizando os conjuntos de dimensionamento de máquinas virtuais que expandem os tipos de nós para usar discos gerenciados. Veja como atualizar um cluster de Service Fabric existente para usar o Azure Managed disks com pouco ou nenhum tempo de inatividade do cluster.

A estratégia geral para atualizar um Service Fabric nó de cluster para usar discos gerenciados é:

1. Implante um conjunto de escala de máquina virtual duplicado de outra forma do tipo de nó, mas com o objeto [managedDisk](/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) adicionado à `osDisk` seção do modelo de implantação do conjunto de dimensionamento de máquinas virtuais. O novo conjunto de dimensionamento deve se associar ao mesmo balanceador de carga/IP que o original, para que os clientes não tenham uma interrupção de serviço durante a migração.

2. Depois que os conjuntos de dimensionamento original e atualizado estiverem em execução lado a lado, desabilite as instâncias de nó originais uma de cada vez para que os serviços do sistema (ou réplicas dos serviços com estado) migrem para o novo conjunto de dimensionamento.

3. Verifique se o cluster e os novos nós estão íntegros e, em seguida, remova o conjunto de dimensionamento original e o estado do nó para os nós excluídos.

Este artigo orientará você pelas etapas de atualização do tipo de nó primário de um cluster de exemplo para usar discos gerenciados, ao mesmo tempo em que evita qualquer tempo de inatividade do cluster (consulte a observação abaixo). O estado inicial do exemplo de cluster de teste consiste em um tipo de nó de [durabilidade prateada](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster), apoiado por um único conjunto de dimensionamento com cinco nós.

> [!NOTE]
> As limitações de um balanceador de carga de SKU básico impedem que um conjunto de dimensionamento adicional seja adicionado. É recomendável usar o balanceador de carga SKU padrão em vez disso. Para obter mais informações, consulte [uma comparação entre as duas SKUs](/azure/load-balancer/skus).

> [!CAUTION]
> Você terá uma interrupção com esse procedimento somente se tiver dependências no DNS do cluster (por exemplo, ao acessar [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)). A [prática recomendada de arquitetura para serviços de front-end](/azure/architecture/microservices/design/gateway) é ter algum tipo de [balanceador de carga](/azure/architecture/guide/technology-choices/load-balancing-overview) na frente de seus tipos de nó para tornar a troca de nó possível sem uma interrupção.

Aqui estão os [modelos e cmdlets](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) para Azure Resource Manager que usaremos para concluir o cenário de atualização. As alterações no modelo serão explicadas em [implantar um conjunto de dimensionamento atualizado para o tipo de nó primário](#deploy-an-upgraded-scale-set-for-the-primary-node-type)  abaixo.

## <a name="set-up-the-test-cluster"></a>Configurar o cluster de teste

Vamos configurar o cluster inicial de teste de Service Fabric. Primeiro, [Baixe](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) o Azure Resource Manager modelos de exemplo que usaremos para concluir este cenário.

Em seguida, entre na sua conta do Azure.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Os comandos a seguir irão orientá-lo na geração de um novo certificado autoassinado e na implantação do cluster de teste. Se você já tiver um certificado que deseja usar, pule para [usar um certificado existente para implantar o cluster](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Gerar um certificado autoassinado e implantar o cluster

Primeiramente, atribua as variáveis necessárias para a implantação do cluster do Service Fabric. Ajuste os valores de `resourceGroupName`, `certSubjectName`, `parameterFilePath` e `templateFilePath` da sua conta e ambiente específicos:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Initial-1NodeType-UnmanagedDisks.json"
$parameterFilePath = "C:\Initial-1NodeType-UnmanagedDisks.parameters.json"
```

> [!NOTE]
> Verifique se a localização de `certOutputFolder` existe no computador local, antes de executar o comando para implantar um novo cluster do Service Fabric.

Em seguida, abra o [*Initial-1NodeType-UnmanagedDisks.parameters.jsno*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) arquivo e ajuste os valores de `clusterName` e `dnsName` para corresponder aos valores dinâmicos definidos no PowerShell e salve as alterações.

Em seguida, implante o cluster de teste do Service Fabric:

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

Quando a implantação for concluída, localize o arquivo *.pfx* (`$certPfx`) no seu computador local e importe-o para o repositório de certificados:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

A operação retornará a impressão digital do certificado, que você usará para [se conectar ao novo cluster](#connect-to-the-new-cluster-and-check-health-status) e verificar seu status de integridade. (Pule a seção a seguir, que é uma abordagem alternativa para a implantação de cluster.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Usar um certificado existente para implantar o cluster

Você também pode usar um certificado de Azure Key Vault existente para implantar o cluster de teste. Para fazer isso, você precisará [obter referências ao seu Key Vault e à](#obtain-your-key-vault-references) impressão digital do certificado.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Abra o [*Initial-1NodeType-UnmanagedDisks.parameters.jsno*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) arquivo e altere os valores de `clusterName` e `dnsName` para algo exclusivo.

Por fim, designe um nome de grupo de recursos para o cluster e defina os `templateFilePath` `parameterFilePath` locais e dos arquivos *iniciais-1NodeType-UnmanagedDisks* :

> [!NOTE]
> O grupo de recursos designado já deve existir e estar localizado na mesma região que o seu Key Vault.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

Por fim, execute o seguinte comando para implantar o cluster de teste inicial:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Conectar-se ao novo cluster e verificar o status da integridade

Conecte-se ao cluster e verifique se todos os cinco nós estão íntegros (substituindo as `clusterName` `thumb` variáveis e para o cluster):

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

Com isso, estamos prontos para iniciar o procedimento de atualização.

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>Implantar um conjunto de dimensionamento atualizado para o tipo de nó primário

Para atualizar ou *dimensionar verticalmente*, um tipo de nó, precisaremos implantar uma cópia do conjunto de dimensionamento de máquinas virtuais do tipo de nó, que, de outra forma, é idêntico ao conjunto de dimensionamento original (incluindo referência ao mesmo `nodeTypeRef` , `subnet` , e `loadBalancerBackendAddressPools` ), exceto que ele inclui a atualização/alterações desejadas e sua própria sub-rede separada e pool de endereços NAT de entrada. Como estamos atualizando um tipo de nó primário, o novo conjunto de dimensionamento será marcado como primário ( `isPrimary: true` ), assim como o conjunto de dimensionamento original. (Para atualizações de tipo de nó não primário, basta omitir isso.)

Para sua conveniência, as alterações necessárias já foram feitas para você no modelo *upgrade-1NodeType-2ScaleSets-ManagedDisks* [template](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) e nos arquivos de [parâmetros](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json) .

As seções a seguir explicarão as alterações de modelo em detalhes. Se preferir, você pode ignorar a explicação e continuar na [próxima etapa do procedimento de atualização](#obtain-your-key-vault-references).

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Atualizar o modelo de cluster com o conjunto de dimensionamento atualizado

Aqui estão as modificações seção a seção do modelo de implantação de cluster original para adicionar um conjunto de dimensionamento atualizado para o tipo de nó primário.

#### <a name="parameters"></a>Parâmetros

Adicione parâmetros para o nome da instância, a contagem e o tamanho do novo conjunto de dimensionamento. Observe que `vmNodeType1Name` é exclusivo para o novo conjunto de dimensionamento, enquanto os valores de contagem e tamanho são idênticos ao conjunto de dimensionamento original.

**Arquivo de modelo**

```json
"vmNodeType1Name": {
    "type": "string",
    "defaultValue": "NTvm2",
    "maxLength": 9
},
"nt1InstanceCount": {
    "type": "int",
    "defaultValue": 5,
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType1Size": {
    "type": "string",
    "defaultValue": "Standard_D2_v2"
},
```

**Arquivo de parâmetros**

```json
"vmNodeType1Name": {
    "value": "NTvm2"
},
"nt1InstanceCount": {
    "value": 5
},
"vmNodeType1Size": {
    "value": "Standard_D2_v2"
}
```

### <a name="variables"></a>Variáveis

Na seção modelo de implantação `variables` , adicione uma entrada para o pool de endereços NAT de entrada do novo conjunto de dimensionamento.

**Arquivo de modelo**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>Recursos

Na seção *recursos* do modelo de implantação, adicione o novo conjunto de dimensionamento de máquinas virtuais, tendo em mente estas coisas:

* O novo conjunto de dimensionamento faz referência ao mesmo tipo de nó que o original:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* O novo conjunto de dimensionamento faz referência ao mesmo endereço e sub-rede de back-end do balanceador de carga (mas usa um pool NAT de entrada do balanceador de carga diferente):

   ```json
    "loadBalancerBackendAddressPools": [
        {
            "id": "[variables('lbPoolID0')]"
        }
    ],
    "loadBalancerInboundNatPools": [
        {
            "id": "[variables('lbNatPoolID1')]"
        }
    ],
    "subnet": {
        "id": "[variables('subnet0Ref')]"
    }
   ```

* Assim como o conjunto de dimensionamento original, o novo conjunto de dimensionamento é marcado como o tipo de nó primário. (Ao atualizar tipos de nós não primários, omita essa alteração.)

    ```json
    "isPrimary": true,
    ```

* Ao contrário do conjunto de dimensionamento original, o novo conjunto de dimensionamento é atualizado para usar discos gerenciados.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

Depois de implementar todas as alterações em seus arquivos de modelo e de parâmetros, vá para a próxima seção para adquirir suas referências de Key Vault e implantar as atualizações no cluster.

### <a name="obtain-your-key-vault-references"></a>Obter suas referências de Key Vault

Para implantar a configuração atualizada, primeiro você deve obter várias referências ao seu certificado de cluster armazenado em seu Key Vault. A maneira mais fácil de encontrar esses valores é por meio de portal do Azure. Você precisará do seguinte:

* **A URL de Key Vault do seu certificado de cluster.** Em seu Key Vault em portal do Azure, selecione **certificados**  >  *o*  >  **identificador secreto**do certificado desejado:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **A impressão digital do seu certificado de cluster.** (Provavelmente você já terá isso se estiver [conectado ao cluster inicial](#connect-to-the-new-cluster-and-check-health-status) para verificar seu status de integridade.) Na mesma folha de certificado (**certificados**  >  *seu certificado desejado*) em portal do Azure, copie **a impressão digital X. 509 SHA-1 (em Hex)**:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **A ID de recurso do seu Key Vault.** Em seu Key Vault em portal do Azure, selecione **Propriedades**  >  **ID do recurso**:

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Implantar o modelo atualizado

Ajuste `parameterFilePath` e `templateFilePath` conforme necessário e execute este comando:

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Quando a implantação for concluída, verifique a integridade do cluster novamente e verifique se todos os dez nós (cinco no original e cinco no novo conjunto de dimensionamento) estão íntegros.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>Migrar nós de semente para o novo conjunto de dimensionamento

Agora estamos prontos para começar a desabilitar os nós do conjunto de dimensionamento original. Com a desabilitação desses nós, os serviços do sistema e os nós semente migram para as VMs do novo conjunto de dimensionamento porque ele também está marcado como tipo de nó primário.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Use o Service Fabric Explorer para monitorar a migração dos nós semente para o novo conjunto de dimensionamento e a progressão dos nós no conjunto de dimensionamento original do status *Desabilitando* para o status *Desabilitado*.

![Service Fabric Explorer mostrando o status dos nós desabilitados](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> Pode levar algum tempo para concluir a operação de desabilitação em todos os nós do conjunto de dimensionamento original. Para garantir a consistência dos dados, apenas um nó semente pode ser alterado por vez. Cada alteração de nó semente requer uma atualização do cluster. Portanto, a substituição de um nó semente exige duas atualizações do cluster (uma para adição e outra para remoção do nó). Atualizar os cinco nós semente neste cenário de exemplo resultará em dez atualizações do cluster.

## <a name="remove-the-original-scale-set"></a>Remover o conjunto de dimensionamento original

Quando a operação de desabilitação for concluída, remova o conjunto de dimensionamento.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

No Service Fabric Explorer, os nós removidos (e, portanto, o *estado de integridade do cluster*) agora aparecerão no estado de *Erro*.

![Service Fabric Explorer mostrando nós desabilitados no estado de erro](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Remova os nós obsoletos do Cluster Service Fabric para restaurar o estado de integridade do cluster para *OK*.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Service Fabric Explorer com nós inativos no estado de erro removido](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a atualizar os conjuntos de dimensionamento de máquinas virtuais de um Cluster Service Fabric para usar discos gerenciados, ao mesmo tempo que evita interrupções de serviço durante o processo. Para obter mais informações sobre tópicos relacionados, confira os recursos a seguir.

Saiba como:

* [Dimensionar um tipo de nó primário do cluster do Service Fabric](service-fabric-scale-up-primary-node-type.md)

* [Converter um modelo de conjunto de dimensionamento para usar discos gerenciados](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Remover o tipo de nó do Service Fabric](service-fabric-how-to-remove-node-type.md)

Consulte também:

* [Exemplo: atualizar nós de cluster para usar o Azure Managed disks](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)

* [Considerações de dimensionamento vertical](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)
