# runner-packages
Repositorio do curso de DevOps Pro aula de Runners

# GitHub Actions Runners

> Guia técnico baseado na aula sobre **GitHub Actions Runners**, com foco em tipos de runners, recursos, imagens, softwares disponíveis, instalação de dependências e boas práticas de uso.

---

## 📌 Visão geral

O **Runner** é o agente de execução do GitHub Actions. É o ambiente responsável por executar os `jobs` definidos nos workflows de CI/CD.

De forma geral, existem dois modelos principais:

- **GitHub-hosted runner** — ambiente criado e gerenciado pelo próprio GitHub.
- **Self-hosted runner** — ambiente criado e administrado pela própria organização.

A escolha entre os dois depende principalmente de requisitos de **compliance**, infraestrutura, segurança, customização e necessidade de controle sobre o ambiente de execução.

---

## 🏗️ O que é um Runner?

Um runner é o ambiente onde o GitHub Actions efetivamente executa os comandos definidos no workflow.

Exemplo conceitual:

```text
GitHub Repository
       │
       ▼
GitHub Actions
       │
       ▼
     Job
       │
       ▼
    Runner
       │
       ├── Sistema Operacional
       ├── CPU
       ├── Memória
       ├── Storage
       ├── Ferramentas
       ├── Runtimes
       └── Pacotes
```

O workflow determina o runner que será utilizado por meio da propriedade:

```yaml
runs-on: ubuntu-latest
```

A tag informada em `runs-on` determina o ambiente no qual o job será executado.

---

# 🔧 Tipos de Runner

## GitHub-hosted Runner

São runners:

- Criados pelo GitHub.
- Gerenciados pelo GitHub.
- Provisionados para executar os jobs.
- Mantidos e atualizados pelo GitHub.
- Disponibilizados conforme os recursos e limites da conta/repositório.

Exemplo:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
```

### Quando utilizar?

É a opção recomendada quando não existem requisitos específicos que obriguem a execução dentro de uma infraestrutura própria.

Vantagens:

- Menor esforço operacional.
- Não é necessário administrar servidores.
- Não é necessário manter o agente.
- O ambiente é disponibilizado pelo GitHub.
- Facilita a adoção de CI/CD.

> **Boa prática:** quando não houver requisito de compliance ou necessidade específica de infraestrutura própria, prefira GitHub-hosted runners.

---

# 🖥️ Self-hosted Runner

O **self-hosted runner** é um agente de execução administrado pela própria organização.

Pode ser executado em:

- Servidores físicos.
- Máquinas virtuais.
- Data centers próprios.
- Ambientes cloud.
- Clusters Kubernetes.
- Infraestrutura interna.

Conceitualmente:

```text
GitHub Actions
      │
      ▼
Self-hosted Runner
      │
      ├── VM
      ├── Servidor
      ├── Data Center
      └── Kubernetes
```

### Quando utilizar?

Pode ser necessário quando existem:

- Requisitos de compliance.
- Restrições de segurança.
- Dependências internas.
- Necessidade de acesso à rede privada.
- Necessidade de hardware específico.
- Necessidade de maior controle sobre o ambiente.

> O self-hosted runner oferece maior controle, porém também transfere para a organização a responsabilidade pelo gerenciamento do ambiente.

---

# 🏷️ `runs-on` e Tags

O `runs-on` define qual ambiente será utilizado pelo job.

Exemplo:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
```

Também é possível especificar versões ou ambientes específicos, dependendo das opções disponibilizadas pelo GitHub.

Exemplos:

```yaml
runs-on: ubuntu-24.04
```

```yaml
runs-on: ubuntu-22.04
```

```yaml
runs-on: windows-2022
```

```yaml
runs-on: macos-12
```

A escolha da tag determina o ambiente de execução.

---

# 🐧 Linux, 🪟 Windows e 🍎 macOS

Os GitHub-hosted runners disponibilizam diferentes sistemas operacionais.

## Linux

É o ambiente normalmente mais utilizado para automações de DevOps e workloads cloud.

Exemplo:

```yaml
runs-on: ubuntu-latest
```

Também podem existir versões específicas:

```yaml
runs-on: ubuntu-24.04
```

```yaml
runs-on: ubuntu-22.04
```

---

## Windows

Pode ser utilizado quando a automação depende de:

- Aplicações Windows.
- Ferramentas específicas do ecossistema Microsoft.
- Sistemas legados.
- Scripts PowerShell.
- Builds específicos para Windows.

Exemplo:

```yaml
runs-on: windows-2022
```

---

## macOS

É especialmente relevante para workloads que dependem do ecossistema Apple/macOS.

Exemplo:

```yaml
runs-on: macos-12
```

O uso de macOS pode representar um custo maior, portanto deve ser escolhido quando realmente houver necessidade.

