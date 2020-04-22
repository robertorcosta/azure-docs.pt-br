---
title: Implantar a plataforma de contêineres OpenShift 3.11 oferta de marketplace auto-gerenciado no Azure
description: Implantar a plataforma de contêineres OpenShift 3.11 oferta de marketplace auto-gerenciada no Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 1cf6c7417aa86d47e59e08786e7807e32c175a25
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759583"
---
# <a name="configure-prerequisites"></a>Configurar pré-requisitos

Antes de usar a oferta do Marketplace para implantar um cluster OpenShift Container Platform 3.11 autogerenciado no Azure, alguns pré-requisitos devem ser configurados.  Leia o artigo [de pré-requisitos do OpenShift](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-container-platform-3x-prerequisites) para instruções para criar uma chave ssh (sem uma senha), cofre de chaves Do Zure, segredo do cofre de chaves e um diretor de serviço.

 
## <a name="deploy-using-the-marketplace-offer"></a>Implantar usando a oferta do Marketplace

A maneira mais simples de implantar um cluster OpenShift Container Platform 3.11 auto-gerenciado no Azure é usar a oferta do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy).

Esta opção é a mais simples, mas também tem recursos limitados de personalização. A oferta do Marketplace implanta a Plataforma de Contêineres OpenShift 3.11.82 e inclui as seguintes opções de configuração:

- **Nós mestres**: tipo de instância de nós mestres três (3) com configurável.
- **Nós mestres**: tipo de instância de nós mestres três (3) com configurável.
- **Nós**: O número de nós (entre 1 e 9) e o tipo de ocorrência são configuráveis.
- **Tipo de Disco**: Discos gerenciados são usados.
- **Rede**: Suporte para rede nova ou existente e gama de CIDR personalizado.
- **CNS**: CNS pode ser habilitado.
- **Métricas**: As métricas Hawkular podem ser habilitadas.
- **Registro**: O registro efk pode ser habilitado.
- **Provedor de nuvem do Azure**: Habilitado por padrão, pode ser desativado.

