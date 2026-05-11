---
name: sei
description: "Skill para interagir com o sistema SEI (Sistema Eletrônico de Informações), usado por diversas instituições públicas brasileiras. Use sempre que o usuário mencionar: abrir ou pesquisar processo SEI, abrir documento SEI, filtrar processos atribuídos, trocar unidade, gerar ZIP de processo, resumir processo SEI, ou qualquer navegação/ação típica dentro do sistema SEI."
---

## Objetivo
Automatizar o acesso ao sistema SEI e interagir com processos e documentos de forma estruturada, funcionando para qualquer instituição que utilize o SEI.

## Sobre o SEI
O SEI (Sistema Eletrônico de Informações) é uma plataforma de gestão de processos e documentos eletrônicos utilizada por centenas de instituições públicas brasileiras. Cada instituição possui sua própria instância do sistema, com URL própria.

**Importante:** A interface do SEI pode apresentar variações entre diferentes versões do sistema. Embora a estrutura básica e os principais elementos sejam consistentes, a localização exata de alguns botões e ícones pode variar ligeiramente.

## Pré-requisitos
- URL do SEI da instituição (solicitada ao usuário se não detectada automaticamente)
- Credenciais de acesso válidas (solicitadas ao usuário se necessário)

---

## Passo Inicial: Detectar Sessão e Obter Acesso ao SEI

Antes de qualquer ação, o agente deve verificar se o usuário já está com o SEI aberto e autenticado no navegador.

### Verificação de sessão ativa

**Ação:** Tirar um screenshot do navegador e verificar a URL e o conteúdo da página.

```
SE a URL contém o domínio do SEI (ex: "/sei/controlador.php") E o conteúdo mostra o menu lateral do SEI
  ENTÃO o usuário já está autenticado → prosseguir para o fluxo solicitado ✓
SENÃO SE a página exibe formulário de login do SEI
  ENTÃO solicitar credenciais ao usuário → executar Fluxo de Login
SENÃO (SEI não está aberto)
  ENTÃO perguntar ao usuário: "Qual é a URL do SEI da sua instituição?"
        Navegar para a URL informada
        SE página de login aparecer → executar Fluxo de Login
        SE já estiver autenticado → prosseguir para o fluxo solicitado ✓
```

**Notas Importantes:**
- Nunca assuma uma URL específica — cada instituição tem a sua própria
- Sempre verificar o estado atual do navegador antes de tentar navegar
- Se o usuário já estiver na página correta do SEI, não é necessário navegar novamente

---

## Fluxo: Login no SEI

Use este fluxo para autenticar o usuário no sistema SEI. Este fluxo é autônomo e pode ser chamado por outros fluxos que necessitem de autenticação.

### **Passo 1: Navegar para a URL do SEI**
**Objetivo:** Acessar a página de login da instituição

**Ação:**
```
SE o usuário já informou a URL → navegar para ela
SENÃO → perguntar: "Qual é a URL do SEI da sua instituição? (ex: https://sei.minhainstituicao.gov.br)"
         Aguardar resposta e navegar para a URL informada
```

**Verificação:**
- A página deve exibir o formulário de login com campos "Usuário" e "Senha"
- Deve estar presente o botão "ACESSAR" (ou equivalente)

**Elemento de Referência:**
- Formulário de login com título "Sistema Eletrônico de Informações"

---

### **Passo 2: Solicitar e inserir credenciais**
**Objetivo:** Autenticar o usuário no sistema SEI

**Ação:**
```
1. Informar o usuário: "Por favor, informe seu usuário e senha do SEI."
2. Aguardar o usuário fornecer as credenciais no chat
3. Inserir o usuário no campo "Usuário"
4. Inserir a senha no campo "Senha"
5. Clicar no botão "ACESSAR"
```

**Verificação:**
- O sistema deve redirecionar para a página inicial após autenticação bem-sucedida
- Um pop-up de notificação pode aparecer

**Notas Importantes:**
- Sempre solicitar as credenciais ao usuário — nunca as armazene ou assuma valores anteriores
- Nunca exiba ou registre a senha em logs ou respostas
- Após o login, a URL base da instituição fica conhecida para uso nos passos seguintes

---

### **Passo 3: Fechar pop-up de notificações**
**Objetivo:** Remover o pop-up de notificações/informações que aparece após login

**Ação:**
```
Clicar no botão [X] no canto superior direito do pop-up
```

**Elemento a localizar:**
- **Descrição:** Botão de fechamento do pop-up
- **Localização:** Canto superior direito do modal/janela
- **Dica:** Procurar por um ícone de fechar (X) ou usar a função find com query: "button to close popup"

**Verificação:**
- O pop-up deve desaparecer
- A página "Controle de Processos" deve ficar visível

**Notas Importantes:**
- Este passo é **CRÍTICO** para garantir que operações subsequentes funcionem corretamente
- Se o pop-up não fechar, a próxima ação pode não funcionar corretamente
- Após este passo, o usuário está autenticado e na página "Controle de Processos"
- Este é o **último passo do Fluxo de Login**

