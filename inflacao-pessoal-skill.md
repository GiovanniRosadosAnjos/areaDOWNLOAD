---
name: inflacao-pessoal
description: Acompanha os gastos reais do usuário (por nota fiscal e categoria) ao longo do ano e calcula a "inflação pessoal" — a variação de preço ponderada pelo padrão de consumo dele mesmo, em vez da cesta média usada pelo IPCA/INPC. Use esta skill sempre que o usuário mencionar inflação pessoal, custo de vida, quanto gastou em determinada categoria, quiser lançar uma nota fiscal ou um gasto, pedir para comparar preços de um ano para o outro, ou quiser saber se a inflação oficial reflete o estilo de vida dele — mesmo que ele não diga explicitamente "inflação pessoal".
---

# Inflação Pessoal

## Por que essa skill existe

Índices oficiais como IPCA/INPC usam uma cesta de consumo média nacional, com pesos fixos por categoria (alimentação, transporte, moradia, saúde etc.). Isso quase nunca reflete o consumo real de uma pessoa específica: quem não tem carro não sente a variação da gasolina; quem trabalha de casa gasta mais com internet e energia e menos com transporte público, por exemplo. A "inflação pessoal" é o índice recalculado usando os pesos reais de gasto do próprio usuário, ano a ano — e, no nível de detalhe certo, também mostra qual produto específico (ex.: carne, óleo de soja, uma fruta) está puxando o aumento dentro de uma categoria.

## Onde os dados vivem

O registro do usuário é o arquivo **`inflacao-pessoal.md`**, mantido no repositório GitHub `GiovanniRosadosAnjos/areaDOWNLOAD` (na branch de trabalho em uso no momento — confira com o usuário se não estiver claro qual). Esse arquivo é a **única fonte de verdade**.

**O Google Drive não deve mais ser usado para este projeto.** Foi testado como opção, mas as ferramentas de Drive disponíveis não editam conteúdo de um arquivo existente — só leem, criam ou apagam — o que obrigava a recriar o arquivo inteiro a cada lançamento. O Git resolve isso de verdade: dá pra editar só a linha que mudou, com histórico de versões. Não leia, não grave e não recrie nada no Drive a partir de agora, mesmo que ainda exista um arquivo antigo por lá.

Fluxo de leitura/escrita:
1. Leia o estado atual com `mcp__github__get_file_contents` (owner, repo, path `inflacao-pessoal.md`, `ref` apontando pra branch em uso).
2. Para alterar, edite apenas o trecho necessário (ex.: adicionar uma linha na tabela do mês) e grave com `mcp__github__create_or_update_file`, passando o `sha` do arquivo atual (obtido no passo 1) e uma mensagem de commit curta e descritiva.
3. Não crie arquivos duplicados ou paralelos sem que o usuário peça.

## Nota de terminologia

O usuário às vezes evita dizer a palavra "skill" em voz alta porque o reconhecimento de voz erra a transcrição dela (não é uma palavra em português). Quando ele disser "função" nesse contexto (ex.: "chame a função X", "sem invocar a função"), entenda como sinônimo de "skill".

## Estrutura de dados (padrão atual — lançamento ITEMIZADO por padrão)

Desde 06/09/2026, todo lançamento novo é feito **por item individual da nota** (produto, quantidade, preço unitário), não mais como total agregado da nota inteira. Isso é o que permite responder perguntas como "quanto custava o limão em setembro" ou "o que mais subiu: carne ou fruta?" — um total agregado nunca vai responder isso, porque dilui o preço de cada produto.

1. **Categorias e subcategorias** — lista criada a partir do que o usuário realmente gasta (não da cesta do IPCA). Ponto de partida:
   - **Alimentação**: Carnes, Grãos/Cereais, Frutas, Verduras/Legumes, Laticínios, Padaria/Doces, Bebidas, Higiene/Limpeza, Outros
   - Moradia, Transporte, Saúde, Lazer, Assinaturas/Serviços digitais, Outros

   Adicione, remova ou renomeie categorias/subcategorias sempre que o uso real do usuário pedir.