---

# ⚠️ `latest` é dinâmico

Um ponto importante é que tags como:

```yaml
runs-on: ubuntu-latest
```

não devem ser interpretadas como uma versão imutável.

O ambiente associado ao `latest` pode mudar conforme o GitHub atualiza sua infraestrutura.

Por isso, a recomendação é:

### Use `latest` quando:

- Você deseja acompanhar a versão estável disponibilizada pelo GitHub.
- Sua aplicação é compatível com atualizações do sistema operacional.
- Não existe dependência rígida de uma versão específica.

### Use uma versão específica quando:

- Existe requisito de compatibilidade.
- O projeto depende de determinada versão do sistema operacional.
- Você precisa de maior previsibilidade.
- Uma atualização automática poderia quebrar o pipeline.

Exemplo:

```yaml
runs-on: ubuntu-22.04
```

em vez de:

```yaml
runs-on: ubuntu-latest
```

---

# 🧮 Recursos do Runner

Os runners possuem recursos computacionais próprios.

Entre os principais recursos estão:

- CPU.
- Memória RAM.
- Storage.
- Sistema operacional.
- Arquitetura.

Esses recursos variam conforme o tipo de runner e o contexto de utilização.

A aula apresenta como exemplo ambientes Linux com múltiplos CPUs, memória RAM e armazenamento provisionados para a execução dos jobs.

> **Importante:** os recursos e configurações dos runners podem mudar. Para obter os valores atuais, consulte sempre a documentação oficial do GitHub Actions.

---

# 📦 Software disponível no Runner

Não basta conhecer apenas o hardware.

Para executar uma aplicação corretamente, também é necessário conhecer os softwares disponíveis no ambiente.

Podem existir ferramentas como:

- Bash.
- Node.js.
- Python.
- Ruby.
- Kotlin.
- Mono.
- Docker.
- Kubectl.
- Kind.
- Ansible.
- Pacotes NPM.
- Outros runtimes e ferramentas.

A aula destaca que a melhor maneira de verificar exatamente o que está disponível é analisar a execução do próprio workflow.

---

# 🔍 Como verificar o ambiente do Runner

No GitHub:

```text
Repository
   │
   └── Actions
        │
        └── Workflow Run
             │
             └── Job
                  │
                  └── Set up job
                       │
                       └── Runner
```

Na execução do job é possível verificar informações sobre:

- Imagem utilizada.
- Sistema operacional.
- Versão da imagem.
- Software instalado.
- Linguagens.
- Runtimes.
- Ferramentas.

A aula destaca que o `Runner Image` apresenta a lista de softwares incluídos naquela imagem.

Isso é importante porque a imagem é atualizada constantemente.

---

# 🔄 Runner é um ambiente baseado no Job

Um conceito fundamental apresentado na aula é que o ambiente do runner é preparado para o job.

Portanto, qualquer preparação necessária para a execução deve fazer parte do workflow.

Exemplo:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Preparar ambiente
        run: |
          sudo apt update
          sudo apt install -y ffmpeg

      - name: Executar aplicação
        run: |
          ffmpeg -version
```

Isso garante que o job prepare o ambiente necessário antes de executar a aplicação.

---

# 📥 Instalando pacotes no Runner

Não ter determinado software pré-instalado **não significa necessariamente que seja necessário criar um self-hosted runner**.

Em um GitHub-hosted runner, é possível instalar dependências durante a execução do workflow.

Por exemplo, no Ubuntu:

```bash
sudo apt update
sudo apt install -y ffmpeg
```

No workflow:

```yaml
- name: Instalar FFmpeg
  run: |
    sudo apt update
    sudo apt install -y ffmpeg
```

Depois:

```yaml
- name: Verificar FFmpeg
  run: |
    ffmpeg -version
```

---

# 🎥 Exemplo prático: FFmpeg

Na aula foi utilizado o **FFmpeg** como exemplo de dependência que não estava disponível no ambiente inicialmente.

Sem o pacote instalado:

```bash
ffmpeg
```

o comando pode falhar porque o executável não existe no ambiente.

A solução apresentada foi instalar o pacote:

```bash
sudo apt update
sudo apt install -y ffmpeg
```

Depois da instalação:

```bash
ffmpeg -version
```

pode ser executado normalmente.

### Workflow completo

```yaml
name: Install Runner Packages

on:
  workflow_dispatch:

jobs:
  install:
    runs-on: ubuntu-latest

    steps:
      - name: Instalar FFmpeg
        run: |
          sudo apt update
          sudo apt install -y ffmpeg

      - name: Validar instalação
        run: |
          ffmpeg -version