**Resultado Final do Fluxo de Login:**
✅ Usuário autenticado no sistema SEI  
✅ Pop-up de notificações fechado  
✅ Página "Controle de Processos" visível e pronta para uso

---

## Fluxo: Trocar Unidade do Usuário

Use este fluxo quando o usuário quiser trocar a unidade ativa no SEI. A unidade determina quais processos são exibidos no "Controle de Processos" e em qual contexto as ações são realizadas.

---

### **Passo 1: Verificar acesso ao SEI**
**Ação:**
```
Executar o "Passo Inicial: Detectar Sessão e Obter Acesso ao SEI"
```
Se o usuário já estiver autenticado no SEI, prosseguir diretamente para o Passo 2. Caso contrário, executar o Fluxo de Login primeiro.

---

### **Passo 2: Abrir a página de seleção de unidade**
**Objetivo:** Acessar a lista de unidades disponíveis para o usuário

**Ação:**
```
Localizar e clicar no campo de unidade atual exibido no header, à direita da barra de pesquisa
```

**Elemento a localizar:**
- **Tipo:** Campo/link clicável no header
- **Conteúdo:** Sigla da unidade atual do usuário (ex: `SELOG/SR/PF/RR`)
- **Localização:** Header superior, imediatamente à direita da barra de pesquisa ("Pesquisar...")
- **Query para find:** `"unidade atual do usuário no header"`

**Verificação:**
- A página deve navegar para a tela **"Trocar Unidade [SIGLA_ATUAL]"**
- Deve exibir uma tabela com colunas: **Sigla**, **Descrição**, **Órgão**
- O cabeçalho deve indicar o total de registros: `"Lista de Unidades com Permissão (N registros)"`
- A unidade atual deve aparecer com o radio button selecionado (destacada em azul)

---

### **Passo 3: Identificar a unidade desejada**
**Objetivo:** Localizar a unidade-alvo na lista de unidades disponíveis

**Ação:**
```
1. Verificar se a unidade desejada está visível na lista
2. SE não estiver visível → usar os campos de filtro "Sigla" ou "Descrição" e clicar em "Pesquisar"
3. Localizar a linha correspondente à unidade desejada
```

**Elementos de filtro (opcionais):**
- **Campo "Sigla":** Filtrar por sigla parcial ou completa (ex: `SETEC`)
- **Campo "Descrição":** Filtrar por nome da unidade
- **Botão:** "Pesquisar" — canto superior direito da página

**Verificação:**
```
SE a unidade desejada aparece na lista
  ENTÃO prosseguir para o Passo 4 ✓
SENÃO
  ENTÃO informar ao usuário que a unidade não está disponível para sua conta ✗
```

---

### **Passo 4: Selecionar a unidade desejada**
**Objetivo:** Trocar a unidade ativa clicando no radio button da unidade desejada

**Ação:**
```
Clicar no radio button à esquerda da linha da unidade desejada
```

**Elemento a localizar:**
- **Tipo:** Radio button
- **Localização:** Coluna mais à esquerda da tabela, na linha da unidade desejada
- **Query para find:** `"radio button [SIGLA_DA_UNIDADE]"`

**Verificação:**
- O sistema deve redirecionar automaticamente para a página **"Controle de Processos"** da nova unidade
- A troca ocorre imediatamente ao clicar — não há botão de confirmação separado

---

### **Passo 5: Confirmar a troca de unidade**
**Objetivo:** Verificar que a unidade ativa foi alterada corretamente

**Ação:**
```
Verificar o campo de unidade no header (à direita da barra de pesquisa)
```

**Verificação:**
```
SE o campo de unidade no header exibe a sigla da unidade recém-selecionada
  ENTÃO troca realizada com sucesso ✓
SENÃO
  ENTÃO a troca não foi efetivada — repetir o Passo 4 ✗
```

**Notas Importantes:**
- A lista de processos exibida no "Controle de Processos" reflete a nova unidade
- Filtros ativos anteriormente (ex: "Atribuídos a mim") são resetados após a troca
- Este é o **último passo deste fluxo**

**Resultado Final do Fluxo:**
✅ Unidade trocada para a unidade desejada  
✅ Campo de unidade no header exibe a nova sigla  
✅ Página "Controle de Processos" exibindo processos da nova unidade

---

## Comportamento da Barra de Pesquisa Rápida

A barra de pesquisa rápida no header (ao lado do botão "Menu") funciona para localizar tanto **processos** (pelo número no formato `XXXXX.XXXXXX/AAAA-DD`) quanto **documentos** (pelo número SEI).

**Resultado quando encontrado:**
- Processo: abre diretamente a página do processo com a árvore de documentos no painel esquerdo
- Documento: abre o processo do documento com o documento específico já selecionado e visível no painel direito

**Resultado quando NÃO encontrado:**
- O SEI redireciona automaticamente para a **página de Pesquisa avançada** (`acao=protocolo_pesquisa_rapida`), com o texto digitado já preenchido no campo "Texto para Pesquisa"
- Isso vale tanto para processos quanto para documentos inexistentes
- Nesse caso, informe o usuário que o processo/documento não foi encontrado e que a página de pesquisa foi aberta