2. **Lançamentos por ano/mês, em duas tabelas possíveis por período**:
   - **Itemizada** (padrão para tudo daqui pra frente): uma linha por produto, com categoria, subcategoria, item, quantidade, valor unitário e valor total, mais a origem (nome do estabelecimento).
   - **Agregada** (só para notas antigas, de antes da mudança de formato, onde o detalhamento por item não está disponível): uma linha por nota, com categoria, descrição e valor total.

```markdown
## 2026

### Setembro — Lançamentos agregados (sem detalhamento por item)
| Data | Categoria | Descrição | Valor (R$) |
|------|-----------|-----------|------------|
| 05/09 | Alimentação | Supermercado Dalben (cebola, limão, carne, frango a passarinho) | 51,71 |

### Setembro — Lançamentos itemizados
| Data | Categoria | Subcategoria | Item | Qtd | Valor unit. (R$) | Valor total (R$) | Origem |
|------|-----------|--------------|------|-----|-------------------|-------------------|--------|
| 06/09 | Alimentação | Verduras/Legumes | Batata | 0,775 kg | 8,99/kg | 6,97 | Supermercado Dalben |
```

## Registrando um gasto

Quando o usuário disser algo como "gastei X em Y", mandar a foto de uma nota fiscal, ou mandar dados de um gasto:

1. **Sempre que houver detalhamento por item disponível (foto de nota, lista de produtos com preço) — lance ITEMIZADO por padrão**, sem precisar perguntar se deve fazer assim: uma linha por produto, com categoria, subcategoria (pergunte ou infira pelo tipo do produto — carne vira Carnes, fruta vira Frutas, etc.), quantidade, valor unitário e valor total, e a origem (estabelecimento).
2. Só use o formato agregado (uma linha só, com total da nota) quando o usuário **não tiver** o detalhamento por item (ex.: só sabe o total gasto, ou a nota já foi perdida) — nesse caso, deixe claro que aquele lançamento ficará sem rastreio de preço por produto.
3. Leia o arquivo atual no GitHub, adicione a(s) linha(s) na tabela do mês/ano e formato correspondente (crie a seção do mês/ano/formato se ainda não existir) e grave de volta com `create_or_update_file`.
4. Confirme o lançamento em uma frase curta — não é necessário mostrar o arquivo inteiro a cada lançamento, mas ofereça mostrar a tabela se o usuário pedir para "ver como ficou".
5. Se o usuário mandar uma nota de uma data já lançada como agregada anteriormente e agora tiver o detalhamento por item, ofereça substituir aquele lançamento agregado pela versão itemizada (não deixe os dois duplicados).

## Calculando a inflação pessoal (comparação ano a ano ou por produto)

**Regra central: nunca confundir efeito-preço, efeito-consumo e efeito-loja.** O valor total pago por um produto ou categoria muda por motivos independentes entre si:
- **Efeito-preço**: o preço unitário do produto mudou ao longo do tempo (isso é inflação de fato).
- **Efeito-consumo**: o usuário comprou mais ou menos quantidade (não é inflação).
- **Efeito-loja**: o produto foi comprado em estabelecimentos diferentes, que cobram preços diferentes pelo mesmo item no mesmo período (também não é inflação — é diferença de preço entre lojas).

Uma variação no valor total, sozinha, não separa nenhum desses três. Sempre que for falar em "inflação" (variação de preço no tempo), a comparação correta é: **mesmo produto, mesma origem/loja (coluna Origem), valor unitário, em datas diferentes**. Se a origem mudar entre os dois pontos comparados, isso é um sinal de alerta — a diferença pode ser efeito-loja, não inflação.

Quando o usuário pedir o cálculo (ex.: "qual foi minha inflação pessoal esse ano", "compara com o IPCA", "quanto custava o limão em tal mês", "isso subiu porque ficou mais caro, porque comprei mais, ou porque troquei de mercado?", "qual mercado é mais barato para X"):

