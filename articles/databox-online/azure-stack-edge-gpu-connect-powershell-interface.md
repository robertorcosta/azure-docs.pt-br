---
title: Conectar e gerenciar Microsoft Azure Stack dispositivo de borda por meio da interface do Windows PowerShell | Microsoft Docs
description: Descreve como se conectar ao e gerenciar o Azure Stack Edge por meio da interface do Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 26d4476217d5a6f6abea6093f453cd52d654911e
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083029"
---
# <a name="manage-an-azure-stack-edge-gpu-device-via-windows-powershell"></a>Gerenciar um dispositivo de GPU de borda Azure Stack por meio do Windows PowerShell

Azure Stack solução Edge permite processar dados e enviá-los pela rede para o Azure. Este artigo descreve algumas das tarefas de configuração e gerenciamento para seu dispositivo Azure Stack Edge. Você pode usar o portal do Azure, a interface do usuário da Web local ou a interface do Windows PowerShell para gerenciar seu dispositivo.

Este artigo se concentra em como você pode se conectar à interface do PowerShell do dispositivo e às tarefas que você pode fazer usando essa interface. 


## <a name="connect-to-the-powershell-interface"></a>Conectar-se à interface do PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Criar um pacote de suporte

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Carregar um certificado

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Você também pode carregar IoT Edge certificados para habilitar uma conexão segura entre o dispositivo IoT Edge e os dispositivos downstream que podem se conectar a ele. Há três certificados de IoT Edge (formato *. pem* ) que você precisa instalar:

- Certificado de autoridade de certificação raiz ou autoridade de certificação do proprietário
- Certificado de autoridade de certificação de dispositivo
- Certificado de chave do dispositivo

O exemplo a seguir mostra o uso desse cmdlet para instalar IoT Edge certificados:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Ao executar esse cmdlet, você será solicitado a fornecer a senha para o compartilhamento de rede.

