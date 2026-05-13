Fonte: https://github.com/heygen-com/hyperframes

# HyperFrames

HyperFrames é um framework open source para criar, pré-visualizar e renderizar vídeos a partir de HTML. A proposta central é simples: escrever uma composição em HTML, CSS e JavaScript, visualizar no navegador e renderizar o resultado como vídeo, normalmente em MP4, usando uma pipeline baseada em navegador headless e FFmpeg.

O projeto é construído com foco em agentes de IA. Em vez de exigir que o usuário trabalhe diretamente com uma DSL proprietária ou com componentes React, o HyperFrames usa HTML com atributos `data-*` para descrever cenas, duração, camadas, áudio, vídeo, imagens e sobreposições. Isso facilita o uso por ferramentas de vibecode e agentes como Claude Code, Codex, Cursor, Gemini CLI e outros, já que esses agentes já costumam gerar HTML, CSS e animações com facilidade.

Uma composição típica define um elemento de palco com resolução e duração, adiciona vídeos, imagens, textos, áudio e animações, e depois usa a CLI do HyperFrames para iniciar o projeto, abrir preview com live reload, validar a composição e renderizar o vídeo final. O fluxo básico é `npx hyperframes init`, `npx hyperframes preview` e `npx hyperframes render`.

O HyperFrames se diferencia por ser HTML-native, determinístico e orientado a automação. Ele permite usar runtimes de animação como GSAP, Anime.js, CSS Animations, Lottie, Three.js e Web Animations API por meio de um padrão de adapters, mantendo as animações buscáveis e frame-accurate durante a renderização. Isso é importante porque vídeos renderizados por agentes precisam ser reproduzíveis: o mesmo código deve gerar o mesmo resultado.

O repositório também inclui uma coleção de skills para ensinar agentes a usar o framework corretamente. Essas skills cobrem autoria de composições, uso da CLI, pré-processamento de mídia, instalação de blocos do catálogo, conversão de websites para vídeo, tradução de composições Remotion para HyperFrames e padrões específicos para bibliotecas de animação.

Além do núcleo de renderização, o projeto oferece pacotes separados para CLI, core, engine, producer, studio, player e transições com shaders. Há também um catálogo de blocos reutilizáveis, como overlays sociais, transições, gráficos animados e efeitos visuais, que podem ser adicionados a projetos com comandos da CLI.

Em comparação com Remotion, o HyperFrames aposta em HTML como formato principal de autoria, enquanto Remotion usa componentes React. Ambos usam navegador headless e renderização determinística, mas o HyperFrames busca reduzir a necessidade de build step e facilitar a geração direta por agentes. O projeto é licenciado sob Apache 2.0, permitindo uso comercial e redistribuição dentro dos termos dessa licença.

Em resumo, HyperFrames é uma ferramenta para transformar HTML animado em vídeo de forma programável, reprodutível e amigável a agentes de IA. Ele é especialmente útil para vídeos gerados por código, demos de produto, motion graphics, overlays, gráficos animados, conteúdos sociais, composições com áudio e pipelines automatizadas de criação de vídeo.