---

## Fluxo: Pesquisar e Abrir Processo pelo Número

Use este fluxo quando o usuário fornecer um número de processo específico (ex: "abra o processo 08211.000635/2025-08").

### **Passo 1: Verificar acesso ao SEI**
**Ação:**
```
Executar o "Passo Inicial: Detectar Sessão e Obter Acesso ao SEI"
```
Se o usuário já estiver autenticado no SEI, prosseguir diretamente para o Passo 2. Caso contrário, executar o Fluxo de Login primeiro.

---

### **Passo 2: Usar a barra de pesquisa rápida no header**
**Objetivo:** Localizar o processo pelo número usando o campo de busca ao lado do botão "Menu"

**Ação:**
```
1. Localizar o campo de pesquisa no topo da página, à direita do botão "Menu"
2. Clicar no campo (placeholder "Pesquisar...")
3. Digitar o número do processo exatamente como fornecido (ex: 08211.000635/2025-08)
4. Pressionar Enter
```

**Elemento a localizar:**
- **Tipo:** Campo de texto (textbox)
- **Placeholder:** "Pesquisar..."
- **Localização:** Header superior, à direita do botão "Menu"
- **Query para find:** `"barra de pesquisa ao lado de Menu"`

**Verificação — Processo encontrado:**
- O sistema abre diretamente a página do processo
- O número do processo aparece destacado no painel esquerdo
- A estrutura de volumes (I, II, III, IV...) e documentos fica visível no painel esquerdo
- Se o processo estiver em outra unidade, uma mensagem indica isso (ex: "Processo aberto somente na unidade CGOF/DLOG/PF (atribuído para fulano)")

**Verificação — Processo NÃO encontrado:**
- O SEI redireciona para a página de **Pesquisa avançada** com o número preenchido no campo "Texto para Pesquisa"
- Informar o usuário que o processo não foi localizado

**Notas Importantes:**
- Esta pesquisa rápida funciona para qualquer processo SEI, mesmo os atribuídos a outras unidades
- O número deve ser digitado no formato exato com pontos e barra (ex: 08211.000635/2025-08)
- Processos de outras unidades ficam visíveis em modo leitura
- A URL resultante tem o formato: `https://<dominio-sei>/sei/controlador.php?acao=procedimento_trabalhar&...`

---

### **Passo 3: Expandir todos os volumes da árvore de documentos**
**Objetivo:** Abrir todos os volumes do processo para facilitar a navegação pelos documentos

**Ação:**
Após o processo ser aberto, localizar e clicar no ícone "+" (expandir tudo) que fica à direita do número do processo na raiz da árvore de documentos (painel esquerdo).

**Elemento a localizar:**
- **Tipo:** Ícone/botão de expansão
- **Visual:** Ícone "+" (sinal de mais) — aparece logo à direita do número do processo no topo da árvore
- **Localização:** Painel esquerdo, linha do número do processo (raiz da árvore), após os ícones de cadeado/marcador
- **Query para find:** `"expandir árvore de documentos"` ou `"botão + expandir processo"`

**Verificação:**
- Todos os volumes (I, II, III, etc.) devem aparecer expandidos, mostrando todos os documentos
- Os documentos de cada volume ficam visíveis diretamente na árvore, sem necessidade de clicar em cada pasta

**Notas Importantes:**
- Este passo é **OBRIGATÓRIO** sempre que um processo for aberto — facilita a navegação e evita cliques extras nas pastas
- Se o ícone "+" não estiver visível, tente rolar o painel esquerdo para cima até encontrá-lo na linha do número do processo
- Após expandir, a árvore mostrará todos os documentos em ordem, prontos para navegação

---

## Fluxo: Pesquisar e Abrir Documento pelo Número SEI

Use este fluxo quando o usuário fornecer um número de documento SEI específico (ex: "abra o documento 145869893").

### **Passo 1: Verificar acesso ao SEI**
**Ação:**
```
Executar o "Passo Inicial: Detectar Sessão e Obter Acesso ao SEI"
```
Se o usuário já estiver autenticado no SEI, prosseguir diretamente para o Passo 2. Caso contrário, executar o Fluxo de Login primeiro.

---

### **Passo 2: Usar a barra de pesquisa rápida no header**
**Objetivo:** Localizar o documento pelo número SEI usando o campo de busca ao lado do botão "Menu"

**Ação:**
```
1. Localizar o campo de pesquisa no topo da página, à direita do botão "Menu"
2. Clicar no campo (placeholder "Pesquisar...")
3. Digitar o número do documento (apenas os dígitos, ex: 145869893)
4. Pressionar Enter
```

**Elemento a localizar:**
- **Tipo:** Campo de texto (textbox)
- **Placeholder:** "Pesquisar..."
- **Localização:** Header superior, à direita do botão "Menu"
- **Query para find:** `"barra de pesquisa ao lado de Menu"`

