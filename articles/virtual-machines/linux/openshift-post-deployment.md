---
title: "OpenShift em tarefas de implementação do Azure Post | Microsoft Docs"
description: "Tarefas de implementação OpenShift Post"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 12e6785358f5f412326418b0c64eeaeabdaa3b5f
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="post-deployment-tasks"></a>Tarefas de pós-implementação

Depois do cluster OpenShift é implementado, existirem itens adicionais que podem ser configurados. Este artigo irá abranger o seguinte:

- Configurar o início de sessão único com o Azure Active Directory (AAD)
- Configurar OMS monitorizar OpenShift
- Configurar as métricas e registo

## <a name="single-sign-on-using-aad"></a>Início de sessão único a utilizar o AAD

Para utilizar o AAD para autenticação, um registo de aplicações do Azure AD tem de ser criado pela primeira vez. Este processo irá envolve dois passos - criação do registo de aplicação e, em seguida, configurar as permissões.

### <a name="create-app-registration"></a>Criar registo de aplicação

Utilizaremos a CLI do Azure para criar o registo de aplicação e o GUI (Portal) para definir as permissões. Para criar o registo de aplicação, serão necessário cinco tipos de informações.

- Nome a apresentar: Nome do registo de aplicação (ex: OCPAzureAD)
- Home Page: URL da consola OpenShift (ex: https://masterdns343khhde.westus.cloudapp.azure.com:8443/consola)
- Identificador URI: URL da consola OpenShift (ex: https://masterdns343khhde.westus.cloudapp.azure.com:8443/consola)
- URL de resposta: Mestre URL público e o nome do registo de aplicação (ex: oauth2callback/https://masterdns343khhde.westus.cloudapp.azure.com:8443/OCPAzureAD)
- Palavra-passe: Palavra-passe segura (utilize uma palavra-passe segura)

O exemplo seguinte irá criar um registo de aplicação utilizando as informações de acima.

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

Se o comando for bem sucedido, obterá uma saída JSON semelhante a:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD"
  ]
}
```

Tome nota da propriedade appId devolvida do comando de um passo posterior.

No **Portal do Azure**:

1.  Selecione **do Azure Active Directory** --> **registo de aplicação**
2.  Pesquisa de registo da aplicação (ex: OCPAzureAD)
3.  Nos resultados, clique o registo de aplicação
4.  No painel de definições, selecione **as permissões necessárias**
5.  No painel de permissões necessárias, clique em **adicionar**

  ![Registo de aplicação](media/openshift-post-deployment/app-registration.png)

6.  Clique no passo 1: selecionar API e, em seguida, clique em **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)** e clique em **selecione** na parte inferior

  ![API de seleção de registo de aplicação](media/openshift-post-deployment/app-registration-select-api.png)

7.  No passo 2: Selecione as permissões, selecione **iniciar sessão e ler o perfil de utilizador** em **permissões delegadas** e clique em **selecione**

  ![Acesso de registo de aplicação](media/openshift-post-deployment/app-registration-access.png)

8.  Clique em **concluído**

### <a name="configure-openshift-for-azure-ad-authentication"></a>Configurar OpenShift para autenticação do Azure AD

Para configurar OpenShift para utilizar o Azure AD como um fornecedor de autenticação, o **/etc/origin/master/master-config.yaml** ficheiro tem de ser editado em todos os nós do mestre.

Pode encontrar o Id de inquilino utilizando o seguinte comando da CLI:

```azurecli
az account show
```

No ficheiro yaml, localize as seguintes linhas:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Insira as seguintes linhas imediatamente após as linhas acima:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

Pode encontrar o Id de inquilino utilizando o seguinte comando da CLI:```az account show```

Reinicie os serviços de OpenShift mestre em todos os nós de principal

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**Plataforma de contentor OpenShift com múltiplos mestres**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**Plataforma de contentor OpenShift com mestre única**

```bash
sudo systemctl restart atomic-openshift-master
```

Na consola do OpenShift, agora verá duas opções para a autenticação - htpasswd_auth e **[registo de aplicação]**.

## <a name="monitor-openshift-with-oms"></a>Monitor OpenShift com o OMS

Monitorização OpenShift com o OMS pode ser conseguido através de uma das duas opções - instalação do agente do OMS no anfitrião VM ou contentor do OMS. Este artigo fornece instruções sobre como implementar o contentor do OMS.

## <a name="create-an-openshift-project-for-oms-and-set-user-access"></a>Criar um projeto de OpenShift para OMS e definir o acesso de utilizador

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-daemon-set-yaml-file"></a>Criar ficheiro de yaml do conjunto de daemon

Crie um ficheiro denominado ocp-omsagent.yml.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: oms
spec:
  selector:
    matchLabels:
      name: omsagent
  template:
    metadata:
      labels:
        name: omsagent
        agentVersion: 1.4.0-45
        dockerProviderVersion: 10.0.0-25
    spec:
      nodeSelector:
        zone: default
      serviceAccount: omsagent
      containers:
      - image: "microsoft/oms"
        imagePullPolicy: Always
        name: omsagent
        securityContext:
          privileged: true
        ports:
        - containerPort: 25225
          protocol: TCP
        - containerPort: 25224
          protocol: UDP
        volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        livenessProbe:
          exec:
            command:
              - /bin/bash
              - -c
              - ps -ef | grep omsagent | grep -v "grep"
          initialDelaySeconds: 60
          periodSeconds: 60
      volumes:
      - name: docker-sock
        hostPath:
          path: /var/run/docker.sock
      - name: omsagent-secret
        secret:
         secretName: omsagent-secret
````

## <a name="create-secret-yaml-file"></a>Criar ficheiro de yaml secreta

Para criar o ficheiro yaml secreta, dois tipos de informações serão necessárias - ID da área de trabalho OMS e a chave de partilhada área de trabalho do OMS. 

Ficheiro de ocp-secret.yml de exemplo 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

Substituir wsid_data com o Base64 codificado ID da área de trabalho OMS e substituir key_data com o Base64 codificado a chave de partilhada área de trabalho do OMS.

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-secret-and-daemon-set"></a>Criar conjunto segredo e daemon

Implementar o ficheiro secreto

```bash
oc create -f ocp-secret.yml
```

Implementar o conjunto de Daemon de agente do OMS

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>Configurar as métricas e registo

O modelo OpenShift contentor plataforma (OCP) Resource Manager fornece parâmetros de entrada para ativar as métricas e registo. O modelo de oferta do Marketplace OpenShift contentor plataforma e o Gestor de recursos de origem OpenShift não.

Se utilizou o modelo OCP Resource Manager e métricas e registo não foram ativadas no momento de instalação ou a oferta do OCP Marketplace foi utilizada, estes podem ser facilmente ativadas depois de ocorrerem. Se utilizar o modelo de Gestor de recursos de origem OpenShift, alguns pré-trabalho é necessário.

### <a name="openshift-origin-template-pre-work"></a>Trabalho prévia de modelo de origem OpenShift

SSH para o primeiro nó mestre utilizando a porta 2200

Exemplo

```bash
ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
```

Editar o **/etc/ansible/hosts ficheiro** e adicione as seguintes linhas depois da secção de fornecedor de identidade (# ativar HTPasswdPasswordIdentityProvider)

```yaml
# Setup metrics
openshift_hosted_metrics_deploy=false
openshift_metrics_cassandra_storage_type=dynamic
openshift_metrics_start_cluster=true
openshift_metrics_hawkular_nodeselector={"type":"infra"}
openshift_metrics_cassandra_nodeselector={"type":"infra"}
openshift_metrics_heapster_nodeselector={"type":"infra"}
openshift_hosted_metrics_public_url=https://metrics.$ROUTING/hawkular/metrics

# Setup logging
openshift_hosted_logging_deploy=false
openshift_hosted_logging_storage_kind=dynamic
openshift_logging_fluentd_nodeselector={"logging":"true"}
openshift_logging_es_nodeselector={"type":"infra"}
openshift_logging_kibana_nodeselector={"type":"infra"}
openshift_logging_curator_nodeselector={"type":"infra"}
openshift_master_logging_public_url=https://kibana.$ROUTING
```

Substitua $ROUTING com a cadeia utilizada para **openshift_master_default_subdomain** opção no mesmo **/etc/ansible/hosts** ficheiro.

### <a name="azure-cloud-provider-in-use"></a>Fornecedor de nuvem do Azure em utilização

No primeiro nó mestre (origem) ou Bastion nó (OCP), utilizando as credenciais fornecidas durante a implementação de SSH. Emita o comando seguinte:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>Fornecedor de nuvem do Azure não em utilização

No primeiro nó mestre (origem) ou Bastion nó (OCP), utilizando as credenciais fornecidas durante a implementação de SSH. Emita o comando seguinte:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>Passos seguintes

- [Introdução à plataforma de contentor OpenShift](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
- [Introdução ao OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)