```

---

# 🧩 Actions para configurar o ambiente

Além da instalação manual via linha de comando, o GitHub Actions possui **Actions reutilizáveis** que ajudam a preparar o ambiente.

Um exemplo citado na aula é a configuração da versão do Node.js.

Em vez de instalar e configurar manualmente o Node.js, pode-se utilizar uma Action específica.

Exemplo:

```yaml
- name: Setup Node.js
  uses: actions/setup-node@v4
  with:
    node-version: 18
```

Depois:

```yaml
- name: Verificar Node.js
  run: |
    node --version
    npm --version
```

Essa abordagem facilita a padronização da versão utilizada no pipeline.

---

# 🛠️ Preparação de ambiente

Existem duas estratégias principais apresentadas:

## 1. Instalação via linha de comando

Exemplo:

```yaml
- name: Instalar dependências
  run: |
    sudo apt update
    sudo apt install -y ffmpeg
```

## 2. Utilização de Actions

Exemplo:

```yaml
- name: Configurar Node.js
  uses: actions/setup-node@v4
  with:
    node-version: 18
```

A escolha depende do software que precisa ser instalado/configurado.

---

# 🐳 Containers como alternativa

A aula também destaca que a necessidade de um pacote ou ferramenta específica não significa automaticamente que seja necessário utilizar um self-hosted runner.

Outra possibilidade é utilizar **containers** para disponibilizar o ambiente necessário.

Conceitualmente:

```text
GitHub-hosted Runner
        │
        └── Container
              │
              ├── Dependências
              ├── Runtime
              └── Ferramentas
```

Essa estratégia permite maior isolamento e padronização do ambiente.

> A aula menciona containers como uma abordagem que será aprofundada posteriormente.

---

# 💰 Runners maiores — Larger Runners

O GitHub também possui opções de runners com maior capacidade computacional, conhecidos como **Larger Runners**.

Eles podem ser utilizados quando o workload exige mais recursos.

Exemplos de situações:

- Builds muito pesados.
- Processamento intensivo.
- Workloads que precisam de mais CPU.
- Workloads que precisam de mais memória.

Porém, esses recursos podem possuir custos adicionais e normalmente não são necessários para a maioria das automações.

---

# 🌐 Repositórios Públicos e Privados

A configuração e os recursos disponibilizados podem variar conforme o contexto do repositório.

É importante considerar:

- Repositórios públicos.
- Repositórios privados.
- Limites de utilização.
- Recursos disponíveis.
- Arquitetura.
- Necessidade de runners maiores.

A aula também menciona a possibilidade de utilização de arquitetura **ARM** em determinados cenários.

---

# 🧭 Fluxo recomendado para configurar um Runner

Uma abordagem prática pode seguir esta sequência:

```text
1. Definir necessidade
       │
       ▼
2. GitHub-hosted ou Self-hosted?
       │
       ▼
3. Escolher SO
       │
       ▼
4. Escolher versão/tag
       │
       ▼
5. Verificar recursos
       │
       ▼
6. Verificar softwares disponíveis
       │
       ▼
7. Identificar dependências ausentes
       │
       ▼
8. Instalar/configurar dependências
       │
       ▼
9. Executar o Job
       │
       ▼
10. Validar resultado
```

---

# 🔐 Quando escolher Self-hosted?

Utilize **self-hosted runners** quando houver uma necessidade real de controle sobre o ambiente.

Exemplos:

| Necessidade | Opção recomendada |
|---|---|
| CI/CD comum | GitHub-hosted |
| Não administrar infraestrutura | GitHub-hosted |
| Compliance específico | Self-hosted |
| Rede interna/privada | Self-hosted |
| Hardware específico | Self-hosted |
| Dependências internas | Self-hosted |
| Ambiente altamente customizado | Self-hosted |
| Workload comum em Linux | GitHub-hosted |

---

# 🧠 Boas práticas DevOps

## 1. Evite self-hosted sem necessidade

Não utilize self-hosted apenas porque falta uma ferramenta no runner.

Primeiro verifique se é possível:

- Instalar o pacote durante o job.
- Utilizar uma Action.
- Utilizar um container.

---

## 2. Verifique a imagem utilizada

Antes de assumir que uma ferramenta está disponível:

```yaml
runs-on: ubuntu-latest
```

consulte a documentação da imagem e, quando necessário, valide durante a execução.

---

## 3. Evite depender cegamente do `latest`

Se a aplicação depende de uma versão específica do sistema operacional, prefira uma tag versionada.

Exemplo:

```yaml
runs-on: ubuntu-22.04
```

---

## 4. Torne o pipeline reproduzível

As dependências necessárias devem estar declaradas no próprio workflow ou em mecanismos de configuração versionados.

Exemplo:

```yaml
- name: Preparar ambiente
  run: |
    sudo apt update
    sudo apt install -y ffmpeg