Para obter mais informações sobre certificados, acesse [Azure IOT Edge certificados](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) ou [instalar certificados em um gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Exibir informações do dispositivo
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>Exibir informações de driver de GPU

Se a função de computação estiver configurada em seu dispositivo, você também poderá obter as informações de driver de GPU por meio da interface do PowerShell. 

1. [Conecte-se à interface do PowerShell](#connect-to-the-powershell-interface).
2. Use o `Get-HcsGpuNvidiaSmi` para obter as informações de driver de GPU para seu dispositivo.

    O exemplo a seguir mostra o uso desse cmdlet:

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    Anote as informações do driver do exemplo de saída deste cmdlet.

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>Habilitar MPS (serviço de vários processos)

Um MPS (serviço de vários processos) em GPUs NVIDIA fornece um mecanismo em que as GPUs podem ser compartilhadas por vários trabalhos, onde cada trabalho é alocado a algum percentual dos recursos da GPU. Para habilitar os MPS em seu dispositivo Azure Stack Edge, siga estas etapas:

1. Antes de começar, verifique se: 

    1. Você configurou e [ativou seu Azure Stack dispositivo de borda](azure-stack-edge-gpu-deploy-activate.md) com um recurso de Azure Stack edge/gateway do data box no Azure.
    1. Você [configurou a computação neste dispositivo no portal do Azure](azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Conecte-se à interface do PowerShell](#connect-to-the-powershell-interface).
1. Use o comando a seguir para habilitar os MPS em seu dispositivo.

    ```powershell
    Start-HcsGpuMPS
    ```

## <a name="reset-your-device"></a>Redefinir o dispositivo

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Obter logs de computação

Se a função de computação estiver configurada em seu dispositivo, você também poderá obter os logs de computação por meio da interface do PowerShell.

1. [Conecte-se à interface do PowerShell](#connect-to-the-powershell-interface).
2. Use o `Get-AzureDataBoxEdgeComputeRoleLogs` para obter os logs de computação para seu dispositivo.

    O exemplo a seguir mostra o uso desse cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    Aqui está uma descrição dos parâmetros usados para o cmdlet:
    - `Path`: Forneça um caminho de rede para o compartilhamento no qual você deseja criar o pacote de log de computação.
    - `Credential`: Forneça o nome de usuário para o compartilhamento de rede. Quando você executar esse cmdlet, será necessário fornecer a senha de compartilhamento.
    - `FullLogCollection`: Esse parâmetro garante que o pacote de log conterá todos os logs de computação. Por padrão, o pacote de log contém apenas um subconjunto de logs.



## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Depurar problemas de kubernetes relacionados a IoT Edge

Quando o cluster kubernetes é criado, um usuário padrão `aseuser` associado a um namespace do sistema `iotedge` também é criado. Para depurar quaisquer problemas relacionados a IoT Edge, você pode usar esse usuário e o namespace do sistema.  

### <a name="create-config-file-for-system-namespace"></a>Criar arquivo de configuração para o namespace do sistema

Para solucionar problemas, primeiro crie o `config` arquivo correspondente ao `iotedge` namespace com `aseuser` .

Execute o `Get-HcsKubernetesUserConfig -AseUser` comando e salve a saída como `config` arquivo (sem extensão de arquivo). Salve o arquivo na `.kube` pasta do seu perfil de usuário no computador local.

A seguir está a saída de exemplo do `Get-HcsKubernetesUserConfig` comando.

```PowerShell
[10.100.10.10]: PS>Get-HcsKubernetesUserConfig -AseUser
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EVXhNekl4TkRRME5sb1hEVE13TURVeE1USXhORFEwTmxvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBS0M1CjlJbzRSU2hudG90QUdxdjNTYmRjOVd4UmJDYlRzWXU5S0RQeU9xanVoZE1UUE9PcmROOGNoa0x4NEFyZkZaU1AKZithUmhpdWZqSE56bWhucnkvZlprRGdqQzQzRmV5UHZzcTZXeVVDV0FEK2JBdi9wSkJDbkg2MldoWGNLZ1BVMApqU1k0ZkpXenNFbzBaREhoeUszSGN3MkxkbmdmaEpEanBQRFJBNkRWb2pIaktPb29OT1J1dURvUHpiOTg2dGhUCkZaQXJMZjRvZXRzTEk1ZzFYRTNzZzM1YVhyU0g3N2JPYVVsTGpYTzFYSnpFZlZWZ3BMWE5xR1ZqTXhBMVU2b1MKMXVJL0d1K1ArY
===========CUT=========================================CUT===================
    server: https://compute.myasegpu1.wdshcsso.com:6443
    name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: aseuser
    name: aseuser@kubernetes
current-context: aseuser@kubernetes
kind: Config
preferences: {}
users:
- name: aseuser
    user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJY1hOTXRPU2VwbG93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBMU1UTXlNVFEwTkRaYUZ3MHlNVEExTVRNeU1UVXhNVEphTUJJeApFREFPQmdOVkJBTVRCMkZ6WlhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCCkFRRHVjQ1pKdm9qNFIrc0U3a1EyYmVjNEJkTXdpUEhmU2R2WnNDVVY0aTRRZGY1Yzd0dkE3OVRSZkRLQTY1d08Kd0h0QWdlK3lLK0hIQ1Qyd09RbWtNek1RNjZwVFEzUlE0eVdtRDZHR1cWZWMExBR1hFUUxWWHRuTUdGCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==

[10.100.10.10]: PS>
```

Em um dispositivo Azure Stack Edge que tem a função de computação configurada, todos os `kubectl` comandos estão disponíveis para monitorar ou solucionar problemas de módulos. Para ver uma lista de comandos disponíveis, execute `kubectl --help` na janela de comando.

```PowerShell
C:\Users\myuser>kubectl --help
kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

Para obter uma lista abrangente dos `kubectl` comandos, acesse [ `kubectl` cola](https://kubernetes.io/docs/reference/kubectl/cheatsheet/). 

### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Para obter o IP do serviço ou módulo exposto fora do cluster kubernetes

Para obter o IP de um serviço de balanceamento de carga ou módulos expostos fora do kubernetes, execute o seguinte comando:

`kubectl get svc -n iotedge`

Veja a seguir um exemplo de saída de todos os serviços ou módulos expostos fora do cluster kubernetes. 


```powershell
C:\Users\user>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

C:\Users\user>
```
O endereço IP na coluna IP externa corresponde ao ponto de extremidade externo do serviço ou do módulo. Você também pode [obter o IP externo no painel do kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules).


### <a name="to-check-if-module-deployed-successfully"></a>Para verificar se o módulo foi implantado com êxito

Os módulos de computação são contêineres que têm uma lógica de negócios implementada. Um pod kubernetes pode ter vários contêineres em execução. Para verificar se um módulo de computação foi implantado com êxito, execute o `get pods` comando e verifique se o contêiner (correspondente ao módulo de computação) está em execução.

Para obter a lista de todos os pods em execução em um namespace específico, execute o seguinte comando:

`get pods -n <namespace>`

A seguir está um exemplo de saída de todos os pods em execução no `iotedge` namespace.

```
C:\Users\myuser>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

C:\Users\myuser>
```

O **status** de status indica que todos os pods no namespace estão em execução e o **pronto** indica o número de contêineres implantados em um pod. No exemplo anterior, todos os pods estão em execução e todos os módulos implantados em cada um dos pods estão em execução. 

Para obter uma saída mais detalhada de um pod específico para um determinado namespace, você pode executar o seguinte comando:

`kubectl describe pod <pod name> -n <namespace>` 

A saída de exemplo é mostrada aqui.

```
C:\Users\myuser>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


C:\Users\myuser>
```

### <a name="to-get-container-logs"></a>Para obter logs de contêiner

Para obter os logs de um módulo, execute o seguinte comando:

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Como `all-containers` o sinalizador irá despejar todos os logs de todos os contêineres, uma boa maneira de ver os erros recentes é usar a opção `--tail 10` .

Veja a seguir um exemplo de saída. 

```
C:\Users\myuser>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

C:\Users\myuser>
```

## <a name="exit-the-remote-session"></a>Sair da sessão remota

Para sair da sessão remota do PowerShell, feche a janela do PowerShell.

## <a name="next-steps"></a>Próximas etapas

- Implante [Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md) no portal do Azure.
