---
title: Visão geral do suporte à nuvem para VMs Linux no Azure
description: Visão geral dos recursos de entrada na nuvem para configurar uma VM no momento de provisionamento no Azure.
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
ms.date: 01/23/2019
ms.author: danis
ms.openlocfilehash: 1f0395956fa6977be5d1d6f4f4faf06b84c094d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79465032"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>suporte em nuvem para máquinas virtuais no Azure
Este artigo explica o suporte que existe para [o cloud-init](https://cloudinit.readthedocs.io) configurar uma máquina virtual (VM) ou conjuntos de escala de máquina virtual no momento de provisionamento no Azure. Essas configurações de entrada na nuvem são executadas na primeira inicialização, uma vez que os recursos foram provisionados pelo Azure.  

O Provisionamento vm é o processo em que o Azure passará seus valores de parâmetro de Criação de VM, como nome de host, nome de usuário, senha etc., e os disponibilizará para a VM à medida que ele inicializa. Um 'agente de provisionamento' consumirá esses valores, configurará a VM e reportará de volta quando concluído. 

O Azure suporta dois agentes de provisionamento [em nuvem](https://cloudinit.readthedocs.io), e o [Azure Linux Agent (WALA)](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux).

## <a name="cloud-init-overview"></a>visão geral cloud-init
[cloud-init](https://cloudinit.readthedocs.io) é uma abordagem amplamente usada para personalizar um VM Linux como ele inicializa pela primeira vez. Você pode utilizar a inicialização de nuvem para instalar pacotes e gravar arquivos, ou para configurar usuários e segurança. Como o cloud-init é executado durante o processo de inicialização inicial, não há etapa adicional ou agentes necessários para aplicar a configuração.  Para obter mais informações sobre `#cloud-config` como formatar corretamente seus arquivos ou outras entradas, consulte o [site de documentação na nuvem](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  Os arquivos `#cloud-config` são arquivos de texto codificados em base64.

cloud-init também funciona em todas as distribuições. Por exemplo, você não usa **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, você pode definir uma lista de pacotes para instalar. cloud-init usa automaticamente a ferramenta nativa de gerenciamento de pacotes para o distro selecionado.

Trabalhamos ativamente com nossos parceiros endossados de distribuição de Linux para termos imagens de cloud-init habilitadas disponíveis no marketplace do Azure. Essas imagens farão com que as implantações e as configurações de cloud-init funcionem perfeitamente com VMs e conjuntos de dimensionamento de máquinas virtuais. Inicialmente, colaboramos com os parceiros de distro Linux endossados e upstream para garantir funções de entrada na nuvem com o SO no Azure, então os pacotes são atualizados e disponibilizados publicamente nos repositórios do pacote distro. 

Existem dois estágios para disponibilizar o cloud-init para os sodores Linux endossados no Azure, suporte a pacotes e, em seguida, suporte a imagens:
* 'suporte a pacotes de nuvem no Azure' documentos que pacotes de entrada na nuvem em diante são suportados ou em visualização, para que você possa usar esses pacotes com o SO em uma imagem personalizada.
* Documentos 'em nuvem de imagem' se a imagem já estiver configurada para usar o cloud-init.


### <a name="canonical"></a>Canônico
| Editor / Versão| Oferta | SKU | Versão | imagem nuvem-init pronto | suporte a pacote sinit de nuvem no Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical 18.04 |UbuntuServer |18.04-LTS |mais recente |sim | sim |
|Canonical 16.04|UbuntuServer |16.04-LTS |mais recente |sim | sim |
|Canonical 14.04|UbuntuServer |14.04.5-LTS |mais recente |sim | sim |

### <a name="rhel"></a>RHEL
| Editor / Versão | Oferta | SKU | Versão | imagem nuvem-init pronto | suporte a pacote sinit de nuvem no Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |sim | sim - suporte da versão do pacote: *18.2-1.el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | sim (note que esta é uma imagem de visualização, e uma vez que todas as imagens RHEL 7.7 suportam cloud-init, isso será removido em meados de 2020, aviso será dado) | sim - suporte da versão do pacote: *18.5-3.el7*|
|RedHat 7.7 |RHEL |7-RAW | n/d| não - atualizações de imagem para completar final de abril de 2020| sim - suporte da versão do pacote: *18.5-3.el7*|
|RedHat 7.7 |RHEL |7-LVM | n/d| não - atualizações de imagem para completar o final de abril| sim - suporte da versão do pacote: *18.5-3.el7*|
|RedHat 7.7 |RHEL |7.7 | n/d| não - atualizações de imagem para completar o final de abril | sim - suporte da versão do pacote: *18.5-3.el7*|
|RedHat 7.7 |rhel-byos | rhel-lvm77 | n/d|não - atualizações de imagem para completar o final de abril  | sim - suporte da versão do pacote: *18.5-3.el7*|

### <a name="centos"></a>CentOS

| Editor / Versão | Oferta | SKU | Versão | imagem nuvem-init pronto | suporte a pacote sinit de nuvem no Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7.7 |CentOS |7-CI |7.7.20190920 |Sim (note que esta é uma imagem de visualização, e uma vez que todas as imagens do CentOS 7.7 suportam cloud-init, isso será removido em meados de 2020, o aviso será dado) | sim - suporte da versão do pacote: *18.5-3.el7.centos*|

* As imagens do CentOS 7.7 que serão habilitadas para nuvem serão atualizadas aqui em março de 2020 

### <a name="oracle"></a>Oracle

| Editor / Versão | Oferta | SKU | Versão | imagem nuvem-init pronto | suporte a pacote sinit de nuvem no Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7.7 |Oracle-Linux |77-ci |7.7.01| imagem de visualização (note que esta é uma imagem de visualização, e uma vez que todas as imagens Oracle 7.7 suportam cloud-init, isso será removido em meados de 2020, aviso será dado) | não, na pré-visualização, o pacote é: *18.5-3.0.1.el7*

### <a name="debian--suse-sles"></a>Debian & SuSE SLES
No momento, estamos trabalhando para visualizar o suporte, esperar atualizações em fevereiro e março de 2020.

Atualmente, o Azure Stack suportará o provisionamento de imagens habilitadas para nuvem.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Qual é a diferença entre cloud-init e o Agente do Linux (WALA)?
WALA é um agente específico da plataforma Azure usado para provisionar e configurar VMs e lidar com [extensões Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux). 

Estamos reforçando a tarefa de configurar VMs para usar o Cloud-init em vez do Linux Agent, a fim de permitir que os clientes existentes na nuvem usem seus scripts de entrada na nuvem atuais ou novos clientes para tirar proveito da rica funcionalidade de configuração em nuvem. Se você tiver investimentos existentes em scripts de entrada na nuvem para configurar sistemas Linux, não há **configurações adicionais necessárias** para permitir que o processe em nuvem. 

cloud-init não pode processar extensões do Azure, então wala ainda é necessário na imagem para processar extensões, mas precisará ter seu código de provisionamento desativado, para imagens de distros Linux endossadas que estão sendo convertidas para provisão por cloud-init, eles terão WALA instalado e configurado corretamente.

Ao criar uma VM, se você não `--custom-data` incluir o switch Azure CLI no tempo de provisionamento, o cloud-init ou wala toma os parâmetros mínimos de provisionamento de VM necessários para provisionar a VM e concluir a implantação com os padrões.  Se você referenciar a configuração `--custom-data` de cloud-init com o switch, o que estiver contido em seus dados personalizados estará disponível para nuvem-init quando a VM for inicializada.

configurações de nuvem-init aplicadas a VMs não têm restrições de tempo e não farão com que uma implantação falhe ao cronometrar. Isso não é verdade para wala, se você alterar os padrões wala para processar dados personalizados, ele não pode exceder o subsídio total de tempo de provisionamento vm de 40mins, se assim for, o VM Create falhará.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Implantando uma Máquina Virtual habilitada para cloud-init
Implantar uma máquina virtual habilitada para cloud-init é tão simples quanto fazer referência a uma distribuição habilitada para cloud-init durante a implantação.  Os mantenedores da distribuição de Linux precisam optar por habilitar e integrar o cloud-init em suas imagens publicadas da base do Azure. Depois de confirmar, a imagem que você deseja implantar é habilitada para cloud-init, e você pode usar a CLI do Azure para implantar a imagem. 

A primeira etapa para implantar essa imagem é criar um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no *local eastus.*

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
 
