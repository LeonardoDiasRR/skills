# Skill SEI - Sistema Eletrônico de Informações

Uma skill desenvolvida para auxiliar agentes de IA (como GitHub Copilot, Claude, e similares) a automatizar interações com o **Sistema Eletrônico de Informações (SEI)**, utilizado por diversas instituições públicas brasileiras.

## 📋 Visão Geral

O SEI é uma plataforma de gestão de processos e documentos eletrônicos adotada por centenas de órgãos públicos no Brasil. Esta skill fornece instruções estruturadas para que agentes de IA possam:

- **Autenticar** usuários no sistema
- **Pesquisar e abrir** processos e documentos por número
- **Filtrar** processos atribuídos ao usuário
- **Gerar e baixar** arquivos ZIP contendo todos os documentos de um processo
- **Navegar** entre páginas e validar estados do sistema

## 🎯 Casos de Uso

### Login Automático
Detecta automaticamente se o usuário está autenticado, solicita credenciais quando necessário e executa o login completo, incluindo fechamento de pop-ups de notificação.

### Pesquisa de Processos
Localiza processos pelo número (formato `XXXXX.XXXXXX/AAAA-DD`) usando a barra de pesquisa rápida, funcionando para processos de qualquer unidade.

### Pesquisa de Documentos
Encontra documentos específicos pelo número SEI (ex: `145869893`), abrindo automaticamente o processo correspondente com o documento selecionado.

### Filtro de Processos Atribuídos
Aplica filtros para exibir apenas processos atribuídos ao usuário logado, com validação automática do estado do filtro.

### Geração de ZIP
Gera e baixa arquivos ZIP contendo todos os documentos de um processo, com nomenclatura padronizada `SEI_[número_do_processo].zip`.

## ✨ Principais Características

### 🔍 Detecção Automática de Sessão
- Verifica automaticamente se o usuário já está autenticado
- Identifica a página atual e redireciona para o fluxo apropriado
- Evita navegações desnecessárias

### 🏢 Multi-Instituição
- Funciona com qualquer instituição que utilize o SEI
- Solicita URL da instituição quando necessário
- Exemplos: Polícia Federal, Ministérios, autarquias federais, estaduais e municipais

### ✅ Validação de Estados
- Checkpoints críticos em cada etapa
- Verificação de elementos na interface
- Tratamento de erros com soluções específicas

### 📝 Documentação Detalhada
- Instruções passo a passo para cada fluxo
- Queries de localização de elementos
- Exemplos de URLs e formatos de dados
- Notas sobre variações entre versões do SEI

## 🚀 Fluxos Disponíveis

### 1. Fluxo: Login no SEI
Autentica o usuário no sistema, fecha pop-ups e prepara a interface para uso.

**Passos:**
1. Navegar para a URL do SEI
2. Solicitar e inserir credenciais
3. Fechar pop-up de notificações

### 2. Fluxo: Pesquisar e Abrir Processo pelo Número
Localiza e abre um processo específico usando a barra de pesquisa rápida.

**Passos:**
1. Verificar acesso ao SEI
2. Usar a barra de pesquisa rápida no header

### 3. Fluxo: Pesquisar e Abrir Documento pelo Número SEI
Localiza e abre um documento específico pelo número SEI.

**Passos:**
1. Verificar acesso ao SEI
2. Usar a barra de pesquisa rápida no header

### 4. Fluxo: Filtrar Processos Atribuídos ao Usuário
Aplica filtro para exibir apenas processos atribuídos ao usuário logado.

**Passos:**
1. Verificar acesso ao SEI
2. Aplicar filtro "Ver atribuídos a mim"
3. Verificar se o filtro foi aplicado

### 5. Fluxo: Gerar Arquivo ZIP de Processo
Gera e baixa um arquivo ZIP contendo todos os documentos de um processo aberto.

**Passos:**
1. Verificar que está em um processo aberto
2. Clicar no ícone ZIP
3. Configurar opções (todos os documentos)
4. Baixar o arquivo ZIP
5. (Opcional) Retornar à página de Controle de Processos

## 🔧 Requisitos

- **Navegador web** com suporte a JavaScript
- **Credenciais válidas** do SEI da instituição
- **URL do SEI** da instituição (ex: `https://sei4.pf.gov.br/sei/`)
- **Ferramentas de automação web** (Playwright, Puppeteer, Selenium, etc.)

## 📚 Estrutura da Skill

### Passo Inicial
Todo fluxo começa com a detecção de sessão ativa, verificando se o usuário já está autenticado antes de prosseguir.

### Fluxos Independentes
Cada fluxo é autônomo e pode ser executado separadamente ou combinado com outros fluxos conforme necessário.

### Checkpoints Críticos
Validações em pontos-chave garantem que cada etapa foi concluída com sucesso antes de prosseguir.

### Tratamento de Erros
Soluções específicas para os problemas mais comuns em cada fluxo.

## 🎓 Exemplo de Uso Combinado

Para processar múltiplos processos atribuídos ao usuário:

```
1. Executar Fluxo de Login (se necessário)
2. Executar Fluxo "Filtrar Processos Atribuídos ao Usuário"
3. Para cada processo na lista:
   a. Clicar no processo para abri-lo
   b. Executar Fluxo "Gerar Arquivo ZIP de Processo"
   c. Retornar à lista
   d. Verificar se o filtro continua ativo
4. Repetir até processar todos os processos
```

## ⚠️ Considerações Importantes

### Variações de Interface
A interface do SEI pode apresentar variações entre diferentes versões. A skill fornece orientações gerais, mas pode ser necessário adaptar a localização de elementos específicos.

### Credenciais
- Nunca armazene credenciais em código ou logs
- Sempre solicite credenciais diretamente ao usuário
- Nunca exiba senhas em respostas ou outputs

### Processos de Outras Unidades
- Processos de outras unidades podem ser visualizados em modo leitura
- Algumas ações podem não estar disponíveis para processos não atribuídos

### Performance
- Aguarde o carregamento completo das páginas antes de interagir
- Alguns processos grandes podem demorar para gerar o ZIP
- Filtros e navegação podem levar alguns segundos

## 🐛 Tratamento de Erros Comuns

### Pop-up não fecha
- Tentar clicar novamente
- Recarregar a página (F5)
- Verificar se há múltiplos pop-ups

### Filtro não é aplicado
- Verificar se o elemento foi encontrado
- Aguardar carregamento da página
- Verificar presença do elemento de confirmação

### Ícone ZIP não encontrado
- Verificar se o processo foi aberto corretamente
- Verificar se há ações pendentes (leitura de despacho)

### Download não inicia
- Verificar configurações de pop-ups do navegador
- Permitir downloads automáticos
- Aguardar processamento do arquivo

## 📖 Documentação Completa

Para instruções detalhadas passo a passo, consulte o arquivo [`SKILL.md`](./SKILL.md).

## 🔗 Compatibilidade

Esta skill foi desenvolvida para ser compatível com:
- SEI versão 3.x e 4.x
- Qualquer instituição pública que utilize o SEI
- Agentes de IA com capacidade de automação web

## 📝 Notas da Versão

**Versão:** 1.0.0  
**Data:** Maio 2026

### Funcionalidades Iniciais
- Detecção automática de sessão
- Login completo com tratamento de pop-ups
- Pesquisa de processos e documentos
- Filtro de processos atribuídos
- Geração de arquivos ZIP
- Validação de estados e checkpoints
- Tratamento de erros

---

**Autor:** Leonardo Dias  
**Última Atualização:** Maio 2026