No canto superior esquerdo do portal Azure, clique em **Criar um recurso,** digite 'plataforma de contêiner de turno aberto' na caixa de pesquisa e clique em Enter.

   ![Nova pesquisa de recursos](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

A página Resultados será aberta com **a Plataforma de Contêineres Red Hat OpenShift 3.11 Auto-Gerenciada** na lista. 

   ![Novo resultado de pesquisa de recursos](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Clique na oferta para ver detalhes da oferta. Para implantar esta oferta, clique em **Criar**. A ui para inserir os parâmetros necessários aparecerá. A primeira tela é a lâmina **básica.**

   ![Página do título da oferta](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Noções básicas**

Para obter ajuda em qualquer um dos parâmetros de entrada, paire sobre o ***i*** ao lado do nome do parâmetro.

Digite valores para os parâmetros de entrada e clique em **OK**.

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Nome do usuário do VM Admin | O usuário administrador a ser criado em todas as instâncias de VM |
| Chave pública SSH para usuário de admin | Chave pública SSH usada para entrar na VM - não deve ter uma senha |
| Subscription | Assinatura do Azure para implantar cluster em |
| Grupo de recursos | Crie um novo grupo de recursos ou selecione um grupo de recursos vazio existente para recursos de cluster |
| Location | Região do Azure para implantar cluster em |

   ![Ofereça lâmina básica](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Configurações de infra-estrutura**

Digite valores para os parâmetros de entrada e clique em **OK**.

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Prefixo de nome de cluster OCP | Prefixo de cluster usado para configurar nomes de host para todos os nós. Entre 1 e 20 caracteres |
| Tamanho do nó mestre | Aceite o tamanho padrão da VM ou clique **em Alterar tamanho** para selecionar um tamanho de VM diferente.  Selecione o tamanho de VM apropriado para sua carga de trabalho |
| Tamanho do nó de infra-estrutura | Aceite o tamanho padrão da VM ou clique **em Alterar tamanho** para selecionar um tamanho de VM diferente.  Selecione o tamanho de VM apropriado para sua carga de trabalho |
| Número de números de aplicativos | Aceite o tamanho padrão da VM ou clique **em Alterar tamanho** para selecionar um tamanho de VM diferente.  Selecione o tamanho de VM apropriado para sua carga de trabalho |
| Tamanho do nó da aplicação | Aceite o tamanho padrão da VM ou clique **em Alterar tamanho** para selecionar um tamanho de VM diferente.  Selecione o tamanho de VM apropriado para sua carga de trabalho |
| Tamanho do host bastião | Aceite o tamanho padrão da VM ou clique **em Alterar tamanho** para selecionar um tamanho de VM diferente.  Selecione o tamanho de VM apropriado para sua carga de trabalho |
| Rede virtual nova ou existente | Crie um novo vNet (Default) ou use um vNet existente |
| Escolha as configurações padrão do CIDR ou personalize o CIDR (IPRange) | Aceite as faixas DE CIDR padrão ou Selecione **faixa de IP personalizada** e digite informações CIDR personalizadas.  Configurações padrão criarão vNet com CIDR de 10.0.0.0/14, master subnet com 10.1.0.0/16, infra subnet com 10.2.0.0/16 e sub-rede de computação e cns com 10.3.0.0/16 |
| Nome do grupo de recursos do cofre chave | O nome do Grupo de Recursos que contém o Cofre de Chaves |
| Nome do cofre de chaves | O nome do Cofre chave que contém o segredo com a chave privada ssh.  Apenas caracteres alfanuméricos e traços são permitidos, e estar entre 3 e 24 caracteres |
| Nome Secreto | O nome do segredo que contém a chave privada ssh.  Apenas caracteres alfanuméricos e traços são permitidos |

   ![Oferecer lâmina de infra-estrutura](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Tamanho da mudança**

Para selecionar um tamanho diferente de VM, clique ***em Alterar o tamanho***.  A janela de seleção da VM será aberta.  Selecione o tamanho da VM desejada e clique **em Selecionar**.

   ![Selecione o tamanho do VM](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Rede Virtual existente**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Nome da rede virtual existente | Nome do vNet existente |
| Nome da sub-rede para nó mestre | Nome da sub-rede existente para nómestre.  Precisa conter pelo menos 16 endereços IP e seguir o RFC 1918 |
| Nome da sub-rede para nóinfra | Nome da sub-rede existente para nóinfra.  Precisa conter pelo menos 32 endereços IP e seguir o RFC 1918 |
| Nome da sub-rede para nós computacionais e cns | Nome da sub-rede existente para nó sustais e computação.  Precisa conter pelo menos 32 endereços IP e seguir o RFC 1918 |
| Grupo de recursos para a Rede Virtual existente | Nome do grupo de recursos que contém o vNet existente |

   ![Oferecer infra-estrutura existente vnet](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Faixa IP personalizada**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Intervalo de endereços para a rede virtual | CIDR personalizado para o vNet |
| Intervalo de endereço para a sub-rede que contém os nós mestre | CIDR personalizado para sub-rede mestre |
| Intervalo de endereço susceptino da sub-rede que contém os áldenos de infra-estrutura | CIDR personalizado para sub-rede de infra-estrutura |
| Intervalo de endereço para sub-rede contendo os nós computado e cns | CIDR personalizado para os nódulos de computação e cns |

   ![Ofereça uma gama ip personalizada de infra-estrutura](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

Digite valores para os Parâmetros de Entrada e clique em **OK**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Senha de usuário do OpenShift Admin | Senha para o usuário inicial do OpenShift.  Este usuário também será o administrador do cluster |
| Confirme a senha de usuário do OpenShift Admin | Redigite a senha de usuário do OpenShift Admin |
| Nome de usuário do gerenciador de assinaturas Red Hat | Nome de Usuário para acessar sua Assinatura red hat ou ID da organização.  Esta credencial é usada para registrar a instância RHEL para sua assinatura e não será armazenada pela Microsoft ou Red Hat |
| Senha de usuário do Gerenciador de Assinaturas Red Hat | Senha para acessar sua Assinatura red hat ou chave de ativação.  Esta credencial é usada para registrar a instância RHEL para sua assinatura e não será armazenada pela Microsoft ou Red Hat |
| Red Hat Subscription Manager OpenShift Pool ID | ID do pool que contém direito à plataforma de contêineres OpenShift. Certifique-se de ter direitos suficientes da Plataforma de Contêineres OpenShift para a instalação do cluster |
| Red Hat Subscription Manager OpenShift Pool ID para Corretor / Nó Mestre | ID do pool que contém direitos da Plataforma de Contêineres OpenShift para Corretor/ Nó Mestre. Certifique-se de ter direitos suficientes da Plataforma de Contêineres OpenShift para a instalação do cluster. Se não estiver usando o ID do pool de corretor/master, digite o Pool ID para Dedos de Aplicativo |
| Configurar o Provedor de Nuvem Azure | Configure o OpenShift para usar o Azure Cloud Provider. Necessário se usar o disco Azure para volumes persistentes.  Padrão é Sim |
| Azure AD Service Principal Cliente ID GUID | Azure AD Service Principal Cliente ID GUID - também conhecido como AppID. Só é necessário se configurar o Provedor de Nuvem Azure definido como **Sim** |
| Azure AD Service Principal Cliente ID Secreto | Azure AD Service Principal Cliente ID Secret. Só é necessário se configurar o Provedor de Nuvem Azure definido como **Sim** |
 
   ![Ofereça lâmina OpenShift](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Configurações adicionais**

A lâmina Configurações adicionais permite a configuração do CNS para armazenamento de glusterfs, registro, métricas e domínio do Router Sub.  O padrão não instalará nenhuma dessas opções e usará nip.io como sub domínio do roteador para fins de teste. A habilitação do CNS instalará três nolos adicionais de computação com três discos adicionais conectados que hospedarão pods de glusterfs.  

Digite valores para os Parâmetros de Entrada e clique em **OK**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| Configurar o Cns (Container Native Storage, armazenamento nativo de contêineres) | Instala o CNS no cluster OpenShift e o habilite como armazenamento. Será padrão se o Provedor Azure estiver desativado |
| Configurar o registro de clusters | Instala a funcionalidade de registro do EFK no cluster.  Nó infra de tamanho apropriadamente para hospedar pods EFK |
| Configurar métricas para o cluster | Instala métricas Hawkular no cluster OpenShift.  Tamanho de nóinfra apropriadamente para hospedar pods métricas Hawkular |
| Domínio do Sub do roteador padrão | Selecione nipio para testes ou personalizados para inserir seu próprio subdomínio para produção |
 
   ![Ofereça uma lâmina adicional](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Configurações adicionais - Parâmetros extras**

| Parâmetro de entrada | Descrição do parâmetro |
|-----------------------|-----------------|
| (CNS) Tamanho do nó | Aceite o tamanho do nó padrão ou selecione **Tamanho de alteração** para selecionar um novo tamanho de VM |
| Insira seu subdomínio personalizado | O domínio de roteamento personalizado a ser usado para expor aplicativos através do roteador no cluster OpenShift.  Certifique-se de criar a entrada dns curinga apropriada] |
 
   ![Ofereça cns adicionais Instalar](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Resumo**

A validação ocorre nesta fase para verificar a cota principal é suficiente para implantar o número total de VMs selecionados para o cluster.  Reveja todos os parâmetros que foram inseridos.  Se as entradas forem aceitáveis, clique em **OK** para continuar.

   ![Oferecer lâmina de resumo](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Comprar**

Confirme as informações de contato na página Comprar e clique em **Comprar** para aceitar os termos de uso e iniciar a implantação do cluster OpenShift Container Platform.

   ![Oferecer lâmina de compra](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Conectar-se ao cluster OpenShift

Quando a implantação for concluída, recupere a conexão da seção de saída da implantação. Conecte-se ao console OpenShift com seu navegador usando a URL do **console OpenShift**. você também pode SSH para o anfitrião Bastion. A seguir está um exemplo em que o nome de usuário do administrador é clusteradmin e o IP público de bastiões FQDN do DNS é bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

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
- 