---
title: Visão geral do suporte de Cloud-init para VMs Linux no Azure
description: Visão geral de recursos de inicialização de nuvem para configurar uma VM no tempo de provisionamento no Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79465032"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>suporte de inicialização de nuvem para máquinas virtuais no Azure
Este artigo explica o suporte que existe para [Cloud-init](https://cloudinit.readthedocs.io) para configurar uma VM (máquina virtual) ou conjuntos de dimensionamento de máquinas virtuais no tempo de provisionamento no Azure. Essas configurações de inicialização de nuvem são executadas na primeira inicialização depois que os recursos são provisionados pelo Azure.  

O provisionamento de VM é o processo em que o Azure passará seus valores de parâmetro de criação de VM, como nome de host, nome de usuário, senha, etc., e disponibilizá-los para a VM à medida que ele for inicializado. Um ' agente de provisionamento ' consumirá esses valores, configurará a VM e relatará quando for concluído. 

O Azure dá suporte a dois agentes [de provisionamento Cloud-init](https://cloudinit.readthedocs.io)e ao [wala (agente Linux do Azure)](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux).

## <a name="cloud-init-overview"></a>Visão geral de Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) é uma abordagem amplamente usada para personalizar uma VM do Linux à medida que ela é inicializada pela primeira vez. Você pode utilizar a inicialização de nuvem para instalar pacotes e gravar arquivos, ou para configurar usuários e segurança. Como o cloud-init é executado durante o processo de inicialização inicial, não há etapa adicional ou agentes necessários para aplicar a configuração.  Para obter mais informações sobre como formatar corretamente seus `#cloud-config` arquivos ou outras entradas, consulte o [site de documentação de Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  Os arquivos `#cloud-config` são arquivos de texto codificados em base64.

Cloud-init também funciona em distribuições. Por exemplo, você não usa **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, você pode definir uma lista de pacotes para instalar. Cloud-init usa automaticamente a ferramenta de gerenciamento de pacotes nativo para o distribuição que você selecionar.

Trabalhamos ativamente com nossos parceiros endossados de distribuição de Linux para termos imagens de cloud-init habilitadas disponíveis no marketplace do Azure. Essas imagens farão com que as implantações e as configurações de cloud-init funcionem perfeitamente com VMs e conjuntos de dimensionamento de máquinas virtuais. Inicialmente, colaboros com os parceiros endossados do Linux distribuição e upstream para garantir as funções de Cloud-init com o sistema operacional no Azure, então os pacotes são atualizados e disponibilizados publicamente nos repositórios de pacotes do distribuição. 

Há dois estágios para disponibilizar a Cloud-init para o sistema operacional distribuição do Linux endossado no Azure, suporte a pacotes e suporte a imagens:
* o "suporte a pacotes Cloud-init no Azure" documenta quais pacotes Cloud-init em diante têm suporte ou em versão prévia, para que você possa usar esses pacotes com o sistema operacional em uma imagem personalizada.
* documentos "imagem de nuvem-inicialização pronta" se a imagem já estiver configurada para usar Cloud-init.


### <a name="canonical"></a>Canônico
| Publicador/versão| Oferta | SKU | Versão | imagem de nuvem-inicialização pronta | suporte ao pacote Cloud-init no Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical 18, 4 |UbuntuServer |18.04-LTS |mais recente |sim | sim |
|Canonical 16, 4|UbuntuServer |16.04-LTS |mais recente |sim | sim |
|Canonical 14, 4|UbuntuServer |14.04.5-LTS |mais recente |sim | sim |

### <a name="rhel"></a>RHEL
| Publicador/versão | Oferta | SKU | Versão | imagem de nuvem-inicialização pronta | suporte ao pacote Cloud-init no Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |sim | Sim-suporte da versão do pacote: *18.2-1. el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | Sim (Observe que esta é uma imagem de visualização e, uma vez que todas as imagens RHEL 7,7 dão suporte a Cloud-init, isso será removido em meados de 2020, o aviso será fornecido) | Sim-suporte da versão do pacote: *18.5 -3. EL7*|
|RedHat 7.7 |RHEL |7-RAW | N/D| nenhuma atualização de imagem para concluir o fim de abril de 2020| Sim-suporte da versão do pacote: *18.5 -3. EL7*|
|RedHat 7.7 |RHEL |7-LVM | N/D| nenhuma atualização de imagem para concluir o fim de abril| Sim-suporte da versão do pacote: *18.5 -3. EL7*|
|RedHat 7.7 |RHEL |7,7 | N/D| nenhuma atualização de imagem para concluir o fim de abril | Sim-suporte da versão do pacote: *18.5 -3. EL7*|
|RedHat 7.7 |RHEL-BYOS | RHEL-lvm77 | N/D|nenhuma atualização de imagem para concluir o fim de abril  | Sim-suporte da versão do pacote: *18.5 -3. EL7*|

### <a name="centos"></a>CentOS

| Publicador/versão | Oferta | SKU | Versão | imagem de nuvem-inicialização pronta | suporte ao pacote Cloud-init no Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7,7 |CentOS |7-CI |7.7.20190920 |Sim (Observe que esta é uma imagem de visualização e, uma vez que todas as imagens CentOS 7,7 dão suporte a Cloud-init, isso será removido em meados de 2020, o aviso será fornecido) | Sim-suporte da versão do pacote: *18.5 -3. EL7. centos*|

* As imagens CentOS 7,7 que serão habilitadas para Cloud-init serão atualizadas aqui em março de 2020 

### <a name="oracle"></a>Oracle

| Publicador/versão | Oferta | SKU | Versão | imagem de nuvem-inicialização pronta | suporte ao pacote Cloud-init no Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7,7 |Oracle-Linux |77-CI |7.7.01| imagem de visualização (Observe que esta é uma imagem de visualização e, uma vez que todas as imagens do Oracle 7,7 dão suporte a Cloud-init, isso será removido em meados de 2020, o aviso será fornecido) | Não, na versão prévia, o pacote é: *18.5-3.0.1. EL7*

### <a name="debian--suse-sles"></a>Debian & SuSE SLES
No momento, estamos trabalhando para visualizar o suporte, espere atualizações em fevereiro e março de 2020.

Atualmente Azure Stack dará suporte ao provisionamento de imagens habilitadas para Cloud-init.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Qual é a diferença entre cloud-init e o Agente do Linux (WALA)?
WALA é um agente específico da plataforma do Azure usado para provisionar e configurar VMs e manipular [extensões do Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux). 

Estamos aprimorando a tarefa de configurar VMs para usar Cloud-init em vez do agente do Linux para permitir que os clientes existentes de Cloud-init usem seus scripts de Cloud-init atuais ou novos clientes para aproveitar a funcionalidade avançada de configuração de inicialização de nuvem. Se você tiver investimentos existentes em scripts de Cloud-init para configurar sistemas Linux, não haverá **configurações adicionais necessárias** para habilitar o processo de Cloud-init. 

Cloud-init não pode processar extensões do Azure, portanto, WALA ainda é necessário na imagem para processar extensões, mas precisará ter seu código de provisionamento desabilitado, para imagens do Linux distribuições endossadas que estão sendo convertidas para provisionar por Cloud-init, elas terão o WALA instalado e serão configuradas corretamente.

Ao criar uma VM, se você não incluir a opção CLI do Azure `--custom-data` no tempo de provisionamento, o Cloud-init ou o wala usará os parâmetros mínimos de provisionamento de VM necessários para provisionar a VM e concluir a implantação com os padrões.  Se você fizer referência à configuração Cloud-init com `--custom-data` a opção, o que estiver contido em seus dados personalizados estará disponível para Cloud-init quando a VM for inicializada.

as configurações de Cloud-init aplicadas às VMs não têm restrições de tempo e não causarão a falha da implantação ao atingir o tempo limite. Isso não é verdade para WALA, se você alterar os padrões de WALA para processar dados personalizados, ele não poderá exceder a provisão total do tempo de provisionamento da VM de 40mins, se for o caso, a criação da VM falhará.

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
 