**Verificação — Documento encontrado:**
- O sistema abre o processo ao qual o documento pertence
- O documento específico aparece selecionado (destacado em azul) no painel esquerdo
- O conteúdo do documento é exibido diretamente no painel direito
- A URL contém `acao=procedimento_trabalhar` com o `id_protocolo` do processo

**Verificação — Documento NÃO encontrado:**
- O SEI redireciona para a página de **Pesquisa avançada** com o número preenchido no campo "Texto para Pesquisa"
- A URL conterá `acao=protocolo_pesquisa_rapida`
- Informar o usuário que o documento não foi localizado

**Notas Importantes:**
- O número do documento é puramente numérico (sem pontos ou barras)
- Documentos de processos de outras unidades também podem ser localizados
- Após abrir o documento, é possível lê-lo diretamente no painel direito ou fazer scroll para ver o conteúdo completo

---

## Fluxo: Filtrar Processos Atribuídos ao Usuário

Use este fluxo para exibir apenas os processos atribuídos ao usuário logado na página "Controle de Processos". Este fluxo é independente e pode ser combinado com outros fluxos. Requer que o usuário já esteja autenticado.

### **Passo 1: Verificar acesso ao SEI**
**Ação:**
```
Executar o "Passo Inicial: Detectar Sessão e Obter Acesso ao SEI"
```
Se o usuário já estiver autenticado no SEI, prosseguir diretamente para o Passo 2. Caso contrário, executar o Fluxo de Login primeiro.

---

### **Passo 2: Filtrar processos atribuídos ao usuário**
**Objetivo:** Aplicar filtro para exibir apenas processos atribuídos ao usuário logado

**Ação:**
```
Localizar e clicar no link "Ver atribuídos a mim"
```

**Elemento a localizar:**
- **Tipo:** Link/botão de filtro
- **Texto:** "Ver atribuídos a mim"
- **Localização:** Barra de filtros na página "Controle de Processos"
- **Coordenadas aproximadas:** Próximo aos outros filtros ("Ver por marcadores", "Ver por tipo", "Ver por prioridade")

**Query para localizar:**
```
"Ver atribuídos a mim" link button filter
```

**Verificação:**
- A página deve recarregar e mostrar apenas processos do usuário
- O elemento "Remover filtro de processos atribuídos a mim" deve aparecer (próximo passo)

---

### **Passo 3: Verificar se o filtro foi aplicado**
**Objetivo:** Confirmar que o filtro "Atribuídos a mim" está ativo

**Ação:**
```
Verificar a presença do elemento "Remover filtro de processos atribuídos a mim"
```

**Elemento a verificar:**
- **Tipo:** Link/chip de filtro
- **Texto:** "Remover filtro de processos atribuídos a mim"
- **Descrição:** Este elemento substitui o link "Ver atribuídos a mim" quando o filtro está ativo
- **Localização:** Barra de filtros

**Verificação de Confirmação:**
```
SE elemento "Remover filtro de processos atribuídos a mim" está presente
  ENTÃO filtro está ATIVO ✓
SENÃO
  ENTÃO filtro não foi aplicado corretamente ✗
```

**Notas Importantes:**
- Este é um **checkpoint crítico** para validar o sucesso do filtro
- Sem este elemento, o filtro não está realmente ativo
- Todos os processos listados devem ter o atributo "Atribuído para [nome_do_usuário]"
- Este é o **último passo deste fluxo**

**Resultado Final do Fluxo:**
✅ Filtro "Atribuídos a mim" aplicado e ativo  
✅ Página "Controle de Processos" exibindo apenas processos atribuídos ao usuário  
✅ Elemento de confirmação "Remover filtro de processos atribuídos a mim" visível

---

## Fluxo: Gerar Arquivo ZIP de Processo

Use este fluxo para gerar e baixar um arquivo ZIP contendo todos os documentos de um processo SEI. Este fluxo funciona em **qualquer processo já aberto**, independentemente de como você chegou até ele.

### **Passo 1: Verificar que está em um processo aberto**
**Objetivo:** Confirmar que o usuário está visualizando um processo específico

**Ação:**
```
Verificar se a URL contém "acao=procedimento_trabalhar" 
E se o painel esquerdo mostra a árvore de documentos do processo
```

**Verificação:**
- A URL deve conter: `acao=procedimento_trabalhar`
- O painel esquerdo deve exibir a estrutura de volumes e documentos
- O número do processo deve estar visível no header ou título
- A barra de ferramentas com ícones de ação deve estar presente

**Se NÃO estiver em um processo aberto:**
- Execute o Fluxo "Pesquisar e Abrir Processo pelo Número" para abrir um processo específico, OU
- Execute o Fluxo "Filtrar Processos Atribuídos ao Usuário" e então clique em um processo da lista, OU
- Navegue manualmente até um processo na página "Controle de Processos"

**Notas Importantes:**
- Este fluxo funciona em QUALQUER processo aberto, não importa como você chegou até ele
- O processo pode estar atribuído a você ou a outra unidade (modo leitura)
- Anote o número do processo para referência futura

---

### **Passo 2: Gerar arquivo ZIP do processo**
**Objetivo:** Abrir o diálogo para gerar arquivo ZIP com os documentos do processo

