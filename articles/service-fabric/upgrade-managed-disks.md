---
title: Atualize os ádeos de cluster para usar discos gerenciados do Azure
description: Veja como atualizar um cluster de malha de serviço existente para usar discos gerenciados do Azure com pouco ou nenhum tempo de inatividade do seu cluster.
ms.topic: how-to
ms.date: 3/01/2020
ms.openlocfilehash: 2bda5572eda5579bb31c3613b220885f27220d99
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758047"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Atualize os ádeos de cluster para usar discos gerenciados do Azure

[Os discos gerenciados do Azure](../virtual-machines/windows/managed-disks-overview.md) são a oferta recomendada de armazenamento em disco para uso com máquinas virtuais Azure para armazenamento persistente de dados. Você pode melhorar a resiliência de suas cargas de trabalho de malha de serviço atualizando os conjuntos de escala de máquina virtual que estão por trás de seus tipos de nó para usar discos gerenciados. Veja como atualizar um cluster de malha de serviço existente para usar discos gerenciados do Azure com pouco ou nenhum tempo de inatividade do seu cluster.

A estratégia geral para atualizar um nó de cluster de malha de malha de serviço para usar discos gerenciados é:

1. Implante um conjunto de escala de máquina virtual duplicado desse tipo de `osDisk` nó, mas com o objeto [GerenciadoDisk](https://docs.microsoft.com/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) adicionado à seção do modelo de implantação do conjunto de escala de máquina virtual. O novo conjunto de escalas deve se ligar ao mesmo balanceador de carga / IP do original, para que seus clientes não experimentem uma paralisação de serviço durante a migração.

2. Uma vez que os conjuntos de escala original e atualizado estejam funcionando lado a lado, desative as instâncias do nó original uma de cada vez para que os serviços do sistema (ou réplicas de serviços estatais) migrem para o novo conjunto de escalas.

3. Verifique se o cluster e os novos nós estão saudáveis, em seguida, remova o conjunto de escala original e o estado do nó para os nós excluídos.

Este artigo irá levá-lo através das etapas de atualização do tipo de nó primário de um cluster de exemplo para usar discos gerenciados, evitando qualquer tempo de inatividade do cluster (veja nota abaixo). O estado inicial do cluster de teste de exemplo consiste em um tipo de nó de [durabilidade silver,](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)apoiado por um conjunto de escala única com cinco nós.

> [!CAUTION]
> Você só sofrerá uma paralisação com este procedimento se tiver dependências do Cluster DNS (como ao acessar o [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)). A [melhor prática arquitetônica para serviços front-end](https://docs.microsoft.com/azure/architecture/microservices/design/gateway) é ter algum tipo de [balanceador](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) de carga na frente de seus tipos de nó para tornar possível a troca de nó sem uma paralisação.

Aqui estão os [modelos e cmdlets](https://github.com/erikadoyle/service-fabric-scripts-and-templates/tree/managed-disks/templates/nodetype-upgrade-no-outage) para o Azure Resource Manager que usaremos para completar o cenário de upgrade. As alterações de modelo serão explicadas em [Implantar um conjunto de escala atualizado para o tipo de nó primário](#deploy-an-upgraded-scale-set-for-the-primary-node-type) abaixo.

## <a name="set-up-the-test-cluster"></a>Configure o cluster de teste

Vamos configurar o cluster inicial de teste de malha de serviço. Primeiro, [baixe](https://github.com/erikadoyle/service-fabric-scripts-and-templates/tree/managed-disks/templates/nodetype-upgrade-no-outage) os modelos de exemplo do gerenciador de recursos do Azure que usaremos para concluir este cenário.

Em seguida, faça login na sua conta do Azure.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Os seguintes comandos guiarão você através da geração de um novo certificado auto-assinado e da implantação do cluster de teste. Se você já tiver um certificado que deseja usar, pule para [Usar um certificado existente para implantar o cluster](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Gerar um certificado auto-assinado e implantar o cluster

Primeiro, atribua as variáveis que você precisará para a implantação do cluster Service Fabric. Ajuste os `resourceGroupName`valores `parameterFilePath`para `templateFilePath` , `certSubjectName`e para sua conta e ambiente específicos:

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
> Certifique-se `certOutputFolder` de que a localização exista em sua máquina local antes de executar o comando para implantar um novo cluster de malha de serviço.

Em seguida, abra o arquivo [*Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) e ajuste os valores para `clusterName` e `dnsName` corresponda aos valores dinâmicos definidos no PowerShell e salve suas alterações.

Em seguida, implante o cluster de teste service fabric:

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

Uma vez concluída a implantação, localize`$certPfx`o arquivo *.pfx* ( ) em sua máquina local e importe-o para sua loja de certificados:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

A operação devolverá a impressão digital do certificado, que você usará [para se conectar ao novo cluster](#connect-to-the-new-cluster-and-check-health-status) e verificar seu estado de saúde. (Pule a seção a seguir, que é uma abordagem alternativa à implantação de cluster.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Use um certificado existente para implantar o cluster

Você também pode usar um certificado Azure Key Vault existente para implantar o cluster de teste. Para fazer isso, você precisará [obter referências ao seu Key Vault](#obtain-your-key-vault-references) e impressão digital do certificado.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Abra o arquivo [*Inicial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) e `clusterName` `dnsName` altere os valores para e para algo único.

Finalmente, designe um nome de grupo `templateFilePath` `parameterFilePath` de recursos para o cluster e defina os locais de seus arquivos *Inicial-1NodeType-UnmanagedDisks:*

> [!NOTE]
> O grupo de recursos designado já deve existir e estar localizado na mesma região que seu Key Vault.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

Finalmente, execute o seguinte comando para implantar o cluster de teste inicial:

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

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Conecte-se ao novo cluster e verifique o estado de saúde

Conecte-se ao cluster e certifique-se de que todos `clusterName` `thumb` os cinco de seus nós estejam saudáveis (substituindo as variáveis e as variáveis para o seu cluster):

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

Com isso, estamos prontos para começar o procedimento de atualização.

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>Implantar um conjunto de escala atualizado para o tipo de nó primário

Para atualizar, ou *dimensionar verticalmente,* um tipo de nó, precisaremos implantar uma cópia do conjunto de escala de máquina virtual desse tipo de `nodeTypeRef` `subnet`nó, `loadBalancerBackendAddressPools`que de outra forma é idêntico ao conjunto de escala original (incluindo referência ao mesmo , e ) exceto que inclui o pacote de upgrade/alterações desejado e seu próprio pool de endereços NAT de entrada e sub-rede. Como estamos atualizando um tipo de nó primário, o`isPrimary: true`novo conjunto de escalas será marcado como principal (), assim como o conjunto de escala original. (Para upgrades de tipo de nó não primário, basta omiti-lo.)

Para conveniência, as alterações necessárias já foram feitas para você no [modelo](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) *Upgrade-1NodeType-2ScaleSets-ManagedDisks* e [arquivos de parâmetros.](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json)

As seções a seguir explicarão as alterações do modelo em detalhes. Se preferir, pode pular a explicação e continuar até [a próxima etapa do procedimento de atualização.](#obtain-your-key-vault-references)

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Atualize o modelo de cluster com o conjunto de escala atualizado

Aqui estão as modificações de seção por seção do modelo original de implantação de cluster para adicionar um conjunto de escala atualizado para o tipo de nó principal.

#### <a name="parameters"></a>parâmetros

Adicione parâmetros para o nome de exemplo, contagem e tamanho do novo conjunto de escalas. Observe `vmNodeType1Name` que é exclusivo do novo conjunto de escala, enquanto os valores de contagem e tamanho são idênticos ao conjunto de escala original.

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

**Arquivo parâmetros**

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

### <a name="variables"></a>variáveis

Na seção `variables` modelo de implantação, adicione uma entrada para o pool de endereços NAT de entrada do novo conjunto de escalas.

**Arquivo de modelo**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>Recursos

Na seção *de recursos* do modelo de implantação, adicione o novo conjunto de escala de máquina virtual, tendo em mente essas coisas:

* O novo conjunto de escalas faz referência ao mesmo tipo de nó do original:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* O novo conjunto de escalas faz referência ao mesmo endereço e sub-rede do balanceador de carga (mas usa um pool NAT de entrada balanceador de carga diferente):

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

* Como o conjunto de escala original, o novo conjunto de escalas é marcado como o tipo de nó primário. (Ao atualizar tipos de nó não primários, omita essa alteração.)

    ```json
    "isPrimary": true,
    ```

* Ao contrário do conjunto de escalaoriginal, o novo conjunto de escalas é atualizado para usar discos gerenciados.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

Depois de implementar todas as alterações em seus arquivos de modelo e parâmetros, prossiga para a próxima seção para adquirir suas referências do Key Vault e implantar as atualizações no seu cluster.

### <a name="obtain-your-key-vault-references"></a>Obtenha suas referências do Key Vault

Para implantar a configuração atualizada, primeiro você obterá várias referências ao seu certificado de cluster armazenado satisfazem seu Cofre de Chaves. A maneira mais fácil de encontrar esses valores é através do portal Azure. Você precisará de:

* **A URL do Key Vault do seu certificado de cluster.** No portal Key Vault no portal Azure, selecione **Certificados** > O**identificador secreto de***certificado* > desejado :

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **A impressão digital do seu certificado de cluster.** (Você provavelmente já tem isso se você [se conectou ao cluster inicial](#connect-to-the-new-cluster-and-check-health-status) para verificar seu estado de saúde.) A partir da mesma lâmina de certificado **(Certificados** > *Seu certificado desejado)* no portal Azure, copie **X.509 SHA-1 Thumbprint (em hex)**:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **O ID de recursos do seu cofre de chaves.** No portal Key Vault no azure, selecione**ID de recursos de** **propriedades:** > 

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Implantar o modelo atualizado

Ajuste `parameterFilePath` o `templateFilePath` e conforme necessário e execute o seguinte comando:

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

Quando a implantação for concluída, verifique novamente a saúde do cluster e assegure que todos os dez nós (cinco no original e cinco no novo conjunto de escala) estejam saudáveis.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>Migrar os nódulos de sementes para o novo conjunto de escalas

Estamos prontos para começar a desativar os nós do conjunto de escala original. À medida que esses nós ficam desativados, os serviços do sistema e os nós de sementes migram para as VMs do novo conjunto de escala, porque também é marcado como o tipo de nó primário.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Use o Service Fabric Explorer para monitorar a migração de nódulos de sementes para o novo conjunto de escalas e a progressão de álos na escala original definida de *desabilitação* para *desativada.*

![Explorador de malha de serviço mostrando o status dos nódulos desativados](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> Pode levar algum tempo para concluir a operação de desativação em todos os nós do conjunto de escala original. Para garantir a consistência dos dados, apenas um nó de sementes pode mudar de cada vez. Cada alteração de nó de semente requer uma atualização de cluster; assim, substituir um nó de sementes requer duas atualizações de cluster (uma para adição e remoção de nó). A atualização dos cinco nódulos de sementes neste cenário amostral resultará em dez upgrades de cluster.

## <a name="remove-the-original-scale-set"></a>Remova o conjunto de escala original

Uma vez que a operação de desativação esteja concluída, remova o conjunto de escalas.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

No Service Fabric Explorer, os nós removidos (e, portanto, o *Estado de Saúde*do Cluster ) aparecerão agora no estado de *erro.*

![Explorador de malha de serviço mostrando á67s desativados em estado de erro](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Remova os álos obsoletos do cluster Service Fabric para restaurar o Estado de Saúde do Cluster em *OK*.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Explorador de malha de serviço com nós para baixo no estado de erro removido](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>Próximas etapas

Neste passo a passo, você aprendeu como atualizar os conjuntos de escala de máquina virtual de um cluster de malha de serviço para usar discos gerenciados, evitando paralisações de serviço durante o processo. Para obter mais informações sobre os tópicos relacionados, confira os seguintes recursos.

Saiba como:

* [Dimensionar um tipo de nó primário do cluster do Service Fabric](service-fabric-scale-up-node-type.md)

* [Converter um modelo de conjunto de escalas para usar discos gerenciados](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Remover o tipo de nó do Service Fabric](service-fabric-how-to-remove-node-type.md)

Consulte também:

* [Amostra: Atualizar os nós de cluster para usar discos gerenciados do Azure](https://github.com/erikadoyle/service-fabric-scripts-and-templates/tree/managed-disks/templates/nodetype-upgrade-no-outage)

* [Considerações de dimensionamento vertical](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)