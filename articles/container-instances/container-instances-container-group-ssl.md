---
title: Habilite o TLS com o recipiente sidecar
description: Crie um ponto final SSL ou TLS para um grupo de contêineres em execução em Instâncias de Contêiner Azure executando o Nginx em um contêiner sidecar
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: b9ea9367219db694b89d6bf4a1e52efb373c71c4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984599"
---
# <a name="enable-a-tls-endpoint-in-a-sidecar-container"></a>Habilite um ponto final TLS em um recipiente sidecar

Este artigo mostra como criar um [grupo de contêineres](container-instances-container-groups.md) com um contêiner de aplicativo e um contêiner sidecar executando um provedor TLS/SSL. Ao configurar um grupo de contêineres com um ponto final TLS separado, você habilita conexões TLS para seu aplicativo sem alterar o código do aplicativo.

Você configura um grupo de contêineres de exemplo composto por dois contêineres:
* Um contêiner de aplicativo que executa um simples aplicativo web usando a imagem pública da Microsoft [aci-helloworld.](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) 
* Um contêiner sidecar executando a imagem [pública Nginx,](https://hub.docker.com/_/nginx) configurado para usar TLS. 

Neste exemplo, o grupo de contêineres só expõe a porta 443 para Nginx com seu endereço IP público. A Nginx encaminha solicitações HTTPS para o aplicativo web companheiro, que ouve internamente na porta 80. Você pode adaptar o exemplo para aplicativos de contêiner que ouvem em outras portas. 

Consulte [os próximos passos](#next-steps) para outras abordagens para habilitar o TLS em um grupo de contêineres.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você pode usar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir esse arquivo. Se você quer usá-lo localmente, recomendamos usar a versão 2.0.55 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Crie um certificado autoassinado

Para configurar o Nginx como provedor De TLS, você precisa de um certificado TLS/SSL. Este artigo mostra como criar e configurar um certificado TLS/SSL auto-assinado. Para os cenários de produção, você deve obter um certificado de uma autoridade de certificado.

Para criar um certificado TLS/SSL auto-assinado, use a ferramenta [OpenSSL](https://www.openssl.org/) disponível no Azure Cloud Shell e muitas distribuições Linux, ou use uma ferramenta cliente comparável em seu sistema operacional.

Primeiro crie uma solicitação de certificado (arquivo.csr) em um diretório de trabalho local:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Siga as instruções para adicionar as informações de identificação. Para Nome Comum, digite o nome de host associado ao certificado. Quando solicitado para obter uma senha, pressione Enter sem digitar, para pular a adição de uma senha.

Execute o seguinte comando para criar o certificado auto-assinado (arquivo.crt) a partir da solicitação de certificado. Por exemplo:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Agora você deve ver três arquivos no diretório: a solicitação de certificado (`ssl.csr`), a chave privada (`ssl.key`), e o certificado auto-assinado (`ssl.crt`). Você `ssl.key` usa `ssl.crt` e em passos posteriores.

## <a name="configure-nginx-to-use-tls"></a>Configure o Nginx para usar o TLS

### <a name="create-nginx-configuration-file"></a>Criar arquivo de configuração Nginx

Nesta seção, você cria um arquivo de configuração para o Nginx usar o TLS. Comece copiando o texto a seguir `nginx.conf`em um novo arquivo chamado . No Azure Cloud Shell, você pode usar o Visual Studio Code para criar o arquivo em seu diretório de trabalho:

```console
code nginx.conf
```

Em `location`, certifique-se de definir `proxy_pass` com a porta correta para o seu aplicativo. Neste exemplo, definimos a porta `aci-helloworld` 80 para o contêiner.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize TLS/SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive TLS/SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Segredos e arquivo de configuração da Base64

O arquivo de configuração Do Base64, o certificado TLS/SSL e a tecla TLS. Na próxima seção, digite o conteúdo codificado em um arquivo YAML usado para implantar o grupo de contêineres.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Implantar grupo de contêineres

Agora implante o grupo de contêineres especificando as configurações do contêiner em um [arquivo YAML](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Criar arquivo YAML

Copie o YAML a seguir `deploy-aci.yaml`em um novo arquivo chamado . No Azure Cloud Shell, você pode usar o Visual Studio Code para criar o arquivo em seu diretório de trabalho:

```console
code deploy-aci.yaml
```

Digite o conteúdo dos arquivos codificados com base64 onde indicado em `secret`. Por exemplo, `cat` cada um dos arquivos codificados com base64 para ver seu conteúdo. Durante a implantação, esses arquivos são adicionados a um [volume secreto](container-instances-volume-secret.md) no grupo de contêineres. Neste exemplo, o volume secreto é montado no recipiente Nginx.

```YAML
api-version: 2018-10-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>Implantar o grupo de contêineres

Crie um grupo de recursos com o comando [az group create:](/cli/azure/group#az-group-create)

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Implante o grupo de contêineres com o comando [az container create,](/cli/azure/container#az-container-create) passando o arquivo YAML como argumento.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Exibir estado da implantação

Para exibir o estado da implantação, use o comando [az container show](/cli/azure/container#az-container-show) a seguir:

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Para uma implantação bem-sucedida, a saída é semelhante à seguinte:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-tls-connection"></a>Verifique a conexão TLS

Use seu navegador para navegar até o endereço IP público do grupo de contêineres. O endereço IP mostrado `52.157.22.76`neste exemplo é **https://52.157.22.76**, portanto, a URL é . Você deve usar HTTPS para ver o aplicativo em execução, por causa da configuração do servidor Nginx. As tentativas de conexão sobre HTTP falham.

![Captura de tela de navegador mostrando aplicativo em execução em uma instância de contêiner do Azure](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Como este exemplo usa um certificado auto-assinado e não um de uma autoridade de certificado, o navegador exibe um aviso de segurança ao se conectar ao site em HTTPS. Você pode precisar aceitar o aviso ou ajustar as configurações do navegador ou certificado para prosseguir para a página. O comportamento é esperado.

>

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como configurar um contêiner Nginx para habilitar conexões TLS a um aplicativo web em execução no grupo de contêineres. Você pode adaptar este exemplo para aplicativos que ouvem em portas diferentes da porta 80. Você também pode atualizar o arquivo de configuração Nginx para redirecionar automaticamente as conexões do servidor na porta 80 (HTTP) para usar HTTPS.

Enquanto este artigo usa Nginx no sidecar, você pode usar outro provedor DeT, como [o Caddy](https://caddyserver.com/).

Se você implantar seu grupo de contêineres em uma [rede virtual Do Azure,](container-instances-vnet.md)você poderá considerar outras opções para habilitar um ponto final TLS para uma instância de contêiner backend, incluindo:

* [Proxies de funções do Azure](../azure-functions/functions-proxies.md)
* [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md)
* [Gateway de aplicativo do Azure](../application-gateway/overview.md) - veja um modelo de [implantação](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)de amostra .