**Ação:**
```
Localizar e clicar no ícone de ZIP na barra de ferramentas
```

**Elemento a localizar:**
- **Tipo:** Ícone/botão
- **Visual:** Ícone verde e branco com "ZIP"
- **Hint/Title:** "Gerar Arquivo ZIP do Processo"
- **Localização:** Barra de ferramentas (toolbar) na página do processo
- **Coordenadas aproximadas:** Lado direito da barra de ferramentas, próximo aos outros ícones de ação

**Query para localizar:**
```
"Gerar Arquivo ZIP do Processo" icon button
```

**Verificação:**
- Um modal/diálogo deve aparecer com o título "Gerar Arquivo ZIP do Processo"
- Deve exibir três opções de radio buttons:
  - "Todos os documentos disponíveis"
  - "Todos exceto selecionados"
  - "Apenas selecionados"
- Deve haver botões "Gerar" e "Fechar"

---

### **Passo 3: Configurar opções de ZIP**
**Objetivo:** Garantir que a opção correta está selecionada antes de gerar o arquivo

**Ação:**
```
1. Verificar se a opção "Todos os documentos disponíveis" está selecionada
2. SE não estiver selecionada, clicar no radio button para selecioná-la
```

**Elemento a verificar:**
- **Tipo:** Radio button
- **Label:** "Todos os documentos disponíveis"
- **Status esperado:** Selecionado (preenchido)

**Verificação:**
```
SE radio button "Todos os documentos disponíveis" está marcado
  ENTÃO continuar para próximo passo ✓
SENÃO
  ENTÃO clicar no radio button para selecionar
  E verificar novamente
```

**Notas Importantes:**
- Esta opção deve estar selecionada por padrão
- Usar esta opção garante que TODOS os documentos do processo serão incluídos no ZIP
- Não usar "Apenas selecionados" a menos que especificamente indicado

---

### **Passo 4: Baixar o arquivo ZIP**
**Objetivo:** Executar a geração e download do arquivo ZIP com todos os documentos

**Ação:**
```
Clicar no botão "Gerar"
```

**Elemento a localizar:**
- **Tipo:** Botão
- **Texto:** "Gerar"
- **Localização:** Canto superior direito do diálogo modal
- **Cor:** Azul (padrão do sistema)

**Verificação:**
- O download deve iniciar automaticamente
- O arquivo deve ser nomeado como: **SEI_[número_do_processo].zip**
  - Exemplo: `SEI_08485.001877_2026_71.zip`
- O arquivo deve estar salvo na pasta de downloads padrão do navegador
- O modal/diálogo deve fechar após o download

**Notas Importantes:**
- O download é automático e não requer confirmação adicional
- O nome do arquivo segue o padrão SEI com o número do processo
- Não feche a janela/tab antes do download completar
- O arquivo ZIP conterá todos os documentos do processo em uma estrutura de pastas

---

### **Passo 5: Retornar à página de Controle de Processos (Opcional)**
**Objetivo:** Navegar de volta para a página de listagem de processos

**Ação:**
```
Clicar no ícone "Controle de Processos" no header/navegação superior
```

**Elemento a localizar:**
- **Tipo:** Ícone/link no header
- **Visual:** Ícone com múltiplos quadrados/linhas (representando uma lista/tabela)
- **Texto alternativo:** "Controle de Processos"
- **Localização:** Barra de navegação superior (header)
- **Posição:** Lado esquerdo da barra de navegação, próximo ao logo SEI

**Query para localizar:**
```
"Controle de Processos" icon header navigation
```

**Verificação:**
- A página deve navegar para a página "Controle de Processos"
- O título "Controle de Processos" deve aparecer na página
- A lista de processos deve ser exibida

**Notas Importantes:**
- Este passo é **OPCIONAL** - execute apenas se precisar retornar à listagem de processos
- Útil quando você está processando múltiplos processos em sequência
- Preserva o filtro "Atribuídos a mim" se ele estava ativo
- Pode levar alguns segundos para carregar
- Se você não precisa retornar, pode permanecer no processo atual
- Este é o **último passo deste fluxo**

**Resultado Final do Fluxo:**
✅ Arquivo ZIP gerado com todos os documentos do processo  
✅ Nome do arquivo: `SEI_[número_do_processo].zip`  
✅ Arquivo salvo na pasta de downloads  
✅ (Opcional) Retorno à página de Controle de Processos

---

## Fluxo: Resumir Processo

Use este fluxo quando o usuário solicitar um resumo de um processo SEI aberto. O agente deve adaptar a estratégia de leitura conforme o tamanho do processo, priorizando sempre os documentos mais relevantes.

---

### **Passo 1: Verificar que está em um processo aberto**
**Objetivo:** Confirmar que o usuário está visualizando um processo específico antes de iniciar a leitura

**Ação:**
```
Verificar se a URL contém "acao=procedimento_trabalhar"
E se o painel esquerdo mostra a árvore de documentos do processo
```

**Verificação:**
- A URL deve conter: `acao=procedimento_trabalhar`
- O painel esquerdo deve exibir a estrutura de volumes e documentos
- O número do processo deve estar visível no header ou título

