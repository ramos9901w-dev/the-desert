# The Desert — projeto modular

Versão migrada de `deserto_jogo_v136.html` (964 KB, um único arquivo) para uma estrutura de arquivos separados.

**Status:** migração estrutural concluída e validada (sintaxe, hashes, ordem de carregamento, Three.js vendorizado). **Os testes de jogo ainda não foram feitos.** Ver `docs/ARQUITETURA.md`, seção "Testes".

## Como abrir

**Modo online (padrão):** abra `index.html`. Ele lê `manifest.json` e os módulos do GitHub pelo jsDelivr e reconstrói o jogo.

**Modo local:** precisa de um servidor local (módulos ES não funcionam com `file://`):

```
python -m http.server 8000
```

Depois abra `http://localhost:8000/index.html?mode=local`.

O modo também pode ser trocado no topo de `index.html` (`mode: 'online'` ou `'local'`).

## Estrutura

```
index.html          HTML leve: canvas, configuração de caminhos e bootstrap do importmap
manifest.json       Lista ordenada dos arquivos, hashes, dependências e descrições
core/loader.js      Carregador: lê o manifesto, pré-busca em paralelo e executa em ordem
ui/                 Inventário (CSS e JS)
player/             Cavalo do jogador e arqueiro
animals/            Cavalos de tráfego, fauna (lagartos/águias), crocodilos
world/              Estradas, terreno, captura de ponteiro
combat/             Efeitos de combate, orbe de vida, inimigos com arco
assets/             Modelos da vitrine
libraries/three/    Three.js 0.181.0 (usado no modo local)
tools/              Script para atualizar o manifesto após editar arquivos
docs/               Documentação de arquitetura
```

## Arquivos

| Ordem | Arquivo | Tipo | Bytes | Minificado* | Descrição |
|---|---|---|---|---|---|
| 1 | `ui/inventory.css` | css | 3.852 | não | Estilo do painel de inventário (#invRoot). Injetado pelo carregador. |
| 2 | `ui/inventory.js` | classic | 10.161 | sim | Interface do inventário: equipar roupas. armaduras e acessórios. Script clássico. executado antes dos módulos (como no original). |
| 3 | `player/horse-system.js` | module | 65.450 | não | Sistema de cavalos (window.__createHorseSystem). Descrição provisória: confirmar. |
| 4 | `animals/traffic-horses.js` | module | 200.387 | sim | Tráfego autônomo de cavalos com cavaleiro: grafo de estradas. A*. evasão e máquina de estados. Portado de jogo_mexicanos_vida_min. |
| 5 | `animals/fauna.js` | module | 91.948 | não | Fauna: sistemas de lagarto. águia e crocodilo. com constantes de ragdoll (window.__createLizardSystem. __eagleBoot. __crocBoot). Descrição provisória. |
| 6 | `animals/crocodiles.js` | module | 40.330 | não | Crocodilos: boot. spawn e depuração (window.__crocBoot. __crocs. __crocSpawnNow). Descrição provisória. |
| 7 | `world/roads-and-terrain.js` | module | 335.519 | sim | Mundo: planejamento de estradas. cache de rotas por célula. captura de ponteiro e testes de terreno. Contém código minificado. Descrição provisória. |
| 8 | `assets/vitrine-models.js` | module | 12.487 | sim | Modelos da vitrine. Expõe THREE como window.__T. Contém código minificado. |
| 9 | `combat/combat-fx-and-enemy-bows.js` | module | 146.384 | sim | Efeitos de combate. orbe de vida do jogador. sangue e inimigos com arco. Contém código minificado. Descrição provisória. |
| 10 | `player/bow.js` | module | 57.031 | sim | Arqueiro do jogador: arco. flecha e animação copiada do gerador de arco. Contém código minificado em partes. |

\* "Minificado" é marcado quando há linhas com mais de 500 caracteres. Esses trechos vieram assim do original e foram mantidos sem alteração.

## Editando

1. Edite o arquivo da funcionalidade. Você não precisa tocar em `index.html`.
2. Rode `python tools/atualizar_manifesto.py` para recalcular hashes e a versão.
3. Publique (commit e push). Em modo online, o jogador recebe a nova versão porque a URL muda com o hash do conteúdo.

Para criar um sistema novo: crie o arquivo, adicione uma entrada em `manifest.json` na posição certa da ordem de execução e rode o script de atualização.

Mais detalhes em `docs/ARQUITETURA.md`.
