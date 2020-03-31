---
title: Executar contêineres embutidos FAQ
description: Encontre respostas para as perguntas freqüentes sobre os contêineres Linux incorporados no Azure App Service.
keywords: serviço de aplicativo do Azure, aplicativo Web, perguntas frequentes, Linux, OSS, aplicativo Web para contêineres, vários contêineres
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: f0a8b1758571a9473402d11a4d5141a11f76504d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245813"
---
# <a name="azure-app-service-on-linux-faq"></a>Perguntas frequentes sobre o Serviço de Aplicativo do Azure no Linux

Com o lançamento do Serviço de Aplicativo no Linux, estamos trabalhando para adicionar recursos e fazer melhorias em nossa plataforma. Este artigo fornece as respostas para as perguntas que nossos clientes têm feito recentemente.

Se você tiver qualquer dúvida, comente este artigo.

## <a name="built-in-images"></a>Imagens internas

**Quero bifurcar os contêineres Docker embutidos que a plataforma fornece. Onde posso encontrar esses arquivos?**

É possível encontrar todos os arquivos do Docker no [GitHub](https://github.com/azure-app-service). É possível encontrar todos os contêineres do Docker no [Hub do Docker](https://hub.docker.com/u/appsvc/).

<a id="#startup-file"></a>

**Quais são os valores esperados para a seção Arquivo de Inicialização quando configuro a pilha de runtime?**

| Pilha           | Valor Esperado                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | o comando para iniciar seu aplicativo `java -jar /home/site/wwwroot/app.jar --server.port=80`JAR (por exemplo, ) |
| Tomcat          | a localização de um script para executar quaisquer `/home/site/deployments/tools/startup_script.sh`configurações necessárias (por exemplo, )          |
| Node.js         | o arquivo de configuração pm2 ou seu arquivo de script                                |
| .NET Core       | o nome DLL compilado como`dotnet <myapp>.dll`                                 |
| Ruby            | o script Ruby que você deseja inicializar seu aplicativo com                     |

Esses comandos ou scripts são executados depois que o contêiner Docker incorporado é iniciado, mas antes que o código do aplicativo seja iniciado.

## <a name="management"></a>Gerenciamento

**O que acontece quando eu pressiono o botão de reinicialização no portal do Azure?**

Esta ação é igual a um reinício do Docker.

**Posso usar o SSH (Secure Shell) para me conectar à VM (máquina virtual) do contêiner de aplicativo?**

Sim, você pode fazer isso por meio do site de gerenciamento do controle de origem (SCM) .

> [!NOTE]
> Você também pode se conectar ao contêiner de aplicativo diretamente do seu computador de desenvolvimento local usando SSH, SFTP ou Visual Studio Code (para aplicativos do Node.js de depuração ao vivo). Para obter mais informações, consulte [Depuração remota e SSH no Serviço de Aplicativo no Linux](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html).
>

**Como criar um plano de Serviço de Aplicativo Linux por meio de um SDK ou um modelo do Azure Resource Manager?**

Defina o campo **reservado** do serviço de aplicativo como *verdadeiro*.

## <a name="continuous-integration-and-deployment"></a>Integração e implantação contínuas

**Meu aplicativo web ainda usa uma imagem de contêiner Docker antiga depois que eu atualizei a imagem no Docker Hub. Você suporta integração contínua e implantação de contêineres personalizados?**

Sim, para configurar integração/implantação contínua para o Registro de Contêiner do Azure ou DockerHub, seguindo a [Implantação contínua com o Aplicativo Web para Contêineres](./app-service-linux-ci-cd.md). Para registros privados, é possível atualizar o contêiner parando e, em seguida, iniciando o Aplicativo Web. Se preferir, é possível alterar ou adicionar uma configuração de aplicativo fictício para forçar uma atualização do contêiner.

**Há suporte para ambientes de preparo?**

Sim.

**Posso usar *WebDeploy/MSDeploy* para implantar meu aplicativo Web?**

Sim, você precisa definir uma configuração de aplicativo chamada `WEBSITE_WEBDEPLOY_USE_SCM` como *false*.

**A implantação do Git do meu aplicativo falha ao usar o aplicativo web Linux. Como posso contornar o assunto?**

Se a implantação do Git falhar no aplicativo Web do Linux, escolha uma das opções a seguir para implantar o código do aplicativo:

- Use o recurso De entrega contínua (Preview): Você pode armazenar o código-fonte do seu aplicativo em um repo Azure DevOps Git ou no GitHub repo para usar o Azure Continuous Delivery. Para obter mais informações, consulte [Como configurar a Entrega Contínua para aplicativos Web do Linux](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Usar a [API de implantação via arquivo ZIP](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): para usar essa API, [adicione o SSH ao seu aplicativo Web](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support) e vá para a pasta onde você deseja implantar seu código. Execute o seguinte código:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Se você receber uma mensagem de erro informando que o comando `curl` não foi encontrado, instale a ondulação usando `apt-get install curl` antes de executar o comando `curl` anterior.

## <a name="language-support"></a>Suporte ao idioma

**Eu quero usar websockets no aplicativo Node.js, há definições ou configurações especiais a serem definidas?**

Sim, desabilite `perMessageDeflate` no código Node.js do servidor. Por exemplo, se estiver usando o socket.io, use o código a seguir:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Há suporte para aplicativos .NET Core não compilados?**

Sim.

**Há suporte para o Criador como um gerenciador de dependências para aplicativos PHP?**

Sim, durante uma implantação do Git, o Kudu deve detectar que você está implantando um aplicativo PHP (graças à presença de um arquivo composer.lock) e o Kudu, então, disparará uma instalação de criador.

## <a name="custom-containers"></a>Contêineres personalizados

**Estou usando meu próprio recipiente personalizado. Quero que a plataforma monte um `/home/` compartilhamento de SMB para o diretório.**

Se `WEBSITES_ENABLE_APP_SERVICE_STORAGE` a configuração não for `/home/` **especificada** ou definida como *true,* o diretório será **compartilhado** em instâncias de escala e os arquivos gravados **persistirão** em reinicializações. A configuração `WEBSITES_ENABLE_APP_SERVICE_STORAGE` explícita *de falso* desativará a montagem.

**Meu contêiner personalizado demora para iniciar e a plataforma o reinicia antes que ele termine a inicialização.**

Você pode configurar o tempo que a plataforma aguardará antes de reiniciar o contêiner. Para fazer isso, defina a configuração de aplicativo `WEBSITES_CONTAINER_START_TIME_LIMIT` como o valor desejado. O valor padrão é 230 segundos e o valor máximo permitido é 1800 segundos.

**Qual é o formato da URL do servidor do Registro privado?**

Forneça a URL completa do registro, incluindo `http://` ou `https://`.

**Qual é o formato do nome da imagem na opção de Registro privado?**

Adicione o nome de imagem completa, incluindo a URL de registro particular (por exemplo, myacr.azurecr.io/dotnet:latest). Os nomes de imagem que usam a porta personalizada [não podem ser inseridos por meio do portal](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Para `docker-custom-image-name`definir, [ `az` ](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set)use a ferramenta de linha de comando .

**Posso expor mais de uma porta em minha imagem de contêiner personalizados?**

Não apoiamos expor mais de um porto.

**Posso colocar meu próprio armazenamento?**

Sim, [Traga seu próprio armazenamento](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage) está em versão prévia.

**Por que não é possível navegar os processos do meu contêiner personalizado em execução ou de sistema de arquivos do site do SCM?**

O site do SCM é executado em um contêiner separado. Não é possível verificar o sistema de arquivos ou os processos em execução do contêiner de aplicativo.

**Meu contêiner personalizado ouve um porto diferente do porto 80. Como posso configurar meu aplicativo para encaminhar solicitações para essa porta?**

Temos a detecção automática de porta. Também é possível especificar uma configuração de aplicativo chamada *WEBSITES_PORT* e fornecer a ela o valor do número da porta esperada. Anteriormente, a plataforma usava a configuração de aplicativo *PORTA*. Nós estamos planejando substituir essa configuração de aplicativo e usar *WEBSITES_PORT* exclusivamente.

**É necessário implementar o HTTPS no meu contêiner personalizado?**

Não, a plataforma manipula a terminação HTTPS nos front-ends compartilhados.

## <a name="multi-container-with-docker-compose"></a>Multi-contêiner com Docker Compose

**Como fazer para configurar o ACR (Registro de Contêiner do Azure) para usá-lo com vários contêineres?**

Para usar o ACR com vários contêineres, **todas as imagens de contêiner** precisam estar hospedadas no mesmo servidor de registro do ACR. Uma vez que eles estejam no mesmo servidor de registro, você precisará criar configurações de aplicativo e, em seguida, atualizar o arquivo de configuração Docker Compose para incluir o nome de imagem ACR.

Crie as seguintes configurações de aplicativo:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (URL completo, `https://<server-name>.azurecr.io`ex: )
- DOCKER_REGISTRY_SERVER_PASSWORD (habilite o acesso de administrador nas configurações do ACR)

No arquivo de configuração, referencie a imagem do ACR como o seguinte exemplo:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Como saber qual contêiner é acessível pela Internet?**

- Apenas um contêiner pode ser aberto para acesso
- Somente as portas 80 e 8080 são acessíveis (portas expostas)

Estas são as regras para determinar qual contêiner está acessível – na ordem de precedência:

- Configuração de aplicativo `WEBSITES_WEB_CONTAINER_NAME` definida como o nome do contêiner
- O primeiro contêiner a definir a porta 80 ou 8080
- Se nenhuma das opções acima for verdadeira, o primeiro contêiner definido no arquivo estará acessível (exposto)


## <a name="web-sockets"></a>Soquetes Web

Os Soquetes da Web são suportados em aplicativos Linux.

> [!IMPORTANT]
> Os Soquetes da Web não são suportados atualmente para aplicativos Linux em Planos de Serviço de Aplicativos Gratuitos. Estamos trabalhando na remoção dessa limitação e planejamos suportar até 5 conexões de soquete web em planos de serviço de aplicativo gratuitos.

## <a name="pricing-and-sla"></a>Preço e SLA

**Qual é o preço agora que o serviço está disponível?**

Os preços variam de acordo com o SKU e a região, mas você pode ver mais detalhes em nossa página de preços: [App Service Pricing](https://azure.microsoft.com/pricing/details/app-service/linux/).

## <a name="other-questions"></a>Outras perguntas

**O que significa "Recurso solicitado não está disponível no grupo de recursos"?**

Você pode ver essa mensagem ao criar um aplicativo web usando o Arm (Azure Resource Manager, gerenciador de recursos do Azure). Com base em uma limitação atual, para o mesmo grupo de recursos, você não pode misturar aplicativos Windows e Linux na mesma região.

**Quais são os caracteres com suporte em nomes de configurações do aplicativo?**

Você pode usar apenas letras (A-Z, a-z), números (0-9) e o caractere de sublinhado (_) para as configurações do aplicativo.

**Onde posso solicitar os novos recursos?**

É possível enviar sua ideia para o [fórum de comentários dos Aplicativos Web](https://aka.ms/webapps-uservoice). Adicione “[Linux]” ao título de sua ideia.

## <a name="next-steps"></a>Próximas etapas

- [O que é o Serviço de Aplicativo do Azure no Linux?](app-service-linux-intro.md)
- [Configurar ambientes de preparo no Serviço de Aplicativo do Azure](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Implantação contínua com web app para contêineres](./app-service-linux-ci-cd.md)