**Se NÃO estiver em um processo aberto:**
- Execute o Fluxo "Pesquisar e Abrir Processo pelo Número", OU
- Execute o Fluxo "Filtrar Processos Atribuídos ao Usuário" e clique em um processo da lista

---

### **Passo 2: Contar os volumes do processo**
**Objetivo:** Determinar a estratégia de leitura com base no tamanho do processo

**Ação:**
```
1. Observar a árvore de documentos no painel esquerdo
2. Contar o número de volumes listados (Volume I, Volume II, Volume III, etc.)
3. Registrar o número total de volumes
```

**Elemento a localizar:**
- **Tipo:** Itens da árvore de documentos no painel esquerdo
- **Visual:** Ícones de pasta com rótulos "I", "II", "III", etc. (volumes em algarismos romanos)
- **Query para find:** `"volumes lista árvore documentos processo"`

**Decisão:**
```
SE número de volumes ≤ 5
  ENTÃO executar Estratégia A: Download ZIP e leitura completa
SENÃO (número de volumes > 5)
  ENTÃO executar Estratégia B: Leitura seletiva dos documentos iniciais e finais
```

---

### **Estratégia A: Processo com até 5 volumes — Leitura completa via ZIP**

Use quando o processo tiver **5 volumes ou menos** (até aproximadamente 100 documentos no total).

#### **Passo A1: Baixar o arquivo ZIP do processo**
**Objetivo:** Obter todos os documentos do processo de uma só vez

**Ação:**
```
Executar o Fluxo "Gerar Arquivo ZIP de Processo" na íntegra
```

- Localize o ícone ZIP na barra de ferramentas (ícone verde com "ZIP")
- Selecione "Todos os documentos disponíveis"
- Clique em "Gerar" e aguarde o download

**Verificação:**
- Arquivo `SEI_[número_do_processo].zip` salvo na pasta de downloads
- O nome do arquivo confirma o processo correto

---

#### **Passo A2: Ler e analisar todos os documentos**
**Objetivo:** Extrair as informações relevantes de todos os documentos do processo

**Ação:**
```
1. Acessar o arquivo ZIP baixado
2. Extrair e ler todos os documentos disponíveis
3. Identificar em cada documento:
   - Tipo e data do documento
   - Unidade de origem
   - Principais informações, decisões ou solicitações
   - Qualquer referência ao motivo do encaminhamento ao setor do usuário
```

**Notas Importantes:**
- Documentos em PDF podem exigir OCR se forem imagens escaneadas
- Priorize documentos do tipo: Despacho, Informação, Ofício, Decisão, Memorando
- Anote a sequência cronológica dos documentos para entender o fluxo do processo

---

#### **Passo A3: Produzir o resumo completo**
**Objetivo:** Consolidar as informações em um resumo claro e objetivo

**Ação:**
```
Produzir um resumo estruturado com os seguintes elementos:
1. Identificação do processo (número, tipo, assunto)
2. Origem e motivação (como e por que o processo foi aberto)
3. Trâmite (principais etapas, decisões e encaminhamentos)
4. Situação atual (último despacho, unidades com o processo aberto)
5. Motivo do encaminhamento ao setor do usuário (insight principal)
```

**Resultado Final da Estratégia A:**
✅ Todos os documentos lidos  
✅ Resumo completo produzido  
✅ Motivo do encaminhamento ao setor identificado  

---

### **Estratégia B: Processo com mais de 5 volumes — Leitura seletiva**

Use quando o processo tiver **mais de 5 volumes**. Neste caso, leia os **5 primeiros** e os **5 últimos** documentos da árvore, que concentram as informações mais relevantes: a origem do processo e seu estado atual.

#### **Passo B1: Identificar e ler os 5 primeiros documentos**
**Objetivo:** Compreender a origem e motivação inicial do processo

**Ação:**
```
1. No painel esquerdo, expandir o primeiro volume (Volume I)
2. Clicar no primeiro documento da lista
3. Ler o conteúdo exibido no painel direito
4. Repetir para os próximos 4 documentos em ordem sequencial
5. Se o Volume I tiver menos de 5 documentos, continuar no Volume II
```

**Elemento a localizar:**
- **Tipo:** Links de documentos no painel esquerdo
- **Localização:** Primeiro volume expandido, documentos listados de cima para baixo
- **Query para find:** `"primeiro documento lista árvore volume"`

**O que extrair:**
- Tipo e assunto do processo
- Unidade e servidor que abriu o processo
- Motivação original (pedido, solicitação, determinação, etc.)
- Data de abertura

---

#### **Passo B2: Identificar e ler os 5 últimos documentos**
**Objetivo:** Compreender o estado atual e o motivo do encaminhamento ao setor do usuário

**Ação:**
```
1. No painel esquerdo, expandir o último volume (de maior numeração)
2. Rolar até o final da lista de documentos desse volume
3. Clicar no último documento da lista
4. Ler o conteúdo exibido no painel direito
5. Repetir para os 4 documentos anteriores em ordem reversa
6. Se o último volume tiver menos de 5 documentos, retroceder ao volume anterior
```

