---
title: Implantar plataforma de contêineres OpenShift 3.11 no Azure
description: Implantar plataforma de contêineres OpenShift 3.11 no Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 04/05/2020
ms.author: haroldw
ms.openlocfilehash: 7d6cd4c6ce7991ae83f6f4a1dd6d8b86fe7eedbc
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757892"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>Implantar plataforma de contêineres OpenShift 3.11 no Azure

Você pode usar um dos vários métodos para implantar a Plataforma de Contêineres OpenShift 3.11 no Azure:

- Você pode implantar manualmente os componentes de infra-estrutura do Azure necessários e, em seguida, seguir a [documentação da Plataforma de Contêiner OpenShift](https://docs.openshift.com/container-platform).
- Você também pode usar um [modelo do Gerenciador de Recursos](https://github.com/Microsoft/openshift-container-platform/) existente que simplifica a implantação do cluster do OpenShift Container Platform.
- Outra opção é usar a [Oferta do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy).

Para ambas as opções, é necessária uma assinatura do Red Hat. Durante a implantação, a instância Red Hat Enterprise Linux é registrada na Assinatura do Red Hat e anexada à ID do Pool que contém os direitos para o OpenShift Container Platform.
Certifique-se de que você tenha uma senha, uma ID do Pool e um Nome de usuário de gerente de Assinatura do Red Hat (RHSM) válidos. Você pode usar uma Chave de Ativação, ID da Organização e ID do Pool. Você pode verificar essas informações entrando em https://access.redhat.com.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>Implantar usando o modelo OpenShift Container Platform Resource Manager 3.11

### <a name="private-clusters"></a>Clusters privados

A implantação de clusters OpenShift privados requer mais do que apenas não ter um IP público associado ao balanceador de carga mestre (console web) ou ao balanceador de carga infra (roteador).  Um cluster privado geralmente usa um servidor DNS personalizado (não o DNS padrão do Azure), um nome de domínio personalizado (como contoso.com) e rede virtual pré-definida).  Para clusters privados, você precisa configurar sua rede virtual com todas as sub-redes e configurações de servidor DNS apropriadas com antecedência.  Em seguida, use **mastersubnetreference existente,** **existenteInfraSubnetReference,** **existenteCnsSubnetReference**e **nonodeSubnetReference existente** para especificar a sub-rede existente para uso pelo cluster.

Se o master privado for selecionado **(masterClusterType**=private), um IP privado estático deverá ser especificado para **masterPrivateClusterIp**.  Este IP será atribuído à extremidade frontal do balanceador de carga mestre.  O IP deve estar dentro do CIDR para a sub-rede mestre e não em uso.  **masterClusterDnsType** deve ser definido como "personalizado" e o nome DNS mestre deve ser fornecido para **masterClusterDns**.  O nome DNS deve mapear para o IP privado estático e será usado para acessar o console nos nós mestres.

Se o roteador privado estiver selecionado **(roteirizadoRClusterType**=private), um IP privado estático deverá ser especificado para **o roteadorPrivateClusterIp**.  Este IP será atribuído à extremidade frontal do balanceador de carga infra.  O IP deve estar dentro do CIDR para a sub-rede infra e não em uso.  **roteamentoSubDomainType** deve ser definido como "personalizado" e o nome DNS curinga para roteamento deve ser fornecido para **roteamentoSubDomain**.  

Se os mestres privados e o roteador privado forem selecionados, o nome de domínio personalizado também deve ser inserido para **domainName**

Após uma implantação bem-sucedida, o Nó Bastion é o único nó com um IP público em que você pode ssh.  Mesmo que os nós mestres estejam configurados para acesso público, eles não estão expostos para acesso ssh.

Para implantar usando o modelo do Resource Manager, você usa um arquivo de parâmetros para fornecer os parâmetros de entrada. Para personalizar ainda mais a implantação, bifurque o repositório do GitHub e altere os itens apropriados.

Algumas opções comuns de personalização incluem, mas não estão limitadas a:

- Tamanho de VM de bastião (variável em azuredeploy.json)
- Convenções de nomenclatura (variáveis em azuredeploy.json)
- Especificações de cluster do OpenShift – modificadas por meio do arquivo de hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Configurar o arquivo de parâmetros

O [modelo do OpenShift Container Platform](https://github.com/Microsoft/openshift-container-platform) tem várias ramificações disponíveis para diferentes versões do OpenShift Container Platform.  Com base em suas necessidades, você pode implantar diretamente do repositório ou você pode criar fork do repositório e fazer alterações personalizadas para os modelos ou scripts antes de implantar.

Use o valor `appId` da entidade de serviço que você criou anteriormente para o parâmetro `aadClientId`.

O exemplo a seguir mostra um arquivo de parâmetros chamado azuredeploy.parameters.json com todas as entradas necessárias.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

Substitua os parâmetros por suas informações específicas.

Versões diferentes podem ter parâmetros diferentes. Portanto, verifique os parâmetros necessários para a ramificação que você usar.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy. Parâmetros.json arquivo explicado

| Propriedade | Descrição | Opções válidas | Valor Padrão |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL para artefatos (json, scripts, etc.) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Região do Azure para implantar recursos para |  |  |
| `masterVmSize` | Tamanho do Master VM. Selecione entre um dos tamanhos de VM permitidos listados no arquivo azuredeploy.json |  | Standard_E2s_v3 |
| `infraVmSize` | Tamanho do Infra VM. Selecione entre um dos tamanhos de VM permitidos listados no arquivo azuredeploy.json |  | Standard_D4s_v3 |
| `nodeVmSize` | Tamanho do Aplicativo Node VM. Selecione entre um dos tamanhos de VM permitidos listados no arquivo azuredeploy.json |  | Standard_D4s_v3 |
| `cnsVmSize` | Tamanho da VM de nó de armazenamento nativo do contêiner (CNS). Selecione entre um dos tamanhos de VM permitidos listados no arquivo azuredeploy.json |  | Standard_E4s_v3 |
| `osImageType` | A imagem RHEL para usar. defaultgallery: On-Demand; marketplace: imagem de terceiros | defaultgallery <br> marketplace | defaultgallery |
| `marketplaceOsImage` | Se `osImageType` é marketplace, então insira os valores apropriados para 'editor', 'offer', 'sku', 'versão' da oferta de marketplace. Este parâmetro é um tipo de objeto |  |  |
| `storageKind` | O tipo de armazenamento a ser usado  | gerenciado<br> não gerenciado | gerenciado |
| `openshiftClusterPrefix` | Prefixo de cluster usado para configurar nomes de host para todos os nós.  Entre 1 e 20 caracteres |  | mycluster |
| `minoVersion` | A versão menor da Plataforma de Contêineres OpenShift 3.11 para implantar |  | 69 |
| `masterInstanceCount` | Número de ádeus masters para implantar | 1, 3, 5 | 3 |
| `infraInstanceCount` | Número de nós infra para implantar | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Número de ádeus para implantar | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Número de nódulos CNS para implantar | 3, 4 | 3 |
| `osDiskSize` | Tamanho do disco do SISTEMA OPERACIONAL para a VM (em GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Tamanho do disco de dados para anexar aos nós para o volume do Docker (em GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Tamanho do disco de dados para anexar aos nódulos CNS para uso por glusterfs (em GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Nome de usuário do Admin para o login do SISTEMA OPERACIONAL (VM) e do usuário inicial do OpenShift |  | ocpadmin |
| `enableMetrics` | Habilite métricas. As métricas exigem mais recursos, então selecione o tamanho adequado para Infra VM | true <br> false | false |
| `enableLogging` | Habilite o registro. elásticopod requer 8 GB de RAM para selecionar tamanho adequado para Infra VM | true <br> false | false |
| `enableCNS` | Habilitar o armazenamento nativo do contêiner | true <br> false | false |
| `rhsmUsernameOrOrgId` | Nome de usuário ou ID do Gerenciador de Assinaturas Red Hat |  |  |
| `rhsmPoolId` | O ID do Pool do Gerenciador de Assinaturas red hat que contém seus direitos openshift para nós de computação |  |  |
| `rhsmBrokerPoolId` | O ID do Pool do Gerenciador de Assinaturas Red Hat que contém seus direitos openshift para mestres e nomes infra. Se você não tiver diferentes IDs de pool, digite o mesmo ID de pool que 'rhsmPoolId' |  |
| `sshPublicKey` | Copie sua chave pública SSH aqui |  |  |
| `keyVaultSubscriptionId` | O ID de assinatura da assinatura que contém o Key Vault |  |  |
| `keyVaultResourceGroup` | O nome do Grupo de Recursos que contém o Cofre de Chaves |  |  |
| `keyVaultName` | O nome do Cofre de Chaves que você criou |  |  |
| `enableAzure` | Habilitar o provedor de nuvem do Azure | true <br> false | true |
| `aadClientId` | ID do cliente do azure Active Directory também conhecido como ID de aplicativo para principal de serviço |  |  |
| `domainName` | Nome do nome de domínio personalizado a ser usado (se aplicável). Definido como "nenhum" se não implantar cluster totalmente privado |  | none |
| `masterClusterDnsType` | Tipo de domínio para console web OpenShift. 'default' usará o selo DNS do IP infra público mestre. 'personalizado' permite que você defina seu próprio nome | default <br> personalizado | default |
| `masterClusterDns` | O nome DNS personalizado para usar para acessar o console web OpenShift se você selecionou 'personalizado' para`masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Se definido como 'nipio', `routingSubDomain` usará nip.io.  Use 'personalizado' se você tiver seu próprio domínio que deseja usar para roteamento | nipio <br> personalizado | nipio |
| `routingSubDomain` | O nome DNS curinga que você deseja usar para roteamento se você selecionou 'personalizado' para`routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Selecione se deve usar uma Rede Virtual existente ou criar uma nova Rede Virtual | Existente <br> novo | novo |
| `virtualNetworkResourceGroupName` | Nome do Grupo de Recursos para a nova Rede Virtual se você selecionou 'novo' para`virtualNetworkNewOrExisting` |  | resourceGroup().nome |
| `virtualNetworkName` | O nome da nova Rede Virtual para criar se você selecionou 'novo' para`virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Prefixo de endereço da nova rede virtual |  | 10.0.0.0/14 |
| `masterSubnetName` | O nome da sub-rede mestre |  | mastersubnet |
| `masterSubnetPrefix` | CIDR usado para a sub-rede mestre - precisa ser um subconjunto do endereçoPrefix |  | 10.1.0.0/16 |
| `infraSubnetName` | O nome da sub-rede infra |  | infrasubnet |
| `infraSubnetPrefix` | CIDR usado para a sub-rede infra - precisa ser um subconjunto do endereçoPrefix |  | 10.2.0.0/16 |
| `nodeSubnetName` | O nome da sub-rede de nó |  | sub-rede de nó |
| `nodeSubnetPrefix` | CIDR usado para a sub-rede de nó - precisa ser um subconjunto do endereçoPrefix |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Referência completa à sub-rede existente para nós mestres. Não é necessário se criar uma nova vNet / sub-rede |  |  |
| `existingInfraSubnetReference` | Referência completa à sub-rede existente para nóinfra. Não é necessário se criar uma nova vNet / sub-rede |  |  |
| `existingCnsSubnetReference` | Referência completa à sub-rede existente para os nós CNS. Não é necessário se criar uma nova vNet / sub-rede |  |  |
| `existingNodeSubnetReference` | Referência completa à sub-rede existente para nós computacionais. Não é necessário se criar uma nova vNet / sub-rede |  |  |
| `masterClusterType` | Especifique se o cluster usa nós mestres privados ou públicos. Se for escolhido o privado, os nós mestres não serão expostos à Internet através de um IP público. Em vez disso, ele usará o IP privado especificado no`masterPrivateClusterIp` | públicos <br> particulares | públicos |
| `masterPrivateClusterIp` | Se os nós mestres privados forem selecionados, então um endereço IP privado deve ser especificado para uso pelo balanceador de carga interno para nós mestres. Este IP estático deve estar dentro do bloco CIDR para a sub-rede mestre e ainda não está em uso. Se os nós mestres públicos forem selecionados, esse valor não será usado, mas ainda deve ser especificado |  | 10.1.0.200 |
| `routerClusterType` | Especifique se o cluster usa nós infra-tés privados ou públicos. Se for escolhido o privado, os nós infra não serão expostos à Internet através de um IP público. Em vez disso, ele usará o IP privado especificado no`routerPrivateClusterIp` | públicos <br> particulares | públicos |
| `routerPrivateClusterIp` | Se os nós infra privados forem selecionados, então um endereço IP privado deve ser especificado para uso pelo balanceador de carga interno para nós infra. Este IP estático deve estar dentro do bloco CIDR para a sub-rede infra e ainda não está em uso. Se os nós de infra-estar públicos forem selecionados, esse valor não será usado, mas ainda deve ser especificado |  | 10.2.0.200 |
| `routingCertType` | Use certificado personalizado para roteamento de domínio ou o certificado auto-assinado padrão - siga as instruções na seção **Certificados Personalizados** | auto-assinado <br> personalizado | auto-assinado |
| `masterCertType` | Use o certificado personalizado para domínio mestre ou o certificado auto-assinado padrão - siga as instruções na seção **Certificados Personalizados** | auto-assinado <br> personalizado | auto-assinado |

<br>

### <a name="deploy-using-azure-cli"></a>Implantar usando a CLI do Azure

> [!NOTE] 
> O comando a seguir requer a CLI do Azure 2.0.8 ou posterior. Você pode verificar a versão CLI com o comando `az --version`. Para atualizar a versão da CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

O exemplo a seguir implanta o cluster do OpenShift e todos os recursos relacionados em um grupo de recursos denominado openshiftrg, com um nome de implantação de myOpenShiftCluster. O modelo é referenciado diretamente do repositório GitHub e um arquivo de parâmetros local chamado azuredeploy.parameters.json é usado.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A implantação leva pelo menos 60 minutos para ser concluída, com base no número total de nós implantados e nas opções configuradas. O FQDN do DNS de bastiões e a URL do console do OpenShift imprime no terminal quando a implantação for concluída.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Se você não quiser amarrar a linha de comando esperando a conclusão da implementação, inclua `--no-wait` como uma das opções para a implementação do grupo. A saída da implantação pode ser recuperada do portal do Azure na seção de implantação do grupo de recursos.

## <a name="connect-to-the-openshift-cluster"></a>Conectar-se ao cluster OpenShift

Quando a implantação for concluída, recupere a conexão da seção de saída da implantação. Conecte-se ao console OpenShift com seu navegador usando a URL do **console OpenShift**. Você também pode ssh para o anfitrião Bastion. A seguir está um exemplo em que o nome de usuário do administrador é clusteradmin e o IP público de bastiões FQDN do DNS é bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos, o cluster OpenShift e todos os recursos relacionados.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Próximas etapas

- [Tarefas de pós-implantação](./openshift-container-platform-3x-post-deployment.md)
- [Solução de problemas de implantação do OpenShift no Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Introdução ao OpenShift Container Platform](https://docs.openshift.com)
