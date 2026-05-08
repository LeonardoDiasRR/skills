---
name: sei
description: "Skill para interagir com o sistema SEI (Sistema Eletrônico de Informações), usado por diversas instituições públicas brasileiras. Use sempre que o usuário mencionar: abrir ou pesquisar processo SEI, abrir ou pesquisar documento SEI pelo número, filtrar processos atribuídos ao usuário, gerar arquivo ZIP de documentos de processo aberto, ou qualquer tarefa no sistema SEI — independentemente da instituição (Polícia Federal, Ministérios, autarquias, etc.). Inclui: detecção automática de sessão ativa, login assistido, pesquisa rápida por número de processo ou documento, filtro de processos atribuídos, navegação no sistema, extração de documentos ZIP e retorno à listagem."
---

## Objetivo
Automatizar o acesso ao sistema SEI e interagir com processos e documentos de forma estruturada, funcionando para qualquer instituição que utilize o SEI.

## Sobre o SEI
O SEI (Sistema Eletrônico de Informações) é uma plataforma de gestão de processos e documentos eletrônicos utilizada por centenas de instituições públicas brasileiras. Cada instituição tem sua própria URL (ex: `https://sei4.pf.gov.br/sei/` para a Polícia Federal, `https://sei.fazenda.gov.br/sei/` para a Receita Federal, etc.).

**Importante:** A interface do SEI pode apresentar variações entre diferentes versões do sistema. Embora a estrutura básica e os principais elementos sejam consistentes, a localização exata de botões, ícones e menus pode variar ligeiramente dependendo da versão utilizada pela instituição. Adapte a busca por elementos conforme necessário.

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

## Comportamento da Barra de Pesquisa Rápida

A barra de pesquisa rápida no header (ao lado do botão "Menu") funciona para localizar tanto **processos** (pelo número no formato `XXXXX.XXXXXX/AAAA-DD`) quanto **documentos** (pelo número SEI, ex: `145869893`).

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

Use este fluxo para exibir apenas os processos atribuídos ao usuário logado na página "Controle de Processos". Este fluxo é independente e pode ser combinado com outros fluxos. Requer que o usuário já esteja autenticado no SEI (execute o Fluxo de Login primeiro, se necessário).

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

Use este fluxo para gerar e baixar um arquivo ZIP contendo todos os documentos de um processo SEI. Este fluxo funciona em **qualquer processo já aberto**, independentemente de como você chegou a ele (pesquisa direta, filtro de atribuídos, navegação manual, etc.). Requer que o usuário já esteja autenticado no SEI (execute o Fluxo de Login primeiro, se necessário).

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

---

## Checkpoints Críticos

Os seguintes checkpoints devem ser validados em TODOS os ciclos:

| Checkpoint | Passo | Elemento | Status |
|-----------|-------|---------|--------|
| **Fluxo: Login no SEI** |
| Pop-up fechado | 3 | Ausência do modal | ✓ Obrigatório |
| **Fluxo: Filtrar Processos Atribuídos ao Usuário** |
| Filtro aplicado | 3 | "Remover filtro de processos atribuídos a mim" | ✓ Obrigatório |
| **Fluxo: Gerar Arquivo ZIP de Processo** |
| Processo aberto | 1 | Número do processo no header | ✓ Obrigatório |
| Modal de ZIP aberto | 2 | Diálogo "Gerar Arquivo ZIP do Processo" | ✓ Obrigatório |
| Opção correta selecionada | 3 | "Todos os documentos disponíveis" marcado | ✓ Obrigatório |
| Download iniciado | 4 | Arquivo ZIP no folder downloads | ✓ Obrigatório |
| Retorno à lista (opcional) | 5 | Título "Controle de Processos" | ○ Opcional |

---

## Resultados Esperados

### Após completar o Fluxo "Login no SEI":
✅ Acesso autenticado ao sistema SEI  
✅ Pop-up de notificações fechado  
✅ Página "Controle de Processos" visível e pronta para uso

### Após completar o Fluxo "Filtrar Processos Atribuídos ao Usuário":
✅ Filtro "Atribuídos a mim" aplicado e ativo  
✅ Página "Controle de Processos" exibindo apenas processos do usuário  
✅ Elemento de confirmação "Remover filtro de processos atribuídos a mim" visível

### Após completar o Fluxo "Gerar Arquivo ZIP de Processo":
✅ Arquivo ZIP gerado com todos os documentos do processo  
✅ Nome do arquivo: `SEI_[número_do_processo].zip`  
✅ Arquivo salvo na pasta de downloads  
✅ (Opcional) Retorno à página de origem  