**O que extrair:**
- Último despacho ou encaminhamento
- Motivo pelo qual o processo foi enviado ao setor do usuário
- Solicitação ou pendência específica dirigida ao setor
- Prazo ou urgência, se mencionado

**Notas Importantes:**
- Os documentos finais são os mais críticos: eles explicam **por que o processo está com o usuário**
- Preste atenção especial a despachos com verbos como "encaminhar", "solicitar providências", "para manifestação", "para ciência", "para adoção de medidas"
- Identifique se há uma ação específica esperada do setor do usuário

---

#### **Passo B3: Produzir o resumo seletivo**
**Objetivo:** Consolidar as informações lidas em um resumo claro, com aviso de limitação

**Ação:**
```
Produzir um resumo estruturado com os seguintes elementos:
1. Aviso de limitação: informar que o resumo é baseado nos 5 primeiros e 5 últimos documentos
2. Identificação do processo (número, tipo, assunto)
3. Origem e motivação (com base nos primeiros documentos)
4. Situação atual (com base nos últimos documentos)
5. Motivo do encaminhamento ao setor do usuário (insight principal)
6. Ação esperada do setor, se identificada
```

**Texto de aviso a incluir no resumo:**
> ⚠️ *Este resumo foi elaborado com base nos 5 primeiros e nos 5 últimos documentos do processo, pois o processo possui mais de 5 volumes. Informações intermediárias podem não estar refletidas.*

**Resultado Final da Estratégia B:**
✅ Primeiros 5 documentos lidos (origem do processo)  
✅ Últimos 5 documentos lidos (situação atual e motivo do encaminhamento)  
✅ Resumo seletivo produzido com aviso de limitação  
✅ Motivo do encaminhamento ao setor identificado  

---

### **Estrutura recomendada do resumo (ambas as estratégias)**

```
**Processo:** [número]
**Assunto:** [assunto/tipo do processo]
**Aberto por:** [unidade/servidor de origem] em [data]

**Origem:** [breve descrição de como e por que o processo foi aberto]

**Trâmite:** [principais etapas e encaminhamentos, em ordem cronológica]

**Situação atual:** [último despacho, unidades com o processo aberto]

**Por que está com o seu setor:** [insight principal — motivo do encaminhamento e ação esperada]
```

---

**Resultado Final do Fluxo:**
✅ Estratégia de leitura definida com base no número de volumes  
✅ Documentos relevantes lidos e analisados  
✅ Resumo estruturado produzido  
✅ Insight sobre o motivo do encaminhamento ao setor identificado e destacado

---

## Tratamento de Erros

### Erro: Pop-up não fecha no Passo 3
**Solução:**
- Tentar clicar novamente no botão X
- Se persistir, recarregar a página com F5
- Verificar se há múltiplos pop-ups abertos

### Erro: Filtro não é aplicado no Passo 2 (Fluxo "Filtrar Processos Atribuídos ao Usuário")
**Solução:**
- Verificar se o elemento "Ver atribuídos a mim" foi encontrado
- Clicar novamente no elemento
- Aguardar o carregamento da página (pode levar alguns segundos)
- Verificar a presença do elemento "Remover filtro de processos atribuídos a mim"

### Erro: Ícone ZIP não encontrado no Passo 2 (Fluxo "Gerar Arquivo ZIP de Processo")
**Solução:**
- Verificar se o processo foi aberto corretamente
- Procurar na barra de ferramentas por um ícone verde com "ZIP"
- Se não encontrado, o sistema pode ter exigido uma ação anterior (como ler um despacho)

### Erro: Download não inicia no Passo 4 (Fluxo "Gerar Arquivo ZIP de Processo")
**Solução:**
- Verificar as configurações de bloqueador de pop-ups do navegador
- Permitir downloads automáticos para o site SEI
- Aguardar alguns segundos (o processo pode estar processando)
- Tentar clicar no botão "Gerar" novamente

### Erro: Filtro removido após retornar no Passo 5 (quando processando múltiplos processos)
**Solução:**
- Aplicar o filtro novamente (executar Fluxo "Filtrar Processos Atribuídos ao Usuário")
- Verificar se a navegação foi para a página correta
- Se o problema persistir, usar o link "Controle de Processos" no menu lateral

### Erro: Não está em um processo aberto no Passo 1 (Fluxo "Gerar Arquivo ZIP de Processo")
**Solução:**
- Execute o Fluxo "Pesquisar e Abrir Processo pelo Número" para abrir um processo específico, OU
- Execute o Fluxo "Filtrar Processos Atribuídos ao Usuário" e clique em um processo da lista, OU
- Navegue até "Controle de Processos" e clique em qualquer processo

### Erro: Unidade não encontrada no Passo 3 (Fluxo "Trocar Unidade do Usuário")
**Solução:**
- Verificar a ortografia da sigla ou nome da unidade
- Usar os campos de filtro "Sigla" ou "Descrição" para buscar
- Confirmar com o usuário se ele tem permissão de acesso à unidade desejada
- Se a unidade não aparecer, o usuário não possui permissão para acessá-la