1. **Consulta de preço de um produto específico**: busque todas as linhas itemizadas desse produto (ou variações próximas do nome) ao longo do tempo e monte a série de **valor unitário** (R$/kg, R$/un etc.) por data **e por origem** — nunca o valor total. Se o produto só aparecer em lançamentos agregados (sem detalhamento), avise que não é possível isolar o preço dele naquela nota.
2. **Efeito-preço por produto (mesma origem, ao longo do tempo)**: para cada produto que aparece em duas ou mais datas **na mesma origem**, calcule a variação percentual do valor unitário: `(preço_unit_atual - preço_unit_anterior) / preço_unit_anterior * 100`. Essa é a inflação real daquele produto naquele estabelecimento, livre de quanto foi comprado e de qual loja foi usada.
3. **Efeito-loja (mesma época, origens diferentes)**: se o mesmo produto aparecer em origens diferentes num período próximo, compare os valores unitários entre elas e reporte como "diferença entre lojas", nunca como inflação — ex.: "no Mercado A custava R$10/kg, no Mercado B R$12/kg, na mesma época: o Mercado B é mais caro para esse produto, isso não é inflação."
4. **Quando origem muda entre os dois pontos de tempo comparados** (ex.: comprou no Mercado A em janeiro e só tem o mesmo produto no Mercado B em março): não atribua a diferença a inflação sem antes checar se há um ponto de mesma origem em outra data que permita isolar o efeito-preço puro. Se não houver, avise explicitamente que a variação observada mistura efeito-preço com efeito-loja e não pode ser separada com os dados disponíveis.
   - **Exceção prática — janela de tempo curta**: se as duas compras em origens diferentes aconteceram poucos dias (ou pouquíssimas semanas) uma da outra, inflação real não costuma se mover o suficiente para explicar diferenças grandes nesse intervalo. Nesse caso, é razoável concluir diretamente que a diferença é **efeito-loja** (aquele mercado é mais caro), não inflação — não é preciso ficar em cima do muro. Diga isso com confiança (ex.: "de segunda pra quinta o preço não pula 50% por inflação; isso é o Mercado B sendo mais caro que o Mercado A para esse produto"). Só trate como ambíguo quando o intervalo de tempo for longo o bastante (meses) para que preço realmente possa ter mudado nesse meio tempo.
5. **Efeito-preço por categoria**: combine as variações de valor unitário (calculadas preferencialmente dentro da mesma origem, como no passo 2) dos produtos da categoria, ponderando pela participação de gasto de cada produto no total da categoria no período anterior — isso dá a "inflação pessoal" da categoria isolada de mudança de consumo e de troca de loja.
6. **Efeito-consumo (opcional, mas mostre quando for relevante)**: se o valor total gasto num produto/categoria mudou muito mais (ou menos, ou até na direção oposta) do que a variação de preço unitário explicaria, aponte isso explicitamente — ex.: "o preço do limão subiu 5%, mas seu gasto total com limão subiu 300% porque você comprou 4x mais quantidade; não é o limão que ficou mais caro, é você consumindo mais."
7. **Inflação pessoal total**: ponderando pela participação de cada categoria no gasto total do ano anterior (peso real do usuário, não o peso do IPCA):
   `inflação_pessoal = soma(peso_categoria * variação_de_preço_da_categoria)`
   — use a variação de **preço** isolada (passos 2 e 5), não a variação de gasto total nem comparações entre origens diferentes, para este número representar inflação de fato.
8. Apresente o resultado por categoria e por produto (do que mais subiu para o que menos subiu/caiu), separando claramente efeito-preço, efeito-consumo e efeito-loja, o número consolidado de inflação pessoal, e compare com a inflação oficial do período se o usuário tiver informado o valor do IPCA/INPC.
9. Seja transparente sobre limitações: se faltarem meses de lançamento, avise que a comparação é parcial; se um produto/categoria não teve nenhum gasto num dos períodos, não dá pra calcular variação percentual dele (deixe claro em vez de inventar um número); se um produto só existir em lançamento agregado num dos períodos, avise que não dá pra separar os efeitos para ele; se só houver dados do produto em origens diferentes (sem nenhum par na mesma origem), avise que a variação de preço não pode ser isolada da diferença entre lojas.

## O que evitar

- Não presuma categorias ou pesos do IPCA — a ideia inteira é o oposto disso.
- Não invente valores, quantidades ou preços unitários que o usuário não informou (nem que não estejam legíveis na foto da nota).
- Não lance agregado quando o detalhamento por item está disponível — isso é o que perde a capacidade de rastrear preço por produto no futuro.
- Não crie planilhas/arquivos novos sem necessidade; `inflacao-pessoal.md` no GitHub é a fonte única, a menos que o usuário peça explicitamente outro formato.
- Não leia nem grave nada no Google Drive para este projeto — foi descontinuado.
