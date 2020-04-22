---
title: Solucionar problemas – Azure IoT Edge | Microsoft Docs
description: Use este artigo para aprender habilidades de diagnóstico padrão para o Azure IoT Edge, como recuperar status e logs do componente e resolver problemas comuns
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 3529d6a67c6c8c19c053fe3170298658e90b4a54
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729267"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Problemas comuns e resoluções para o Azure IoT Edge

Se você tiver problemas com o Azure IoT Edge no seu ambiente, use este artigo como um guia para a solução desses problemas.

## <a name="run-the-iotedge-check-command"></a>Execute o comando 'check' do iotedge

O primeiro passo para solucionar problemas do IoT Edge deve ser usar o `check` comando, que executa uma coleção de testes de configuração e conectividade para problemas comuns. O `check` comando está disponível na [versão 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) e posterior.

Você pode `check` executar o comando da `--help` seguinte forma ou incluir o sinalizador para ver uma lista completa de opções:

* No Linux:

  ```bash
  sudo iotedge check
  ```

* No Windows:

  ```powershell
  iotedge check
  ```

Os tipos de verificações executadas pela ferramenta podem ser classificados como:

* Verificações de configuração: Examina detalhes que poderiam impedir que os dispositivos Edge se conectem à nuvem, incluindo problemas com *config.yaml* e o motor do contêiner.
* Verificações de conexão: Verifica que o tempo de execução do IoT Edge pode acessar portas no dispositivo host e todos os componentes da IoT Edge podem se conectar ao IoT Hub.
* Verificações de prontidão de produção: Procura as práticas recomendadas de produção, como os certificados de estado da autoridade de certificado de dispositivo (CA) e a configuração do arquivo de registro de módulos.

