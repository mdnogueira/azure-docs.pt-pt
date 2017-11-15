---
title: "OpenShift em tarefas de pós-implementação do Azure | Microsoft Docs"
description: Tarefas adicionais para depois de um cluster de OpenShift foi implementada.
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
ms.openlocfilehash: 77c4719b5cee7f5736d73ee10cf6abf12229ea11
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="post-deployment-tasks"></a>Tarefas de pós-implementação

Depois de implementar um cluster de OpenShift, pode configurar itens adicionais. Este artigo abrange o seguinte:

- Como configurar o início de sessão único através do Azure Active Directory (Azure AD)
- Como configurar o Operations Management Suite para monitorizar OpenShift
- Como configurar as métricas e registo

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Configurar o início de sessão único através do Azure Active Directory

Para utilizar o Azure Active Directory para autenticação, primeiro tem de criar um registo de aplicações do Azure AD. Este processo envolve dois passos: criar o registo de aplicação e a configuração de permissões.

### <a name="create-an-app-registration"></a>Criar um registo de aplicação

Estes passos utilizam a CLI do Azure para criar o registo de aplicação e o GUI (portal) para definir as permissões. Para criar o registo de aplicação, terá dos seguintes cinco tipos de informações:

- Nome a apresentar: nome de registo da aplicação (por exemplo, OCPAzureAD)
- Página inicial: OpenShift URL da consola (por exemplo, https://masterdns343khhde.westus.cloudapp.azure.com:8443/consola)
- Identificador URI: URL da consola OpenShift (por exemplo, https://masterdns343khhde.westus.cloudapp.azure.com:8443/consola)
- URL de resposta: URL público mestre e o nome do registo de aplicação (por exemplo, https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD)
- Palavra-passe: Palavra-passe segura (utilize uma palavra-passe segura)

O exemplo seguinte cria um registo de aplicação utilizando as informações:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

Se o comando for bem sucedido, receberá uma saída JSON semelhante a:

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

No portal do Azure:

1.  Selecione **do Azure Active Directory** > **registo de aplicação**.
2.  Procure o registo da aplicação (por exemplo, OCPAzureAD).
3.  Nos resultados, clique o registo de aplicação.
4.  Em **definições**, selecione **as permissões necessárias**.
5.  Em **permissões obrigatórias**, selecione **adicionar**.

  ![Registo de aplicação](media/openshift-post-deployment/app-registration.png)

6.  Clique no passo 1: Selecionar API e, em seguida, clique em **Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Clique em **selecione** na parte inferior.

  ![API de seleção de registo de aplicação](media/openshift-post-deployment/app-registration-select-api.png)

7.  No passo 2: Selecione as permissões, selecione **iniciar sessão e ler o perfil de utilizador** em **permissões delegadas**e, em seguida, clique em **selecione**.

  ![Acesso de registo de aplicação](media/openshift-post-deployment/app-registration-access.png)

8.  Selecione **feito**.

### <a name="configure-openshift-for-azure-ad-authentication"></a>Configurar OpenShift para autenticação do Azure AD

Para configurar OpenShift para utilizar o Azure AD como um fornecedor de autenticação, o ficheiro /etc/origin/master/master-config.yaml tem de ser editado em todos os nós principais.

Localize o ID de inquilino utilizando o seguinte comando da CLI:

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

Insira as seguintes linhas imediatamente após as linhas anteriores:

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

Localize o ID de inquilino utilizando o seguinte comando da CLI:```az account show```

Reinicie os serviços de mestres de OpenShift em todos os nós principais:

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**Plataforma de contentor OpenShift (OCP) com vários modelos de estrutura mestres**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**Plataforma de contentor OpenShift com um único principal**

```bash
sudo systemctl restart atomic-openshift-master
```

Na consola do OpenShift, agora, ver duas opções para a autenticação: htpasswd_auth e [registo de aplicação].

## <a name="monitor-openshift-with-operations-management-suite"></a>Monitor OpenShift no Operations Management Suite

Para monitorizar OpenShift com o Operations Management Suite, pode utilizar uma das duas opções: instalação do agente do OMS no anfitrião VM ou o contentor do OMS. Este artigo fornece instruções para implementar o contentor do OMS.

## <a name="create-an-openshift-project-for-operations-management-suite-and-set-user-access"></a>Criar um projeto de OpenShift para o Operations Management Suite e defina o acesso de utilizador

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-a-daemon-set-yaml-file"></a>Criar um ficheiro de yaml do conjunto de daemon

Crie um ficheiro denominado ocp-omsagent.yml:

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

## <a name="create-a-secret-yaml-file"></a>Criar um ficheiro de yaml secreta

Para criar o ficheiro yaml secreta, precisa de dois tipos de informações: ID da área de trabalho OMS e a chave de partilhada área de trabalho do OMS. 

Um ficheiro de ocp-secret.yml de exemplo seguinte: 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

Substituir wsid_data com o Base64 codificado ID da área de trabalho OMS. Em seguida, substitua key_data o com codificação Base64 OMS área de trabalho chave partilhada.

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-the-secret-and-daemon-set"></a>Criar um segredo e daemon

Implemente o ficheiro secreto:

```bash
oc create -f ocp-secret.yml
```

Implemente o conjunto de daemon de agente do OMS:

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>Configurar as métricas e registo

O modelo Azure Resource Manager para a plataforma de contentor OpenShift fornece parâmetros de entrada para ativar as métricas e registo. A oferta do Marketplace de plataforma do contentor OpenShift e o modelo de Gestor de recursos de origem OpenShift não.

Se utilizou o modelo OCP Resource Manager e métricas e registo não foram ativado no momento de instalação ou se tiver utilizado a oferta do OCP Marketplace, pode ser facilmente ative depois de ocorrerem. Se estiver a utilizar o modelo de Gestor de recursos de origem OpenShift, alguns pré-trabalho é necessário.

### <a name="openshift-origin-template-pre-work"></a>Trabalho prévia de modelo de origem OpenShift

1. SSH para o primeiro nó principal através da porta 2200.

   Exemplo:

   ```bash
   ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
   ```

2. Edite o ficheiro de /etc/ansible/hosts e adicione as seguintes linhas depois da secção de fornecedor de identidade (# ativar HTPasswdPasswordIdentityProvider):

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

3. Substitua a cadeia utilizada para a opção de openshift_master_default_subdomain no mesmo ficheiro /etc/ansible/hosts $ROUTING.

### <a name="azure-cloud-provider-in-use"></a>Fornecedor de nuvem do Azure em utilização

No primeiro nó principal (origem) ou o nó de bastion (OCP), SSH utilizando as credenciais fornecidas durante a implementação. Emita o comando seguinte:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>Fornecedor de nuvem do Azure não em utilização

No primeiro nó principal (origem) ou o nó de bastion (OCP), SSH utilizando as credenciais fornecidas durante a implementação. Emita o comando seguinte:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>Passos seguintes

- [Introdução à plataforma de contentor OpenShift](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
- [Introdução ao OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)
