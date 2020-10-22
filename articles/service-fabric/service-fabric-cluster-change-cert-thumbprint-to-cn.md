---
title: Atualizar um cluster para usar o nome comum do certificado
description: Saiba como converter um certificado de Cluster Service Fabric de declarações baseadas em impressão digital para nomes comuns.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 224798565921593d3c91dfcc187efa71a71b1fdd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368053"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>Converter certificados de cluster de declarações baseadas em impressão digital para nomes comuns
A assinatura de um certificado (coloquialmente conhecido como "impressão digital") é exclusiva, o que significa que um certificado de cluster declarado pela impressão digital refere-se a uma instância específica de um certificado. Isso, por sua vez, torna o gerenciamento e a substituição do certificado, em geral, difícil e explícito: cada alteração requer orquestração das atualizações do cluster e dos hosts de computação subjacentes. A conversão de Service Fabric declarações de certificado de um cluster de uma impressão digital baseada em declarações com base no nome comum da entidade do certificado simplifica o gerenciamento consideravelmente específico, a transferência de um certificado não exige mais uma atualização do cluster. Este artigo descreve como converter um cluster existente para declarações baseadas em nome comum sem tempo de inatividade.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="moving-to-certificate-authority-ca-signed-certificates"></a>Migrando para certificados de autoridade de certificação (CA) assinados
A segurança de um cluster cujo certificado é declarado pela impressão digital permanece no fato de que é impossível ou computacionalmente impraticável para forjar um certificado com a mesma assinatura de outro. Nesse caso, a comprovante do certificado é menos importante e, portanto, os certificados autoassinados são adequados. Por outro lado, a segurança de um cluster com certificados declarados por fluxos de nome comuns do serviço de infraestrutura de chave pública (PKI) que emitiu esse certificado e inclui aspectos como suas práticas de certificação, se sua segurança operacional é auditada e muitas outras. Por esse motivo, a escolha de uma PKI é importante, um conhecimento profundo dos emissores (autoridade de certificação ou CA) é necessário e os certificados autoassinados são essencialmente inúteiss. Um certificado declarado pelo nome comum (CN) normalmente é considerado válido se sua cadeia puder ser compilada com êxito, a entidade tem o elemento CN esperado e seu emissor (imediato ou superior na cadeia) é confiável para o agente que executa a validação. O Service Fabric dá suporte à declaração de certificados por CN com o emissor ' implícito ' (a cadeia deve terminar em uma âncora de confiança) ou com os emissores declarados pela impressão digital ("fixação do emissor"); consulte este  [artigo](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) para obter mais detalhes. Para converter um cluster usando um certificado autoassinado declarado pela impressão digital para o nome comum, o certificado assinado por AC deve ser introduzido primeiro no cluster por impressão digital; somente então é a conversão de TP para CN possível.

