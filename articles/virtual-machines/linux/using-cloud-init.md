---
title: Visão geral do suporte à cloud-init para VMs do Linux no Azure
description: Visão geral de funcionalidades da cloud-init para configurar uma VM no tempo de provisionamento no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/19/2019
ms.author: danis
ms.openlocfilehash: 9e42229b08d7817b64c66c4ab23877c837339475
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83827311"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Suporte à cloud-init para máquinas virtuais no Azure
Este artigo mostra que o suporte que existe para a [cloud-init](https://cloudinit.readthedocs.io) para configurar uma máquina virtual VM ou conjuntos de dimensionamento de máquinas virtuais no momento do provisionamento no Azure. Essas configurações de cloud-init são executadas na primeira inicialização depois que os recursos são provisionados pelo Azure.  

O provisionamento de VM é o processo em que o Azure passará seus valores de parâmetro de criação de VM, como nome de host, nome de usuário, senha etc. e os disponibilizará para a VM à medida que ele for inicializado. Um 'agente de provisionamento' consumirá esses valores, vai configurar a VM e relatará quando isso for concluído. 

O Azure dá suporte a dois agentes de provisionamento de [cloud-init](https://cloudinit.readthedocs.io) e ao [WALA (Agente Linux do Azure)](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux).

## <a name="cloud-init-overview"></a>Visão geral da cloud-init
A [cloud-init](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM do Linux quando ela é inicializada pela primeira vez. Você pode utilizar a inicialização de nuvem para instalar pacotes e gravar arquivos, ou para configurar usuários e segurança. Como o cloud-init é executado durante o processo de inicialização inicial, não há etapa adicional ou agentes necessários para aplicar a configuração.  Para obter mais informações sobre como formatar corretamente seus arquivos `#cloud-config` ou outras entradas, confira o [site de documentação da cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  Os arquivos `#cloud-config` são arquivos de texto codificados em base64.

A cloud-init também funciona entre distribuições diferentes. Por exemplo, você não usa **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, você pode definir uma lista de pacotes para instalar. A cloud-init usará automaticamente a ferramenta de gerenciamento de pacote nativo de distribuição que você selecionar.

Trabalhamos ativamente com nossos parceiros endossados de distribuição de Linux para termos imagens de cloud-init habilitadas disponíveis no marketplace do Azure. Essas imagens farão com que as implantações e as configurações de cloud-init funcionem perfeitamente com VMs e conjuntos de dimensionamento de máquinas virtuais. Inicialmente, colaboramos com os parceiros da distribuição de Linux endossada e upstream para garantir as funções de cloud-init com o sistema operacional no Azure, então os pacotes são atualizados e disponibilizados publicamente nos repositórios de pacotes da distribuição. 

Há dois estágios para disponibilizar o cloud-init para os sistemas operacionais de distribuição do Linux endossados no Azure, o suporte a pacotes e o suporte a imagens:
* o "suporte a pacotes do cloud-init no Azure" documenta quais pacotes do cloud-init, juntamente com as versões posteriores, são compatíveis ou estão em versão prévia, para que você possa usar esses pacotes com o sistema operacional em uma imagem personalizada.
* "imagem pronta para o cloud-init" documenta se a imagem já está configurada para usar o cloud-init.


### <a name="canonical"></a>Canônico
| Fornecedor/versão| Oferta | SKU | Versão | imagem pronta para o cloud-init | suporte ao pacote do cloud-init no Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical 20.04 |UbuntuServer |18.04-LTS |mais recente |sim | sim |
|Canonical 18.04 |UbuntuServer |18.04-LTS |mais recente |sim | sim |
|Canonical 16.04|UbuntuServer |16.04-LTS |mais recente |sim | sim |
|Canonical 14.04|UbuntuServer |14.04.5-LTS |mais recente |sim | sim |

### <a name="rhel"></a>RHEL
| Fornecedor/versão | Oferta | SKU | Versão | imagem pronta para o cloud-init | suporte ao pacote do cloud-init no Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |sim | sim – suporte da versão do pacote: *18.2-1.el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | sim (observe que esta é uma imagem em versão prévia e, depois que todas as imagens do RHEL 7.7 forem compatíveis com o cloud-init, ela será removida em 1º de setembro de 2020) | sim – suporte da versão do pacote: *18.5-6.el7*|
|RedHat 7.7 (Gen1)|RHEL |7.7 | 7.7.2020051912 | não – atualizações de imagem em andamento, a serem concluídas no fim de maio | sim – suporte da versão do pacote: *18.5-6.el7*|
|RedHat 7.7 (Gen2)|RHEL | 77-gen2 | 7.7.2020051913 | não – atualizações de imagem em andamento, a serem concluídas no fim de maio | sim – suporte da versão do pacote: *18.5-6.el7*|
|RedHat 7.7 (Gen1)|RHEL |7-LVM | 7.7.2020051921 | não – atualizações de imagem em andamento, a serem concluídas no fim de maio | sim – suporte da versão do pacote: *18.5-6.el7*|
|RedHat 7.7 (Gen2)|RHEL | 7lvm-gen2 | 7.7.2020051922  | não – atualizações de imagem em andamento, a serem concluídas no fim de maio | sim – suporte da versão do pacote: *18.5-6.el7*|
|RedHat 7.7 (Gen1) |rhel-byos | rhel-lvm77 | 7.7.20200416 | não – atualizações de imagem em andamento, a serem concluídas no fim de maio  | sim – suporte da versão do pacote: *18.5-6.el7*|
|RedHat 8.1 (Gen1) |RHEL |8.1-ci |8.1.2020042511 | sim (observe que esta é uma imagem em versão prévia e, depois que todas as imagens do RHEL 8.1 forem compatíveis com o cloud-init, ela será removida em 1º de agosto de 2020) | Não, ETA para suporte completo em junho de 2020|
|RedHat 8.1 (Gen2) |RHEL |81-ci-gen2 |8.1.2020042524 | sim (observe que esta é uma imagem em versão prévia e, depois que todas as imagens do RHEL 8.1 forem compatíveis com o cloud-init, ela será removida em 1º de agosto de 2020) | Não, ETA para suporte completo em junho de 2020 |

As imagens do RedHat: RHEL 7.8 e 8.2 (Gen1 e Gen2) são provisionadas usando cloud-init.

### <a name="centos"></a>CentOS

| Fornecedor/versão | Oferta | SKU | Versão | imagem pronta para o cloud-init | suporte ao pacote do cloud-init no Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7.7 |CentOS |7-CI |7.7.20190920 |sim (observe que esta é uma imagem em versão prévia e, depois que todas as imagens do CentOS 7.7 forem compatíveis com o cloud-init, ela será removida em 1º de setembro de 2020) | sim – suporte da versão do pacote: *18.5-3.el7.centos*|

* As imagens do CentOS 7.7 que serão habilitadas para o cloud-init serão atualizadas aqui em junho de 2020 
* As imagens do CentOS 7.8 são provisionadas usando o cloud-init.


### <a name="oracle"></a>Oracle

| Fornecedor/versão | Oferta | SKU | Versão | imagem pronta para o cloud-init | suporte ao pacote do cloud-init no Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7.7 |Oracle-Linux |77-ci |7.7.01| imagem em versão prévia (observe que esta é uma imagem em versão prévia e, uma vez que todas as imagens do Oracle 7.7 forem compatíveis com o cloud-init, isso será removido em meados de 2020, após um aviso prévio ter sido fornecido) | não, na versão prévia, o pacote é: *18.5-3.0.1.el7*

### <a name="suse-sles"></a>SUSE SLES
| Fornecedor/versão | Oferta | SKU | Versão | imagem pronta para o cloud-init | suporte ao pacote do cloud-init no Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|SUSE SLES 15 SP1 |suse |sles-15-sp1-basic |cloud-init-preview| Confira o [blog da SUSE sobre o cloud-init](https://suse.com/c/clout-init-coming-to-suse-images-in-azure/) para obter detalhes | Não, em versão prévia. |
|SUSE SLES 15 SP1 |suse |sles-15-sp1-basic |gen2-cloud-init-preview| Confira o [blog da SUSE sobre o cloud-init](https://suse.com/c/clout-init-coming-to-suse-images-in-azure/) para obter detalhes | Não, em versão prévia. |


### <a name="debian"></a>Debian
No momento, estamos trabalhando para dar suporte à versão prévia, atualizações são esperadas para junho de 2020.

Atualmente, o Azure Stack dará suporte ao provisionamento de imagens habilitadas para cloud-init.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Qual é a diferença entre cloud-init e o Agente do Linux (WALA)?
O WALA é um agente específico da plataforma do Azure usado para provisionar e configurar VMs e lidar com [extensões do Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux). 

Estamos aprimorando a tarefa de configuração de VMs para usar cloud-init em vez do agente do Linux, para permitir que os clientes existentes de cloud-init usem seus scripts atuais de cloud-init ou que os novos clientes aproveitem as funcionalidades avançadas de configuração do cloud-init. Se você tiver investimentos existentes em scripts do cloud-init para configurar os sistemas Linux, não há **nenhuma configuração adicional necessária** para habilitar o cloud-init para processá-los. 

O cloud-init não pode processar extensões do Azure, portanto, o WALA ainda é necessário na imagem para processar extensões, mas precisará ter o respectivo código de provisionamento desabilitado, pois imagens de distribuições do Linux endossadas que estejam sendo convertidas para provisionamento pelo cloud-init terão o WALA instalado e estarão configuradas corretamente.

Ao criar uma VM, se você não incluir a opção `--custom-data` da CLI do Azure no momento de provisionamento, o cloud-init ou o WALA usa os parâmetros necessários mínimos para provisionar a VM e concluir a implantação com os padrões.  Se você fizer referência à configuração do cloud-init com a opção `--custom-data`, tudo o que estiver contido em seus dados personalizados estará disponível para o cloud-init quando a VM for inicializada.

As configurações de cloud-init aplicadas às VMs não têm restrições de tempo e não farão com que uma implantação falhe por tempo limite. Isso não se verifica para o WALA, pois se você alterar os padrões do WALA para processar dados personalizados, ele não poderá exceder o tempo de provisionamento total permitido de 40 minutos da VM, caso contrário, a criação da VM falhará.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Implantando uma Máquina Virtual habilitada para cloud-init
Implantar uma máquina virtual habilitada para cloud-init é tão simples quanto fazer referência a uma distribuição habilitada para cloud-init durante a implantação.  Os mantenedores da distribuição de Linux precisam optar por habilitar e integrar o cloud-init em suas imagens publicadas da base do Azure. Depois de confirmar, a imagem que você deseja implantar é habilitada para cloud-init, e você pode usar a CLI do Azure para implantar a imagem. 

A primeira etapa para implantar essa imagem é criar um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
A próxima etapa é criar um arquivo no shell atual, chamado *cloud-init.txt*, e colar a configuração a seguir. Para este exemplo, crie o arquivo no Cloud Shell, não no seu computador local. Você pode usar qualquer editor que queira. Insira `sensible-editor cloud-init.txt` para criar o arquivo e ver uma lista de editores disponíveis. Escolha #1 para usar o editor **nano**. Certifique-se de que o arquivo de inicialização de nuvem inteiro seja copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Pressione `ctrl-X` para sair do arquivo, digite `y` para salvar o arquivo e pressione `enter` para confirmar o nome do arquivo na saída.

A etapa final cria uma VM com o comando [az vm create](/cli/azure/vm). 

O exemplo a seguir cria uma VM denominada *centos74* e cria chaves SSH, se elas ainda não existirem em um local de chave padrão. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.  Utiçize o `--custom-data` parâmetro para passar no arquivo de configuração de inicialização de nuvem. Forneça o caminho completo para a configuração *cloud-init.txt* se você salvou o arquivo fora do seu diretório de trabalho atual. O exemplo a seguir cria uma VM chamada *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Quando a VM tiver sido criada, a CLI do Azure mostrará informações específicas para a sua implantação. Anote `publicIpAddress`. Esse endereço é usado para acessar a VM.  Leva algum tempo para que a VM seja criada, os pacotes sejam instalados e o aplicativo comece a funcionar. Há tarefas em segundo plano que continuarão em execução depois que a CLI do Azure faz você voltar para o prompt. Você pode usar SSH na VM e usar as etapas descritas na seção de resolução de problemas para exibir os logs de cloud-init. 

## <a name="troubleshooting-cloud-init"></a>Resolução de problemas do cloud-init
Depois que a VM tiver sido provisionada, o cloud-init será executado em todos os módulos e o script definido em `--custom-data` para configurar a VM.  Se você precisar solucionar quaisquer erros ou omissões da configuração, você precisará pesquisar o nome do módulo (`disk_setup` ou `runcmd` por exemplo) no log do cloud-init - localizado em **/var/log/cloud-init.log**.

> [!NOTE]
> Nem toda falha do módulo resulta em uma falha fatal de configuração geral do cloud-init. Por exemplo, usando o módulo `runcmd`, se o script falhar, o cloud-init ainda relatará o provisionamento bem-sucedido, porque o módulo runcmd foi executado.

Para obter mais detalhes de registro do cloud-init, consulte a [documentação do cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Próximas etapas
Para obter exemplos de alterações de configuração do cloud-init, consulte os seguintes documentos:
 
- [Add an additional Linux user to a VM](cloudinit-add-user.md) (Adicionar um usuário adicional do Linux a uma VM)
- [Run a package manager to update existing packages on first boot](cloudinit-update-vm.md) (Executar um gerenciador de pacotes para atualizar os pacotes existentes na primeira inicialização)
- [Change VM local hostname](cloudinit-update-vm-hostname.md) (Alterar o nome do host local da VM) 
- [Install an application package, update configuration files and inject keys](tutorial-automate-vm-deployment.md) (Instalar um pacote de aplicativo, atualizar os arquivos de configuração e injetar chaves)
 