```

---

## 5. Prefira Actions oficiais quando fizer sentido

Exemplo:

```yaml
- uses: actions/setup-node@v4
  with:
    node-version: 18
```

Isso evita scripts de instalação desnecessários e torna o workflow mais simples.

---

## 6. Valide as versões durante o diagnóstico

Quando houver problemas de ambiente, valide:

```bash
uname -a
```

```bash
node --version
```

```bash
npm --version
```

```bash
python --version
```

```bash
docker --version
```

```bash
kubectl version --client
```

ou qualquer ferramenta necessária para o pipeline.

---

# 🧪 Exemplo de Workflow para Diagnóstico

Um workflow simples para verificar o ambiente:

```yaml
name: Runner Diagnostics

on:
  workflow_dispatch:

jobs:
  diagnostics:
    runs-on: ubuntu-latest

    steps:
      - name: Sistema operacional
        run: |
          uname -a
          cat /etc/os-release

      - name: CPU e memória
        run: |
          nproc
          free -h

      - name: Versão do Bash
        run: |
          bash --version

      - name: Node.js
        run: |
          node --version
          npm --version

      - name: Python
        run: |
          python3 --version

      - name: Docker
        run: |
          docker --version

      - name: Kubectl
        run: |
          kubectl version --client

      - name: Git
        run: |
          git --version
```

Esse tipo de workflow pode ser útil para diagnosticar diferenças entre ambientes.

---

# 🚨 Problemas comuns

## `command not found`

Exemplo:

```text
ffmpeg: command not found
```

### Causa

O software não está instalado no runner.

### Solução

Instalar a dependência:

```yaml
- name: Instalar FFmpeg
  run: |
    sudo apt update
    sudo apt install -y ffmpeg
```

---

## Pipeline funcionando e depois quebrando

Uma possível causa é alteração da imagem ou das versões de ferramentas disponibilizadas pelo runner.

Verifique:

1. `runs-on`.
2. Versão do sistema operacional.
3. Runner image.
4. Versões dos softwares.
5. Dependências do projeto.

---

## Dependência de uma versão específica

Se o pipeline exige uma versão determinada, não dependa apenas de:

```yaml
runs-on: ubuntu-latest
```

Prefira uma versão explicitamente suportada quando a estabilidade exigir isso.

---

# 📚 Checklist de Runner

Antes de colocar um pipeline em produção:

- [ ] Defini se usarei GitHub-hosted ou self-hosted.
- [ ] Verifiquei os requisitos de compliance.
- [ ] Escolhi o sistema operacional.
- [ ] Escolhi a versão do runner.
- [ ] Verifiquei CPU e memória necessários.
- [ ] Verifiquei o storage necessário.
- [ ] Verifiquei os softwares disponíveis.
- [ ] Identifiquei dependências ausentes.
- [ ] Configurei as dependências no workflow.
- [ ] Avaliei o uso de Actions específicas.
- [ ] Avaliei containers quando apropriado.
- [ ] Validei as versões durante a execução.
- [ ] Evitei dependências desnecessárias de `latest`.
- [ ] Testei o workflow em uma execução real.

---

# 📌 Resumo

O **GitHub Actions Runner** é o agente responsável pela execução dos jobs de um workflow.

A principal decisão é escolher entre:

```text
GitHub-hosted
     │
     └── GitHub administra o ambiente

Self-hosted
     │
     └── Organização administra o ambiente
```

Para a maioria dos pipelines convencionais, o GitHub-hosted runner oferece simplicidade e reduz a carga operacional.

Quando existem requisitos de **compliance**, rede interna, hardware específico ou necessidade de controle avançado, o **self-hosted runner** passa a ser uma alternativa adequada.

Também é importante entender que o runner é um ambiente de execução que precisa ser preparado para o job. Quando uma ferramenta não está disponível, ela pode ser instalada durante o workflow, configurada por uma Action ou disponibilizada através de um container.

---

## 🔗 Referências

A aula recomenda consultar a documentação do GitHub para verificar os runners, imagens, recursos e softwares disponíveis, pois esses ambientes são atualizados dinamicamente.

> **Nota:** valores exatos de CPU, memória, armazenamento, versões de sistemas operacionais, limites de minutos e softwares pré-instalados podem mudar com o tempo. Para implementação em produção, consulte sempre a documentação atual do GitHub Actions.

---

## 🎯 Conceito-chave

> **Runner é o ambiente de execução do seu Job.**

Antes de criar um self-hosted runner, verifique se o GitHub-hosted runner consegue atender ao requisito através de:

1. Configuração do `runs-on`;
2. Instalação de pacotes;
3. GitHub Actions;
4. Containers;
5. Ou, somente quando necessário, um self-hosted runner.
