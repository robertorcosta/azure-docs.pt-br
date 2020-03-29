---
title: Serviços web seguros usando TLS
titleSuffix: Azure Machine Learning
description: Saiba como ativar o HTTPS para garantir um serviço web implantado através do Azure Machine Learning. O Azure Machine Learning usa a versão 1.2 do TLS para proteger modelos implantados como serviços web.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: a58b0120feaba907c62bc646f4f85d9185227fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287332"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>Use o TLS para garantir um serviço web através do Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo mostra como proteger um serviço web implantado através do Azure Machine Learning.

Você usa [HTTPS](https://en.wikipedia.org/wiki/HTTPS) para restringir o acesso a serviços web e proteger os dados que os clientes enviam. O HTTPS ajuda a proteger as comunicações entre um cliente e um serviço web, criptografando as comunicações entre os dois. A criptografia usa [TLS (Transport Layer Security, segurança de camada de transporte)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS às vezes ainda é referido como *Secure Sockets Layer* (SSL), que foi o antecessor do TLS.

> [!TIP]
> O Azure Machine Learning SDK usa o termo "SSL" para propriedades relacionadas a comunicações seguras. Isso não significa que seu serviço web não use *TLS*. SSL é apenas um termo mais comumente reconhecido.
>
> Especificamente, os serviços web implantados através do Azure Machine Learning só suportam a versão 1.2 do TLS.

TLS e SSL dependem de *certificados digitais,* que ajudam na criptografia e na verificação de identidade. Para obter mais informações sobre como os certificados digitais funcionam, consulte o tópico Wikipedia [Infraestrutura de chaves públicas](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Se você não usar HTTPS para o seu serviço web, os dados enviados para e do serviço podem ser visíveis para outros na internet.
>
> O HTTPS também permite que o cliente verifique a autenticidade do servidor ao que ele está se conectando. Esse recurso protege os clientes contra ataques [man-in-the-middle.](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)

Este é o processo geral para garantir um serviço web:

1. Obter um nome de domínio.

2. Obtenha um certificado digital.

3. Implantar ou atualizar o serviço web com TLS ativado.

4. Atualizar o DNS para apontar para o serviço Web.

> [!IMPORTANT]
> Se você estiver implantando no Azure Kubernetes Service (AKS), você pode comprar seu próprio certificado ou usar um certificado fornecido pela Microsoft. Se você usar um certificado da Microsoft, você não precisa obter um nome de domínio ou certificado TLS/SSL. Para obter mais informações, consulte [o Ativar TLS e implantar a](#enable) seção deste artigo.

Há pequenas diferenças quando você segura s entre [alvos de implantação](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Obter um nome de domínio

Se você ainda não possui um nome de domínio, compre um de um *registrador*de nome de domínio . O processo e o preço diferem entre os registradores. O registrador fornece ferramentas para gerenciar o nome de domínio. Você usa essas ferramentas para mapear um nome de domínio totalmente\.qualificado (FQDN) (como www contoso.com) para o endereço IP que hospeda seu serviço web.

## <a name="get-a-tlsssl-certificate"></a>Obtenha um certificado TLS/SSL

Existem muitas maneiras de obter um certificado TLS/SSL (certificado digital). O mais comum é comprar um de uma *autoridade certificadora* (CA). Independentemente de onde você obtenha o certificado, você precisa dos seguintes arquivos:

* Um **certificado**. O certificado deve conter a cadeia completa de certificados, e deve ser "codificado por PEM".
* Uma **chave**. A chave também deve ser codificada por PEM.

Ao solicitar um certificado, você deve fornecer o FQDN do endereço que você pretende\.usar para o serviço web (por exemplo, www contoso.com). O endereço que está estampado no certificado e o endereço que os clientes usam são comparados para verificar a identidade do serviço web. Se esses endereços não coincidirem, o cliente recebe uma mensagem de erro.

> [!TIP]
> Se a autoridade do certificado não puder fornecer o certificado e a chave como arquivos codificados pelo PEM, você poderá usar um utilitário como [o OpenSSL](https://www.openssl.org/) para alterar o formato.

> [!WARNING]
> Use certificados *auto-assinados* apenas para desenvolvimento. Não os use em ambientes de produção. Certificados autoassinados podem causar problemas nos aplicativos clientes. Para obter mais informações, consulte a documentação das bibliotecas de rede que seu aplicativo cliente usa.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a>Habilite o TLS e implante

Para implantar (ou reimplantar) o serviço com O TLS ativado, defina o parâmetro *ssl_enabled* como "True" onde for aplicável. Defina o parâmetro *ssl_certificate* para o valor do arquivo do *certificado.* Defina o *ssl_key* para o valor do *arquivo-chave.*

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Implantar em AKS e matriz de portão programável em campo (FPGA)

  > [!NOTE]
  > As informações nesta seção também se aplicam quando você implanta um serviço web seguro para o designer. Se você não está familiarizado com o uso do Python SDK, veja [o que é o Azure Machine Learning SDK para Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)

Quando você implanta no AKS, você pode criar um novo cluster AKS ou anexar um já existente. Para obter mais informações sobre como criar ou anexar um cluster, consulte [Implantar um modelo em um cluster do Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).
  
-  Se você criar um novo cluster, você usará **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)**
- Se você anexar um cluster existente, você usará **[AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** Ambos retornam um objeto de configuração que tem um método **enable_ssl.**

O método **enable_ssl** pode usar um certificado fornecido pela Microsoft ou um certificado que você compra.

  * Quando você usa um certificado da Microsoft, você deve usar o parâmetro *leaf_domain_label.* Este parâmetro gera o nome DNS para o serviço. Por exemplo, um valor de "contoso" cria\<um nome de domínio de "contoso seis caracteres aleatórios>. \<azureregion>.cloudapp.azure.com", \<onde a região azul> é a região que contém o serviço. Opcionalmente, você pode usar o parâmetro *overwrite_existing_domain* para substituir as *leaf_domain_label*existentes .

    Para implantar (ou reimplantar) o serviço com O TLS ativado, defina o parâmetro *ssl_enabled* como "True" onde for aplicável. Defina o parâmetro *ssl_certificate* para o valor do arquivo do *certificado.* Defina o *ssl_key* para o valor do *arquivo-chave.*

    > [!IMPORTANT]
    > Quando você usa um certificado da Microsoft, você não precisa comprar seu próprio certificado ou nome de domínio.

    O exemplo a seguir demonstra como criar uma configuração que habilite um certificado TLS/SSL da Microsoft:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * Quando você usa *um certificado que você comprou,* você usa os *parâmetros ssl_cert_pem_file,* *ssl_key_pem_file*e *ssl_cname.* O exemplo a seguir demonstra como usar arquivos *.pem* para criar uma configuração que usa um certificado TLS/SSL que você comprou:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Para obter mais informações sobre *enable_ssl,* consulte [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) e [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)

### <a name="deploy-on-azure-container-instances"></a>Implantar em instâncias de contêiner do Azure

Quando você é implantado no Azure Container Instances, você fornece valores para parâmetros relacionados ao TLS, como mostra o seguinte trecho de código:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Para obter mais informações, consulte [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)

## <a name="update-your-dns"></a>Atualizar o DNS

Em seguida, você precisa atualizar o DNS para apontar para o serviço Web.

+ **Para instâncias de contêiner:**

  Use as ferramentas do registrador de nome de domínio para atualizar o registro DNS para o nome do seu domínio. O registro deve apontar para o endereço IP do serviço.

  Pode haver um atraso de minutos ou horas antes que os clientes possam resolver o nome de domínio, dependendo do registrador e do "tempo de vida" (TTL) configurado para o nome de domínio.

+ **Para AKS:**

  > [!WARNING]
  > Se você usou *leaf_domain_label* para criar o serviço usando um certificado da Microsoft, não atualize manualmente o valor de DNS para o cluster. O valor deve ser definido automaticamente.

  Atualize o DNS do endereço IP público do cluster AKS na guia **Configuração** em **Configurações** no painel esquerdo. (Veja a imagem a seguir.) O Endereço IP público é um tipo de recurso criado o grupo de recursos que contém os ádenos de agente AKS e outros recursos de rede.

  [![Aprendizado de máquina do Azure: protegendo serviços web com TLS](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>Atualize o certificado TLS/SSL

Os certificados TLS/SSL expiram e devem ser renovados. Normalmente isso acontece todo ano. Use as informações nas seguintes seções para atualizar e renovar seu certificado para modelos implantados no Azure Kubernetes Service:

### <a name="update-a-microsoft-generated-certificate"></a>Atualize um certificado gerado pela Microsoft

Se o certificado foi originalmente gerado pela Microsoft (ao usar o *leaf_domain_label* para criar o serviço), use um dos seguintes exemplos para atualizar o certificado:

**Use o SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**Como usar a CLI**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True
```

Para obter mais informações, consulte os seguintes dados de referência:

* [Configuração ssl](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguração](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>Atualizar certificado personalizado

Se o certificado foi originalmente gerado por uma autoridade de certificado, use as seguintes etapas:

1. Use a documentação fornecida pela autoridade certificadora para renovar o certificado. Esse processo cria novos arquivos de certificados.

1. Use o SDK ou a CLI para atualizar o serviço com o novo certificado:

    **Use o SDK**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **Como usar a CLI**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

Para obter mais informações, consulte os seguintes dados de referência:

* [Configuração ssl](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguração](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-tls"></a>Desativar TLS

Para desativar o TLS para um modelo implantado no Azure Kubernetes Service, crie um `SslConfiguration` com `status="Disabled"`e execute uma atualização:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Próximas etapas
Saiba como:
+ [Consumir um modelo de machine learning implantado como um serviço web](how-to-consume-web-service.md)
+ [Execute com segurança experimentos e inferênciadentro de uma rede virtual do Azure](how-to-enable-virtual-network.md)