### Erro: Troca de unidade não efetivada no Passo 5 (Fluxo "Trocar Unidade do Usuário")
**Solução:**
- Verificar se o radio button foi clicado corretamente
- Aguardar alguns segundos para o sistema processar (pode haver lentidão)
- Repetir o Passo 4 clicando novamente no radio button
- Se persistir, recarregar a página e tentar novamente

### Erro: Documentos no ZIP ilegíveis (Fluxo "Resumir Processo" - Estratégia A)
**Solução:**
- Ler os documentos diretamente pela árvore do processo no painel esquerdo
- Clicar em cada documento e usar get_page_text para extrair o conteúdo textual
- Se o PDF for imagem escaneada, pode ser necessário OCR externo

### Erro: Volume não expande na árvore de documentos (Fluxo "Resumir Processo")
**Solução:**
- Clicar diretamente no ícone de pasta do volume para expandi-lo
- Aguardar o carregamento e tentar novamente
- Se persistir, recarregar a página do processo

### Erro: Processo com muitos documentos por volume (Fluxo "Resumir Processo")
**Solução:**
- Manter a estratégia definida pelo número de volumes
- Na Estratégia B, garantir que os 5 primeiros e 5 últimos sejam os documentos nas extremidades absolutas da árvore (não apenas do volume)

---

## Checkpoints Críticos

Os seguintes checkpoints devem ser validados em TODOS os ciclos:

| Checkpoint | Passo | Elemento | Status |
|-----------|-------|---------|--------|
| **Fluxo: Login no SEI** |
| Pop-up fechado | 3 | Ausência do modal | ✓ Obrigatório |
| **Fluxo: Trocar Unidade do Usuário** |
| Página de troca aberta | 2 | Título "Trocar Unidade [SIGLA_ATUAL]" | ✓ Obrigatório |
| Unidade trocada | 5 | Sigla da nova unidade no header | ✓ Obrigatório |
| **Fluxo: Pesquisar e Abrir Processo pelo Número** |
| Árvore expandida | 3 | Todos os volumes visíveis e expandidos | ✓ Obrigatório |
| **Fluxo: Filtrar Processos Atribuídos ao Usuário** |
| Filtro aplicado | 3 | "Remover filtro de processos atribuídos a mim" | ✓ Obrigatório |
| **Fluxo: Gerar Arquivo ZIP de Processo** |
| Processo aberto | 1 | Número do processo no header | ✓ Obrigatório |
| Modal de ZIP aberto | 2 | Diálogo "Gerar Arquivo ZIP do Processo" | ✓ Obrigatório |
| Opção correta selecionada | 3 | "Todos os documentos disponíveis" marcado | ✓ Obrigatório |
| Download iniciado | 4 | Arquivo ZIP no folder downloads | ✓ Obrigatório |
| Retorno à lista (opcional) | 5 | Título "Controle de Processos" | ○ Opcional |
| **Fluxo: Resumir Processo** |
| Processo aberto | 1 | Número do processo no header | ✓ Obrigatório |
| Volumes contados | 2 | Número de volumes registrado | ✓ Obrigatório |
| Estratégia definida | 2 | A ou B conforme número de volumes | ✓ Obrigatório |
| Documentos lidos | A2/B1/B2 | Conteúdo extraído com sucesso | ✓ Obrigatório |
| Resumo produzido | A3/B3 | Resumo estruturado completo | ✓ Obrigatório |

---

## Resultados Esperados

### Após completar o Fluxo "Login no SEI":
✅ Acesso autenticado ao sistema SEI  
✅ Pop-up de notificações fechado  
✅ Página "Controle de Processos" visível e pronta para uso

### Após completar o Fluxo "Trocar Unidade do Usuário":
✅ Unidade trocada para a unidade desejada  
✅ Campo de unidade no header exibe a nova sigla  
✅ Página "Controle de Processos" exibindo processos da nova unidade

### Após completar o Fluxo "Pesquisar e Abrir Processo pelo Número":
✅ Processo localizado e aberto  
✅ Árvore de documentos totalmente expandida  
✅ Volumes e documentos visíveis para navegação

### Após completar o Fluxo "Filtrar Processos Atribuídos ao Usuário":
✅ Filtro "Atribuídos a mim" aplicado e ativo  
✅ Página "Controle de Processos" exibindo apenas processos do usuário  
✅ Elemento de confirmação "Remover filtro de processos atribuídos a mim" visível

### Após completar o Fluxo "Gerar Arquivo ZIP de Processo":
✅ Arquivo ZIP gerado com todos os documentos do processo  
✅ Nome do arquivo: `SEI_[número_do_processo].zip`  
✅ Arquivo salvo na pasta de downloads  
✅ (Opcional) Retorno à página de origem

### Após completar o Fluxo "Resumir Processo":
✅ Estratégia de leitura definida (A ou B conforme número de volumes)  
✅ Documentos relevantes lidos e analisados  
✅ Resumo estruturado produzido  
✅ Insight sobre o motivo do encaminhamento ao setor identificado  
