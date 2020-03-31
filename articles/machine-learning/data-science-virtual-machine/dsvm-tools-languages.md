---
title: Idiomas com suporte
titleSuffix: Azure Data Science Virtual Machine
description: Os idiomas do programa suportados e ferramentas relacionadas pré-instaladas na Máquina Virtual de Data Science.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: e7b32579712e89c0d5595303ee7e03d8b2462607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283648"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Linguagens com suporte na Máquina Virtual de Ciência de Dados 

A Máquina Virtual de Data Science (DSVM) vem com várias linguagens pré-construídas e ferramentas de desenvolvimento para construir suas aplicações de inteligência artificial (IA). Aqui estão alguns dos notáveis.

## <a name="python-windows-server-2016-edition"></a>Python (edição 2016 do Windows Server)

|    |           |
| ------------- | ------------- |
| Versões de idioma suportadas | Python 2.7 e 3.7 |
| Edições DSVM suportadas      | Windows Server 2016     |
| Como é configurado/instalado no DSVM?  | Dois `conda` ambientes globais são criados: <br /> * `root` O ambiente `/anaconda/` localizado é o Python 3.7. <br/> * `python2` O ambiente `/anaconda/envs/python2` localizado é o Python 2.7.       |
| Links para exemplos      | Os notebooks Jupyter de amostra para Python estão incluídos.     |
| Ferramentas relacionadas no DSVM      | PySpark, R, Julia.      |

> [!NOTE]
> As compilações do Windows Server 2016 criadas antes de março de 2018 contêm Python 3.5 e Python 2.7. Python 2.7 é o ambiente **raiz** conda, e **py37** é o ambiente Python 3.7.

### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

* Execute em um prompt de comando:

  Abra um prompt de comando e use um dos seguintes métodos, dependendo da versão do Python que deseja executar:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* Use em um IDE:

  Use ferramentas Python para Visual Studio (PTVS), instaladas na edição Visual Studio Community. Por padrão, o único ambiente configurado automaticamente no PTVS é o Python 3.6. 

    > [!NOTE]
    > Para apontar PTVS no Python 2.7, você deve criar um ambiente personalizado no PTVS. Para definir esse caminho de ambiente no Visual Studio Community Edition, vá para **Ferramentas** -> **Python Tools** -> **Python Environments** e selecione **+ Custom**. Em seguida, defina o local como **c:\anaconda\envs\python2** e selecione **Auto Detect**.

* Uso em Jupyter:

  Abra o Jupyter e selecione **O Novo** para criar um novo notebook. Você pode definir o tipo de kernel como _Python [Raiz de Conda]_ para Python 3.7 e _Python [Conda env:python2]_ para Python 2.7.

* Instale pacotes Python:

  Os ambientes Python padrão no DSVM são ambientes globais que podem ser legíveis por todos os usuários. Mas apenas os administradores podem escrever e instalar pacotes globais. Para instalar pacotes no ambiente global, ative-o no `activate` ambiente raiz ou python2 usando o comando como administrador. Em seguida, você pode `conda` usar `pip` um gerenciador de pacotes como ou para instalar ou atualizar pacotes.

## <a name="python-linux-edition"></a>Python (edição Linux)

|    |           |
| ------------- | ------------- |
| Versões de idioma suportadas | Python 2.7 e 3.5 |
| Edições DSVM suportadas      | Linux   |
| Como é configurado/instalado no DSVM?  | Dois `conda` ambientes globais são criados: <br /> * `root`ambiente localizado `/anaconda/` em Python 2.7. <br/> * `py35`ambiente localizado `/anaconda/envs/py35`em Python 3.5.       |
| Links para exemplos      | Os notebooks Jupyter de amostra para Python estão incluídos.     |
| Ferramentas relacionadas no DSVM      | PySpark, R, Julia      |
### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

* Executado em um terminal:

  Abra o terminal e faça um dos seguintes, dependendo da versão do Python que deseja executar:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* Use em um IDE:

  Use PyCharm, instalado na edição Visual Studio Community. 

* Uso em Jupyter:

  Abra o Jupyter e selecione **O Novo** para criar um novo notebook. Você pode definir o tipo de kernel como **Python [Raiz de Conda]** para Python 2.7 e **Python [Conda env:py35]** para o ambiente Python 3.5. 

* Instale pacotes Python:

  Os ambientes Python padrão no DSVM são ambientes globais legíveis por todos os usuários. Mas apenas os administradores podem escrever e instalar pacotes globais. Para instalar pacotes no ambiente global, ative para o `source activate` ambiente raiz ou py35 usando o comando como administrador ou como usuário com permissões sudo. Em seguida, você pode `conda` usar `pip` um gerenciador de pacotes como ou para instalar ou atualizar pacotes.


## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Versões de idioma suportadas | Microsoft R Open 3.x (100% compatível com CRAN-R)<br /> Microsoft R Server 9.x Edição developer (uma plataforma R escalável pronta para a empresa)|
| Edições DSVM suportadas      | Linux, Windows     |
| Como é configurado/instalado no DSVM?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Links para exemplos      | Amostra de cadernos Jupyter para R estão incluídos.     |
| Ferramentas relacionadas no DSVM      | SparkR, Python, Julia      |
### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

**Janelas:**

* Execute em um prompt de comando:

  Abra um prompt de comando e digite `R`.

* Use em um IDE:

  Use RTVS (RTools para Visual Studio) instalado no Visual Studio Community Edition ou RStudio. Estes estão disponíveis no menu Iniciar ou como um ícone de desktop. 

* Uso em Jupyter

  Abra o Jupyter e selecione **O Novo** para criar um novo notebook. Você pode definir o tipo de kernel como **R** para usar o kernel Jupyter R (IRKernel).

* Instalar pacotes R:

  R é instalado no DSVM em um ambiente global que é legível por todos os usuários. Mas apenas os administradores podem escrever e instalar pacotes globais. Para instalar pacotes no ambiente global, execute R usando um dos métodos anteriores. Em seguida, você pode `install.packages()` executar o gerenciador de pacotes R para instalar ou atualizar pacotes.

**Linux**:

* Executado no terminal:

  Abra um terminal `R`e execute.  

* Use em um IDE:

  Use o RStudio, instalado no Linux DSVM.  

* Uso em Jupyter:

  Abra o Jupyter e selecione **O Novo** para criar um novo notebook. Você pode definir o tipo de kernel como **R** para usar o kernel Jupyter R (IRKernel). 

* Instalar pacotes R:

  R é instalado no DSVM em um ambiente global que é legível por todos os usuários. Mas apenas os administradores podem escrever e instalar pacotes globais. Para instalar pacotes no ambiente global, execute R usando um dos métodos anteriores. Em seguida, você pode `install.packages()` executar o gerenciador de pacotes R para instalar ou atualizar pacotes.


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Versões de idioma suportadas | 0.6 |
| Edições DSVM suportadas      | Linux, Windows     |
| Como é configurado/instalado no DSVM?  | Windows: instalado em `C:\JuliaPro-VERSION`<br /> Linux: instalado em `/opt/JuliaPro-VERSION`    |
| Links para exemplos      | Amostras de cadernos jupyter para Julia estão incluídas.     |
| Ferramentas relacionadas no DSVM      | Python, R      |
### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

**Janelas:**

* Executar em um prompt de comando

  Abra um prompt `julia`de comando e execute .
* Use em um IDE:

  Use `Juno` com o Julia IDE instalado no DSVM e disponível como um atalho de desktop.

* Uso em Jupyter:

  Abra o Jupyter e selecione **O Novo** para criar um novo notebook. Você pode definir o tipo de kernel como **Julia VERSION**.

* Instale os pacotes Julia:

  A localização padrão de Julia é um ambiente global que é legível por todos os usuários. Mas apenas os administradores podem escrever e instalar pacotes globais. Para instalar pacotes no ambiente global, execute Julia usando um dos métodos anteriores. Em seguida, você pode executar `Pkg.add()` comandos do gerenciador de pacotes Julia como instalar ou atualizar pacotes.


**Linux**:
* Executado em um terminal:

  Abra um terminal `julia`e execute.
* Use em um IDE:

  Use `Juno`, com o Julia IDE instalado no DSVM e disponível como um atalho de menu **de aplicativo.**

* Uso em Jupyter:

  Abra o Jupyter e selecione **O Novo** para criar um novo notebook. Você pode definir o tipo de kernel como **Julia VERSION**.

* Instale os pacotes Julia:

  A localização padrão de Julia é um ambiente global que é legível por todos os usuários. Mas apenas os administradores podem escrever e instalar pacotes globais. Para instalar pacotes no ambiente global, execute Julia usando um dos métodos anteriores. Em seguida, você pode executar `Pkg.add()` comandos do gerenciador de pacotes Julia como instalar ou atualizar pacotes.

## <a name="other-languages"></a>Outros idiomas

**C#**: Disponível no Windows e acessível `Developer Command Prompt for Visual Studio`através da edição `csc` visual studio community ou no , onde você pode executar o comando.

**Java**: OpenJDK está disponível nas edições Linux e Windows do DSVM e está definido no caminho. Para usar java, `javac` `java` digite o ou comando em um prompt de comando no Windows ou no bash shell no Linux.

**Node.js**: Node.js está disponível nas edições Linux e Windows do DSVM e está definido no caminho. Para acessar node.js, `node` `npm` digite o comando ou em um prompt de comando no Windows ou no bash shell no Linux. No Windows, a extensão Visual Studio para as ferramentas Node.js é instalada para fornecer um IDE gráfico para desenvolver seu aplicativo Node.js.

**F#**: Disponível no Windows e acessível `Developer Command Prompt for Visual Studio`através da edição `fsc` Visual Studio Community ou em um , onde você pode executar o comando.
