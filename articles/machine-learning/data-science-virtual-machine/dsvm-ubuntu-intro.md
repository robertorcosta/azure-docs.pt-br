---
title: 'Início Rápido: Criar uma DSVM do Ubuntu'
description: Configure e crie uma Máquina Virtual de Ciência de Dados para Linux (Ubuntu) para realizar a análise e o aprendizado de máquina.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/10/2019
ms.openlocfilehash: 951191422e80ef6224cd140beed782de31cb3822
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677856"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-linux-ubuntu"></a>Início Rápido: Configurar a Máquina Virtual de Ciência de Dados para Linux (Ubuntu)

Inicie o funcionamento usando a Máquina Virtual de Ciência de Dados do Ubuntu.

## <a name="prerequisites"></a>Pré-requisitos

Para criar uma Máquina Virtual de Ciência de Dados do Windows, é necessário ter uma assinatura do Azure. [Experimentar o Azure gratuitamente](https://azure.com/free).
Observe que as contas gratuitas do Azure não dão suporte a SKUs de máquina virtual habilitada para GPU.

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Criar sua Máquina Virtual de Ciência de Dados para Linux

Veja as etapas para criar uma instância da Máquina Virtual de Ciência de Dados para Linux:

1. Acesse o [portal do Azure](https://portal.azure.com). Você pode ser solicitado a entrar na sua conta do Azure, caso ainda não esteja conectado.
1. Localize a listagem de máquinas virtuais digitando "máquina virtual de ciência de dados" e selecionando "Máquina Virtual de Ciência de Dados para Linux (Ubuntu)".
    
    ![Listagem de VM do Ubuntu](./media/dsvm-ubuntu-intro/search-ubuntu.png)

1. Na janela subsequente, selecione **Criar**.

   [![](media/dsvm-ubuntu-intro/create-linux.png "Botão para criar um computador Ubuntu")](media/dsvm-ubuntu-intro/create-linux-expanded.png#lightbox)

1. Você deve ser redirecionado para a folha "Criar uma máquina virtual".
   
   ![Guia Noções básicas correspondente à Máquina Virtual Ubuntu](./media/dsvm-ubuntu-intro/review-create-ubuntu.png)

1. Insira as seguintes informações para configurar cada etapa do assistente:

    1. **Noções básicas**:
    
       * **Assinatura**: se você tiver mais de uma assinatura, selecione aquela em que o computador será criado e cobrado. Você deve ter privilégios de criação de recurso nessa assinatura.
       * **Grupo de recursos**: crie um novo grupo ou use um existente.
       * **Nome da máquina virtual**: Insira o nome da máquina virtual. É assim que ela aparecerá no seu portal do Azure.
       * **Região**: selecione o datacenter mais apropriado. Para um acesso mais rápido à rede, é o data center que contém a maioria dos seus dados ou que está mais próximo de sua localização física. Leia mais sobre as [Regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Imagem**: Mantenha o valor padrão.
       * **Tamanho**: Isso deve ser preenchido automaticamente com um tamanho apropriado para cargas de trabalho gerais. Leia mais sobre os [tamanhos de VM do Linux no Azure](../../virtual-machines/linux/sizes.md).
       * **Tipo de autenticação**: Para configuração mais rápida, selecione "Senha". 
         
         > [!NOTE]
         > Caso pretenda usar o JupyterHub, selecione "Senha", pois o JupyterHub *não* é configurado para usar chaves públicas SSH.

       * **Nome de usuário**: Insira o nome de usuário do administrador. Esse é o nome de usuário que você usará para fazer logon em sua máquina virtual e não precisa ser o mesmo que o seu nome de usuário do Azure. *Não* use letras em maiúsculas.
         
         > [!NOTE]
         > Se você usar letras em maiúsculas em seu nome de usuário, o JupyterHub não funcionará e você obterá um erro de servidor interno 500.

       * **Senha**: Insira a senha que você usará para fazer logon em sua máquina virtual.    
    
   1. Selecione **Examinar + criar**.
   1. **Examinar + criar**
      * Verifique se todas as informações inseridas estão corretas. 
      * Selecione **Criar**.
    
    O provisionamento deve demorar cerca de 5 minutos. O status é exibido no portal do Azure.

## <a name="how-to-access-the-ubuntu-data-science-virtual-machine"></a>Como acessar a Máquina Virtual de Ciência de Dados do Ubuntu

É possível acessar a DSVM do Ubuntu usando três métodos:

  * SSH para sessões de terminal
  * X2Go para sessões gráficas
  * JupyterHub e JupyterLab para notebooks Jupyter

Também é possível anexar uma Máquina Virtual de Ciência de Dados ao Azure Notebooks para executar os notebooks Jupyter na VM e ignorar as limitações da camada de serviço gratuita. Para saber mais, confira [Gerenciar e configurar projetos do Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>SSH

Depois que a VM for criada, se ela tiver sido configurada com acesso SSH, você poderá entrar nela usando SSH. Use as credenciais da conta criada na seção **Noções básicas** da etapa 3 para a interface shell de texto. No Windows, é possível baixar uma ferramenta de cliente SSH, como o [PuTTY](https://www.putty.org). Se preferir uma área de trabalho gráfica (Sistema Windows X), você poderá usar o encaminhamento X11 no PuTTY.

> [!NOTE]
> O cliente X2Go apresentou desempenho melhor do que o encaminhamento X11 em testes. Recomendamos o uso do cliente X2Go para uma interface gráfica de área de trabalho.

### <a name="x2go"></a>X2Go

A VM Linux já está provisionada com um servidor X2Go e pronta para aceitar conexões de cliente. Para se conectar à área de trabalho gráfica da VM do Linux, realize o seguinte procedimento em seu cliente:

1. Baixe e instale o cliente X2Go para sua plataforma de cliente [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Anote o endereço IP público da máquina virtual, que pode ser encontrado no portal do Azure abrindo a máquina virtual que você acabou de criar.

   ![Endereço IP do computador Ubuntu](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

1. Execute o cliente X2Go. Se a janela "Nova sessão" não aparecer automaticamente, vá para Sessão-> Nova sessão.

1. Na janela de configuração resultante, insira os seguintes parâmetros de configuração:
   * **Guia Sessão**:
     * **Host**: Insira o endereço IP de sua VM, que você anotou anteriormente.
     * **Logon**: Insira o nome de usuário na VM Linux.
     * **Porta SSH**: Deixe em 22, o valor padrão.
     * **Tipo de Sessão**: Altere o valor para **XFCE**. No momento, a VM Linux dá suporte apenas à área de trabalho XFCE.
   * **Guia Mídia**: Você poderá desligar o suporte a som e impressão de cliente se não precisar usá-los.
   * **Pastas compartilhadas**: Caso você queira que os diretórios de seus computadores cliente sejam montados na VM Linux, adicione os diretórios de computador cliente que você deseja compartilhar com a VM nesta guia.

   ![Configuração do X2Go](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Selecione **OK**.
1. Clique na caixa no painel à direita da janela X2Go para abrir a tela de logon de sua VM.
1. Insira a senha de sua VM.
1. Selecione **OK**.
1. Talvez seja necessário conceder permissão ao X2Go para ignorar o firewall para concluir a conexão.
1. Agora você deve ver a interface gráfica para sua DSVM do Ubuntu. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub e JupyterLab

A DSVM do Ubuntu executa o [JupyterHub](https://github.com/jupyterhub/jupyterhub), um servidor Jupyter multiusuário. Para conectar, execute as seguintes etapas:

   1. Anote o endereço IP público de sua VM, pesquisando e selecionando sua VM no portal do Azure.
      ![Endereço IP do computador Ubuntu](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

   1. No computador local, abra um navegador da Web e navegue até https: https:\//seu-IP-de-VM:8000, substituindo "Seu-IP-de-VM" pelo endereço IP que anotou anteriormente.
   1. Insira o nome de usuário e a senha usados para criar a VM e entre. 

      ![Insira o logon do Jupyter](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Procure os vários notebooks de exemplo disponíveis.

O JupyterLab, a próxima geração de notebooks Jupyter e JupyterHub, também está disponível. Para acessá-lo, entre no JupyterHub e navegue até a URL https: \//seu-IP-de-VM:8000/usuário/seu-nome-de-usuário/lab, substituindo "seu-nome-de-usuário" pelo nome de usuário que você escolheu ao configurar a VM.

É possível definir o JupyterLab como o servidor de Notebook padrão adicionando esta linha a `/etc/jupyterhub/jupyterhub_config.py`:

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Próximas etapas

Veja como você pode continuar seu aprendizado e exploração:

* O passo a passo [Ciência de dados na Máquina Virtual de Ciência de Dados para Linux](linux-dsvm-walkthrough.md) mostra como executar várias tarefas comuns de ciência de dados com a DSVM Linux provisionada aqui. 
* Explore as várias ferramentas de ciência de dados na DSVM experimentando as ferramentas descritas neste artigo. Você também pode executar `dsvm-more-info` no shell contido na máquina virtual para uma introdução básica e ponteiros para obter mais informações sobre as ferramentas instaladas na VM.  
* Saiba como criar soluções completas de análise sistematicamente usando o [Processo de Ciência de Dados de Equipe](https://aka.ms/tdsp).
* Visite a [Galeria de IA do Azure](https://gallery.azure.ai/) para obter exemplos de análise de dados e aprendizado de máquina que usam os serviços de IA do Azure.
* Consulte a [documentação de referência](./reference-ubuntu-vm.md) apropriada para esta máquina virtual.