Para obter uma lista completa de verificações de diagnóstico, consulte [a funcionalidade de solução de problemas incorporada](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-iotedge-support-bundle-command"></a>Coletar informações de depuração com o comando 'support-bundle' do iotedge

Quando você precisa coletar registros de um dispositivo IoT Edge, `support-bundle` a maneira mais conveniente é usar o comando. Por padrão, este comando coleta módulo, IoT Edge Security Manager e registros do mecanismo de contêiner, saída JSON 'iotedge' e outras informações úteis de depuração. Ele os comprime em um único arquivo para fácil compartilhamento. O `support-bundle` comando está disponível na [versão 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) e posterior.

Execute `support-bundle` o comando `--since` com a bandeira para especificar quanto tempo do passado você deseja obter logs. Por `6h` exemplo, obterá registros desde `6d` as últimas 6 `6m` horas, desde os últimos 6 dias, desde os últimos 6 minutos e assim por diante. Inclua `--help` o sinalizador para ver uma lista completa de opções.


* No Linux:

  ```bash
  sudo iotedge support-bundle --since 6h
  ```

* No Windows:

  ```powershell
  iotedge support-bundle --since 6h
  ```

> [!WARNING]
> A saída `support-bundle` do comando pode conter nomes de host, dispositivo e módulo, informações registradas por seus módulos etc. Por favor, esteja ciente disso se compartilhar a produção em um fórum público.

## <a name="standard-diagnostic-steps"></a>Etapas de diagnóstico padrão

Se você encontrar um problema, você pode aprender mais sobre o estado do seu dispositivo IoT Edge revisando os registros de contêineres e as mensagens que passam para e a partir do dispositivo. Use as ferramentas e os comandos desta seção para coletar informações.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Verifique o status do IoT Edge Security Manager e seus logs

No Linux:

* Para ver o status do Gerenciador de Segurança do IoT Edge:

   ```bash
   sudo systemctl status iotedge
   ```

* Para ver os logs do Gerenciador de Segurança do IoT Edge:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Para ver mais detalhadamente os logs do Gerenciador de Segurança do IoT Edge:

  * Edite as configurações de daemon iotedge:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Atualize as seguintes linhas:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Reinicie o daemon de segurança do IoT Edge:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

No Windows:

* Para ver o status do Gerenciador de Segurança do IoT Edge:

   ```powershell
   Get-Service iotedge
   ```

* Para ver os logs do Gerenciador de Segurança do IoT Edge:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Se o Gerenciador de segurança de IoT Edge não está em execução, verifique se o arquivo de configuração yaml

> [!WARNING]
> Os arquivos YAML não podem conter guias como recuo. Use 2 espaços no lugar. Elementos de alto nível não devem ter espaços de liderança.

No Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

No Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Verifique os logs de contêiner de problemas

Depois que o Daemon de segurança de IoT Edge está em execução, examine os logs de contêineres para detectar problemas. Comece com seus contêineres implantados e, em seguida, olhe para os contêineres que compõem o tempo de execução do IoT Edge: edgeAgent e edgeHub. Os registros do agente IoT Edge normalmente fornecem informações sobre o ciclo de vida de cada contêiner. Os logs do hub IoT Edge fornecem informações sobre mensagens e roteamento.

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Veja as mensagens que passam pelo hub IoT Edge

Você pode visualizar as mensagens que passam pelo hub IoT Edge e coletar insights de registros verbosdos dos contêineres de tempo de execução. Para ativar os logs detalhados nesses contêineres, defina `RuntimeLogLevel` em seu arquivo de configuração yaml. Para abrir o arquivo:

No Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

No Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Por padrão, o elemento `agent` será parecido com o seguinte exemplo:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Substitua `env: {}` por:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > Arquivos YAML não podem conter guias como recuo. Use 2 espaços no lugar. Itens de nível superior não podem ter espaço em branco líder.

Salve o arquivo e reinicie o gerenciador de segurança do IoT Edge.

Você também pode verificar as mensagens que estão sendo enviadas entre os dispositivos do Hub IoT e do IoT Edge. Veja essas mensagens usando a [extensão Azure IoT Hub para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Para obter mais informações, confira [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/) (Ferramenta útil ao desenvolver com o Azure IoT).

### <a name="restart-containers"></a>Reinicie os contêineres

Após investigar os logs e as mensagens para obter informações, você poderá tentar reiniciar contêineres:

```cmd
iotedge restart <container name>
```

Reiniciar contêineres de runtime do IoT Edge:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Reinicie o gerenciador de segurança do IoT Edge

Se o problema ainda é persistente, você pode tentar reiniciar o Gerenciador de segurança de IoT Edge.

No Linux:

   ```cmd
   sudo systemctl restart iotedge
   ```

No Windows:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>Agente ioT Edge pára depois de cerca de um minuto

O módulo edgeAgent é iniciado e executado com sucesso por cerca de um minuto e, em seguida, pára. Os registros indicam que o agente IoT Edge tenta se conectar ao IoT Hub através do AMQP e, em seguida, tenta se conectar usando AMQP através do WebSocket. Quando isso falha, o agente IoT Edge sai.

Exemplo de logs edgeAgent:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Causa raiz**

Uma configuração de rede na rede host está impedindo que o agente IoT Edge chegue à rede. O agente tenta se conectar com AMQP (porta 5671) primeiro. Se a conexão falhar, ele tentará usar WebSockets (porta 443).

O runtime do IoT Edge configura uma rede para que cada um dos módulos se comunique. No Linux, esta rede é uma rede de ponte. No Windows, ele usa o NAT. Esse problema é mais comum em dispositivos Windows com contêineres do Windows que usam a rede NAT.

**Resolução**

Verifique se existe uma rota para a Internet para os endereços IP atribuídos a esta rede de ponte/NAT. Às vezes, uma configuração de VPN no host substitui a rede do IoT Edge.

## <a name="iot-edge-hub-fails-to-start"></a>O hub IoT Edge não consegue iniciar

O módulo edgeHub não é iniciale e imprime a seguinte mensagem nos logs:

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

**Causa raiz**

Algum outro processo no computador host tem a porta 443 associada. O hub IoT Edge mapeia as portas 5671 e 443 para uso em cenários de gateway. Esse mapeamento de porta falhará se outro processo já estiver associado a essa porta.

**Resolução**

Localize e interrompa o processo que está usando a porta 443. Esse processo geralmente é um servidor Web.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>O agente IoT Edge não pode acessar a imagem de um módulo (403)

Um contêiner não é executado, e os logs do edgeAgent mostram um erro de 403.

**Causa raiz**

O agente IoT Edge não tem permissões para acessar a imagem de um módulo.

**Resolução**

Certifique-se de que suas credenciais de registro estão especificadas corretamente em seu manifesto de implantação

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>O daemon de segurança de IoT Edge falhará com um nome de host inválido

O comando `sudo journalctl -u iotedge` falha e imprime a mensagem a seguir:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Causa raiz**

O runtime do IoT Edge só pode oferecer suporte a nomes de host com menos de 64 caracteres. Normalmente, máquinas físicas não têm nomes de host longos, mas o problema é mais comum em uma máquina virtual. Os nomes de host gerados automaticamente para máquinas virtuais do Windows hospedadas no Azure, em particular, tendem a ser longos.

**Resolução**

Quando você vir esse erro, você pode resolvê-lo a configurar o nome DNS de sua máquina virtual e, em seguida, definindo o nome DNS de nome de host no comando de instalação.

1. No portal do Azure, navegue até a página de visão geral de sua máquina virtual.
2. Selecione **configurar** em nome DNS. Caso sua máquina virtual já tenha um nome DNS configurado, você não precisará configurar um novo.

   ![Configurar o nome DNS da máquina virtual](./media/troubleshoot/configure-dns.png)

3. Forneça um valor para **rótulo do nome DNS** e selecione **Salvar**.
4. Copie o novo nome DNS, que deve estar no formato ** \<\>DNSnamelabel\< . vmlocation\>.cloudapp.azure.com**.
5. Dentro da máquina virtual, use o comando a seguir para configurar o runtime do IoT Edge com seu nome DNS:

   * No Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * No Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Problemas de estabilidade em dispositivos com restrição de recursos

Talvez você encontre problemas de estabilidade em dispositivos com restrição como o Raspberry Pi, principalmente quando usados como um gateway. Os sintomas incluem exceções fora da memória no módulo de hub do edge, os dispositivos downstream não podem se conectar ou o dispositivo padra de enviar mensagens de telemetria após algumas horas.

**Causa raiz**

O hub IoT Edge, que faz parte do tempo de execução do IoT Edge, é otimizado para desempenho por padrão e tenta alocar grandes pedaços de memória. Essa otimização não é ideal para dispositivos de borda com restrição e pode causar problemas de estabilidade.

**Resolução**

Para o hub IoT Edge, defina uma variável de ambiente **OptimizeForPerformance** como **falsa**. Existem duas maneiras de definir variáveis de ambiente:

No Portal do Azure:

Em seu IoT Hub, selecione seu dispositivo IoT Edge e na página de detalhes do dispositivo e selecione **Set Modules** > **Runtime Settings**. Crie uma variável de ambiente para o módulo Edge Hub chamada *OptimizeForPerformance* que é definida como *falsa*.

![OptimizeForPerformance definido como false](./media/troubleshoot/optimizeforperformance-false.png)

**OU**

No manifesto de implantação:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Não é possível obter os logs do daemon do IoT Edge no Windows

Se você receber uma EventLogException ao usar `Get-WinEvent` no Windows, verifique suas entradas de registro.

**Causa raiz**

O comando do PowerShell `Get-WinEvent` depende de uma entrada de Registro estar presente para encontrar logs por um `ProviderName` específico.

**Resolução**

Defina uma entrada de registro para o daemon do IoT Edge. Crie um arquivo **iotedge.reg** com o conteúdo a seguir e importe para o Registro do Windows, clicando duas vezes nele ou usando o comando`reg import iotedge.reg`:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>Falha do módulo do IoT Edge ao enviar uma mensagem para o edgeHub com o erro 404

Um módulo do IoT Edge personalizado falha ao enviar uma mensagem para o edgeHub com um erro 404 `Module not found`. O daemon do IoT Edge imprime a seguinte mensagem nos logs:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Causa raiz**

O daemon do IoT Edge impõe a identificação do processo para todos os módulos que se conectam ao edgeHub por motivos de segurança. Ele verifica se todas as mensagens enviadas por um módulo vêm da ID do processo principal do módulo. Se uma mensagem estiver sendo enviada por um módulo de uma ID de processo diferente da estabelecida inicialmente, ele rejeitará a mensagem com uma mensagem de erro 404.

**Resolução**

A partir da versão 1.0.7, todos os processos do módulo estão autorizados a se conectar. Se não for possível atualizar para 1.0.7, complete as seguintes etapas. Para obter mais informações, consulte o [changelog de versão 1.0.7](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Verifique se a mesma ID de processo sempre é usada pelo módulo personalizado do IoT Edge para enviar mensagens ao edgeHub. `ENTRYPOINT` Por exemplo, certifique-se `CMD` de, em vez `CMD` de comandar em seu arquivo Docker, uma vez que levará a um `ENTRYPOINT` ID de processo para o módulo e outro ID de processo para o comando bash executando o programa principal, enquanto levará a um único ID de processo.

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Regras de configuração de firewall e de porta para implantação do IoT Edge

O Azure IoT Edge permite a comunicação de um servidor local para a nuvem DoZure usando protocolos De IoT Hub suportados, veja [a escolha de um protocolo](../iot-hub/iot-hub-devguide-protocols.md)de comunicação . Para maior segurança, os canais de comunicação entre o Azure IoT Edge e o Hub IoT do Azure sempre são configurados para ser de Saída. Essa configuração se baseia no [padrão de comunicação assistida por serviços](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), que minimiza a superfície de ataque a ser explorada por entidades mal-intencionadas. A comunicação de entrada só é necessária para cenários específicos em que o Hub IoT do Azure precisa enviar mensagens por push para o dispositivo do Azure IoT Edge. Mensagens da nuvem para dispositivo são protegidas usando canais TLS seguros e podem ser ainda mais protegidas usando certificados X.509 e módulos de dispositivo do TPM. O Gerenciador de Segurança do Azure IoT Edge rege como essa comunicação pode ser estabelecida. Confira [Gerenciador de Segurança do IoT Edge](../iot-edge/iot-edge-security-manager.md).

Embora o IoT Edge forneça configuração avançada para proteger o runtime do Azure IoT Edge e os módulos implantados, ele ainda depende da configuração do computador e da rede subjacente. Portanto, é imprescindível garantir que as regras adequadas de rede e firewall sejam configuradas para uma borda segura para a comunicação em nuvem. A tabela a seguir pode ser usada como uma diretriz quando as regras de firewall de configuração para os servidores subjacentes onde o tempo de execução do Azure IoT Edge está hospedado:

|Protocolo|Porta|Entrada|Saída|Orientação|
|--|--|--|--|--|
|MQTT|8883|BLOQUEADO (padrão)|BLOQUEADO (padrão)|<ul> <li>Configure a Saída como Aberta ao usar o MQTT como o protocolo de comunicação.<li>Não há suporte para o 1883 para MQTT no IoT Edge. <li>As conexões de Entrada devem ser bloqueadas.</ul>|
|AMQP|5671|BLOQUEADO (padrão)|ABERTO (padrão)|<ul> <li>Protocolo de comunicação padrão do IoT Edge. <li> Precisa ser configurado como Aberto, quando o Azure IoT Edge não está configurado para outros protocolos com suporte ou quando o AMQP é o protocolo de comunicação desejado.<li>Não há suporte para o 5672 para AMQP no IoT Edge.<li>Bloqueie essa porta quando o Azure IoT Edge usar outro protocolo do Hub IoT com suporte.<li>As conexões de Entrada devem ser bloqueadas.</ul></ul>|
|HTTPS|443|BLOQUEADO (padrão)|ABERTO (padrão)|<ul> <li>Configure a Saída para ficar aberta na porta 443 para provisionamento do IoT Edge. Essa configuração é necessária ao usar scripts manuais ou o DPS (serviço de provisionamento de dispositivos) do Azure IoT. <li>A conexão de Entrada deve ser Aberta somente para cenários específicos: <ul> <li>  Se você tiver um gateway transparente com dispositivos de folha que possam enviar solicitações de método. Nesse caso, a Porta 443 não precisa estar aberta para redes externas para conectar-se ao Hub IoT ou fornecer serviços do Hub IoT por meio do Azure IoT Edge. Assim, a regra de entrada pode ser restrita para abrir somente a Entrada da rede interna. <li> Para cenários de cliente para dispositivo (C2D).</ul><li>Não há suporte para a 80 para HTTP no IoT Edge.<li>Se os protocolos que não são HTTP (por exemplo, AMQP ou MQTT) não puderem ser configurados na empresa, as mensagens poderão ser enviadas por meio de WebSockets. A porta 443 será usada para a comunicação do WebSocket nesse caso.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>O módulo Edge Agent continuamente relata 'arquivo de configuração vazia' e nenhum módulo é inicial no dispositivo

O dispositivo tem problemas para iniciar módulos definidos na implantação. Apenas o edgeAgent está sendo executado, mas continuamente relatando 'arquivo de configuração vazio...'.

**Causa raiz**

Por padrão, o IoT Edge inicia módulos em sua própria rede isolada de contêineres. O dispositivo pode estar tendo problemas com a resolução de nomes DNS dentro desta rede privada.

**Resolução**

**Opção 1: Definir o servidor DNS nas configurações do mecanismo do contêiner**

Especifique o servidor DNS para o seu ambiente nas configurações do mecanismo do contêiner, que se aplicará a todos os módulos de contêiner iniciados pelo motor. Crie um `daemon.json` arquivo chamado especificando o servidor DNS para usar. Por exemplo:

```json
{
    "dns": ["1.1.1.1"]
}
```

O exemplo acima define o servidor DNS para um serviço DNS acessível ao público. Se o dispositivo de borda não puder acessar esse IP a partir de seu ambiente, substitua-o por um endereço de servidor DNS acessível.

Coloque `daemon.json` no local certo para sua plataforma:

| Plataforma | Location |
| --------- | -------- |
| Linux | `/etc/docker` |
| Host do Windows com contêineres windows | `C:\ProgramData\iotedge-moby\config` |

Se o local `daemon.json` já contiver arquivo, adicione a chave **dns** a ele e salve o arquivo.

Reinicie o motor do recipiente para que as atualizações entrem em vigor.

| Plataforma | Comando |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Janelas (Powershell de admin) | `Restart-Service iotedge-moby -Force` |

**Opção 2: Definir o servidor DNS na implantação do IoT Edge por módulo**

Você pode definir o servidor DNS para a criação de cada *móduloOpções* na implantação do IoT Edge. Por exemplo:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Certifique-se de definir essa configuração para os módulos *edgeAgent* e *edgeHub* também.

## <a name="next-steps"></a>Próximas etapas

Você acha que encontrou um bug na plataforma IoT Edge? [Envie um problema](https://github.com/Azure/iotedge/issues) para que possamos continuar melhorando.

Se você tiver mais dúvidas, crie uma [Solicitação de suporte](https://portal.azure.com/#create/Microsoft.Support) para obter ajuda.
