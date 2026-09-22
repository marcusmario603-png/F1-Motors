# F1 Motors — Painel Comercial

Dashboard interativo de vendas para a F1 Motors, rede de concessionárias de superluxo (Ferrari, Lamborghini, Porsche, Rolls-Royce, McLaren, Aston Martin e Mercedes-Benz) do sul fluminense. Construído como uma única página HTML autocontida, sem backend, sem instalação e sem dependência de servidor.

## Finalidade

O painel replica, diretamente no navegador, o rigor analítico de uma ferramenta de BI (Power BI/SQL) para acompanhar o desempenho comercial das lojas de Volta Redonda, Barra Mansa, Resende, Vassouras e Barra do Piraí entre janeiro e agosto de 2026: faturamento, volume de veículos, ticket médio, evolução ao longo do tempo, desempenho por vendedor, por marca e por praça — tudo recalculado em tempo real conforme os filtros aplicados.

## Fonte dos dados

- Arquivo original: `Base_Vendas_F1_Motors.xlsx`, aba `Base_Consolidada` (1.819 vendas).
- Campos usados: data da venda, marca, modelo, cliente, vendedor, cidade/loja, quantidade e valor da venda.
- Os dados foram extraídos e embutidos diretamente no HTML como um array JSON (variável `RAW` no `<script>`), por isso o painel não faz nenhuma requisição externa nem depende de planilha aberta em paralelo.

> Para atualizar os dados no futuro, é necessário reprocessar a planilha e substituir o conteúdo da constante `RAW` no arquivo — não há reimportação automática.

## Como usar

Basta abrir o arquivo `.html` em qualquer navegador moderno (Chrome, Edge, Safari, Firefox). Não requer internet, exceto para carregar as fontes (Google Fonts) e a biblioteca de gráficos (ECharts via CDN) na primeira vez.

## KPIs e lógica de cálculo

| KPI | Cálculo |
|---|---|
| **Faturamento total** | Soma de `valor` de todas as vendas que passam pelos filtros ativos. |
| **Veículos vendidos** | Soma de `quantidade` das vendas filtradas. |
| **Ticket médio** | Faturamento total ÷ veículos vendidos. |
| **Crescimento MoM** | Compara o faturamento do "mês em foco" com o do mês anterior. O mês em foco é o mês selecionado no filtro; se nenhum mês estiver selecionado, é o mês mais recente presente no recorte atual (respeitando cidade/vendedor/marca, mas ignorando o próprio filtro de mês). Sem mês anterior disponível (ex.: janeiro) ou sem base de comparação, o indicador mostra "—". |
| **Atingimento de meta** | Meta de R$ 1 bilhão por mês. Quando um mês específico está selecionado, a meta é R$ 1 bi; quando "Todos os meses" está ativo, a meta escala pelo número de meses distintos presentes no recorte filtrado (ex.: 8 meses → meta de R$ 8 bi). A barra mostra o percentual real, mesmo acima de 100%. |

## Filtros dinâmicos

Painel de controle no topo:

- **Mês** — Janeiro a Agosto, ou "Todos os meses".
- **Dia** — fica desabilitado até um mês ser escolhido; então lista apenas as datas daquele mês.
- **Cidade** — chips de múltipla seleção (as 5 praças); todas ativas por padrão, clique para incluir/excluir.
- **Vendedor** — lista dos 25 vendedores.

Qualquer alteração recalcula instantaneamente todos os KPIs, a meta e os quatro gráficos.

## Cross-filtering pelos gráficos

Além dos controles do topo, cada gráfico também funciona como filtro:

- **Evolução do faturamento** (linha/área) — clicar num ponto filtra o painel por aquele dia específico (o mês é ajustado automaticamente).
- **Vendas por região** (mapa estilizado do Médio Paraíba) — clicar numa cidade (bolha ou item da legenda) inclui/exclui aquela praça, sincronizado com os chips do topo.
- **Top 5 vendedores** (barras horizontais) — clicar numa barra isola aquele vendedor.
- **Participação por marca** (donut) — clicar numa fatia isola aquela marca. Esse é o único filtro de marca do painel — não há um seletor dedicado no topo.

**Regras de comportamento:**
- Clicar de novo no mesmo elemento remove aquele filtro específico (alternância liga/desliga).
- Os filtros se combinam (E lógico): é possível clicar numa marca, depois numa cidade e depois num vendedor, e o painel aplica os três ao mesmo tempo.
- Uma barra de **filtros ativos**, logo abaixo do cabeçalho, lista tudo que está em aplicação (inclusive o que foi definido por clique no gráfico) com um "×" para remover cada filtro individualmente, além de um botão **Limpar filtros** para resetar tudo de uma vez.

## Visualizações

1. **Cards de KPI** — faturamento, veículos vendidos, ticket médio e crescimento MoM.
2. **Barra de meta** — progresso visual contra a meta de R$ 1 bilhão/mês (ou múltiplo, conforme o período filtrado).
3. **Evolução do faturamento** — gráfico de área com faturamento diário; usa zoom/scroll quando há muitos pontos no período.
4. **Mapa regional** — SVG customizado com as 5 praças posicionadas de forma aproximada no Médio Paraíba, bolhas proporcionais ao faturamento de cada uma.
5. **Top 5 vendedores** — barras horizontais por faturamento.
6. **Participação por marca** — donut colorido com a identidade visual de cada fabricante (Ferrari em vermelho, Lamborghini em amarelo, Aston Martin em verde-racing, etc.).

## Stack técnica

- **HTML + CSS + JavaScript puro**, arquivo único, sem build/bundler.
- **ECharts 5.4.3** (via CDN cdnjs) para os gráficos de linha, barras e donut.
- **Google Fonts** — Fraunces (números e títulos) e Manrope (interface e rótulos).
- Sem framework, sem backend, sem chamadas de API em tempo de uso.

## Personalização

Pontos de ajuste concentrados no `<script>` do arquivo:

- `META_MENSAL` — valor da meta mensal (hoje R$ 1.000.000.000).
- `BRAND_HEX` / `BRAND_COLOR` — cores de cada marca usadas no donut e na faixa do cabeçalho.
- `CITY_POS` — coordenadas relativas (%) de cada cidade no mapa estilizado.
- `RAW` — o dataset em si; para atualizar os números, gere um novo JSON a partir da planilha e substitua essa constante.

## Limitações conhecidas

- Os dados são estáticos (um retrato da planilha no momento da geração do painel); não há atualização automática.
- O mapa regional é uma representação estilizada e aproximada da geografia do Médio Paraíba, não um mapa geográfico real com coordenadas precisas.
