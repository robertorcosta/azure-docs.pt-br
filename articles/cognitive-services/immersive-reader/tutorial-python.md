---
title: 'Tutorial: Iniciar a Leitura Avançada usando o Python'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você criará um aplicativo Python que inicia a Leitura Avançada.
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 08/02/2019
ms.author: dylankil
ms.openlocfilehash: 6404a5d49bd7af1ed5d74299f03eda8d0bb14b89
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326425"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Tutorial: Iniciar a Leitura Avançada usando o projeto de exemplo do Python

Na [visão geral](./overview.md), você aprendeu sobre o que é a Leitura Avançada e como ela implementa técnicas comprovadas para melhorar a compreensão de leitura para aprendizes do idioma, leitores emergentes e estudantes com diferenças de aprendizado. Este tutorial aborda como criar um aplicativo Web do Python que inicia a Leitura Avançada. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um aplicativo Web do Python com Pip, Flask, Jinja e virtualenv usando um projeto de exemplo
> * Adquirir um token de acesso
> * Iniciar a Leitura Avançada com o conteúdo de exemplo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um recurso de Leitura Avançada configurado para autenticação do Azure AD (Azure Active Directory). Siga [estas instruções](./azure-active-directory-authentication.md) para a configuração. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do ambiente. Salve a saída da sessão em um arquivo de texto para referência futura.
* [Git](https://git-scm.com/)
* [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) e [pip](https://docs.python.org/3/installing/index.html). Do Python 3.4 em diante, o pip é incluído por padrão com os instaladores de binários do Python.
* [Flask](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) e [virtualenvwrapper-win para Windows](https://pypi.org/project/virtualenvwrapper-win/) ou [virtualenvwrapper para OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [módulo de solicitações](https://pypi.org/project/requests/2.7.0/)
* Um IDE como o [Visual Studio Code](https://code.visualstudio.com/)

## <a name="acquire-an-azure-ad-authentication-token"></a>Adquirir um token de autenticação do Azure AD

Escreva uma API de back-end para recuperar um token de autenticação do Azure AD.

Você precisará de alguns valores da etapa de pré-requisito de configuração da autenticação do Azure AD acima para esta parte. Veja novamente o arquivo de texto que você salvou dessa sessão.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Quando você tiver esses valores, crie um arquivo chamado _.env_ e cole o código a seguir nele, fornecendo os valores de propriedade personalizada acima. Substitua o arquivo _.env._ no aplicativo de exemplo pelo arquivo que você acaba de criar.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Certifique-se de não confirmar esse arquivo no controle do código-fonte, uma vez que ele contém segredos que não devem ser tornados públicos.

O ponto de extremidade da API do **getimmersivereadertoken** deve ser protegido por alguma forma de autenticação (por exemplo, [OAuth](https://oauth.net/2/)) para impedir que usuários não autorizados obtenham tokens para usar em seu serviço de Leitura Avançada e cobrança; esse trabalho está além do escopo deste tutorial.

## <a name="create-a-python-web-app-on-windows"></a>Criar um aplicativo Web do Python em Windows

Criar um aplicativo Web do Python usando `flask` em Windows.

Instale o [Git](https://git-scm.com/).

Depois que o Git tiver sido instalado, abra um Prompt de Comando e 'clone' o repositório Git do SDK de Leitura Avançada em uma pasta no computador

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Instale o [Python](https://www.python.org/downloads/).

Marque a caixa Adicionar Python ao PATH.

![Caixa de diálogo de instalação do Python Windows etapa 1](./media/pythoninstallone.jpg)

Adicione Recursos Opcionais marcando as caixas e, em seguida, clique no botão 'Avançar'.

![Caixa de diálogo de instalação do Python Windows etapa 2](./media/pythoninstalltwo.jpg)

Escolha 'Instalação Personalizada' e defina o caminho de instalação como sua pasta raiz, por exemplo, `C:\Python37-32\` e, então, clique no botão 'Instalar'.

![Caixa de diálogo de instalação do Python Windows etapa 3](./media/pythoninstallthree.jpg)

Após a conclusão da Instalação do Python, abra um Prompt de Comando e `cd` para a pasta Scripts do Python.

```cmd
cd C:\Python37-32\Scripts
```

Instale o Flask.

```cmd
pip install flask
```

Instale o Jinja2. Um mecanismo de modelo com recursos completos para Python.

```cmd
pip install jinja2
```

Instale o virtualenv. Uma ferramenta para criar ambientes do Python isolados.

```cmd
pip install virtualenv
```

Instalar virtualenvwrapper-win. A ideia por trás do virtualenvwrapper é facilitar o uso de virtualenv.

```cmd
pip install virtualenvwrapper-win
```

Instale o módulo solicitações. Solicitações é uma biblioteca HTTP Licenciada do Apache2 escrita em Python.

```cmd
pip install requests
```

Instale o módulo python-dotenv. Este módulo lê o par chave-valor no arquivo .env e os adiciona à variável de ambiente.

```cmd
pip install python-dotenv
```

Criar um ambiente virtual

```cmd
mkvirtualenv advanced-python
```

`cd` para a pasta raiz do projeto de exemplo.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Conecte o projeto de exemplo com o ambiente. Isso mapeia o ambiente virtual criado recentemente para a pasta raiz do projeto de exemplo.

```cmd
setprojectdir .
```

Ative o ambiente virtual.

```cmd
activate
```

O projeto agora deve estar ativo e você verá algo como `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` no Prompt de Comando.

Desative o ambiente.

```cmd
deactivate
```

O prefixo `(advanced-python)` deve ter sido removido agora, pois o ambiente agora está desativado.

Para reativar o ambiente, execute `workon advanced-python` na pasta raiz do projeto de exemplo.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>Iniciar a Leitura Avançada com o conteúdo de exemplo

Quando o ambiente estiver ativo, execute o projeto de exemplo digitando `flask run` na pasta raiz do projeto de exemplo.

```cmd
flask run
```

Abra o navegador da Web e navegue até _http://localhost:5000_ .

## <a name="create-a-python-web-app-on-osx"></a>Criar um aplicativo Web do Python em OSX

Crie um aplicativo Web do Python usando `flask` no OSX.

Instale o [Git](https://git-scm.com/).

Depois que o Git tiver sido instalado, abra o terminal e 'clone' o repositório Git do SDK de Leitura Avançada em uma pasta no computador

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Instale o [Python](https://www.python.org/downloads/).

A pasta raiz do Python, por exemplo, `Python37-32`, agora deve estar na pasta Aplicativos.

Após a conclusão da Instalação do Python, abra um Terminal e `cd` para a pasta Scripts do Python.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Instale pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Em seguida, execute o seguinte para instalar o pip para o usuário conectado no momento para evitar problemas de permissões.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Insira a senha quando solicitado.
- Adicione o caminho da instalação do pip à variável PATH.
- Vá para a parte inferior do arquivo e insira o caminho que você deseja adicionar como o último item da lista, por exemplo, `PATH=$PATH:/usr/local/bin`.
- Pressione control-x para sair.
- Insira `Y` para salvar o buffer modificado.
- É isso! Para testá-lo, na janela novo terminal, digite: `echo $PATH`.

Instale o Flask.

```bash
pip install flask --user
```

Instale o Jinja2. Um mecanismo de modelo com recursos completos para Python.

```bash
pip install Jinja2 --user
```

Instale o virtualenv. Uma ferramenta para criar ambientes do Python isolados.

```bash
pip install virtualenv --user
```

Instale o virtualenvwrapper. A ideia por trás do virtualenvwrapper é facilitar o uso de virtualenv.

```bash
pip install virtualenvwrapper --user
```

Instale o módulo solicitações. Solicitações é uma biblioteca HTTP Licenciada do Apache2 escrita em Python.

```bash
pip install requests --user
```

Instale o módulo python-dotenv. Este módulo lê o par chave-valor no arquivo .env e os adiciona à variável de ambiente.

```bash
pip install python-dotenv --user
```

Escolha uma pasta na qual você gostaria de manter seus ambientes virtuais e execute este comando

```bash
mkdir ~/.virtualenvs
```

`cd` para a pasta do aplicativo de exemplo Python do SDK de Leitura Avançada.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Criar um ambiente virtual

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Conecte o projeto de exemplo com o ambiente. Isso mapeia o ambiente virtual criado recentemente para a pasta raiz do projeto de exemplo.

```bash
setprojectdir .
```

Ative o ambiente virtual.

```bash
activate
```

O projeto agora deve estar ativo e você verá algo como `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` no Prompt de Comando.

Desative o ambiente.

```bash
deactivate
```

O prefixo `(advanced-python)` deve ter sido removido agora, pois o ambiente agora está desativado.

Para reativar o ambiente, execute `workon advanced-python` na pasta raiz do projeto de exemplo.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Iniciar a Leitura Avançada com o conteúdo de exemplo

Quando o ambiente estiver ativo, execute o projeto de exemplo digitando `flask run` na pasta raiz do projeto de exemplo.

```bash
flask run
```

Abra o navegador da Web e navegue até _http://localhost:5000_ .

## <a name="next-steps"></a>Próximas etapas

* Explore o [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk) e a [Referência de SDK da Leitura Avançada](./reference.md)
* Visualize exemplos de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