Para fins de teste, um certificado autoassinado pode ser declarado pelo CN, fixando o emissor em sua própria impressão digital; do ponto de vista da segurança, isso é quase equivalente a declarar o mesmo certificado pelo TP. No entanto, observe que uma conversão bem-sucedida desse tipo não garante uma conversão bem-sucedida de TP para CN com um certificado assinado por uma autoridade de certificação. Portanto, é recomendável testar a conversão com um certificado assinado por AC apropriado (existem opções gratuitas).

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Carregar o certificado e instalá-lo no conjunto de dimensionamento
No Azure, o mecanismo recomendado para obter e provisionar certificados envolve o serviço de Azure Key Vault e suas ferramentas. Um certificado correspondente à declaração de certificado de cluster deve ser provisionado em todos os nós dos conjuntos de dimensionamento de máquinas virtuais que abrangem o cluster; consulte os [segredos em conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm) para obter mais detalhes. É importante que os certificados de cluster atuais e de destino sejam instalados nas VMs de cada tipo de nó do cluster antes de fazer alterações nas declarações de certificado do cluster. A jornada da emissão de certificados para o provisionamento em um nó do Service Fabric é discutida em detalhes [aqui](cluster-security-certificate-management.md#the-journey-of-a-certificate).

## <a name="bring-cluster-to-an-optimal-starting-state"></a>Colocar o cluster em um estado de inicialização ideal
A conversão de uma declaração de certificado de com base em impressão digital com base em nome comum afeta ambos:

- Como cada nó no cluster localiza e apresenta suas credenciais para outros nós
- Como cada nó valida as credenciais de sua contraparte ao estabelecer uma conexão segura  

Examine as [regras de validação e apresentação para ambas as configurações](cluster-security-certificates.md#certificate-configuration-rules) antes de continuar. A consideração mais importante ao executar uma conversão de nome de impressão digital para comum é que os nós atualizados e ainda não atualizados (ou seja, os nós que pertencem a domínios de atualização diferentes) devem ser capazes de executar uma autenticação mútua bem-sucedida a qualquer momento durante a atualização. A maneira recomendada para conseguir isso é declarar o certificado de destino/meta por impressão digital em uma atualização inicial e concluir a transição para o nome comum em um subseqüente. Se o cluster já estiver em um estado de início recomendado, esta seção poderá ser ignorada.

Há vários Estados iniciais válidos para uma conversão; a constante é que o cluster já está usando o certificado de destino (declarado pela impressão digital) no início da atualização para o nome comum. Consideramos `GoalCert` `OldCert1` `OldCert2` :

#### <a name="valid-starting-states"></a>Estados de inicialização válidos
- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, onde `GoalCert` tem uma `NotAfter` data posterior à de `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, onde `GoalCert` tem uma `NotAfter` data posterior à de `OldCert1`

Se o cluster não estiver em um dos Estados válidos descritos acima, consulte o apêndice sobre como atingir esse estado no final deste artigo.

## <a name="select-the-desired-common-name-based-certificate-validation-scheme"></a>Selecione o esquema de validação de certificado baseado em nome comum desejado
Conforme descrito anteriormente, Service Fabric dá suporte à declaração de certificados pelo CN com uma âncora de confiança implícita ou com a fixação explícita das impressões digitais do emissor. Consulte [Este artigo](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) para obter detalhes e verifique se você tem uma boa compreensão das diferenças e as implicações de escolher um dos mecanismos. Sintaticamente, essa diferença/opção é determinada pelo valor do `certificateIssuerThumbprintList` parâmetro: Empty significa depender de uma AC raiz confiável (âncora de confiança), enquanto um conjunto de impressões digitais restringe os emissores diretos permitidos de certificados de cluster.

   > [!NOTE]
   > O campo ' certificateIssuerThumbprint ' permite especificar os emissores diretos esperados de certificados declarados pelo nome comum da entidade. Os valores aceitáveis são uma ou mais impressões digitais SHA1 separadas por vírgula. Observe que isso é um reforço da validação do certificado – se nenhum emissor for especificado/a lista estiver vazia, o certificado será aceito para autenticação se sua cadeia puder ser criada e terminará em uma raiz confiável pelo validador. Se uma ou mais impressões digitais do emissor forem especificadas, o certificado será aceito se a impressão digital de seu emissor direto, conforme extraído da cadeia, corresponder a qualquer um dos valores especificados neste campo – independentemente de a raiz ser confiável ou não. Observe que uma PKI pode usar autoridades de certificação (' emissores ') diferentes para assinar certificados com um determinado assunto e, portanto, é importante especificar todas as impressões digitais do emissor esperado para esse assunto. Em outras palavras, a renovação de um certificado não tem garantia de ser assinada pelo mesmo emissor do certificado que está sendo renovado.
   >
   > Especificar o emissor é considerado uma melhor prática. Embora sua omissão continue funcionando – para certificados com uma cadeia confiável até uma raiz confiável –, esse comportamento tem limitações e poderá ser desativado em breve. Observe também que clusters implantados no Azure e protegidos por certificados X509 emitidos por uma PKI privada e declarados por uma entidade talvez não possam ser validados pelo serviço do Azure Service Fabric (para comunicação de cluster com serviço) se a política de certificação da PKI não for localizável, disponível e acessível. 

## <a name="update-the-clusters-azure-resource-management-arm-template-and-deploy"></a>Atualizar o modelo ARM (gerenciamento de recursos do Azure) do cluster e implantar
É recomendável gerenciar clusters de Service Fabric do Azure com modelos ARM; uma alternativa, também o uso de artefatos JSON, é a [Azure Resource Explorer (versão prévia)](https://resources.azure.com). Uma experiência equivalente não está disponível no portal do Azure no momento. Se o modelo original correspondente a um cluster existente não estiver disponível, um modelo equivalente poderá ser obtido no portal do Azure navegando até o grupo de recursos que contém o cluster, selecionando **Exportar modelo** no menu à esquerda da **automação** e, em seguida, selecionando os recursos desejados. no mínimo, o conjunto de dimensionamento de máquinas virtuais e os recursos de cluster, respectivamente, devem ser exportados. O modelo gerado também pode ser baixado. Observação Este modelo pode exigir alterações antes de ser totalmente implantável e pode não corresponder exatamente ao original-ele é uma reflexão do estado atual do recurso de cluster.

As alterações necessárias são as seguintes:
    - Atualizando a definição da extensão do nó de Service Fabric (no recurso de máquina virtual); Se o cluster definir vários tipos de nó, será necessário atualizar a definição de cada conjunto de dimensionamento de máquinas virtuais correspondente
    - Atualizando a definição de recurso de cluster

Exemplos detalhados estão incluídos abaixo.

### <a name="updating-the-virtual-machine-scale-set-resources"></a>Atualizando os recursos do conjunto de dimensionamento de máquinas virtuais
De
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```
Para
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```

### <a name="updating-the-cluster-resource"></a>Atualizando o recurso de cluster
No recurso **Microsoft. perfabric/clusters** , adicione uma propriedade **certificateCommonNames** com uma configuração **commonnames** e remova totalmente a propriedade do **certificado** (todas as suas configurações):

De
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```
Para
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprintList')]"
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```

Para obter mais informações, consulte [implantar um Service Fabric cluster que usa o nome comum do certificado em vez da impressão digital.](./service-fabric-create-cluster-using-cert-cn.md)

## <a name="deploy-the-updated-template"></a>Implantar o modelo atualizado
Reimplante o modelo atualizado após fazer as alterações.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="appendix-achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>Apêndice: obter um estado de inicialização válido para converter um cluster para declarações de certificado baseadas em CN

| Estado inicial | Atualização 1 | Atualização 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` e `GoalCert` tem uma `NotAfter` data posterior a `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` e `OldCert1` tem uma `NotAfter` data posterior a `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, onde `OldCert1` tem uma `NotAfter` data posterior a `GoalCert` | Atualizar para o `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, onde `OldCert1` tem uma `NotAfter` data posterior a `GoalCert` | Atualizar para o `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | Remover um de `OldCert1` ou `OldCert2` para chegar ao estado `Thumbprint: OldCertx, ThumbprintSecondary: None` | Continuar a partir do novo estado inicial |

Para obter instruções sobre como realizar qualquer uma dessas atualizações, consulte [este documento](service-fabric-cluster-security-update-certs-azure.md).


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a [segurança do cluster](service-fabric-cluster-security.md).
* Saiba como [sobrepor um certificado de cluster por nome comum](service-fabric-cluster-rollover-cert-cn.md)
* Saiba como [configurar um cluster para substituição](cluster-security-certificate-management.md) automática sem toque

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
